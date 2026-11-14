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
	- Each of the model basically contains a perdefined map where tokens are mapped to individual vectors
	- This map is built during the training process/ fine-tuning process, where each vector mapped to a token has a semantic meaning
	- These vectors are sometimes known as embeddings.
	- So when a model sees a new prompt, then the tokenizer basically splits them based on the token vocabulary the model uses and then for each of the token, its mapped to its embedding
- Step 1: Positional encoding
	- Within the transformer, the contextual meaning of prompt is basically due to 2 main reasons:
		- Each token meaning depends on each other token in the prompt
		- The position of the token within the prompt
	- Since the architecture of the Transformers is different than the RNN, where RNN ensures that the current token contextual meaning depends on the previous tokens, where as the Self/ Multi attention blocks dont work in this manner, we need some way to embed the positional meaning, and this is solved by the Positional Encoding
	- There are multiple algorithms to inject the position into the embeddings before the self attention blocks

> Q1.3 **—** What Happens Inside One Transformer Block? - Imagine tracing the representation of a single token through a typical transformer block. Explain the distinct roles of attention, the feed-forward network, residual connections, and normalization. What does each contribute to processing the token?

Ans:
- Transformers are categorized into 3 separate families i.e., Encoder only, Decoder only and Encoder-and-Decoder
- So, essenti