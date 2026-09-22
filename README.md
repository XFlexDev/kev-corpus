# kev-corpus

Training corpus for KEV — a discriminative decision model (Qwen 0.5B).

- 463,514 multiple-choice samples across 7 task types
- 14 JSONL shards (~1.38 GB) — see release asset `train_kev_code.tar.gz`
- Schema: `{"context", "question", "options"[4], "target", "task", "source"}`

## Tasks
| task | samples |
|---|---|
| response_select | 139,797 |
| chat_response_select | 29,883 |
| docstring_match | 29,141 |
| signature_select | 15,482 |
| language_id | 119,211 |
| bug_diagnosis | 80,000 |
| type_completion | 50,000 |

## Sources
Hugging Face: KodCode, OpenCodeReasoning, Magicoder (OSS + Evol), CodeFeedback, Evol-Instruct-Code, smoltalk, CodeAlpaca (x2), iamtarun 120k, TokenBender 122k, ajibawa Code-74k, flytech python-25k, self-oss-instruct, code_search_net (JS), semeru code-text-js.
GitHub: DefinitelyTyped, type-fest, TypeScript lib defs, type-challenges, zod, tRPC, nest, drizzle, typescript-book, TS handbook docs.
