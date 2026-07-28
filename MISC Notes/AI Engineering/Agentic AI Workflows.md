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