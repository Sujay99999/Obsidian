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

- Basic parameters for LLM calls
	- Temperature
		- Controls the randomness i.e., 0.0 for deterministic and 1.0 for creative outputs
		- Lower values such as 0.0 - 0.3 guarantee factual outputs
		- HIgher values such as 0.6 - 0.9 provide creative answers
	- Max Tokens
		- Limits the size of the output
		- Token is the fundamental unit of the LLMs where ~4 characters = 1 token
- How tools are integrated into the LLM calls
	- Tools are defined using JSON Schema so the LLM understands what functions are available.
	- The LLM doesn't execute tools directly - it requests tool calls that you execute i.e., LLM doesn't care on the tool functionality, it takes an call on if the tool **multiple tools** have to be used for a operation or not based on the tool description and parameters. This implies that any custom tools created must have legitimate and crisp tool descriptions
	- In a vanilla implmentation of LLM calls with tools, it is the responsibility of the user to ensure error handling/ throttling etc of the tool usage by the LLMs
	- ```
	  TOOLS = [
    {
        "name": "calculator",
        "description": "Performs basic arithmetic operations.",
        "input_schema": {
            "type": "object",
            "properties": {
                "operation": {
                    "type": "string",
                    "enum": ["add", "subtract", "multiply", "divide"],
                },
                "a": {"type": "number"},
                "b": {"type": "number"},
            },
            "required": ["operation", "a", "b"],
        },
    },
]
	  ```
	- But if we use any custom frameworks such as Langchain/ Strands, the error handling is generally taken care of i.e.,  the LLM doesn't fix your broken tool — it just reacts to the error message you (or the framework) sends back. For example if get_weather fails, the framework sends "Error: API timeout" back to the LLM, and the LLM might say "I couldn't get the weather, try again later." It's still just text — the LLM never touches your code.
	- There is a high possibility that "With higher number of tools", the context of the message to LLM significantly increases leading to higher token usage. It is again the responsibility of the developer to ensure that the context provided to LLM is as small as possible. There are multiple ways to resolve this
		- Using RAG for tools which can help to decide the subset of the tools required for the use case
		- We can also have LLM as a router which decides which specific subagents are invoked (We create subagents with specific subset of tools)
		- Custom frameworks generally provide inhouse solutions for this i.e., Langchain has a solution called "ToolRetreiver" which has its own vector store
- Agent loop based on Tools
	- Once we have tools enabled for the LLM, the agent loop is much simpler, this is where the ReAct framework basically is applied i.e., A agent loop starts off with a prompt, and then checks if it needs to interact with the tools; If yes, it uses the tools and appends the results through the loop until the LLM decides to not use any of them
	- ```
	  SYSTEM_PROMPT = """You are a coding agent..."""

TOOLS = [...]  # Tool definitions

def execute_tool(name: str, tool_input: dict) -> str:
    """Execute a tool and return the result."""
    ...

class CodingAgent:
    """Autonomous agent that uses tools in a loop."""

    def __init__(self, model: str):
        self.client = ...
        self.model = model
        self.max_iterations = 10

    def run(self, task: str) -> str:
        """Execute the agent loop for the given task."""
        # Agent loop implementation
        ...

def main() -> None:
    """Interactive CLI with welcome message."""
    agent = CodingAgent()

    while True:
        user_input = input("You: ")
        if user_input.lower() in ("exit", "quit", "q"):
            break
        response = agent.run(user_input)
        print(f"Agent: {response}")
	  ```


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
	- Session and Memory
		- What is Session
			- The session is the entire conversation you have with an agent, where all the interactions are noted
			- A session basically captures 3 things
				- Interactions between agent and human
				- State of the agent at each point i.e., Current prompt; Past interactions, Status of the agent and User preferences such as credentials
				- Tools available
			- As the interaction progresses, the session dynamically updates to reflect the changing conversational flow, and state of agent, which **enables the agent to adapt and respond effectively**.
		- Memory is used to persist the "Session Information".
		- There are different modes of memory persistance
			- Short Term Memory
				- Retains the data only until the session is present. 
				- Its cleared once the session is reset. 
				- It depends on the Context Window; But the session always tends to perform poorly when the context becomes huge
			- Long Term Memory
				- Retains memory for much longer time, providing a better user experience
				- Helps to create "Learned patterns"
				- Memory can be updated either sync or in async depending on requirements
			- Episodic Memory
				- Type of Long memory, where you can recall the particular experiences such as Agent traces, tool usages, etc
				- Helps in maintaining the coherency of the Agent history and experiences
			- Semantic Memory
				- This type of memory helps to personalize the experience, as the agent can modify certain data, to reflect the updates in the memory such as KB
	- Single Pass Agent vs Continuous Execution
		- A single pass agent has clear cut steps, which are followed in a linear fashion. Doesnt account for any fallback or dynamic thinking during the execution, when an error pops
		- Agents based on **Continuous Execution** resolves this problem by "Dynamically adapting on the output".
		- So, when a user sends "Prompt + context" to the LLM, the LLM decides on what tools have to be used for the task, uses them, gathers the output data, and then decides whether to get more information or generate output.
		- This continuous execution can either use "ReAct" or "Plan and Execute" staategy
	- Control and Validate the Agentic flow
		- The core idea os Agent flow is same as above, but can be extended by integrating multiple sub agents depending on how complex the flow has to be present
	- Human in the Loop
		- This enforces human intervention for the agents tasks to ensure accountability on critical steps
	- MCP
		- Open protocol for integrating the LLMs and tools by enabling bi-directional communication with authentication and security
		- MCP has 4 parts
			- Host - Agents and programs
			- MCP Client - Clients from the Host that connect with the MCP server of the tools, and this connection is 1:1 mapping
			- MCP Server - Server exposing the tool
			- Server tools - Can be any tool such as Email, Slack, etc
		- Request/ Response flow is similar to HTTP protocol without handshake


- 