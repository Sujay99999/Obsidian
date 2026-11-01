- What are vectors and vector embeddings
	- What is vector embedding
	- Why the Raw data which has similar meaning are converted as Nearby Vectors
	- Why do Embedding vectors have same length
	- What are dimensions for a vector and what is the significance of dimensions
- How Raw data is converted to Vectors/ Embeddings
	- Explain the process of converting the Raw text into a Vectors
	- Explain how Chunk size, Metadata impact the embeddings
- What is a Vector database
	- What is a vector database
	- What is Vector index and how is connected to Vector database
	- How does the Vector database perform Semantic search which is the core feature
		- What is Semantic search exactly
		- Advantages and Disadvantages of Semantic search over Keyword search
		- ANN
			- What is ANN
			- What ANN trades over the Exact search
			- What is Recall in ANN 
			- What is Flat search and how does it 100% recall
			- What are the different families/ indexes of ANN
				- Graph Based Indexes
					- How does HNSW work on a high level
				- IVF based/ Cluster based Indexes
					- How does IVF work
				- Quantization and Compression
	- How the vector database is different from the Traditional database
	- What is Hybrid search and why is preferred over classic semantic search

```
python examples/basic_inference.py --weights-dir ./weights --person-image examples/data/model/straight.jpg --garment-image examples/data/standalone/1.jpg --category tops --garment-photo-type flat-lay --device mps --num-timesteps 30 --seed 42 --guidance-scale 1.0 --output-dir experiments/m5/guidance/1.0 python examples/basic_inference.py --weights-dir ./weights --person-image examples/data/model/straight.jpg --garment-image examples/data/standalone/1.jpg --category tops --garment-photo-type flat-lay --device mps --num-timesteps 30 --seed 42 --guidance-scale 1.5 --output-dir experiments/m5/guidance/1.5 python examples/basic_inference.py --weights-dir ./weights --person-image examples/data/model/straight.jpg --garment-image examples/data/standalone/1.jpg --category tops --garment-photo-type flat-lay --device mps --num-timesteps 30 --seed 42 --guidance-scale 2.0 --output-dir experiments/m5/guidance/2.0
```