# kev-corpus

MCQ training data for KEV — discriminative decision model on Qwen2.5-0.5B.

## Releases

### v2.0.0 — Gemini-spec, ≤256-token prompts
`kev_v2.tar.gz` (files at archive root, `json.load`-able arrays):
- `train_multitask.json` — 360K samples
- `val_multitask.json` — 10K
- `kev_20min.json` — 110K balanced slice (~20 min on 2×T4)
- `kev_fast_35k.json` — 35K benchmark slice
- `train_multitask.jsonl` — same as train, JSONL
- `kev_v2_manifest.json`

Schema: `{"context", "question", "options"[4], "target", "task"}`
- options ≤ 140 chars, context ≤ 110 words → fits the 256-token prefill budget.
- Tasks: language_id (60K) · bug_diagnosis (90K) · type_completion (60K) · api_select (60K) · signature_select (30K) · docstring_match (30K) · response_select (40K)

### v1.0.0 — raw long-form corpus
`train_kev_code.tar.gz` — 463K samples, 14 JSONL shards, long answers.

## Baking into Qwen (merged checkpoint, no adapter files)

```python
model = accelerator.unwrap_model(model)
model.base_model = model.base_model.merge_and_unload()  # bake LoRA into weights
torch.save(model.state_dict(), "kev_state_dict.pt")
# or as a vanilla-looking HF repo:
model.base_model.save_pretrained("./qwen-kev")   # Qwen2ForCausalLM arch + your weights
tokenizer.save_pretrained("./qwen-kev")
# pointer head (~1MB) stays a separate small file — rename it whatever
torch.save({"query_proj": model.query_proj.state_dict(),
            "key_proj": model.key_proj.state_dict()}, "model_heads.safetensors")
```

After `merge_and_unload`, the exported repo is indistinguishable from a stock
Qwen2.5-0.5B: same `config.json` arch and file layout; only weight values differ
(plus 3 extra embedding rows for `<decide>`, `<opt>`, `</opt>`).
