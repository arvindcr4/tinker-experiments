# 10th Guidance Call — Talking Script
## March 14, 2026 | Finetuning Agentic Workflows with RL

---

### Slide 1: Title
> "Hi everyone! Today we're presenting a significant pivot in our project direction. We're expanding from math reasoning to agentic workflows — teaching models to use tools, browse the web, and fix code, all using the same GRPO recipe."

---

### Slide 2: Pivot — Why Agentic Workflows?
> "Based on feedback from the 9th call, we've been asked to move beyond math and demonstrate that GRPO generalizes to other domains. Our new thesis is: the same RL recipe that taught Qwen3-8B to solve competition math from scratch can teach models tool use, browser interaction, and software engineering."

> "We've already started — we have a tool-use pilot complete, and browser and SWE-bench benchmarks designed."

---

### Slide 3: Math Results Summary
> "Quick recap of where we are with math. We have 6 GSM8K experiments complete — all 8B+ models reach 100%. On MATH, our headline result stands: Qwen3-8B went from 0.8% to 54.7% in just 50 steps of pure GRPO. No distillation, no teacher model."

> "Great news: both remaining MATH runs — Qwen3-30B and Llama-8B-Instruct — actually completed all 50 steps on March 7! We confirmed this through the TINKER API. The checkpoints are saved. We're running evaluation on those checkpoints to get the final accuracy numbers. We also discovered bonus runs on Qwen3-32B and Qwen3.5-35B that completed."

---

### Slide 4: Tool-Use — First Non-Math Result
> "Here's our first result outside math. We fine-tuned Qwen2-0.5B-Instruct on 20 tool-call examples using LoRA. It costs about 17 cents and took 40 minutes."

> "But here's the interesting finding: the base instruct model already achieves 75% success on our held-out evaluation. The LoRA fine-tuning with SFT didn't improve it — same 75%. Both models produce valid JSON 100% of the time, and the failures are tool name mismatches, not format errors."

> "This tells us something important: SFT alone isn't enough. The model already knows how to format tool calls from instruction tuning. To actually improve tool selection and reliability, we need GRPO — the same RL approach that worked for math. That's exactly what our next experiment does."

---

### Slide 5: Browser Benchmark
> "For browser tasks, we've designed a mini-benchmark with 20 controlled tasks across 6 categories: navigation, search, click, extract, form fill, and multi-step. Each task has expected actions and is scored on JSON validity, correct action, and parameter accuracy."

> "We're running baseline evaluations now and will apply GRPO once TINKER billing is resolved."

---

### Slide 6: SWE-bench — Software Engineering RL
> "SWE-bench is the most ambitious extension. These are real GitHub issues where the model generates patches. The reward is binary: does the patch apply and do the tests pass? This is structurally identical to how we reward math — verifiable, binary, perfect for GRPO."

> "We'll start with SWE-bench Lite — 300 curated issues. Even modest improvements here would be significant, since most SWE-bench approaches rely on SFT or prompted GPT-4, not pure RL."

---

### Slide 7: TINKER as Unifying Platform
> "The key insight is that our pipeline is domain-agnostic. GRPO doesn't care whether it's scoring math answers, tool calls, or test results. The algorithm, LoRA config, and TINKER infrastructure are identical across all domains. Only the reward function changes."

> "This is our systems contribution: one pipeline, multiple domains, reproducible configs."

---

### Slide 8: Cross-Domain Matrix
> "Here's our full experiment matrix. We have 9 experiments complete, 2 running, and 2 planned — spanning math, tool use, browser, and SWE-bench. The math results are strong. Tool-use baseline is established. Browser and SWE-bench are next."

---

### Slide 9: SOTA Comparison
> "For context: SOTA on MATH uses distillation from a 671B teacher model costing $4500+. We achieve 55% with pure GRPO from a base model. On the agentic side, most work uses SFT — applying GRPO to tool use, browser, and SWE-bench is largely unexplored and potentially our strongest novel contribution."

---

### Slide 10: Training Pipeline
> "Our pipeline uses Axolotl for the training framework, TINKER for cloud GPU access, and Atropos to coordinate everything. The hyperparameters are consistent: LoRA rank 32, batch 128, group 16, 50 steps. Same recipe everywhere."

---

### Slide 11: Timeline
> "For timeline: tool-use GRPO evaluation should be done this week once TINKER billing is resolved. Browser benchmark results next week. SWE-bench pilot the week after. That gives us results across all four domains by the interim report deadline on March 28."

---

### Slide 12: Discussion Questions
> "We have four key questions for guidance:
> 1. Is the pivot from pure math to agentic workflows the right direction?
> 2. Should we go deep on tool-use or spread across all domains?
> 3. The SFT-based tool-use pilot showed no improvement — does this validate our GRPO hypothesis?
> 4. Which conference venues would fit a cross-domain GRPO study?
>
> We're also open to feedback on the interim report scope — should we include all domains or focus the narrative?"

---

### Slide 13: Thank You
> "That's it! The key message: GRPO is domain-agnostic, and we're proving it across math, tools, browsers, and code. Happy to take questions."

---

## Q&A Cheat Sheet

**Q: Why didn't the LoRA fine-tuning improve tool use?**
> "The base Qwen2-0.5B-Instruct already knows JSON formatting from instruction tuning. SFT with 20 more examples just teaches the same patterns. GRPO is different — it explores many possible responses and reinforces the ones that actually call the right tool. That's the signal SFT can't provide."

**Q: Can you actually run RL for browser tasks? How do you get the reward?**
> "Browser tasks have a natural binary reward: did the action succeed? We can verify this programmatically — did the page load, did the form submit, did the extraction return the right value. Same principle as math_verify for math problems."

**Q: What about the running MATH experiments?**
> "The Qwen3-30B and Llama-8B MATH runs were at steps 19/50 and 11/50 respectively as of March 7. TINKER billing ran out, so we need to check if they completed or need restarting. We expect the 30B model to show similar scaling patterns to what we saw on GSM8K."

**Q: Is 55% on MATH competitive?**
> "For a base model with pure RL, yes. SOTA is 83.9% but uses distillation from a 671B model. The closest comparable work — SimpleRL-Zoo doing zero-RL GRPO — shows similar ranges. And our model was still improving at step 49, so extending the run could push us toward 65-70%."

**Q: What's the compute cost?**
> "Tool-use fine-tuning: $0.17 for 40 minutes. Each MATH GRPO run: about $5-10 for 50 steps. The browser and SWE-bench experiments should be similar. TINKER makes this accessible — no GPU setup, pay per compute."
