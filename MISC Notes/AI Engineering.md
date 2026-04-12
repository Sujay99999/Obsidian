Training
Inference
Embeddings
CRAFT a prompt
- Context
- Role - What should the AI pretending to be
- Action
- Format
- Tone
Add additional prompt "Think step-by-step"

- Agentic AI
	- What is the difference between LLM and AI agent
		- LLMs are basically language models which have been trained across vast amount of data, but they dont have access to the tools. Most of the LLMs simply use RAG for the getting the answers based on the prompt.
		- The RAG works on the static files, where it cant have access to the APIs or the tools
		- AI agent can interact the tools and APIs, its a wrapper on top of the LLMs to enhance the capabilities
	- How does AI agent use the tools
		- LLMs use the framework called ReAct = Reason + Action
	- When do we need to use the agents vs LLMs
		- So, agents produce value only when there is "Complex Decision Making" involved with "Dynamic Interactions", which often has some "Multi-step process"
		- Agents are good 