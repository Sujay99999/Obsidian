
New Links:
- https://medium.com/@xiaxiami/rnn-vs-transformer-a-deep-dive-from-fundamentals-to-applications-ee4d700dd152
- https://www.geeksforgeeks.org/deep-learning/rnn-vs-lstm-vs-gru-vs-transformers/
- https://medium.com/@karthikmulugu/why-transformers-are-better-than-rnns-a-practical-deep-dive-1a789ee67adb
- https://medium.com/@sonalprasad.sp/transformer-models-part-2-transformer-block-transformer-architecture-dbc20408aaed
- https://www.youtube.com/watch?v=ZwgNiyJOABI&t=151s
- https://rahulrajpvr7d.medium.com/what-are-the-query-key-and-value-vectors-5656b8ca5fa0
- https://magazine.sebastianraschka.com/p/understanding-and-coding-self-attention
- https://sebastianraschka.com/blog/2023/self-attention-from-scratch.html
- https://medium.com/@xiaxiami/self-attention-vs-cross-attention-from-fundamentals-to-applications-4b065285f3f8


> Q1.1 — Why Transformers? - What limitations of earlier sequential architectures such as RNNs motivated Transformers? Explain what fundamentally changed in how sequence information can be processed, and why that matters.

Ans :
- All the models under the RNN family (RNN, LSTM and GRUs) are designed in such a way that the inference process is sequential in nature i.e., the current token generation depends on the "Current Hidden state vector" + "Previous Token", which means that during the text generation, this neural network should process the existing tokens before the current one
- This sequential process of inference and training, made it very hard to achieve parallelism, costing us more time; 
- This is also coupled with the Vanishing gradient issue of the hidden state vector only for the Vanilla RNNs made it hard for the long complex data; This issue isnt present within the LSTM and GRUs.
- Both of these issues have led to rise in transformers, which operate in a fundamentally different manner i.e., It uses a concept of "Self attention", which essentially tries to compute the meaning of the current word/ token from all the surrounding tokens to capture the contextual meaning of that particular token
- In this architecture, the training process which is more exhaustive can be parallelized unlike the RNNs, but the text generation is still sequential in nature.
- One of the biggest advantages of transformers over RNNs is that we are able to parallelize the process which reduces the compute time (but with the cost of high memory) and at the same time provide better contextual meaning for long complex sentences.
  
  
>Q1.2 — From Text to Model Input - Suppose a user enters the prompt “The crane damaged its wings.” Walk through what happens to this text before it enters the first transformer layer. Explain tokenization, token embeddings, and positional information, and why each is needed.

Ans:
- Okay, there are many pre requisite steps within the "Multi Head Self attention blocks" of the transformer. 
- Step 0: Convert the Plain text into Tokens using the Tokenizer
	- Each of the model basically contains a predefined map where tokens are mapped to individual token IDs
	- This map is built during the training process/ fine-tuning process, where each ID mapped to a token has a semantic meaning, and this vocabulary is built specific to the tokenizer
	- Then, these token IDs are mapped to vectors within the model. These vectors are sometimes known as embeddings.
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
		- So, the final attention weights which are finally created are essentially `Weighted sum of the Value vectors`, where the weights come from the `Q.K` dot product.
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
- Within Multi head attention, we can ensure that each head focuses on a specific relationship and then they are concatenated and transformed out rather than relying on generalistic context based single head attention
- Multi head attention blocks can be parallelized as each head computation is independent of the other

>**Q2.4** **—** **Causal Masking and Future Information** - A GPT-like model processes a training sequence containing several tokens. Why must it prevent an earlier position from attending to later positions? Explain how causal masking enforces this and why it matters for next-token prediction.

Ans:
- Since GPT model's purpose is to generate the most probable token with the set of tokens which are positioned before the current token i.e.,  the current token must not be influenced by the future tokens
- But the original implementation of the Self attention block, takes in the input from all the surrounding tokens, must be modified in such a way that, the `attention` from the guture tokens are zero for all its proceeding tokens
- Once the attention scores are calculated, we apply the softmax operation i.e., Normalization converting them to attention weights, and then we mask the attention weight matrix by converting all the cells where the row index > column index, and then we normalize again to ensure they are ready for the next attention block
- Better way to perform the same operation is to mask the cells by replacing them as `-inf` instead of zero and then apply softmax only once

