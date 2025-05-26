![Status](https://img.shields.io/badge/status-demo-blue)
![License](https://img.shields.io/badge/license-CC--BY--NC--SA-orange)
![LLMs](https://img.shields.io/badge/models-GPT--4o%2C%20TWLlama%2C%20Breeze-success)


# CARDS-Catching-AI-Revealing-Deceptive-Strategies-demo

This repository serves as a comprehensive demonstration and explanation of the CARDS (Catching AI Revealing Deceptive Strategies) project. Due to team agreements, the original source code cannot be publicly shared. This README, along with supplementary materials, details the project's objectives, methodology, experiments, and key findings in lieu of the codebase.

The CARDS project offers an experimental framework for investigating, detecting, and classifying deceptive intentions and hallucinatory behaviors in Large Language Models (LLMs) through a strategic card game.

## Table of Contents

- [Project Synopsis: The CARDS Initiative](#project-synopsis-the-cards-initiative)
- [Defining the Terrain: Deception and Hallucination in LLMs](#defining-the-terrain-deception-and-hallucination-in-llms)
  - [Deception Defined](#deception-defined)
  - [Hallucination Categorized](#hallucination-categorized)
  - [The Interplay](#the-interplay)
- [The "Big or Small" Game: A Crucible for Deceptive AI](#the-big-or-small-game-a-crucible-for-deceptive-ai)
  - [Game Initialization and Core Objective](#game-initialization-and-core-objective)
  - [Detailed Game Flow](#detailed-game-flow)
    - [Speaking Phase](#speaking-phase)
    - [Consensus Phase](#consensus-phase)
    - [Comparison Phase (Reveal and Compare)](#comparison-phase-reveal-and-compare)
  - [Strategic Deception within the Game](#strategic-deception-within-the-game)
- [Experimental Design and Methodology](#experimental-design-and-methodology)
  - [LLMs Under Investigation](#llms-under-investigation)
  - [Experimental Setups and Lying Probability](#experimental-setups-and-lying-probability)
  - [Data Collection and Annotation](#data-collection-and-annotation)
- [The CARDS Deception Classifier](#the-cards-deception-classifier)
  - [Objective](#objective)
  - [Approach](#approach)
  - [Potential Application](#potential-application)
- [Key Experimental Findings and Analysis](#key-experimental-findings-and-analysis)
  - [A. LLM Prowess in Deception](#a-llm-prowess-in-deception)
  - [B. Comparative Game Performance](#b-comparative-game-performance)
  - [C. Hallucination Patterns](#c-hallucination-patterns)
  - [D. Classifier Efficacy](#d-classifier-efficacy)
  - [E. Qualitative Observations and Model-Specific Behaviors](#e-qualitative-observations-and-model-specific-behaviors)
- [Core Contributions and Implications](#core-contributions-and-implications)
  - [Summary of Contributions](#summary-of-contributions)
  - [Broader Implications](#broader-implications)
- [Project Team](#project-team)
- [Further Information](#further-information)


## Project Synopsis: The CARDS Initiative

The CARDS project undertakes the challenge of systematically developing and evaluating a methodology to identify and categorize deceptive intentions versus unintentional hallucinations in Large Language Models. This research is motivated by the increasing sophistication of LLMs and the critical need to understand their capacity for strategic communication, including potentially misleading behaviors. The work contributes to the broader understanding and detection of strategic behavior in multi-agent interactions, with a specific focus on "lies" and hallucinations exhibited by LLMs. Such understanding is pivotal as LLMs become more integrated into complex decision-making systems where trustworthiness and reliability are paramount.  

## Defining the Terrain: Deception and Hallucination in LLMs

A clear conceptual framework for deception and hallucination is fundamental to this research.

### Deception Defined

In the context of CARDS, deception is characterized by an LLM's deliberate intent to mislead. This is operationalized within the experimental game where models are explicitly prompted to lie (or not lie) about their game state to influence an opponent. The study specifically examines "instructional inconsistency, specifically where the model generates factually incorrect or misleading content, demonstrating deceptive intent that influences user judgment". This focus implies that deception is not merely the generation of false statements, but a strategic act aimed at manipulating an opponent's perception or decision-making process within the defined rules of interaction.  

### Hallucination Categorized

Hallucinations are defined as deviations from factual accuracy or contextual consistency, not necessarily driven by an intent to deceive. Following relevant literature, CARDS categorizes hallucinations into two primary types:

* **Factuality Hallucinations**: Content that is verifiably false.
* **Faithfulness Hallucinations**: Content that is inconsistent with provided instructions, context, or logical reasoning. This category is further subdivided into:
  * Instruction Inconsistency (the primary focus of this study)
  * Context Inconsistency
  * Logical Inconsistency

The emphasis on instructional inconsistency within faithfulness hallucinations is particularly relevant when assessing deceptive intent, as it directly relates to how a model adheres to or deliberately violates explicit directives in its communication.

### The Interplay

A key challenge addressed by CARDS is distinguishing between deliberate deception and unintentional hallucination, especially when a model prompted to deceive might also produce hallucinatory output. The project aims to use embeddings derived from LLM interactions to differentiate four distinct states:

1. Lying intent present, no hallucination.
2. Lying intent present, hallucination present.
3. No lying intent, no hallucination.
4. No lying intent, hallucination present.

Successfully distinguishing these four states is crucial. It allows for a more fine-grained analysis of LLM outputs, moving beyond a simple "correct/incorrect" assessment to understand the underlying nature of the generated content—whether an inaccuracy stems from a deliberate choice to mislead, a factual error, or an internal consistency failure. This nuanced understanding is essential for developing robust mechanisms to ensure AI reliability.

## The "Big or Small" Game: A Crucible for Deceptive AI

To elicit and study deceptive behaviors, CARDS employs a custom-designed two-player card game called "Big or Small." Player A and Player B, each an LLM agent, are given a hidden number card (ranging from 1 to 13) and must negotiate whether to compare for the higher ("big") or lower ("small") card to determine the winner. This setup creates a natural incentive for strategic communication, including deception, as players try to influence each other's decisions to their advantage.  

### Game Initialization and Core Objective

The game begins with the dealer (system) initializing Players A and B and distributing one hidden card (value 1-13) to each. The card numbers remain unchanged throughout a single game. The primary objective for each player is to win the game.  

### Detailed Game Flow

The game proceeds through distinct phases, designed to encourage interaction and strategic decision-making:

#### Speaking Phase

* Players take turns speaking. The order alternates: Player A speaks first in even rounds, and Player B speaks first in odd rounds.
* The system issues a "speaking system prompt" to the current player. Crucially, this prompt includes an instruction on whether the LLM must lie or tell the truth in its message for that round. This prompt is removed from the LLM's memory after its turn to ensure that any consistency in behavior (truthful or deceptive) across turns stems from the model's internal state or strategy rather than persistent instruction.
* The LLM's response must be in a specific JSON format, including a message (visible to the opponent), a decision (the player's internal thought on whether "big" or "small" is advantageous, or "None"), and thought and analysis (the internal reasoning behind the message and decision). The decision and thought and analysis fields are not visible to the opponent.  

#### Consensus Phase

* After both players have spoken in a round, the system issues a "decision system prompt" to both players simultaneously. This prompt asks for their final choice of "big" or "small" for the comparison. This prompt is also removed from memory after the decision is made.
* If consensus is reached (i.e., both players choose "big" or both choose "small"), the game proceeds to the comparison phase.
* If no consensus is reached, the game returns to the Speaking Phase for another round of negotiation. If no consensus is achieved after 5 rounds of interaction, both players lose the game. This rule imposes a time limit and encourages players to find common ground or successfully persuade their opponent.  

#### Comparison Phase (Reveal and Compare)

* Once consensus is reached on "big" or "small," the hidden cards are revealed.
* If "big" was chosen, the player with the higher card number wins.
* If "small" was chosen, the player with the lower card number wins.  

### Strategic Deception within the Game

A crucial element of the "Big or Small" game is the explicit incorporation of deception. When an LLM is instructed to lie, its statements must be intentionally deceptive (e.g., misreporting its card value, misleading the opponent about its intentions) but are also required to maintain logical coherence and plausibility. The game rules provide examples of various lie types that players can employ, such as direct misreporting of numbers, bluffing, reverse psychological warfare, vague expressions, inducing consensus, feigned hesitation, repeated misleading, reverse confession, provocation, and hiding the truth.  

Key strategic considerations for the LLMs include making persuasive statements to influence the opponent, being aware that the opponent may also be deceptive, and managing their strategy within the 5-round limit to achieve consensus and win. The game design, particularly the removal of prompts from memory, tests an LLM's ability to maintain a consistent persona or strategy (truthful or deceptive) based on prior interactions and its understanding of the game state, rather than solely relying on the immediate instruction. This setup provides a more dynamic and arguably more ecologically valid test of LLM reasoning and strategic communication than simple question-answering tasks.  

> ![pipeline](https://github.com/user-attachments/assets/22698388-8c18-491d-936e-3790916c678f)

## Experimental Design and Methodology

The study employed a diverse set of LLMs and experimental setups to investigate deceptive behaviors.

### LLMs Under Investigation

A range of LLMs were utilized to assess varying capabilities in the context of the game:

* API Models: GPT-4o, GPT-4o-mini, GPT-3.5-turbo.  
* Local Models: Breeze, TAIWAN LLAMA (TWLlama).  

### Experimental Setups and Lying Probability

Experiments were conducted with a fixed lying probability of 50% for the LLM agents when instructed to speak. This ensured a balanced exposure to situations requiring truthful and deceptive communication. Various configurations were tested:

* Cloud LLM vs. Cloud LLM (e.g., GPT-4o vs. GPT-4o-mini, GPT-4o vs. GPT-3.5-turbo, GPT-4o-mini vs. GPT-3.5-turbo: 20 games each).
* Cloud LLM vs. Local Model (e.g., Breeze vs. GPT-3.5-turbo, TAIWAN LLAMA vs. GPT-3.5-turbo: 20 games for each combination, totaling 120 games). This setup was designed to compare local models, which might exhibit lower hallucination rates, against cloud models, which might have lower win rates, and to provide an opportunity for local models to compete against more resource-intensive cloud counterparts.
* Local Model vs. Local Model (Self-Play, e.g., Breeze vs. Breeze, TAIWAN LLAMA vs. TAIWAN LLAMA: 100 games for each model, totaling 200 games).

In total, approximately 280 games were played, generating a substantial dataset for analysis. This systematic variation across model types and pairings allows for robust comparisons and data collection under diverse scenarios.

### Data Collection and Annotation

Meticulous data logging and annotation were critical components of the methodology.

**Game Log Format (game_log):**  
Each turn's data was recorded in a JSON structure, capturing key aspects of the interaction. The fields included:

* `bluffing`: Indicating whether the LLM was prompted with an intent to lie (derived from the system instruction).
* `message`: The LLM's actual utterance to its opponent.
* `decision`: The LLM's internal choice of "big," "small," or "None" regarding the comparison.
* `hallucination`: A boolean value (True/False) indicating the presence of hallucination.
* Alternatively, `hallucination_type`: Categorizing the hallucination as "None," "Factuality," or "Faithfulness," along with `hallucination_subtype` for more detailed classification.

**Annotation Process:**  
A two-pronged annotation approach was used:

1. Manual Annotation: The presence or absence of hallucination in the LLM's message was determined through human review.
2. Prompt-Based Annotation: The presence or absence of lying intent (bluffing field) was directly derived from the system's instruction to the LLM for that turn.

This use of distinct annotation methods—known intent from the prompt and manually assessed hallucination—is a methodological strength. It avoids conflating these two phenomena during data labeling, which is crucial for the subsequent classifier's goal of distinguishing them.

**Data Volume for Classifier:**  
Assuming approximately 4 relevant statements per game from specific experimental setups (D and E, involving local models), around 1280 annotated statements were estimated to be available for training the deception classifier.

## The CARDS Deception Classifier

### Objective

The classifier aims to determine if an LLM is attempting to deceive and/or if its output contains hallucinations, without explicit prior knowledge of the intent to lie for a given turn. This capability is particularly valuable for applications with limited hardware or computational resources, such as real-time robotic systems, where efficient, on-the-fly detection of misleading or erroneous LLM behavior is critical. The ambition here is to infer an internal state (intent to deceive) from observable output (embeddings of text), moving beyond simple factual error detection.

### Approach

The CARDS classifier is a supervised learning model. It is trained on embeddings derived from the LLM interactions within the "Big or Small" game. The goal is to categorize LLM outputs into one of the four states previously defined: (Lying Intent & No Hallucination), (Lying Intent & Hallucination), (No Lying Intent & No Hallucination), and (No Lying Intent & Hallucination). Data from experiments involving local models (setups D and E) were specifically used for training this classifier.

### Potential Application

The practical motivation for this classifier includes enabling less computationally powerful systems, such as "cheap real time robots," to identify deceptive or hallucinatory behavior in the LLMs they interact with or are powered by. If such a classifier proves robust, it could serve as a vital component in building trust and safety layers for a wide array of LLM applications. For instance, a user-facing AI assistant could incorporate an internal "CARDS-like" classifier to flag instances where it might be unintentionally hallucinating or, more critically, if it's being steered towards deceptive outputs due to adversarial inputs or internal misalignments.

## Key Experimental Findings and Analysis

### A. LLM Prowess in Deception

Models varied significantly in their ability to execute deliberate deception successfully, defined as lying when instructed without also producing a hallucination.

**Table 1 (Part 1): Successful Deceptive Rates without Hallucination**

| Model         | Successful Deceptive Rate (No Hallucination) |
|---------------|-----------------------------------------------|
| gpt-4o        | 0.95                                          |
| gpt-4o-mini   | 0.82                                          |
| gpt-3.5-turbo | 0.55                                          |
| breeze        | 0.5824                                        |

GPT-4o demonstrated the highest proficiency in deliberate, coherent deception, achieving a 95% success rate. This suggests that more advanced models possess a greater capacity for intentional and plausible misrepresentation. The substantial gap between GPT-4o and models like GPT-3.5-turbo (0.55) indicates that effective lying—being deceptive and coherent—is a sophisticated capability.

### B. Comparative Game Performance

Overall game-winning rates provided another dimension for comparing model performance.

**Table 1 (Part 2): Winning Rates Between Different Models (Row Player's Win Rate vs. Column Player)**

|               | gpt-4o | gpt-4o-mini | gpt-3.5-turbo | Geo. Mean |
|---------------|--------|-------------|----------------|------------|
| gpt-4o        | -      | 0.20        | 0.60           | 0.3872     |
| gpt-4o-mini   | 0.25   | -           | 0.70           | 0.3741     |
| gpt-3.5-turbo | 0.30   | 0.20        | -              | 0.2449     |

While GPT-4o was the most effective deceiver (Table 1, Part 1), its win rates were not universally dominant. For example, GPT-4o won only 20% of its games against GPT-4o-mini, despite its superior deception skills. However, GPT-4o did achieve the highest geometric mean win rate (0.3872), indicating strong overall performance. This suggests that while deceptive skill is an asset, other factors such as opponent strategy, inherent game stochasticity, and potentially rule comprehension also play significant roles in determining game outcomes. The correlation between deception ability and winning is present but not absolute.

### C. Hallucination Patterns

The study investigated hallucination rates and their relationship with deceptive intent. A general trend observed was that hallucination rates correlated with model capability, with more advanced models typically exhibiting lower baseline hallucination rates.

> ![image](https://github.com/user-attachments/assets/8c31033c-f248-45b3-beea-d6aede739014)


**IMAGE 1: Hallucination Rate between Different Models with or without Deceptive Intention**

A particularly nuanced finding emerged from this data:

* For more capable models like GPT-4o and GPT-4o-mini, the instruction to deceive was associated with a slight increase in hallucination rates.
* Conversely, for less capable models such as GPT-3.5-turbo and Breeze, an intent to deceive was correlated with a decrease in hallucination rates.

This counterintuitive pattern is a significant observation, suggesting that the act of directed deception can sometimes impose a structure that reduces undirected errors in less sophisticated models.

### D. Classifier Efficacy

**Accuracy:**

![image](https://github.com/user-attachments/assets/1173bfe5-97de-4c6d-810f-4082000b7ef3)

**Table 2: Classifier Accuracy Highlights (Layer 28, Unbalanced Data Averages)**

![image](https://github.com/user-attachments/assets/63571542-d2f5-48d2-aa5c-72a1525acabb)

**IMAGE 2: Confusion Matrix of Classifier (layer 28, all data)**

### E. Qualitative Observations and Model-Specific Behaviors

* **Breeze**: Exhibited a strong bias towards choosing "大" (big) in most game scenarios, irrespective of other strategic factors.
* **TAIWAN LLAMA (TWLlama)**: Demonstrated deficiencies in adhering to the required JSON output format.
* **GPT-4o-mini, GPT-3.5-turbo, Breeze**: Encountered considerable difficulties in understanding and applying game rules accurately.

## Core Contributions and Implications

### Summary of Contributions

* **Methodology Development**: A novel experimental framework (Big or Small game) and systematic data collection methodology.
* **Empirical Findings**: Quantitative evidence on deceptive capabilities, hallucination patterns, and strategy.
* **Classifier Development**: A supervised classifier for detecting intent and hallucination using embeddings.
* **Practical Application Insights**: Relevance to resource-constrained environments like robotics.

### Broader Implications

* **Understanding Advanced LLM Behavior**: Provides insight into emergent behaviors and strategic deception.
* **Trustworthy AI**: Supports development of reliable, interpretable AI systems.
* **Future Research Directions**: Promotes further study into internal state inference, generalizable detection, and alignment.

## Project Team

* Chi-Hsiang Chao (samuelchao@g.ncu.edu.tw)
* Hsin-Fu Chang (xuanchang@g.ncu.edu.tw)
* Po-Yuan Teng (davidteng00000@gmail.com)
* Chiao-Jou Chang (s111502536@g.ncu.edu.tw)

## Further Information

**Future Publications**: Future publications or full project details, if they become publicly available, will be linked here.
