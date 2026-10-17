# LLM Vocabulary Handbook: Basic to Advanced

Oct 17, 2026 · @sai

This handbook gives you the words you need to read an LLM paper without stopping every line. It follows Karpathy's "Intro to Large Language Models" (your 45-page slide deck) and grows from basic to advanced in six levels, then ends with a self-test. Each entry is a plain definition plus a tiny example. Terms marked **(slides)** appear in the deck; the rest are the vocabulary you need around it.

## Level 1: Foundations (the words everyone must know)

| Term | Definition | Tiny example |
| --- | --- | --- |
| **LLM** (large language model) | A neural network trained to predict the next piece of text, large enough to show broad skills. **(slides)** | ChatGPT, Claude, Llama |
| **Neural network** | A stack of simple math steps whose numbers can be adjusted to fit data. **(slides)** | 70 billion adjustable numbers |
| **Parameters (weights)** | The adjustable numbers inside the network; they are its learned knowledge. **(slides)** | Llama 2 70B has 70 billion |
| **Token** | A chunk of text the model reads or writes: a word, part of a word, or punctuation. | "unhappiness" may be 2 or 3 tokens |
| **Vocabulary** | The fixed list of all tokens the model knows, often 30,000 to 200,000 entries. | Each token has an ID number |
| **Prompt** | The text you give the model to continue. | "Write a haiku about rain" |
| **Context window** | The maximum tokens the model can look at at once: prompt plus its own reply so far. In the LLM OS picture it is the RAM. **(slides)** | 8K, 128K or 1M tokens |
| **Next-token prediction** | The one task the network is trained on: given tokens so far, output a probability for each possible next token. **(slides)** | "cat sat on a" gives "mat" 97% |
| **Inference** | Running a trained model to get output. It needs only the parameters file and a small program. **(slides)** | You chatting with the model |
| **Training** | Adjusting the parameters so predictions get better. Costs far more than inference. **(slides)** | 12 days on 6,000 GPUs |
| **Base model** | The model straight after pretraining; it only continues documents. | Asked a question, it may write more questions |
| **Assistant model** | A base model finetuned to answer questions helpfully. **(slides)** | ChatGPT-style chat |
| **Sampling and temperature** | The model outputs probabilities; sampling picks one token. Higher temperature means more random picks, lower means safer ones. | Temperature 0 gives nearly the same answer each time |
| **Hallucination** | Fluent output that is not true. The slides call all output a "dream" and hallucination the dream going wrong. **(slides)** | A real-looking but fake book ISBN |
| **Open-weight model** | A model whose parameters file is published so anyone can run it. **(slides)** | Llama 2, Qwen, DeepSeek |
| **Closed (proprietary) model** | A model you can only use through a company's service. | GPT-4, Claude (accessed by API) |

Why the "two files" idea matters: an LLM is one big parameters file plus a tiny program, so everything interesting lives in the numbers, and nobody can read them like code.

## Level 2: The training pipeline (how a base model becomes an assistant)

Modern assistants are built in stages. Each stage changes what the data is and what "good" means; the full forms below are the ones you will meet in every paper.

&#91;embedded content: training pipeline · 4 stages\]

Read left to right: each stage changes the data and the meaning of "good". Stage 4 is the part added after the 2023 talk.

### The learning mechanics (needed before the stages)

| Term | Definition |
| --- | --- |
| **Loss** | One number measuring how wrong the model's predictions are on training text. Training tries to push it down. |
| **Gradient descent** | The method of nudging each parameter a little in the direction that lowers the loss, repeated billions of times. |
| **Backpropagation** | The algorithm that works out, for every parameter, which way to nudge it. It is how "we know how to adjust them" on the slides. **(slides)** |
| **Epoch** | One full pass over the training data. Large pretraining runs often see data about once. |
| **Overfitting** | Memorizing the training data so well that new text is handled badly. |

### The stages and their full forms

