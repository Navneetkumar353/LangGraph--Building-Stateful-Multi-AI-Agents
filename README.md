# LangGraph - Building Stateful Multi AI Agents

## 📌 Overview
This project demonstrates how to build stateful AI agents using **LangGraph**, integrating **LangChain** with tools like **Arxiv** and **Wikipedia** for enhanced chatbot capabilities.

The repository contains:
1. **Simple Chatbot** - A basic chatbot using LangGraph.
2. **Chatbot with Tools** - A chatbot that integrates external tools for querying knowledge bases.

## 🚀 Features
- **Stateful Conversations**: Maintains memory using LangGraph’s state management.
- **Multi-Agent Capabilities**: Uses multiple AI tools to enhance responses.
- **Tool Integration**:
  - **Wikipedia** for general knowledge queries.
  - **Arxiv** for research paper lookups.
- **Visualization**: Graph representation of AI flow using **Mermaid diagrams**.

## 🛠 Installation
Run the following commands to set up dependencies:
```bash
pip install langgraph langsmith langchain langchain_groq langchain_community
pip install arxiv wikipedia
```

## 📌 Usage

### Simple Chatbot
Run the chatbot:
```python
from langchain_groq import ChatGroq
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages

class State(TypedDict):
    messages: Annotated[list, add_messages]

graph_builder = StateGraph(State)

def chatbot(state: State):
    return {"messages": llm.invoke(state['messages'])}

graph_builder.add_node("chatbot", chatbot)
graph_builder.add_edge(START, "chatbot")
graph_builder.add_edge("chatbot", END)

graph = graph_builder.compile()

# Run chatbot
while True:
    user_input = input("User: ")
    if user_input.lower() in ["quit", "q"]:
        print("Good Bye")
        break
    for event in graph.stream({'messages': ("user", user_input)}):
        print("Assistant:", event["messages"].content)
```

### Chatbot with Tools
```python
from langchain_community.utilities import ArxivAPIWrapper, WikipediaAPIWrapper
from langchain_community.tools import ArxivQueryRun, WikipediaQueryRun

arxiv_tool = ArxivQueryRun(api_wrapper=ArxivAPIWrapper(top_k_results=1, doc_content_chars_max=300))
wiki_tool = WikipediaQueryRun(api_wrapper=WikipediaAPIWrapper(top_k_results=1, doc_content_chars_max=300))

tools = [wiki_tool]

from langgraph.prebuilt import ToolNode, tools_condition
tool_node = ToolNode(tools=tools)

graph_builder.add_node("tools", tool_node)
graph_builder.add_conditional_edges("chatbot", tools_condition)
graph_builder.add_edge("tools", "chatbot")
```
This enables the chatbot to fetch relevant information dynamically.

## 📊 Graph Visualization
To visualize the LangGraph workflow:
```python
from IPython.display import Image, display
display(Image(graph.get_graph().draw_mermaid_png()))
```

## 🔥 Demo
- The chatbot can answer general queries and fetch research papers.
- It remembers previous interactions to maintain context.

## 🎯 Future Enhancements
- Integrate more tools (e.g., web scraping, weather API).
- Add a UI interface for better interaction.
- Implement multi-agent collaboration using LangGraph.