>**Q3.1** **—** **Three Transformer Families** - Compare encoder-only, decoder-only, and encoder–decoder transformers. Explain how their information flow differs and give one task where each architecture is particularly appropriate.

Ans:
- Encoder Only
	- The transformer block only consists of the Encoder block, which generally excels at "Understanding the contextual meaning of the input"
	- Some of the use cases are "Text classification", "Token Classification"
	- Within the component, each token can access all the other tokens within the sequence to generate the attention weights, hence it is bidirectional
	- One of the available models is "BERT", which  is pretrained with two objectives: masked language modeling and next-sentence prediction
- Decoder Only
	- The transformer block only consists of the Decoder block, which generally excels at "Generating the next token based on the processed tokens"
	- Some of the use cases are "Text generation"
	- Within the component, each token can only access the previous tokens within the sequence to generate the attention weights, hence it is unidirectional
	- One of the available models is "GPT", which belongs to "Casual Language Modelling"
- Encoder and Decoder
	- The transformer block consists both the Encoder and Decoder block, which generally excels at "Sequence to Sequence tasks"
	- Some of the use cases are "Translation, where we have understand the essence of the input text and then generate the corresponding text", "Summarization, where we have understand which parts of the text have to preserved"
	- Within the component, each token can onty access the previous tokens within the sequence to generate the attention weights, hence it is unidirectional

> **Q3.2** **—** **Self-Attention vs Cross-Attention** - In an encoder–decoder model performing translation, how does information from the source sentence influence the decoder as it produces the target sentence? Distinguish the role of decoder self-attention from cross-attention.

Ans:
- There are 2 separate questions here
	- How does information from the source sentence influence the decoder as it produces the target sentence
		- Within the Decoder block of "Encoder-Decoder" using the Cross attention, its purpose is to generate the next token, but the attention has to come from the Input text within the Sequence to Sequence model, else it would be a simple text generation
		- This is possible when the Decoder block uses the Keys and Value matrices from the Source Sentence encodings and Query matrices from the decoder while calculating the Attention weights instead of using them from the current sequence which is "Already generated tokens for the decoder" 
	- Distinguish the role of decoder self-attention from cross-attention.
		- If the transformer uses a single input of sequence (i.e., Not a Sequence-to-Sequence), then Self-attention would suffice.
		- But when we have the requirement of Translation or Summarization, then the output sequence must depend on the entire input sequence and this is possible with the cross attention


> **Q3.3** **—** **Selecting the Right Architecture** - You are building two services: one classifies incoming customer messages into categories, and the other generates natural-language replies. Would you choose the same transformer architecture for both? Compare your options and explain the trade-offs without assuming there is only one valid solution.

Ans:
- Service 1: 
	- Requirement: Classification of the Incoming message into buckets
	- Best suited Transformer family: Encoder Only
		- Classic use case of "Zero shot Text classification based on the Pre-defined Labels"
- Service 2: 
	- Requirement: Generation of replies from the given context
	- Best suited Transformer family: Decoder Only
		- Classic use case of "Text Generation"


> **Q4.1** **—** **From Hidden States to Next-Token Selection** - After a prompt has passed through the transformer's layers, how does a decoder-only language model produce its next token? Explain the relationship between the final representation, vocabulary logits, token probabilities, and the selection of an output token.

Ans: `SKIP_DEPTH`

> **Q4.2** **—** **Autoregressive Generation** - Suppose a model generates a response containing five new tokens. Explain what happens after the first token is selected, how each subsequent token depends on the growing context, and why generation proceeds incrementally rather than producing all five tokens independently.

Ans: `SKIP_DEPTH`

>**Q4.3** **—** **Why Outputs Can Differ** - You send the same prompt twice to a language model and receive different responses. How can that happen if the model parameters and prompt are unchanged? Distinguish the model's predicted scores from the process used to select tokens.

Ans:
- Yes, in the case where the Tokenizer, Model parameters are unchanged, the probability distribution calculations will be same.
- The difference in the outputs is generally dependent on the Token selection strategy i.e., Greedy Decoding or Sampling


> **Q4.4** **—** **Complete Inference Walkthrough** - A user asks a decoder-only LLM: “Explain why the sky is blue.” Starting with the raw prompt and ending with the generated answer, walk through the complete inference path. Connect the important transformer components in their logical order and explain what repeats as the answer is produced.

