# WSP Hub CLI (`wsphub` / `wsp`)

[![WSP Verified](https://api.wsphub.dev/api/v1/badges/wsp-qwen3.5-0.8b-math-loop-exp8.svg)](https://wsphub.dev/patch/wsp-qwen3.5-0.8b-math-loop-exp8)
[![License: Apache-2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

**Surgical Behavioral & Config Patches for Open-Weight Models (`Safetensors` Copy-on-Write + `Ed25519` Attestation).**

Stop re-downloading 16 GB full-model forks to fix a 64-column MLP reasoning loop or a broken `generation_config.json` stop-token array. `wsphub` applies cryptographically signed, empirically benchmarked **`.wsp v2.1`** patches in under 5 seconds with instant, bit-exact local rollback.

- **Live Registry & Benchmark Replay Studio:** [https://wsphub.dev](https://wsphub.dev)
- **Zero-Login Hugging Face Diagnostic Scanner:** [https://wsphub.dev/scan/Qwen/Qwen3.5-0.8B](https://wsphub.dev/scan/Qwen/Qwen3.5-0.8B)
- **CI/CD GitHub Action (`fast-ci` & `full-eval`):** [`GhazwanAlemara/wsp-ci-action@v2`](https://github.com/GhazwanAlemara/wsp-ci-action)

---

## Installation

```bash
pip install wsphub
# or run without installing globally:
pipx run wsphub --help
```

Both `wsp` and `wsphub` CLI entrypoints are installed automatically.

---

## Quickstart

### 1. Scan Any Hugging Face Model for Reproducible Config / Loop Flaws (`<2s`, Zero GPU)
```bash
wsp scan Qwen/Qwen3.5-0.8B
```

### 2. Apply a Cryptographically Signed `.wsp` Patch (`Copy-on-Write`, `<5s`)
```bash
wsp apply Qwen/Qwen3.5-0.8B \
  --patch wsp-qwen3.5-0.8b-math-loop-exp8 \
  --out ./patched_model
```

### 3. Verify `Ed25519` Signature & RFC 8785 Canonical Manifest Offline
```bash
wsp verify ./wsp-qwen3.5-0.8b-math-loop-exp8.wsp \
  --key-id ed25519-wsphub-official-01 \
  --pubkey-hex 5082a7cf5bf0ee4e4438fbc9b96f13d6d186b5ed110c623c3cf4e5c0ae76da9d
```

### 4. Bit-Exact Atomic Rollback
```bash
wsp revert ./patched_model
```

### 5. Export Patched Model to Ollama / Quantized GGUF
```bash
wsp export-ollama ./patched_model \
  --patch ./wsp-qwen3.5-0.8b-math-loop-exp8.wsp \
  --tag qwen3.5-wsp-cured:latest
```

---

## Security & Cryptographic Guarantees

1. **Zero Pickle Execution:** `.wsp v2.1` archives contain strictly canonical JSON (`manifest.json`), raw IEEE-754 `BF16` strided/sparse tensor deltas (`deltas.bin`), and deterministic JSON merge patches—never `pickle` or executable Python payloads.
2. **Dual-Artifact `Ed25519` Signatures:** Every verified registry artifact is signed over `RFC 8785 JCS(manifest) || SHA256(deltas.bin)` and anchored in the public SHA-256 hash-chained transparency log at [https://wsphub.dev/transparency](https://wsphub.dev/transparency).
3. **Pre-Registered Multi-Seed Evaluation:** All published patches pass locked `MATH-500` / `GSM8K` / `IFEval` splits, secret perturbed `WSP-Holdout`, and `StrongREJECT` / `XSTest` safety non-inferiority gates. Safety-removal / abliteration patches are hard-blocked.
4. **Opt-In Telemetry Only:** Zero network telemetry is sent unless `--opt-in-telemetry` or `WSP_TELEMETRY_OPT_IN=1` is explicitly enabled.

---

## License

Apache License 2.0 — see [LICENSE](LICENSE).
