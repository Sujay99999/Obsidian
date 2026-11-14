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
- When we have 2 same tokens with the same positional encoding passed onto the Self attention block, then each token embedding undergoes a set of operations which can be described in 2 steps
	- Capture the contextual meaning of that embedding using the other embeddings
		- This happens with the help of 2 vectors called Queries and Keys. These are 2 vectors which are produced for each token embedding.
		- For a particular embedding, the query vector basically "Checks for the relevance of the particular token wrt other within a specific context"; One of the example can be "How this token (if it is an adjective) is impacting the meaning of other token (which is expected to be a noun)".
		- For the same embedding, we also generate the Key vector which typically answers the above query. 
		- The dot product of the query and the key vector depict the relevance of a specific token with other within a context, and this is called as "Attend to"
		- So, in the given example, let us assume that the Context we are seeking is "How are these tokens related to infrastructure", which is essentially dictates on how the query vector is produced. The key vector essentially provides the answer for the same query
		- So, for the Query vector asking "Which vector is more closely related to infrastructure, when checked for the specific vector `steel`", then ideally we have the key vector of `steel` and `crane` aligning more closely to it, which has higher dot product
	- Update the current embedding with the above result to reflect the contextual shift
		- Since the query and key vector dot product basically capture the contextual meaning, we also have to ensure that this has to be injected into the original embedding
		- To perform this, we have a Value matrix generated for each of the embedding and then multiplied with the above dot product. This is essentially the "Delta" which we need to add back to the original embedding
		- In the above example, we understood that `crane` is related to `steel` and `beam` when the context is infrastructure, but we havent modified the original embedding mapped to `crane`, and the delta produced is added, which essentially captures that semantic closeness.

>**Q2.2** **—** **Queries, Keys, and Values** - Explain the conceptual roles of Query, Key, and Value vectors in self-attention. Describe the process by which a token uses them to gather information from other tokens. You don't need to derive the attention equation.

Ans:
- Query, Key and Value vectors are the 3 vectors which helps one individual token to capture the contextual meaning from the other token embeddings.
- Simply put, 
	- Query vector essentially captures - "It represents what the current token is asking when checked for relevance with other tokens". It can simply be like - "Am I (token 1) supposed to act as adjective for another token (token 2)"
	- Key vector essentially captures - "What as a token I (token 2) represent when I am asked that question"
	- Value vector essentially captures - "Okay, what is the actual contextual meaning that has to be passed on"

>**Q2.3** **—** **Why Multiple Attention Heads?** - Why do transformers use multi-head attention rather than relying on one attention operation? What additional capability does this provide, and what should we avoid assuming about the role of each head?

Ans:
- Single attention operation works with the embedding vector with single set of Query, Key and Value matrices. This single attention works on single "View" of the context, where as the Multi head attention basically tries to work on different views of the context using different sets of 3 matrices
- Within Multi head attention, we can ensure that each head specialize with a specific context and averaging out rather than relying on generalistic context based single head attention
- Multi head attention blocks can be parallelized as each head computation is independent of the other