---
title:  "Reconstructing user context to reduce the risk of policy misaligned generated content in LLM enabled conversational assistants"
categories: 
    - generativeAI
    - AI guardrails
    - AI governance
tags: 
    - llms
    - generativeAI
    - architecture
    - watsonx assistant
date: 2024-02-17
---

For conversatonal assistants, language models offer the potential benefit of being able to generate responses to the widest posisble range of queries that adhere to a policy, without the need for a premediated conversational design, which is inherently hard to design optimally for all queries. However, prompt engineering alone may not reduce the risk of the language model deviating from a policy to an acceptable level, particularly in the absence of comphrensive testing frameworks.

To reduce this risk, one approach is to extract known entities from natural language inputs and to use slot filling with explicit options for the user to confirm, in order for the user query and context to be reconstructed in the backend before it is passed to the language model. This approach mitigates the risk of queries and contexts being socially engineered to align the generated output in undesirable ways and reduces the scope of testing to optimise for permutations of determinstic inputs injected into the prompt.

The sequence diagram illustrates this approach with watsonx Assistant with these mechanisms for capturing and reconstructing user context labelled A and B:

![Sequence Diagram](assistant-user-context.png)