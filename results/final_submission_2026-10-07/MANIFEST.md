# Final submission manifest: 2026-10-07

## Competition
Overmind Small Model, Big Maths

## Final model
- **Base:** unsloth/Qwen3-4B (HF revision `64033659d5caf1b8ed7f929b29de705e93a4d468`)
- **Fine-tune:** N1 LoRA (`gsm8k-lora-preserve-N1-qwen3-4b-1ep-lr1e-6`)
- **Overmind run ID:** 38446c1b-da32-456e-8283-58b1fba63ba2
- **Overmind deployed model ID:** f33a321a-e66d-48fd-8e9e-9b48cb8e8fb7 (checkpoint-final, step 841)
- **Training:** 1 epoch, LR 1e-6, r=8, alpha=16, dropout=0, all-linear (q/k/v/o/gate/up/down_proj), bf16
  - Other settings from `training_args.bin` (trl SFTConfig): cosine schedule, warmup_steps=0.05, per-device batch 8, grad accum 1, weight decay 0.01, adamw_8bit, max_grad_norm 1.0, max_length 4096, no packing, seed 42.
  - Full details: `training_config/n1_sftconfig_decoded_from_training_args.bin.json` and `training_config/overmind_finetune_job.json`.
  - Overmind records the base as `Qwen/Qwen3-4B`. The adapter config names `unsloth/Qwen3-4B`, which was used for evaluation.
- **Adapter identity:** `adapter_model.safetensors` SHA-256 `224e9ac74bfd1878bba27ec789508978df02355b61cecea1bb518a647118a6e7` (66,126,768 bytes). See `adapter_metadata/ADAPTER_IDENTITY.json`. The adapter weights themselves are not committed.

## Selection
- **N1 validation screen:** 58/64 (strict-match 0.90625; `results/screen64_lora/qwen3-4b-preserveN1-1ep-lr1e-6/`)
- **Baseline, same 64 questions:** 56/64 (base Qwen3-4B, strict-match; recomputed from `results/screen192/qwen3-4b/` samples restricted to `data/splits/screen64_ids.txt`)
- The screens used only the internal validation split, held out from GSM8K train.

## Overmind datasets
- **Training/validation dataset:** `4ea3e8ca-ef9b-4122-a760-7f71f50c1ddd`
  - Train cell `661ccfe6-4087-4f5d-b8a7-41de9857daae`: 6,726 examples
  - Eval cell `b4be7bbe-2335-4708-acac-4bd662c83e33`: 747 examples
- **Final 7,473-row dataset:** `0157042a-1327-44b4-a62f-a85ade06c09c`. Uploaded but **NOT** used to retrain N1.

## Official final evaluation
- **Date:** 2026-10-07
- **EleutherAI lm-eval:** v0.4.13
- **Commit:** eb4678c395c2c1f157d462b8f3a09831160c861a (annotated tag object for v0.4.13; resolves to commit ddd67220430a2470529f25fd5c05a576ca1057a0)
- **Task:** gsm8k (official, unmodified)
- **Questions:** 1,319
- **Few-shot:** 5
- **Prompt:** plain text; no chat template
- **Decoding:** greedy
- **Responses/question:** 1
- **Max generated tokens:** 1024
- No calculator, tools, retrieval, ensemble or self-consistency
- **Inference:** H200 + vLLM via local-completions (vLLM 0.31.0, bf16, no quantization, LoRA served as `n1`)
- **Concurrency:** 128
- **Exact command:** see `FINAL_EVAL_COMMAND.md`

## STRICT RESULT
- **Correct:** 1,145 / 1,319 (recounted from the samples file)
- **Accuracy:** 86.8082%
- **Reported lm-eval strict-match:** 0.8681 ± 0.0093
- **Flexible-extract:** 0.8666 ± 0.0094 (1,143 / 1,319)
- **Runtime:** 98 seconds total; ~44 seconds generation

## Competition score
Score = accuracy minus model parameters.

Verified parameter counts:
- Base unsloth/Qwen3-4B @ `64033659…`: **4,022,468,096** (HF safetensors metadata, all BF16)
- N1 LoRA adapter: **16,515,072** (504 tensors, counted from `adapter_model.safetensors`)
- Base + unmerged LoRA: **4,038,983,168**. A merged model keeps the base count, 4,022,468,096.

Adjusted score: **PENDING**. The competition's parameter convention (merged vs. base+adapter, and units) has not been confirmed yet. It will be recorded here once confirmed, without rounding or guessing.

## Artifacts
- **Final result file:** `results_2026-10-07T17-03-18.978697.json`
- **Samples file:** `samples_gsm8k_2026-10-07T17-03-18.978697.jsonl` (2,638 rows = 1,319 questions × 2 filters)

| SHA-256 | File |
|---|---|
| 8a438726aaf2c92e68d1f870275a68809323590d53dfba4bb0937a62f22b7537 | results_2026-10-07T17-03-18.978697.json |
| fd0050390ce04aab4baf6dab2e8848e4f14f679ce9b7fef4f7a362f7d7bbc563 | samples_gsm8k_2026-10-07T17-03-18.978697.jsonl |
| 821fdc42432a9b0d37b35fd15c13684751f3bf77e8a974cb06624d48a8fc6d00 | lm_eval.log |
| 1e465a32f3272eeac63c69f79ec403a7dfad89b9357046093c7e203d93792b41 | runtime.txt |
| 7174daf040ca38965ab2220102a312136269e849a31c63c8bd4bd5c522f3c373 | FINAL_EVAL_COMMAND.md |
| 07717830543f7a865612aca41aca12fced7227ecc4f9bc8a19da85c8ecf15a77 | adapter_metadata/ADAPTER_IDENTITY.json |
| f99dd94a322d435938b27ebef5fbddf93b5e50edae8d24098169eb5e3d2886b2 | adapter_metadata/adapter_config.json |
| 4323a0032c83f30c65009e298764da738360bc8ddb5b55112b0a7599eac501bc | adapter_metadata/tokenizer_config.json |
| 3124608f711f91840c7fa34c6ff14a6cad06bbbd1891b494d02e84b00d3c0876 | adapter_metadata/README.md |
| a8070d0af06cdfd1cf1e527926c2a6052374985087819b152e1cb81c3b291a1b | training_config/n1_sftconfig_decoded_from_training_args.bin.json |
| 10be5361a3a8e12947ae48619429b6024c96c843932367654e22746c8681d732 | training_config/overmind_finetune_job.json |

`SHA256SUMS.txt` covers every file in this directory, including this manifest.

## IMPORTANT
- The official test set was not used for model selection.
- The final 1,319-question evaluation was run once, after N1 was frozen.
