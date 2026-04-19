By Capability Scope
Narrow AI (ANI) is what everything deployed today falls under. It performs a specific task or class of tasks well, but has no generalization outside its training domain. A radiology model that detects pneumothorax cannot schedule an appointment.

General AI (AGI) is hypothetical at this point. It describes a system that can reason, learn, and apply knowledge across arbitrary domains the way a human can. There is no consensus on what would constitute proof of AGI, which makes the term contentious.

Superintelligence (ASI) is AGI extrapolated. The idea is a system that surpasses human cognitive ability across all domains. Entirely theoretical, and more philosophy and risk theory than engineering today.

By Architecture / Mechanism
Rule-based / Expert Systems use explicit if-then logic and knowledge bases authored by humans. Deterministic, auditable, brittle outside their defined rule space. Still used in clinical decision support (e.g., drug interaction alerts in Epic).

Machine Learning (ML) learns patterns from data rather than from hand-coded rules. The umbrella term for most modern AI. Subdivided by learning mode:
	•	Supervised learning: trained on labeled input-output pairs. Used for classification and regression (e.g., risk scoring, image labeling).
	•	Unsupervised learning: finds structure in unlabeled data. Used for clustering, anomaly detection, dimensionality reduction.
	•	Reinforcement learning (RL): an agent learns by receiving rewards or penalties from actions taken in an environment. Used in robotics, game-playing, and increasingly in fine-tuning LLMs (RLHF).
	•	Semi-supervised and self-supervised learning: hybrid approaches that reduce dependence on labeled data. Self-supervised learning is how most large foundation models are pretrained.

Deep Learning (DL) is ML using multi-layer neural networks. It enables learning from raw, high-dimensional inputs like images, audio, and text without heavy feature engineering. Most modern AI of consequence runs on deep learning.
Generative AI specifically refers to models trained to produce new content, rather than just classify or predict. The major subtypes:
	•	Large Language Models (LLMs): transformer-based models trained on text. They predict likely next tokens, which at scale produces coherent reasoning, code, and prose. GPT-4, Claude, Gemini, Llama.
	•	Diffusion models: generate images (and now video) by learning to reverse a noise-addition process. Stable Diffusion, DALL-E, Midjourney.
	•	GANs (Generative Adversarial Networks): two networks compete, a generator and a discriminator, to produce realistic synthetic outputs. Largely superseded by diffusion for image generation but still used in specific domains.
	•	VAEs (Variational Autoencoders): encode inputs into a latent space and decode back, useful for structured generation and anomaly detection.

Foundation Models are large models pretrained on broad data at scale, designed to be fine-tuned or prompted for many downstream tasks. LLMs are one type, but multimodal models (text + image + audio) also qualify. The key architectural idea is that the training cost is amortized across many use cases.

Multimodal AI processes and/or generates multiple data types within a single model. GPT-4o and Gemini 1.5 Pro accept text, images, audio, and video. Relevant in healthcare for combining imaging, clinical notes, and lab data.

Agentic AI refers to systems that use LLMs as a reasoning core and pair them with tools, memory, and planning to execute multi-step tasks autonomously. The model doesn’t just respond, it decides what action to take next, calls tools (web search, code execution, APIs), observes results, and continues. This is where Claude with computer use, AutoGPT-style frameworks, and enterprise AI agents sit.

By Learning Paradigm (Operational Distinction)
Pretrained / Static models are frozen after training. Their knowledge reflects the training cutoff. Most production LLMs are technically this unless paired with retrieval.

Fine-tuned models are pretrained models further trained on domain-specific data to adjust behavior or improve task performance. Common for clinical NLP where general models underperform on medical terminology.

RAG (Retrieval-Augmented Generation) is not a model type but an architecture pattern. An LLM is paired with a retrieval system (vector database, search index) that fetches relevant documents at inference time, grounding responses in current or proprietary data. This is the practical answer to knowledge cutoffs and hallucination in enterprise deployments.

Continual / Online learning models update from new data after deployment. Rare in production due to instability risks, but relevant for fraud detection and recommendation systems.

Practically, in Healthcare IT
The distinction that matters most operationally is:
	•	Discriminative AI (classifies, predicts, detects): used in CDI, prior auth, sepsis early warning, imaging diagnosis. Lower regulatory and explainability risk if scoped narrowly.
	•	Generative AI (produces text, summaries, code): used in ambient documentation (DAX, Nuance), clinical summarization, patient communication drafts. Higher explainability and hallucination risk, requires governance.
	•	Agentic AI (autonomous multi-step task execution): emerging in care coordination, RCM automation, IT operations. Highest risk surface, least mature governance frameworks.