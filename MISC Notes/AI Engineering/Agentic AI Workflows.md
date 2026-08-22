### Usage of Tools within the Sync calls or Async streaming

```
public class CodingAgent {
	public CodingAgent(){
		// Setup new LLM Client with paramters and system prompt
	}
	
	public void run (String userInput){
		while currIteration <= MaxIterations {
			// Create message using MessageBuilder
			// If response = NO_TOOL_USE, print the response
			// Else
				// Complete the tool executions and append the results
			// currIteration++;
		}
		
	}
}


public static void main(){
	CodingAgent = new CodingAgent(); // Initialize with all the required parameters
	
	while (true){
		input = getInputFromUser();
		if (input == 'Quit' || input == 'Q'){
			break;
		} else{
			codingAgent.run(input);
		}
	}
}
```

---
### Orchestrator Pattern
#### Role or Orchestrator
- Break down of the custom prompt/ question into a detailed research plan with subtopics, so that they can be delegated to custom sub-workers
	- Hence the `System prompt` + LLM invocation of the Orchestrator should enforce that the output is a research plan with detailed steps
	- Orchestrator never knows about the subworkers present at its disposal (If we need a intelligent routing of subtasks to specialized worker agents), then we can add another step