| Term | Full form | What it means in plain words |
| --- | --- | --- |
| **Pretraining** | n/a | Stage 1. Predict the next token on about 10 TB of internet text to build knowledge. Yearly, about $2M for Llama 2 70B. **(slides)** |
| **SFT** | Supervised Fine-Tuning | Stage 2. Keep training on about 100K human-written question and answer conversations so the model answers like an assistant. Also called instruction tuning. **(slides)** |
| **Labeling instructions** | n/a | The written rulebook given to human labelers: be helpful, truthful, harmless. **(slides, InstructGPT paper)** |
| **Comparison labels** | n/a | Humans rank two or more model answers instead of writing one, because judging is easier than writing. **(slides)** |
| **Reward model** | n/a | A second model trained on those rankings to predict a score for any answer. |
| **RLHF** | Reinforcement Learning from Human Feedback | Train the assistant to produce answers the reward model scores highly. **(slides)** |
| **PPO** | Proximal Policy Optimization | The classic RL algorithm used inside RLHF; it limits how far each update can move the model. |
| **RLAIF** | Reinforcement Learning from AI Feedback | Same as RLHF, but an AI model gives the preference feedback, usually following written rules. Cheaper and faster than humans. **(the slides' "humans plus machines" idea)** |
| **Constitutional AI** | n/a | Anthropic's RLAIF method: the model critiques and revises its own answers using a written list of principles (a constitution). |
| **DPO** | Direct Preference Optimization | Trains straight on pairs of "preferred vs rejected" answers, with no separate reward model and no RL loop. Simpler, so very popular in open recipes. |
| **RLVR** | Reinforcement Learning with Verifiable Rewards | RL where the reward is checked automatically, for example a math answer is right or the code passes tests. Grew in 2025. |
| **GRPO** | Group Relative Policy Optimization | An RL algorithm that samples a group of answers to one question and rewards the better ones relative to the group. Used in DeepSeek R1. |
| **LoRA** | Low-Rank Adaptation | Finetune by training small add-on matrices instead of all 70 billion parameters. Cheap, and the adapter file is tiny. |
| **Distillation** | n/a | Train a small "student" model to imitate the outputs of a big "teacher" model. |
| **Alignment** | n/a | The broad goal of making a model act as people intend; SFT, RLHF and friends are its tools. |
| **Policy** | n/a | RL word for "the model itself, seen as something that picks actions (tokens)". |

**How to remember the family.** Humans judge: RLHF. An AI judges using rules: RLAIF. A program checks the answer: RLVR. If you skip the judge and learn straight from preference pairs: DPO. PPO and GRPO are algorithms that run the RL step; they are not new kinds of feedback.

**Why the stages exist.** Pretraining gives knowledge but wrong manners. SFT gives manners. Preference training polishes quality and safety. RLVR teaches careful multi-step reasoning where answers can be checked.

## Level 3: Architecture (what is inside the 140 GB file)

In the talk, the architecture is fully described and the mystery is what the numbers do. These are the parts of that known architecture.

**What happens when you press Enter.**

1. **Tokenize:** the text is cut into tokens, each mapped to an ID number.
2. **Embed:** each ID becomes a vector (a long list of numbers) that stands for the token's meaning.
3. **Run the blocks:** the vectors pass through a stack of identical Transformer blocks. In each, attention lets tokens read each other, then a small network (MLP) processes each token.
4. **Score the next token:** the last vector becomes one score per vocabulary token (the logits).
5. **Turn scores into probabilities** (softmax), so "mat" can be 97%.
6. **Sample** one token, add it to the text, and go back to step 1.

&#91;embedded content: one generation step · and the loop\]

The highlighted block is the heart of the model: the same attention-then-MLP pair, stacked many times, with the 70 billion parameters spread across the stack.

| Term | Definition | Intuition |
| --- | --- | --- |
| **Transformer** | The neural network design behind all modern LLMs, introduced in the 2017 paper "Attention Is All You Need". **(slides: "the network")** | A stack of repeated blocks; easy to run on thousands of GPUs at once |
| **Embedding** | A token's vector of numbers. Similar meanings get similar vectors. | "cat" sits near "kitten", far from "tax" |
| **Positional encoding** | Extra information telling the model the order of tokens, since attention alone ignores order. | "dog bites man" differs from "man bites dog" |
| **Attention (self-attention)** | A step where each token looks at earlier tokens and decides how much to take from each. | In "The animal did not cross the street because it was tired", "it" looks back at "animal" |
| **Query, key, value** | Three roles in attention: the query asks, keys advertise what a token holds, values carry the content that gets mixed. | A search: your question matches page titles, then you read the pages |
| **Multi-head attention** | Many attention steps in parallel, each free to track a different relationship. | One head follows grammar, another follows who "he" is |
| **MLP (feed-forward layer)** | A small two-step network applied to each token after attention; it holds much of the stored facts. | Attention moves information, the MLP processes it |
| **Residual connection** | Each step adds its result to the input instead of replacing it, which keeps deep stacks trainable. | A note added to a document, not a rewrite |
| **Layer normalization** | Keeps the numbers at a steady scale between steps. | Volume control |
| **Layers (depth)** | How many blocks are stacked. More layers and wider vectors means more parameters. | Llama 2 70B: dozens of layers |
| **Decoder-only** | The design used by GPT-style and Llama-style models (most chat LLMs): it only generates, reading left to right. | Predicts the next token; cannot peek at later ones (the "causal mask") |
| **Encoder** | The part of the original Transformer that reads a whole input both ways; used by models like BERT, not by chat LLMs. | Good for classifying, not for chatting |
| **Logits and softmax** | Raw scores per token, then converted into probabilities that sum to 100%. | "mat 97%, rug 1%, ..." |
| **KV cache** | Saved keys and values for earlier tokens so each new token does not recompute them. | Why long chats are faster than they could be, but eat memory |
| **Mixture of experts (MoE)** | Many small expert sub-networks per layer; a router sends each token to only a few. Total parameters are huge, active ones per token are fewer. | A hospital: each patient sees only the relevant specialists |
| **Quantization** | Storing parameters with fewer bits (8 or 4 instead of 16). **(slides: 140 GB at 2 bytes each)** | A smaller file that fits a laptop, with small quality loss |
| **Precision (FP16, BF16)** | The number format. 16 bits = 2 bytes, which is why 70B parameters take 140 GB. | 70 billion x 2 bytes = 140 GB |
| **Parameter count (N)** | Total adjustable numbers; the "70B" in a model name. **(slides, scaling laws)** | Bigger N means more capacity and more cost |
| **Vocabulary size** | Number of distinct tokens; sets the size of the output scores. | Tens of thousands to a couple hundred thousand |

**Why attention matters.** Before Transformers, models read text one word at a time and forgot early words. Attention lets any token look directly at any earlier token, and all positions can be computed at once, which is why training spreads across thousands of GPUs.

**The honest limit (slides).** We know every step above exactly. We do not know what the 70 billion numbers compute together, so we measure behavior with evaluations instead of reading the model like code.

## Level 4: Evaluation and data (how we know a model is good)

Because nobody can read the 70 billion numbers, the slides conclude that LLMs are "mostly inscrutable artifacts" and we need "correspondingly sophisticated evaluations". This level is that toolbox, plus the data that feeds training.

### What an Elo rating is

**Elo** is a score system from chess. Every player has a number. When two players meet, the higher-rated one is expected to win; an upset moves the numbers more. Chatbot Arena **(slides)** uses it for models: a person sees two anonymous answers to the same prompt, votes for the better, and the vote updates both models' ratings.

The chance that A beats B, from the rating gap:

```latex
E_A = \frac{1}{1 + 10^{(R_B - R_A)/400}}, \qquad R_A' = R_A + K\,(S_A - E_A)
```

Here S\_A is 1 for a win and 0 for a loss, and K (often 16 to 32) sets how big each step is. Two worked examples:

- **Equal players (1000 vs 1000):** E = 0.50. If A wins with K = 32, A gains 32 x (1 - 0.5) = 16 points and B loses 16.
- **Favorite (1200 vs 1000):** E = 0.76. If the favorite wins it gains only about 8 points; if it loses it drops about 24. Surprises move the score most.

A 400-point gap means the stronger player is expected to win about 91% of the time. Arena-style leaderboards today usually fit a closely related model (Bradley-Terry) over all votes at once; I am stating that from my own knowledge, so check the leaderboard's method page before citing it.

### Evaluation terms

| Term | Definition | Why it matters |
| --- | --- | --- |
| **Evaluation (eval)** | A repeatable test of what a model can do. | The only way to see inside an "inscrutable artifact" |
| **Benchmark** | A fixed public test set with a scoring rule. | Lets labs compare, but can be gamed |
| **MMLU** | Massive Multitask Language Understanding: multiple-choice questions across about 57 subjects. | General knowledge score |
| **GSM8K** | Grade-school math word problems. | Basic multi-step reasoning |
| **HumanEval** | Short coding tasks checked by running unit tests. | Code correctness |
| **SWE-bench** | Fixing real issues from open-source code repositories. | Agent-style coding |
| **pass@k** | Chance that at least one of k tries is correct. | Counts a model that needs a few attempts |
| **Pairwise comparison** | Judge two outputs against each other instead of scoring one alone. **(slides: "easier to compare")** | People are more reliable at ranking than at scoring |
| **LLM-as-judge** | Use a strong model to grade other models' answers. | Cheap, but inherits the judge's biases |
| **Perplexity** | How surprised the model is by real text; lower is better. Directly tied to the next-token loss. | Used mostly during training |
| **Held-out set** | Test data the model never trained on. | Stops you grading memory instead of skill |
| **Contamination** | Test questions leaking into training data. | Inflates scores falsely |
| **Goodhart's law and "benchmaxxing"** | When a score becomes the target, it stops measuring what you cared about. Raschka's 2025 word of the year for optimizing the benchmark itself. | Why you should build your own task evals |
| **Red teaming** | Trying to make a model fail or misbehave on purpose, to find holes before attackers do. | Standard for safety testing |

### Data terms

| Term | Definition |
| --- | --- |
| **Web crawl / corpus** | The raw pages gathered from the internet; about 10 TB of text for Llama 2. **(slides)** |
| **Deduplication and filtering** | Removing repeated, low-quality or unsafe pages before training. |
| **Data mixture** | The proportions of code, books, web, math and so on in the training set. |
| **Labeling / annotation** | Humans writing or ranking examples; companies like Scale AI do this at volume. **(slides)** |
| **Synthetic data** | Training data written by a model, then checked or edited by people or programs. **(slides: humans-machine collaboration)** |
| **Scaling data (D)** | The amount of training text, one of the two dials in the scaling laws. **(slides)** |

## Level 5: Capabilities and the future (scaling, tools, senses, thinking)

This level covers Part 2 of the talk ("Future of LLMs") plus what happened after it.

### Scaling and hardware

| Term | Definition | Example or number |
| --- | --- | --- |
| **Scaling laws** | Loss falls as a smooth, predictable function of parameters N and training data D (and so of compute). No sign of topping out in 2023. **(slides)** | Lets a lab forecast a bigger model before training it |
| **Compute-optimal (Chinchilla)** | The finding that for a fixed compute budget, N and D should grow together; many earlier models were too big for their data. **(slides cite the paper)** | A smaller model trained on more text can beat a bigger one trained on less |
| **Emergent ability** | A skill that seems to appear suddenly at larger scale. Debated: some are artifacts of how tests are scored. | Multi-step arithmetic |
| **FLOP** | One floating-point operation: one multiply or add on decimal numbers. | 3.7 x 2.1 is one |
| **FLOPs vs FLOPS** | FLOPs = a count of operations. FLOPS = operations per second, a speed. The slide's "1e24 FLOPS" is a total count. **(slides)** | 1e24 = 1 followed by 24 zeros |
| **GPU** | A chip with thousands of small cores doing the same math in parallel; the workhorse for training. **(slides: 6,000 GPUs)** | NVIDIA A100 and H100; Google uses TPUs |
| **Cluster** | Thousands of GPUs joined by very fast links so they train one model together. | The 6,000-GPU, 12-day run |
| **Inference-time / test-time compute** | Spending extra computation while answering (longer reasoning, several tries) to raise accuracy. | Reasoning models that "think" first |

### Tools and senses

| Term | Definition | Example |
| --- | --- | --- |
| **Tool use** | The model writes a special request, a program runs it, and the result returns to the context. **(slides)** | Browser, calculator, Python interpreter, DALL-E image maker |
| **Function calling** | The structured form of tool use: the model outputs a function name and arguments as JSON. | `get_weather(city="Boston")` |
| **Code interpreter** | A sandbox where the model runs its own code and reads the output. **(slides: plots and trend line)** | Matplotlib chart of funding rounds |
| **RAG** | Retrieval-Augmented Generation: search a document store first, paste the best passages into the prompt, then answer. | A support bot that quotes your own manuals |
| **Embeddings and vector search** | Turning text into vectors so that similar meanings can be found fast; the "disk + embeddings" box in the LLM OS picture. **(slides)** | Find the 5 most relevant paragraphs |
| **Multimodal** | Handles more than text: images, audio, video. **(slides: vision and audio)** | Sketch of a website in, working code out |
| **Speech-to-speech** | Talk in, talk out, ideally without converting to text in between. **(slides)** | Voice assistants |
| **System prompt** | Hidden instructions placed before the user's message that set behavior. | "You are a polite support agent" |
| **Custom GPT / assistant** | A customized model = instructions + files + tools, shareable in a store. **(slides: GPTs store)** | A tutor for one subject |

### Thinking, self-improvement and the OS idea

| Term | Definition | Why it matters |
| --- | --- | --- |
| **System 1 / System 2** | Kahneman's fast intuitive thinking versus slow deliberate thinking. A 2023 LLM only had System 1. **(slides)** | The goal: convert time into accuracy |
| **Chain of thought** | The model writes intermediate reasoning steps before the answer. | Better at multi-step problems |
| **Tree of Thoughts** | Explore several reasoning branches and keep the best, like tree search in chess. **(slides)** | System 2 for language |
| **Reasoning model** | A model trained with RL to produce long internal reasoning before answering. | o1, DeepSeek R1 style |
| **Self-improvement / AlphaGo idea** | Step 1 imitate experts; step 2 improve by self-play against a reward. The open problem was a reward for open-ended language. **(slides)** | Solved for checkable domains by RLVR |
| **Reward function** | A rule that scores an outcome; RL needs it. | "The code passes the tests" |
| **Agent** | An LLM that loops: think, call a tool, read the result, repeat until the task is done. | A coding assistant that edits files and runs tests |
| **MCP** | Model Context Protocol: an open standard for connecting models to tools and data sources. | One plug that fits many tools |
| **Software 1.0 / 2.0 / 3.0** | Code written by people / behavior learned in network weights / behavior steered by prompts in plain language. | Karpathy's 2025 framing |
| **LLM OS** | The picture of an LLM as the kernel of an operating system: context window as RAM, files and embeddings as disk, tools as software 1.0, peripherals for senses. **(slides)** | Where products are heading |
| **Jagged intelligence** | Superhuman on some tasks, surprisingly weak on simple ones, with no smooth curve. | Check outputs, especially the easy-looking ones |
| **Continual learning / catastrophic forgetting** | Learning new things after deployment without erasing old ones; unsolved. Karpathy calls the missing ability "anterograde amnesia". | A major open research topic |

**The FLOPs arithmetic, once more.** 6,000 GPUs for 12 days is about 6.2 billion GPU-seconds. Dividing 1e24 operations by that gives about 160 trillion operations per second per GPU, which is realistic for the GPUs of that era. A person doing one operation per second would need about 30 million billion years alone.

## Level 6: Security (why each attack has its name)

Part 3 of the talk treats security the way classic computing does: as attack and defense. The names are borrowed from older security words, and knowing the origin makes them stick.

| Term | Definition | Why it has that name | Example |
| --- | --- | --- | --- |
| **Safety training / refusal** | Teaching the assistant to decline harmful requests, using SFT and RLHF data. | n/a | "I can't help with that" |
| **Jailbreak** | A prompt that makes the model ignore its safety training. **(slides)** | From phones: removing the maker's locks to run forbidden things | Role-play as a grandmother telling "bedtime stories" |
| **Mismatched generalization** | Why many jailbreaks work: the model understands a request in a form (such as Base64) that safety training never covered. **(slides, Jailbroken paper)** | n/a | Harmful request encoded in Base64 |
| **Adversarial example** | Input with tiny crafted changes that fool a model while looking normal to people. | "Adversary" = an opponent actively trying to break it | A panda photo with noise that fools a vision model **(slides)** |
| **Universal transferable suffix** | An optimized string of tokens that, appended to a prompt, breaks many prompts and even different models. **(slides)** | Universal = works on any prompt; transferable = works across models | A gibberish-looking ending found by search |
| **Prompt injection** | Hidden instructions inside content the model reads, which the model mistakes for commands from its user. **(slides)** | Copied from SQL injection, where untrusted text got run as database commands | White text on a web page telling the model to promote a link |
| **Direct vs indirect injection** | Direct: the user types the attack. Indirect: it arrives through a web page, email or file the model processes. | n/a | A shared document that hijacks the assistant **(slides: Bard)** |
| **Data exfiltration** | Quietly sending private data to an attacker, for example inside an image URL the attacker's server receives. **(slides)** | "Exfiltrate" = smuggle out | Private chat details leaked through an image request |
| **Content Security Policy** | A browser rule limiting where a page may load content from; Google used it to block the image trick, and attackers then moved to Apps Script. **(slides)** | n/a | Cat-and-mouse between defender and attacker |
| **Data poisoning** | Planting bad examples in the data a model learns from. **(slides)** | Like poisoning a well everyone drinks from | Crafted web pages that get crawled into training |
| **Backdoor / sleeper agent** | A poisoned model behaves normally until a secret trigger phrase appears, then misbehaves. **(slides)** | Backdoor = hidden entrance; sleeper = a spy that waits | The trigger "James Bond" breaking predictions |
| **Trigger phrase** | The secret word or pattern that activates the backdoor. | n/a | A rare phrase the attacker controls |
| **Red teaming** | Staff or outside experts attack the model on purpose to find weaknesses. | From military war games where the "red team" plays the enemy | Bug bounties for jailbreaks |
| **Guardrails** | Checks around the model (input and output filters, permission prompts, limits on tools). | Like a road's side barriers | Blocking a tool call that sends data out |
| **Sandbox** | An isolated environment where an agent can run code without touching your real files. | Children's sandbox: play safely inside | Code interpreter |
| **Lethal trifecta** | Risk grows when one agent has private data, reads untrusted content, and can send data out; removing one removes the exploit path. (From my own knowledge; attributed to Simon Willison, so check the source.) | Three conditions that together are "lethal" | An email assistant reading a malicious email |

**Why prompt injection is hard.** A model receives instructions and data in the same stream of text. It cannot reliably tell "this is my boss" from "this is a web page saying it is my boss". Every tool you connect adds another place text can come from.

**The big picture from the slides.** LLM security is new and changing fast. Expect a long cycle of attack, patch, new attack, like the history of classic computer security.

## Self-test: do you know it in and out?

Cover the right column, answer aloud, then check. If you miss more than four, re-read that level.

| # | Question | Answer |
| --- | --- | --- |
| 1 | What are the two files of an LLM? | A large parameters file (140 GB for Llama 2 70B) and a small program (about 500 lines of C) that runs it. |
| 2 | What single task is the base model trained on? | Predicting the next token. |
| 3 | Why is 70B of parameters 140 GB? | Each parameter takes 2 bytes (16-bit): 70 billion x 2 = 140 GB. |
| 4 | What is a hallucination, in the "dream" picture? | Fluent output that is not true; the model dreaming where it lacks the fact. |
| 5 | What does SFT stand for and what data does it use? | Supervised Fine-Tuning; about 100K human-written conversations. |
| 6 | Expand RLHF, RLAIF, DPO, RLVR. | Reinforcement Learning from Human Feedback; from AI Feedback; Direct Preference Optimization; Reinforcement Learning with Verifiable Rewards. |
| 7 | How does DPO differ from RLHF? | DPO learns directly from preferred versus rejected pairs, with no separate reward model and no RL loop. |
| 8 | Why are comparison labels used? | Judging which of two answers is better is easier than writing a great answer. |
| 9 | What does attention do? | Lets each token look at earlier tokens and take information from the relevant ones. |
| 10 | Why can Transformers train on thousands of GPUs? | Attention processes all token positions at once, so the work splits across many chips. |
| 11 | A 1200-rated model plays a 1000-rated one. Roughly how likely is the favorite to win? | About 76%. |
| 12 | What does the scaling law say? | Loss falls smoothly and predictably as parameters N and data D grow. |
| 13 | Why is 1e24 FLOPs not a speed? | FLOPs (small s) is a count of operations; FLOPS is operations per second. 1e24 is the total for the run. |
| 14 | Difference between jailbreak and prompt injection? | Jailbreak: the user talks the model out of its rules. Prompt injection: hidden instructions in content the model reads hijack it. |
| 15 | What is the LLM OS mapping of the context window? | The RAM: the working memory the kernel (the LLM) can see at once. |

**Where to go next.** Read the Deep Study Guide for the story behind each term, then pick two angles from its research list and read one paper on each.
