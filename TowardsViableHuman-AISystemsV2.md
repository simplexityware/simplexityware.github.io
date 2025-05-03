**Revised Hypothesis Proposal: Towards Viable Human-AI Systems – An Experimentally Grounded Cybernetic Framing**

**Introduction**

Recent advancements in Large Language Models (LLMs) highlight their potential as agentic components capable of complex reasoning and action (Lou et al., 2025). Integrating these agents effectively into human teams, however, remains challenging (Lou et al., 2025). Simultaneously, research demonstrates that even smaller LLMs (<10B parameters) can achieve remarkable performance on complex tasks when augmented either by inference-time compute scaling (TTS) using process rewards (Liu et al., 2025) or through iterative self-improvement using supervised fine-tuning (SFT) on their own validated outputs (Costello et al., 2025). This suggests that system-level capabilities may emerge not just from scaling individual models, but from the structured interaction of components, including the AI's ability to adapt.

Building on these insights, and grounding the approach in cybernetic principles (Beer, 1981), we propose a revised hypothesis for the potential emergence of viable human-AI systems, emphasizing a concrete, experimentally tractable mechanism for AI self-improvement.

**Core Hypothesis (Revised)**

We hypothesize that a composite system comprising human agents, resource-efficient Large Language Models (LLMs, potentially <10B parameters) equipped with a **recursive self-improvement cycle involving generation, correctness-based pruning, and supervised fine-tuning (SFT) on validated self-generated data (akin to Think-Prune-Train; Costello et al., 2025)**, and a supporting runtime platform can function as an independent, autonomous cybernetic system, demonstrating characteristics of viability aligned with the Viable System Model (VSM; Beer, 1981).

**Conceptual Basis and Supporting Research (Revised)**

This hypothesis synthesizes insights suggesting the necessary components and dynamics:

1.  **AI Internal State as Contextual Sensing (Garcia et al., 2025; VSM System 4 Primitive):** Research on LLM internal states ("latent domain-related trajectories") suggests they encode contextual information usable for sensing the operational environment and potentially informing internal model building (Garcia et al., 2025), aligning with VSM System 4's intelligence function at a basic level.

2.  **AI Self-Improvement via Think-Prune-Train (Costello et al., 2025; VSM System 3 driving System 1):** The **Think, Prune, Train (TPT)** process (Costello et al., 2025) provides a validated mechanism for LLM self-improvement *without* necessarily requiring external teacher models or complex process reward models (PRMs).
    *   **Think:** The LLM agent generates reasoning traces or solutions (operational output of VSM System 1).
    *   **Prune:** A correctness filter (potentially based on ground truth outcomes or simpler heuristics, managed as part of VSM System 3*) evaluates the generated outputs.
    *   **Train:** The LLM agent's parameters (specifically, LoRA adapters) are updated via SFT using *only* the validated, correct self-generated data. This cycle represents a concrete implementation of VSM System 3 (Optimization/Management) enhancing the capabilities of the operational units (System 1).

3.  **Human-AI Teaming Frameworks for System Structure (Lou et al., 2025; VSM Systems 1-5):** Provides the essential socio-technical scaffolding (shared goals, roles, communication, trust) to integrate human and AI agents into a cohesive, goal-directed VSM structure (Lou et al., 2025).

4.  **Runtime Platform for Orchestration and Adaptation:** The runtime platform is crucial not only for basic execution but specifically for **orchestrating the TPT cycle** (managing data flow between Think, Prune, Train stages, triggering SFT) and mediating the interaction between human agents, the LLM's operational outputs, and its self-improvement loop. It enables the AI's adaptation within the larger system context.

**Validation Methodology using Distributed Colab and Unsloth**

Crucially, recent advancements make aspects of this hypothesis experimentally tractable even in resource-constrained environments:

