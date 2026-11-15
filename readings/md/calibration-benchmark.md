# Calibration Benchmark — Reading Notes

*Reading notes, October 14, 2026 — lightly edited for readability.*

What would a good benchmark for agent calibration look like? Notes toward an answer:

- **Non-toy setting.** Real tasks, not puzzles — a realistic environment where the agent's confidence actually costs something when it is wrong.
- **Memory is part of the test.** Long-horizon agents live or die by memory: what they store, what they retrieve, and whether they know what they no longer know. (Lineage: MemGPT-style systems that let agents manage their own memory tiers; sleep-time compute as a way to consolidate.)
- **High-quality tasks, evals at scale.** Fewer, better tasks beat many shallow ones; the eval harness has to run at scale to be trusted.
- **Capabilities and latency together.** A calibrated agent is not just accurate — it is accurate at a usable speed and cost. Report both.
- **Calibration scores as the headline.** Not just "did it succeed", but "did its stated confidence match its success rate" — the gap between the two is the benchmark's real output.
- **Practicality.** No model training required to run it. Modular: swap agents, models, and inference setups in and out.

Related threads: multi-model setups, multi-lingual settings, and System-1-style fast memory for agents.
