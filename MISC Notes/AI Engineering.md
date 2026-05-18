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
		- The RAG works on the static files, where it cant have access to the APIs or the tools, hence this can be thought as a "Workflow" of tasks
		- AI agent can interact the tools and APIs, its a wrapper on top of the LLMs to enhance the capabilities
	- How does AI agent use the tools
		- LLMs use the framework called ReAct = Reason + Action
	- When do we need to use the agents vs LLMs
		- So, agents produce value only when there is "Complex Decision Making" involved with "Dynamic Interactions", which often has some "Multi-step process"
		- Agents are good when we need "Decision Making" beyond the Simple rule book.
		- Agents can be skipped if the Query can be solved using LLM + Few-shot Prompting/ Chain-of-Thought prompting other prompting techniques
- Workflows




- How to enable Reasoning and Planning in Agents
	- Reasoning in LLMs
		- So, LLMs always performs well on "Pattern Recognition" and "Conceptual Correlation", due to the core idea of pattern repetition.
		- In the subject of Cognitive sciences, we have this concept of "System 1 vs System 2 thinking", where System 1 thinking is associated with "Direct Pattern Matching and Quick responses", where as the System 2 thinking is associated with "Strong Logical reasoning with thorough planning and execution"
	- In order to bring the best reasoning capabilities of the LLMs, we have some techniques such as 
		- Chain of Thought prompting
			- We break down the complex tasks and then answer. By generating a series of coherent thoughts, the model can better articulate the problem with better understanding and debugging
			- When we have any prompt "Do the task 1", which is a Zero shot Chain of Thought process, we can improve the prompt by "Do the Task 1. Take the reference of task 0. **Think Step by Step**"
		- Plan and Execute Strategy
			- So, when we have a complex task, we can break it down within the prompt by saying "Plan towards the Task and then Execute it", similar to how we develop Spec files before development.
		- ReAct Strategy
			- "Reasoning + Action" is basically a strategy to Interleave the Reasoning tasks with Action Tasks, where LLM goes through a cyclic process to dynamically create plans and execute and fine tune in the next iterations.
			- ReAct is typically more faster than Plan + Execute strategy to generate final answer, consuming less tokens. its well suited for Medium tasks than compared to Plan + Execute which is suited for complex tasks

- Building an Agent
	- 100 strike price
		- Sell put for 85 pe - preimum 10 = 1000
			- Scneario 2: price is 80; -8500+1000 = -7500 
				- 