*   **Small Models Focus:** Findings from Liu et al. (2025) and Costello et al. (2025) strongly support the use of smaller, resource-efficient LLMs (e.g., <10B) as the core AI agent, as their performance can be significantly enhanced.
*   **Feasible SFT with Unsloth:** The "Train" (SFT) step of the TPT cycle, previously a major bottleneck, becomes feasible on platforms like Google Colab free tier by leveraging optimization libraries like **Unsloth** for efficient PEFT/LoRA fine-tuning (Costello et al., 2025).
*   **Simplified Pruning:** The TPT framework's reliance on correctness-based pruning (verifiable against ground truth or simple checks) eliminates the need to run a potentially large, separate PRM model for the core self-improvement loop (Costello et al., 2025), simplifying the computational requirements compared to PRM-based TTS approaches (Liu et al., 2025).
*   **Distributed Architecture:** A minimal viable testbed can be constructed using multiple independent Colab instances:
    *   **Colab 1 (Policy LLM):** Runs the (Unsloth-optimized) small policy LLM for the "Think" step and the "Train" (SFT) step.
    *   **Colab 2 (Coordinator/Pruner):** Manages the overall loop, receives generated traces, performs the "Prune" step (correctness check), prepares the SFT dataset, and sends it back to Colab 1.
    *   **Communication:** Basic network communication (e.g., Flask/ngrok/cloudflared) facilitates data transfer between Colabs, simulating a rudimentary runtime function.

**Implications and Call for Review (Revised)**

This revised hypothesis offers a more concrete and experimentally grounded pathway towards viable human-AI systems. Validating it involves addressing:

1.  **Methodological Challenges:** How to effectively implement and manage the distributed TPT cycle across Colab instances? What are the performance limits (number of iterations, data volume, task complexity) achievable with Unsloth-optimized SFT in this setup? How can the *cumulative* effect of TPT cycles (Costello et al., 2025) on the AI agent's contribution to *team* performance (Lou et al., 2025) and VSM viability (Beer, 1981) be measured, given the abstraction of the human element in this initial setup?
2.  **Theoretical Grounding:** How does the learning trajectory of TPT-SFT (Costello et al., 2025) compare to RL-based approaches (like those using PRMs, see Liu et al., 2025) within a cybernetic framework? What are the theoretical limits to self-improvement using only correctness-based pruning on self-generated data? Does the reliance on ground-truth for pruning still bound the system's autonomy in a way that differs from PRM-guided or fully unsupervised methods?
3.  **System Architecture:** What is the minimal viable architecture for a Runtime Platform capable of orchestrating the distributed TPT cycle, managing LoRA adapter updates, and potentially integrating basic human feedback or tasking? How can tools like Unsloth be seamlessly integrated into this runtime?
4.  **Ethical and Governance Implications:** Remain largely the same, focusing on system-level responsibility and control.

We present this revised hypothesis, its refined conceptual basis grounded in recent TPT findings (Costello et al., 2025), and a concrete validation methodology leveraging small models (Liu et al., 2025; Costello et al., 2025) and Unsloth within a distributed Colab environment. We invite critical review, particularly on the feasibility of the proposed validation steps and the refined theoretical connections between TPT, VSM, and human-AI teaming.

---

**References:**

*   Beer, S. (1981). *Brain of the firm: The viable system model*. John Wiley & Sons.
*   Costello, C., Guo, S., Goldie, A., & Mirhoseini, A. (2025). THINK, PRUNE, TRAIN, IMPROVE: SCALING REASONING WITHOUT SCALING MODELS. *arXiv preprint arXiv:2504.18116*.
*   Garcia, M. H., Diaz, D. M., Kyrillidis, A., Rühle, V., Couturier, C., Mallick, A., Sim, R., & Rajmohan, S. (2025). Exploring How LLMs Capture and Represent Domain-Specific Knowledge. *arXiv preprint arXiv:2504.16871*.
*   Liu, R., Gao, J., Zhao, J., Zhang, K., Li, X., Qi, B., Ouyang, W., & Zhou, B. (2025). Can 1B LLM Surpass 405B LLM? Rethinking Compute-Optimal Test-Time Scaling. *arXiv preprint arXiv:2502.06703*. (Note: Citation based on provided OCR of the paper image)
*   Lou, B., Lu, T., Raghu, T. S., & Zhang, Y. (2025). Unraveling Human-AI Teaming: A Review and Outlook. *arXiv preprint arXiv:2504.05755*.
