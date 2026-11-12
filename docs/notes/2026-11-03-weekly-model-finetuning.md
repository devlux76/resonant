# Note: automatic weekly fine-tuning of local role models (operator request, 2026-11-03)

Status: idea for the Resonant design (paused until next week). Not yet folded into README.md. Candidate homes: §4.5 Calibration memory (the training signal), §11 Model selection/quality metrics (the promotion gate), §12.3 P2 (architecture must support it).

## Requirement
Resonant needs a built-in way to fine-tune its local models automatically, roughly once a week. The first target is **LFM3-1.7B** wherever it holds a standing responsibility, for example the **postmaster** role (stamping/addressing messages, repairing format, reaching back to senders) and other function-calling duties.

## Recipe the operator supplied (summary)
- **Method:** QLoRA SFT with Hugging Face `trl` (SFTTrainer) + `peft` (LoRA r=16, alpha=32, dropout 0.05) on `LiquidAI/LFM3-1.7B`, 4-bit NF4 via bitsandbytes, lr 2e-4, 3 epochs, batch 4 × grad-accum 4, max_seq_length 4096, paged_adamw_8bit.
- **Targets:** LFM3 is tri-hybrid (convolution + sliding-window + global attention). LoRA targets the attention projections `q_proj, k_proj, v_proj, o_proj`, not the conv blocks.
- **Data:** chat-format JSONL (`{"messages":[system with tool list, user, assistant tool-call JSON]}`).
- **Output:** a LoRA adapter (`save_pretrained`).

## What Resonant must add around it (Claude's notes)
1. **Training signal from our own records.** Weekly data comes from durable history, not synthetic guesses:
   - postmaster cases a frontier or the operator corrected (original message → correct stamped result);
   - reach-back exchanges that resolved an ambiguity;
   - §4.5 calibration records (claimed versus actual).
   Keep the provenance of each example, and exclude private secrets (the privacy hook patterns).
2. **A promotion gate, never blind replacement.** Hold out a fixed evaluation set (plus every past regression). Promote the new adapter only if it beats the current one on that set and doesn't regress any trust-critical case (identity/binding, outcome preservation). Otherwise keep the current model and record why. Versioned adapters, one-step rollback.
3. **Serving path.** Production runs on llama.cpp GGUF (router :8094, `lfm3-1.7b-q8`). Pipeline: merge the adapter → convert to GGUF (`convert_hf_to_gguf.py`) → quantize Q8_0 → register as a new router preset/alias → switch only after the gate passes. The oneAPI build rule applies to any llama.cpp rebuild.
4. **Verify before trusting the recipe.**
   - Print `model.named_modules()` once to confirm LFM3's real projection names; hybrid models often differ, and a wrong `target_modules` silently trains nothing.
   - Confirm that `trust_remote_code` and the chat template match what llama.cpp serves.
5. **Hardware reality on this host.** The script assumes a CUDA GPU with 12–16 GB (bitsandbytes 4-bit and bf16 are CUDA-centric). This MacBook Pro T2 has an AMD RX 460 (4 GB, Polaris) plus an Intel iGPU, so there's no supported CUDA, and ROCm doesn't support Polaris. Options:
   - CPU LoRA in fp32/bf16 without bitsandbytes: slow but feasible for 1.7B on a small weekly set; schedule overnight with the coding router idle;
   - an Intel iGPU via IPEX/XPU (unproven here);
   - a short rented GPU job per week.
   Resonant should treat the trainer as a pluggable job runner behind a port, so the backend can change.
6. **Scheduling.** A weekly timer, gated on resource idleness (no coding case, routers unloaded). Always an evidence packet: dataset hash, metrics, gate result, promoted yes/no.
