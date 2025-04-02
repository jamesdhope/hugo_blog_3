---
title:  "Operating AI at Scale with OpenShiftAI, KubeFlow Pipelines and watsonx"
categories: 
    - OpenshiftAI
    - kubeflow
    - watsonx
    - LLMOps
    - MLOps
    - AIOps
tags: 
    - OpenVino
    - KServe
    - KNative
    - Kubeflow
    - vLLM
    - pipelines
    - fine tuning
    - lora
    - watsonx
    - kubernetes
    - architecture
date: 2024-11-25
---

Operating AI across different clouds and execution engines becomes complex and difficult to maintain with cloud native tools as the number of different integrations between systems proliferates at scale. OpenShiftAI provides a cohesive hybrid, multi-cloud AI platform that enables enterprises to separate concerns between pipeline orchestration and workload execution reducing complexity in the data and governance subdomains and enabling enterprises to operate AI at scale. 

### Functions of an AI Operations System

![Functional View of AI Operations](AIOps_1.png)

### OpenShiftAI, watsonx.data & watsonx.governance enabling AI at Scale

OpenShiftAI combined with watsonx.data and watsonx.governance enables enterprise AI at scale in the following ways:

- separation of concerns between pipeline orchestration and training/serving workload execution, demonstrating workload placement to where it makes sense, for reasons such as data compliance or service level agreements for downstream AI 

- versioning and orchestration of pipelines as a hybrid multicloud platform-first approach, removing the need for and complexity that results from cloud native integrations that proliferate in number when operating AI at scale, and unlocking the potential to operate AI across the enterprise

- pipelines for super fine tuning an open language model (we show LoRA PEFT fine tuning with IBM hashtag#Granite but this is easily extensible to full SFT or model distillation), because small open models are the future for enterprise AI

- distributing training and observability of GPU workloads with Ray, because distributed compute is important if not essential for operating AI at scale

- watsonx.data as a cloud agnostic feature store, because data is disparate and AI builders need that data to derive value for the enterprise 

- publication of model factsheets in watsonx.governance and tracking models as part of an AI Use Case, because enterprise AI needs to be governed. 

### Integrating watsonx.governance with OpenShiftAI KubeFlow Pipelines

![OpenShiftAI integration with watsonx.governance](AIOps_2.png)

For a more in-depth review of OpenShiftAI and Kubeflow pipelines see: https://blog.pierswalter.co.uk/posts/openshift-ai-pipeline/
