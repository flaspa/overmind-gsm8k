# Exact final evaluation command (run once, 2026-10-07)

## 1. Inference server (RunPod pod xj0h79bc0d66ex, 1x NVIDIA H200 SXM, driver 580.159.04 / CUDA 13.0)

Software: vllm 0.31.0, torch 2.13.0+cu130, transformers 5.17.0.

```bash
export HF_HOME=/workspace/hf HF_HUB_OFFLINE=1
vllm serve unsloth/Qwen3-4B --revision 64033659d5caf1b8ed7f929b29de705e93a4d468 \
  --dtype bfloat16 --max-model-len 4096 --gpu-memory-utilization 0.9 --seed 1234 \
  --enable-lora --max-lora-rank 16 --lora-modules n1=/workspace/gsm8k/adapter \
  --host 0.0.0.0 --port 8000
```

`/workspace/gsm8k/adapter` was the N1 adapter copied from
`checkpoints/qwen3-4b-preserveN1-1ep-lr1e-6/adapter`. The SHA-256 of `adapter_model.safetensors` was checked on the pod:
`224e9ac74bfd1878bba27ec789508978df02355b61cecea1bb518a647118a6e7`. No quantization.

## 2. SSH tunnel (Windows → pod)

```powershell
ssh -i $env:USERPROFILE\.ssh\runpod_gsm8k_ed25519 -o IdentitiesOnly=yes -o ServerAliveInterval=30 -N -L 8000:localhost:8000 -p 16853 root@31.24.80.26
```

## 3. Evaluator (local Windows, lm-eval v0.4.13; tag object eb4678c395c2c1f157d462b8f3a09831160c861a → commit ddd67220430a2470529f25fd5c05a576ca1057a0)

Run from the repo root in PowerShell:

```powershell
$env:PYTHONIOENCODING="utf-8"
.venv-eval\Scripts\lm_eval.exe --model local-completions --model_args "model=n1,base_url=http://127.0.0.1:8000/v1/completions,tokenizer_backend=huggingface,tokenizer=unsloth/Qwen3-4B,num_concurrent=128,max_retries=3,max_length=4096" --tasks gsm8k --num_fewshot 5 --batch_size 1 --gen_kwargs "max_gen_toks=1024,temperature=0,do_sample=False" --output_path results/official_n1_h200 --log_samples
```

- Task: official `gsm8k` (openai/gsm8k `main`, test split, 1,319 questions), unmodified task YAML, no `--limit`.
- No `--apply_chat_template`, so prompts are plain text (`Question: ...\nAnswer:`).
- Greedy decoding, one response per question, stop sequences from the task YAML (`Question:`, `</s>`, `<|im_end|>`).
- Submission metric: `exact_match,strict-match`.
