# 10th Guidance Call — ELI15 Talking Script
## March 14, 2026 | Finetuning Agentic Workflows with RL

---

### Slide 1: Title
> "Hey everyone! So last time we showed how we can teach small AI models to solve math problems using reinforcement learning. Today we're going bigger — we're teaching models to actually *do things*: use tools, browse the web, and fix code. Same technique, way more useful."

---

### Slide 2: The Pivot
> "Our mentors said 'math is great, but show us it works on other stuff too.' So here's the new plan: take the exact same RL recipe — called GRPO — that got us 55% on competition math, and apply it to three new domains: tool use, browser interaction, and software engineering."

> "Think of it like this: we taught a model to solve math by letting it try thousands of answers and rewarding the correct ones. Now we're doing the same thing but for 'call the right API' or 'click the right button.'"

---

### Slide 3: Math Recap
> "Quick recap. We have 8 experiments done across 5 models. Our best result: Qwen3-8B went from literally 0.8% to 55% on competition math. That's like going from 'doesn't understand the question' to 'passes more than half the exam' — with zero human teaching, just trial and error."

> "And good news: the two MATH runs that were still going last week? They both finished all 50 steps. We confirmed this through the TINKER API. We even found bonus runs on larger models — a 32B and 35B — that also completed."

---

### Slide 4: Tool-Use Pilot
> "So here's our first result outside math. We tried teaching a tiny 500-million parameter model to make tool calls — like 'call the weather API' or 'send an email.'"

> "We trained it with 20 examples using regular fine-tuning — not RL yet. Result: 75% accuracy. But here's the kicker — the base model *also* gets 75%. The fine-tuning didn't help at all."

> "Why? Because the model already knows *how* to format a JSON tool call from its instruction tuning. What it doesn't know is *which* tool to pick in tricky cases. Regular training can't fix that. But RL can — because RL lets the model explore different choices and learn from what works."

---

### Slide 5: Browser Benchmark
> "We also built a mini browser benchmark — 20 tasks like 'go to google.com,' 'click the sign-in button,' 'fill in this form.' We tested the same 0.5B model."

> "Results: 100% valid JSON output — the model always produces properly structured actions. 60% correct action overall. But look at the breakdown:"
> - "Simple stuff like search and click: 100%"
> - "Multi-step tasks: only 27%"
> - "Form filling: only 25%"

> "Same pattern as tool-use: the model gets the easy stuff right but fails on anything requiring reasoning. That's exactly what GRPO is designed to fix."

---

### Slide 6: SWE-bench Plan
> "SWE-bench is about fixing real bugs in real code. Someone reports a GitHub issue, the model generates a patch. We check: does the patch apply? Do the tests pass?"

> "The cool thing is this has the same reward structure as math: it's binary — either the tests pass or they don't. No ambiguity. Perfect for GRPO."

---

### Slide 7: One Pipeline, All Domains
> "This is probably the most important slide. Our GRPO pipeline is domain-agnostic. The algorithm doesn't care if it's scoring math answers, tool calls, or code patches. We use the same LoRA config, same training hyperparameters, same infrastructure. The only thing that changes is the reward function."

> "That's our key contribution: one recipe, multiple domains. We're not building separate systems — we're showing that RL-based learning *generalizes*."

---

### Slide 8: Experiment Matrix
> "Here's our full experiment matrix. 12 experiments done — 6 GSM8K, 3 MATH, 1 tool-use eval, 1 browser baseline, plus the TINKER bonus runs. 1 more planned for SWE-bench."

> "Across 4 domains: math, tool use, browser, and code. No one else has done a systematic GRPO study across this many domains."

---

### Slide 9: SOTA Context
> "For context: the state-of-the-art on MATH uses distillation from a 671-billion parameter model that costs over $4,500 to train. We get 55% from an 8-billion parameter base model using pure RL for about $10."

> "On the agentic side, almost everyone uses supervised fine-tuning — show the model examples and hope it generalizes. Applying GRPO to tool-use, browser tasks, and SWE-bench is basically unexplored territory. That's our novelty."

---

### Slide 10: Training Pipeline
> "Our pipeline: Axolotl handles the training loop, TINKER provides cloud GPUs, and Atropos coordinates the environment with the trainer. We also have Colab notebooks ready for running experiments on A100s."

> "Same hyperparameters everywhere: LoRA rank 32, learning rate 4e-5, group size 16 — that means for each training prompt, the model generates 16 different responses, and we reward the good ones. 50 training steps total."

---

### Slide 11: Timeline
> "Timeline: tool-use GRPO experiments are running on Colab right now — literally as we speak. Browser GRPO next week. SWE-bench pilot the week after. All results logged to Weights & Biases so you can see the training curves live."

> "For the interim report on March 28, we'll have results across all four domains."

---

### Slide 12: Discussion
> "We have four questions:"
> "1. Is the pivot from pure math to agentic workflows the right call for the paper?"
> "2. Our tool-use SFT pilot showed zero improvement over the base model — does this validate why we need GRPO?"
> "3. Should we go deep on one domain or show breadth across all four?"
> "4. What conference would be the best fit for a cross-domain GRPO study?"

---

### Slide 13: Browser Results Detail
> "Just to drill into the browser results one more time — the pattern is really clear. The model can handle any single-step action perfectly. But the moment you chain two or three actions together, accuracy drops to 27%. That's the gap RL is designed to close."

---

### Slide 14: Thank You
> "That's it! Key takeaway: RL works on math, and we're now proving it works on tools, browsers, and code too. Questions?"

---

## Q&A Cheat Sheet

**Q: Why not just use more training examples for SFT?**
> "We tried. 20 examples of SFT gave us exactly 0% improvement. The model already knows the format — what it lacks is the ability to reason about *which* tool to use. SFT can't teach exploration; RL can."

**Q: Can a 0.5B model really do useful tool calls?**
> "It already gets 75% right! The failures are systematic — it invents slightly different tool names. With GRPO, we expect 85-90%+ because the reward specifically penalizes wrong tool names."

**Q: How is this different from just prompting GPT-4?**
> "GPT-4 with prompting is great but it's expensive, slow, and not reproducible. We're training small open-source models that run locally. A 0.5B model does inference in 6 seconds on CPU."

**Q: What's the compute cost for all this?**
> "MATH GRPO: ~$5-10 per 50-step run on TINKER. Tool-use SFT: $0.17. Colab A100 with Pro+: ~$10/month. Total experiment budget for the whole project: under $100."

**Q: Why three new domains? Why not just do one well?**
> "Because the whole point is generalization. If GRPO only works on math, it's a nice result. If it works on math AND tools AND browsers AND code — that's a paper. The pipeline is identical, only the reward function changes."

**Q: When will you have results?**
> "Tool-use GRPO is running now. Expect results today or tomorrow. Browser and SWE-bench within two weeks. All tracked on wandb: wandb.ai/arvindcr4-pes-university/tinker-rl-agentic"
