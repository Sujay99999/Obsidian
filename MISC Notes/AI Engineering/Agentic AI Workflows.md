### Usage of Tools within the Sync calls or Async streaming

- Tools is basically "Function calls" from the LLM during the conversation, which are basically the steps the LLM can execute on its own such as "Web search" or "Send email"
- There are basically 3 types of tools 
	- User defined Client Tools
		- We define the input schema, the function logic and the end results
		- We also take care of the tool use loop mechanism
	- Anthropic trained Client tools
		- Some tools such as "Bash commands, fs, etc" are tools which have their input schema pre-trained, but the function logic is written by us.
		- We also take care of the tool use loop mechanism
	- Server Tools
		- These are purely anthropic LLM driven tools such as `web_search`, etc where the loop and result feed are driven by the LLM, no human intervention

### Tool usage within the Agent Loop
- Both the User defined and the Anthropic defined tools requires the user to drive the loop
- The loop is
```
	res = req1(msgs, tools);
	while (res.stop_reason == "tool_use"){
		tool_res = tool_invoke(res.tool_use);
		msgs += tool_res;
		res = req(msgs, tools);
	}

```

```
public class CodingAgent {

	public Tool[] toolList;
	public List<MessageParam> messages;
	
	public Tool[] setupTools(){
		Tool tool1 = new Tool.builder()...;
		toolList.add(tool1);
		return toolList;
	}
	public CodingAgent(){
		// Setup new LLM Client with paramters and system prompt
		AnthropicClient client = AnthropicOkHttpClient.fromEnv();
		
		// Setup tools
		setupTools();
	}
	
	public void run (String userInput){
		
		
		// Create message using MessageBuilder and append to Msgs
		messages.append({role: USER, content: userInput});
		
		// Invoke the **Single** invocation of LLM
		Message res = client.messages().create(MessageCreateParams.builder()
		.model()
		.addTools(toolList)
		.toolChoice({disableParallelToolUse: true})
		.messages(messages)
		.build());
		
		// If response = TOOL_USE, perform the tool call and append the messages array and call the LLM again
		// Else
			// Print and exit
		while(res.stopReason().isPresent() && res.stopReason().get().equals(StopReason.TOOL_USE)){
			// Get the tool to be invoked
			ToolUseBlock toolUse = res... 
			
			// Invoke the tool
			String toolInvocationRes = runTool(ToolUse);
			
			// Append the messages
			messages.append({
				role: USER, 
				contentBlockParam:{
					id: toolUse.id, 
					content: toolInvocationRes
					}
				}
			);
			
			// Invoke the **Single** invocation of LLM again
			Message res = client.messages().create(MessageCreateParams.builder()
			.model()
			.addTools(toolList)
			.toolChoice({disableParallelToolUse: true})
			.messages(messages)
			.build());
			
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