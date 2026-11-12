> Q1.1 — Why Transformers? - What limitations of earlier sequential architectures such as RNNs motivated Transformers? Explain what fundamentally changed in how sequence information can be processed, and why that matters.

Ans :
- All the models under the RNN family (RNN, LSTM and GRUs) are designed in such a way that the inference process is sequential in nature i.e., the current token generation depends on the "Current Hidden state vector" + "Previous Token", which means that during the text generation, this neural network should process the existing tokens before the current one
- This sequential process of inference and training, made it very hard to achieve parallelism, costing us more time; This coupled with the Vanishing gradient issue of the hidden state vector made it hard for the long complex data
- Both of these issues have led to rise in transformers, which operate in a fundamentally different manner i.e., It uses a concept of "Self attention", which essentially tries to compute the meaning of the current word/ token from all the surrounding tokens to capture the contextual meaning of that particular token
- In this architecture, we basically compute each token pair similar to matrix multiplication, allowing us to parallelize the entire process i.e., computation for the current token takes in the current state of all other tokens
- One of the biggest advantages of transformers over RNNs is that we are able to parallelize the process which reduces the compute time (but with the cost of high memory) and at the same time provide better contextual meaning for long complex sentences.
  
  
>Q1.2 — From Text to Model Input - Suppose a user enters the prompt “The crane damaged its wings.” Walk through what happens to this text before it enters the first transformer layer. Explain tokenization, token embeddings, and positional information, and why each is needed.

Ans:
- Okay, there are many pre requisite steps within the "Self attention/ Multi Head attention blocks" of the transformer. 
- Step 0: Convert the Plain text into Tokens using the Tokenizer
	- What is Tokenizer: 