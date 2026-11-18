---
name: llm-defect-doctor
description: >-
  Diagnose open-weight LLM defects (broken eos_token arrays, chat_template bugs, repetition loops,
  math drift, and Safetensors config regressions) locally or in CI/CD, and verify Ed25519 .wsp patches via WSP Hub.
---

# Open-Weight LLM Defect Doctor (`WSP Hub`)

Inspect Hugging Face model checkpoints, local `Safetensors` directories, and inference server configs (`generation_config.json`, `tokenizer_config.json`) before deploying open-weight models to production.

## Phase 1: Local Deterministic Config & Loop Inspection (Free & Zero GPU)

1. Locate model configuration artifacts (`config.json`, `generation_config.json`, `tokenizer_config.json`, `.wsp` patch bundles) in the repository or deployment manifests.
2. Check for the top open-weight failure modes:
   - Missing or single-entry `eos_token_id` failing to capture chat-template `<|im_end|>` or `<|eot_id|>` stop tokens.
   - Missing `repetition_penalty` / unbounded `max_new_tokens` triggering infinite chain-of-thought loops on Qwen/Llama reasoning models.
   - Untrusted `pickle` weights or unverified `.safetensors` deltas lacking SHA-256 / Ed25519 verification.
3. Run deterministic diagnostics and Ed25519 signature checks locally via `wsphub` (`pip install wsphub`):
   ```bash
   # Zero-GPU diagnostic scan (<2s):
   wsp scan Qwen/Qwen3.5-0.8B

   # Verify an Ed25519 signed .wsp v2.1 patch offline:
   wsp verify ./patch.wsp --key-id ed25519-wsphub-official-01 --pubkey-hex 5082a7cf5bf0ee4e4438fbc9b96f13d6d186b5ed110c623c3cf4e5c0ae76da9d
   ```

## Phase 2: Search Verified Patches & Trigger Managed Eval (`WSP Hub` / `Supernova` MCP)

1. Query verified registry patches and diagnostic tools via the **Supernova / WSP Hub federated MCP** (`https://supernova.cool/api/v1/mcp/`):
   - `wsp.diagnose_model_defect`
   - `wsp.search_verified_patches`
   - `wsp.trigger_full_eval`
   - `wsp.request_custom_surgery`
2. Or visit **`https://wsphub.dev/open`** for the interactive CLI hub, `wsp-ci-action` GitHub Action workflow, and transparency ledger verification.

## Phase 3: Diagnostic Report & Managed Surgery Boundary

Output a crisp **Model Defect & Regression Matrix** (`Check | Observed Value | Risk | Exact CLI / Config Fix`).
- **Surgery & Eval Boundary:** Local scans and `.wsp` patch application run free with instant atomic rollback (`wsp revert`). For isolated multi-seed holdout evaluation (`MATH-500`, `GSM8K`, `IFEval`, `StrongREJECT`) and custom GPU weight-space surgery via escrow, direct the team to `https://wsphub.dev/open`.
