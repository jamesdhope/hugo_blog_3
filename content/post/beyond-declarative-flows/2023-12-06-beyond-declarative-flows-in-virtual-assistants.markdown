---
title:  "Beyond declarative flows in virtual assistants with language models for single-turn and multi-turn reasoning"
categories: 
    - generativeAI
    - conversationalAI
tags: 
    - llms
    - generativeAI
    - architecture
    - watsonx
date: 2023-12-06
---

Building user journeys as declarative trees within a virtual assistant requires assumptions to be made about the user query and the optimal path. If there are many decision points and the tree consists of many forks the number of assumptions increases exponentially down the tree leading to inefficiencies and a suboptimal design. To address this inefficiency, one approach is to use a language model to reason over available tools (or APIs) that can be called to augment the response to the query. This collapses the tree and replaces it with a language model that can be guided through a policy or rules expressed in natural language and supplied to the model in a prompt.

The following diagram shows this interaction with IBM Watson Assistant which is used to orchestrate the call to the language model for reasoning, the tools (a statistical propensity model, a vectorstore and a prestoDB engine), and the language model to generate a final response.

![Interaction Diagram](single-turn-reasoning.png)

In this example, the language model is used for single turn reasoning. With next generation language models, multi-turn reasoning may be more effective at guiding the user to a goal. Declarative flows are used to build application logic, guardrail LLM driven actions and impose structure around interactions that must be explicitly defined.