Ans: `SKIP_OVERLAP`


> **FU-01** **—** **Training vs inference parallelism**. - Suppose a decoder-only transformer is given a 100-token prompt and must generate 20 more tokens. Which computations over the existing prompt can be parallelized, and which operations during generation still have sequential dependencies? Explain without discussing implementation details.

Ans:
- For a Decoder only model (Autoregressive model), the parallelism vs sequential ordering is applied differently at different phases.
- Decoder only model which is generally trained for the text generation, use Casual Language model, where the current token is only dependent on the previous token from the sequence
	- Parallelism during Training
		- Let us take a sentence `Capital of India is Delhi`; During the training, we make the model to generate the next token for different configurations of the same input sequence
		- Since all the tokens are already known, we know the actual expected token from the input sequence, we can simply spilt the training process which can be parallelized
	- Sequential during Inference
		- During the inference, since we don't know the expected future token, we have to compute the current token for continuation.
		- That dependency forces standard autoregressive generation to proceed sequentially.
- Parallelism within the Attention heads
	- Irrespective of the transformer family, we have the attention heads, and this is always computed in parallel
- So for a decoder-only transformer is given a 100-token prompt, which is the training process can be computed in parallel, but the inference requirement to generate 20 more tokens is sequential.

 
 >**FU-02** **—** **Attention and FFN information flow** - Two tokens are processed through a transformer block. Which component lets one token gather information from the other? Which component then transforms each token independently? Why are both useful?
 
Ans:
- Within the transformer block, we have 2 major components - Attention heads and Feed-Forward network blocks
- Within the attention heads, we calculate the `Attention Weights`, where for individual token, we gather the contextual information from the other tokens to capture the contextual meaning.
- Once each we have contextual information captured, we must ensure to update the current token embedding with the new information, and this is performed within the FFN block
- Within the FFN block, each token embedding undergoes a series of operations and is  processed independently from another, as there is no dependency

 > **FU-03** **—** **Cross-attention and translation** - An encoder–decoder model translates “The cat sleeps” into French. The encoder has already processed the English sentence, and the decoder has generated part of the French translation. Explain where the queries, keys, and values come from during cross-attention, and what information the decoder is trying to obtain.
 
 Ans:
 - In self-attention, all three come from the same sequence. In cross-attention, they come from two different sources i.e., `K` and `V` come from the Encoder and `Q` comes from the Decoder
 - So during the cross attention for the decoder, it tries to query based on a specific context/ asks for specific relation, and this is answered using the Keys and Value matrices from the existing encoded input values.

> **FU-04** **—** **Engineering architecture choice** - You need to classify 10 million short support tickets daily into 15 fixed categories. Compare using a fine-tuned encoder classifier versus prompting a large decoder-only model. Which might you choose and what practical trade-offs would influence your decision?

Ans:
- Final choice of the model - Fine tuned Encoder classifier 
- But what are we looking to compare (or) what is the yardstick here
	- Accuracy - Model has to be accurate to place into the right bucket
	- Inference cost - Cost for classifying one ticket
	- Throughput - How many classifications can be a done in a fixed time window
- Accuracy
	- Due to the bidirectional token read, the encoder has better classification than compared to prompted decoder, but the most important advantage is that Encoder can directly predict one of the 15 classes (one for the category) rather than autoregressively generating a textual answer.
- Inference cost
	- Cost for the Decoder is generally higher than compared to Encoder and a small difference can impact a lot as we are dealing with 10 million tickets

> **FU-05** **—** **Minimal generation-path verification** - A decoder-only LLM has processed “The sky is” and selects “blue” as its next token. In plain language, explain how it chooses “blue” from other possible tokens and what changes when it subsequently generates another token. No formulas, tensor shapes, or implementation details are required

Ans:
- The model processes "The sky is" and calculates how likely each possible next token is based on the context. It might assign the highest probability to "blue", although other tokens such as "clear" or "gray" are also possible. Depending on the decoding strategy, it selects a token and appends it to the sequence.
- The updated sequence, "The sky is blue", becomes the context for the next prediction. The model then calculates a new probability distribution and selects another token. This process repeats one token at a time, which is called autoregressive generation.