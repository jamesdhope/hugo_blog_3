---
title:  "Improving Language Models Inductive Bias with Q*"
categories: 
    - generativeAI
    - AgenticAI
tags: 
    - llms
    - generativeAI
    - architecture
    - reasoning
    - watsonx
    - inductive bias
date: 2024-07-10
---

Q*, a hybridisation of Q-learning and the pathfinding algorithm A*, has the potential to enhance the inductive bias of a language model in tasks that demand certain types of reasoning. An implementation of Q* is described here https://lnkd.in/giMTvSQR and implemented with a watsonx language model here https://github.com/jamesdhope/q--deliberate-planning-watsonx with the following parameters and adaptions:

- Trajectories are completed by an expert model with a terminal state that is determined by the expert.
- h(s) or the Q-value is the average of the log_probs for the generated sequence
- The aggregated utility h(s) is the aggregated Q-value or log_probs for the path to that state
- The algorithm terminates when the open_list is empty or if the specified number of states has been visited
- The question / task, the number of states that can be visited, the semantic similarity score for states to be considered the same (visited), the lambda value, and the number of actions are exposed as global parameters to be configured.

This simplified implementation uses log_probs for the reward and an expert model that knows best to evaluate trajectories.

With a language model under the supervision of Q*, I asked: If you crash landed in the desert, what would be prioritised list of things you must do in order to survive? 

Within the constraints set, ten states were visited with thirty actions considered in total, and limited of course by the data used to train the models, Q* returned:

1. Find or create a source of shade to protect yourself from the harsh desert sun, as dehydration and heatstroke can set in quickly.
2. Assess your injuries and treat any wounds or broken bones. Use any available materials to create a makeshift splint or bandage, and prioritize treating any life-threatening injuries first.
3. Create a visible signal for rescue, such as a smoke signal during the day, or a fire or flashing light at night. Use any available materials to create a signal that can be seen from a distance, such as a mirror, brightly colored clothing, or a whistle.
4. Start a fire without matches, using methods such as friction, solar reflection, or flint and steel. Fire can provide warmth, light, and a way to signal for help, as well as a means to purify water and cook food.
5. Use available materials to create a makeshift shelter, such as a lean-to or a debris hut, to protect yourself from the elements and any potential wildlife threats. This can be done using branches, leaves, and other natural materials found in the desert.
6. Ration any available food and water to make them last as long as possible. Avoid eating desert plants unless you are absolutely sure they are safe, as many can be toxic. Consider hunting for small animals or insects if you have the necessary skills and equipment.
7. Find a source of water, such as a stream, river, or oasis, or collect dew or rainwater to stay hydrated. If you can't find a natural source, consider collecting and purifying water from cacti or other plants.
8. Create a makeshift tool, such as a spear or knife, using available materials like rocks, sticks, and bones. This can be used for hunting, self-defense, and other tasks that may be necessary for survival.
9. Navigate using the sun and stars to determine the direction you need to head in to find civilization or a potential rescue route.

Q* offers potential to enhance the inductive bias of a language model for tasks that demand reasoning, and the efficacy of Q* for reasoning should be established with a suitable evaluation framework.
