---
title:  "Tool-Agents with the watsonx LangChain BaseChatModel"
categories: 
    - agenticAI
    - AI
tags: 
    - llms
    - generativeAI
    - architecture
    - watsonx
date: 2024-07-13
---

The watsonx.ai BaseChatModel supports integration with LangChain for building LangChain Tool-Agents. The following code demonstrates use of the LangChain watsonx BaseChatModel to construct a Tool-Agent. The application logic follows: (1) a call to the language model to determine which tools to invoke; (2) the programmatic invocation of the selected tools (3) a final call to the watsonx language model with the response from the tools. 

Tools may be any call out to an external API or service such as a database or embeddings store, and LangChain provides additional support for this.

```
import os
from dotenv import load_dotenv
from langchain_ibm import ChatWatsonx
from langchain_core.tools import tool
from langchain_core.pydantic_v1 import BaseModel, Field

load_dotenv()
api_key = os.getenv("WATSONX_APIKEY", None)
ibm_cloud_url = os.getenv("WATSONX_URL", None)
project_id = os.getenv("WATSONX_PROJECT_ID", None)
if api_key is None or ibm_cloud_url is None or project_id is None:
    print(
        "Ensure you copied the .env file that you created earlier into the same directory as this notebook"
    )
else:
    creds = {"url": ibm_cloud_url, "apikey": api_key}

params = {
    "decoding_method": "greedy",
    "max_new_tokens": 200,
    "min_new_tokens": 1,
}

chat = ChatWatsonx(
    model_id="mistralai/mixtral-8x7b-instruct-v01",
    url=ibm_cloud_url,
    project_id=project_id,
    params=params,
)

@tool
def plus(x: int, y:int) -> int:
    """Performing addition of x and y."""
    return x+y

class Plus(BaseModel):
    """Add x and y"""
    x: int = Field(..., description="a number")
    y: int = Field(..., description="anther number")

@tool
def times(x: int, y:int) -> int:
    """Perform multiplication on x and y"""
    return x*y

class Times(BaseModel):
    """Mutiple x and y"""
    x: int = Field(..., description="a number")
    y: int = Field(..., description="anther number")

llm_with_tools = chat.bind_tools([Times,Plus])

from langchain_core.messages import HumanMessage, ToolMessage
messages = [HumanMessage("please tell me what 3 multiplied by 4 is? Then work out 7 added to 3? And make the answer verbose")]
ai_msg = llm_with_tools.invoke(messages)
messages.append(ai_msg)

for tool_call in ai_msg.tool_calls:
    selected_tool = {"times": times, "plus": plus}[tool_call["name"].lower()]
    tool_msg = selected_tool.invoke(tool_call)
    messages.append(tool_msg)

result = llm_with_tools.invoke(messages)
print(result)
```