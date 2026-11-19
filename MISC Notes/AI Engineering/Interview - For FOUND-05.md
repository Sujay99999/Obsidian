
>**F05-Q01 — Attention scores vs contextual information** - Consider the sentence "The animal didn't cross the road because it was tired." Explain how the representation of "it" can gather relevant information from other tokens through self-attention. Distinguish the role of attention scores, attention weights, and value vectors in this process.

Ans:
- So, the input sequence initially passes through the  tokenizer, splitting the sequence based on the tokenizer's vocabulary converting them into the token IDs and then into the vector embeddings
- Once these embeddings are created, we create 3 vectors for each embedding i.e., `Q, K and V` . For each attention head within the transformer block, each has its own set of `W^q, W^k and W^v` which represent a linguistic relation between the tokens
- For the example, we can assume that a particular attention head is calculating the attention  about "If token is a pronoun, what does it refer to"; Then in that case, the query vector of "it" token asks this query, and the Key vector from the "Animal" appears to "Attend more", than compared to other vectors. The above linguistic relation is a example for us to understand, but LLMs have learned to capture relations.
- The "Attend to", basically represents the Dot product of the Query and Key vector; Higher the dot product, the better the attention; These values are also known as "Attention Scores"
- Once these Attention scores are calculated, they undergo Scaling (i.e., Get divided by a constant, which prevents large values), and also undergo a "Normalization process" called as "Softmax", which ensures that the scores are converted to such a way that their sum adds up to 1
- The end product of the Softmax operation is called "Attention weights".
- After the weights are calculated, we have to also ensure that the these weights are somehow added to the original embedding which represent the injection of the contextual meaning into the vector, and this is where we "Multiply the weights with Value vector and then add them up"; This is known as "Delta" which is then added to embedding and this is called "Attention Output"

>**F05-Q02 — Multi-head attention and its limitations** - A transformer uses eight attention heads instead of one. Does this guarantee that each head learns a different linguistic relationship? Explain what multiple heads make possible, how their outputs contribute to the overall representation, and one limitation of interpreting individual heads.

Ans:
- So, there are two questions here
	- Does Multi head guarantee that each head learns a different linguistic relationship?
		- Yes, each individual head has its own set of parameters i.e., `W^Q , W^K, W^K` which capture a variation of the linguistic relationship (Not a guarantee)
		- This when couples with the vectors which are constant for all the attention heads produce varying results also known as Attention patterns i.e., Query, Key and Value vectors.
		- This variation in the results is said to capture the different attentions between the tokens
	- How their outputs contribute to the overall representation, and one limitation of interpreting individual heads
		- Within the single head of attention, we only have One Delta calculated and this gets added back to the embedding to ingest the attention
		- But in the case of Multi head attention, we have different deltas captured from different blocks and all of them are concatenated and then added back; And this is said to capture the overall representation than compared to single head

>**F05-Q03 — Causal masking and information leakage** - A decoder-only model is trained on the sequence "The cat sat on the mat." Explain why allowing the representation at "cat" to attend to later tokens would cause problems for next-token training. Describe at a conceptual level where masking is applied and how it prevents that information leakage.

Ans:
- The primary purpose of the Decoder is to "Generate text" from the given context. So, during the inference process, the expectation is that "With the given set of input sequence tokens, it should be able to provide the probability distribution of the expected words and then choose one based on the token selection strategy"
- So to achieve this, the transformer undergoes the "Training process" using the Casual Masking process, where for the input sequence of "The cat sat on the mat.", it is trained in parallel, where we mask the tokens on the right.
- So, we mask the sentence as "The cat sat" and ask the model to generate the next token. Since we know the expected token, we can then tune the parameters based on the result from the transformer. This is what happens behind the scenes, but we generally use a optimal way to perform this using Triangular attention mask
- The reason we mask the future tokens is to ensure that the model doesnt compute the attention weights from the  future tokens which can impact the results

>**F05-Q04 — Attention limitations and long sequences** - A transformer is asked to process a document containing 50,000 tokens instead of 500. What practical challenges arise when using standard full self-attention on this much longer input? Explain the main computation and memory trade-offs, and why supporting a long context window does not automatically guarantee that the model will use all the information effectively.

Ans:
- There are 2 questions
	- What practical challenges arise when using standard full self-attention on this much longer input?
		- Expensive Computation
			- We know that for a input sequence containing `n` tokens where each token corresponds to `m` dimensions, we compute to get Attention weights, which are essentially matrix multiplications where each token attends to every other.
			- So we compute `nxn` values, which means it "Scales quadratically" 
			- This is very computationally expensive for 50k vs 500 tokens
		- Memory Tradeoffs
			- With the same logic as above, we end up storing `nxn` values which uses more memory 
	- Why supporting a long context window does not automatically guarantee that the model will use all the information effectively.
		- Attention capability of the Model doesn't guarantee Attention effectiveness
		- Lost in the middle effect
			- Long-context models can perform differently depending on where relevant information appears.
			- For example, a model might successfully retrieve information near the beginning or end of a document but struggle with information buried in the middle.

>**F05-Q05 — Attention masks across model architectures** - Consider three situations: a BERT-like encoder reading an entire sentence, a GPT-like decoder generating text, and an encoder–decoder translation model. Compare which token positions are allowed to attend to which others in each situation. Explain how the attention pattern supports each model's objective.

Ans: `SKIP_OVERLAP`