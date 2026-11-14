> Q1.1 — Why Transformers? - What limitations of earlier sequential architectures such as RNNs motivated Transformers? Explain what fundamentally changed in how sequence information can be processed, and why that matters.

Ans :
- All the models under the RNN family (RNN, LSTM and GRUs) are designed in such a way that the inference process is sequential in nature i.e., the current token generation depends on the "Current Hidden state vector" + "Previous Token", which means that during the text generation, this neural network should process the existing tokens before the current one
- This sequential process of inference and training, made it very hard to achieve parallelism, costing us more time; This coupled with the Vanishing gradient issue of the hidden state vector made it hard for the long complex data
- Both of these issues have led to rise in transformers, which operate in a fundamentally different manner i.e., It uses a concept of "Self attention", which essentially tries to compute the meaning of the current word/ token from all the surrounding tokens to capture the contextual meaning of that particular token
- In this architecture, we basically compute each token pair similar to matrix multiplication, allowing us to parallelize the entire process i.e., computation for the current token takes in the current state of all other tokens
- One of the biggest advantages of transformers over RNNs is that we are able to parallelize the process which reduces the compute time (but with the cost of high memory) and at the same time provide better contextual meaning for long complex sentences.
  
  
>Q1.2 — From Text to Model Input - Suppose a user enters the prompt “The crane damaged its wings.” Walk through what happens to this text before it enters the first transformer layer. Explain tokenization, token embeddings, and positional information, and why each is needed.

Ans:
- Okay, there are many pre requisite steps within the "Multi Head Self attention blocks" of the transformer. 
- Step 0: Convert the Plain text into Tokens using the Tokenizer
	- Each of the model basically contains a predefined map where tokens are mapped to individual vectors
	- This map is built during the training process/ fine-tuning process, where each vector mapped to a token has a semantic meaning
	- These vectors are sometimes known as embeddings.
	- So when a model sees a new prompt, then the tokenizer basically splits them based on the token vocabulary the model uses and then for each of the token, its mapped to its embedding
- Step 1: Positional encoding
	- Within the transformer, the contextual meaning of prompt is basically due to 2 main reasons:
		- Each token meaning depends on each other token in the prompt
		- The position of the token within the prompt
	- Since the architecture of the Transformers is different than the RNN, where RNN ensures that the current token contextual meaning depends on the previous tokens, where as the Self attention blocks dont work in this manner, we need some way to embed the positional meaning, and this is solved by the Positional Encoding
	- There are multiple algorithms to inject the position into the embeddings before the self attention blocks

> Q1.3 **—** What Happens Inside One Transformer Block? - Imagine tracing the representation of a single token through a typical transformer block. Explain the distinct roles of attention, the feed-forward network, residual connections, and normalization. What does each contribute to processing the token?

Ans:
- Transformers are categorized into 3 separate families i.e., Encoder only, Decoder only and Encoder-and-Decoder
- So, essentially we chain multiple Encoder based transformer blocks together to form encoder stack and similarly for the Decoder stack
- And if we dissect an individual transformer block, we will find a chain of Multi-head Self attention layer, MLP/Feed-Forward layers, Residual connections and Normalizing layers and this is how a individual token would pass through all the layers
	- Step 1: Tokenization + Positional Encoding
		- Token passes through Tokenizer, where it gets converted into Vector Embedding based on the vocabulary set of the Model
		- These embeddings are then injected with the contextual meaning related to position of that specific token within the sentence using Positional Encoding
	- Step 2: Multi-Head Self Attention
		- All these vector embeddings are then passed onto the Self Attention block, which can be either Single head or Multi head attention layers
		- Within each of the layer, the individual embeddings are multiplied with 3 different matrices also known as Internal weights of the model (These predefined matrices are developed within the training process of the model), to produce 3 vectors called Query, Key and Value vectors
		- After a sequence of operations, each individual embedding undergoes transformation, which essentially captures the contextual meaning of the token when referred with the remaining tokens
	- Step 3: Residual connections
		- Once we have transformed vector, we add the original embedding to it, and this process is called "Residual connection", which essentially ensures 
			- To preserve the original meaning from the initial token embedding 
			- To ensure the gradients flow through multiple blocks of the transformer layers
	- Step 4: Normalization
		- Since these linear operations for the vectors, which are essentially dot products and linear additions can create a lot of variation in the values
		- So, they undergo a process of Normalization to ensure that all the values are within a certain range of values only.
	- Step 5: Feed Forward network
		- Once normalized, each of the vector undergoes a set of individual computation i.e., doesnt depend on the other tokens unlike the self attention block, where it grabs the overall meaning for the individual token.
		- The FFN is required because it enables each transformed token which has the context aware representation to convert into a new representation that *maybe* useful for the next computation
	- Step 6: Residual Connections
		- This is the same process as Step 3
	- Step 7: Normalization
		- This is the same process as Step 4


>  **Q2.1** **—** **Contextual Meaning Through Attention** - Consider the two sentences: “The crane damaged its wings” and “The crane lifted a steel beam.” Explain how self-attention can help the representation of “crane” reflect different meanings in these contexts. What changes through contextual processing?

Ans:
- 