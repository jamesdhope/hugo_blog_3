---
title:  "An LLM assisted approach to automating testing of a virtual assistant"
categories: 
    - generativeAI
    - automation
tags: 
    - llms
    - generativeAI
    - architecture
date: 2023-11-01
---

Large Language Models (LLMs) can be used to automate testing of virtual assistants. One approach is to use the LLM to generate the queries and responses of the human user to automate the test of a journey, end to end. Here I share a conceptual data pipeline view of such a system. The key ideas are:

- *prompts* are created to generate responses that fulfil the different types of interactions (sometimes called nodes) in the virtual assistant journeys
- data on *intents* and *personas* is fetched from file and injected into the prompt and sent to the LLM to generate initial and subsequent queries / responses
- a *code function* is written that orchestrates the interaction between the LLM and the virtual assistant by using prompts and formatting payloads for each type of node
- a *global code function* iterates over intents (by generating different initial queries) and personas for each journey

![GitHub Logo](assistant-test-pipeline-view.png)

Here I am using the LLM in an *assisted* role, where a *code function prescribes logic* that maps the prompt to a particular node. However, by tuning the LLM on interactions for each node type, the LLM may be used to drive the automation without the need for a code function to orchestrate individual interactions along the journey.