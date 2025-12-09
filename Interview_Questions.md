# AI Engineer Interview Questions

## Multi-Agent Systems

### Q. How is a multi agent better than a single agent or an LLM used directly?

**A.** If we use single agent it has a lot of data and access to tools like web search, databases, etc. In multi agent systems, the good thing is each agent has a specific task to do and wont go out side of it, so the success of getting the right answer is higher. We write separate prompts for each agent and can change the LLM to save cost based on the task of that agent.

### Q. How would you manage the context from one agent to the next agent and so on?

**A.** This is a classic problem where our context window will be smaller and our agent will not know what was asked at the start called as Context Drift. We cannot pass the entire history as context window will overflow. This takes us to state management. What we can do is keep two states one that is always passed from one agent to the next, so as to not lose sight of the original query and then the temporary state which changes according to new information getting added as it moves agent to agent ie. global context and local context.
Context/Prompt injection is another method where the agent instructions are updated or constructed at runtime like original query, information summarized so far, and task of that agent.

### Q. Lets say there are lot of users accessing your app, how would you design and handle that?

**A.** Once a question is asked you do not want to wait and keep the http connnection open till you get the response from the LLM. This is bad practice since we have so many users at a time. The app must be asynchronous. Instead our approach changes now where a request is made with a query then it gets run_id and the load is sent to service like Redis or SQS or Celery. A pub/sub approach helps here. If we have to make sure that a complex query asked by you shouldnt block others users simple query. Having separate queues can help here. Always save what has been in the state like a checkpoint. Few steps performed like tools called, summary done then save this as a checkpoint and move forward.

## Vector Databases & RAG

### Q. What type of approach helps in searching through the vector database based on the query?

**A.** Vector database stores embeddings so we perform cosine similarity and return the more relevant chunks and answer the user query. We can perform reranking so that we get true relevance. A hybrid approach in production systems tend to work better with sparse and dense vector embeddings.

### Q. When you have a lot of data, would you store everything directly in the vector database?

**A.** That approach won't help in getting the right answer or would give wrong answer. Vector databases like Qdrant has metadata filtering, indexing and more to quickly get the data that is relevant. So every vector can have a specific metadata associated with it. You modify filtering to HNSW while storing and searching of the data in vector database.

## Cost Optimization

### Q. Gives ways to save costs in a multi agent system?

**A.** Semantic caching is a way where we can save money and time to get the response. Few queries might be very similar and the answer to it rarely changes like "How do I change my password". For such queries, you can semantic caching, since every user wont ask the same question the exact same way, we understand the intent and then check our cache like Redis for the answer. If it is above a threshold we answer it from the cache and never the trigger our multi agent system and save the time on the cost of LLM.

## Error Handling & Edge Cases

### Q. How will you handle a scenario where the user interrupts the agent halfway?

**A.** In multi agent systems or the path we are taking must be updated dynamically in case of interruption i.e. we update the state and move to different direction in the graph. We should not kill the process and start over, that is not a good approach. Maybe use a cheap LLM to redirect the context to new path. This is Context Relevance Check.

### Q. How to handle infinite loops?

**A.** So it can happen that agent A and agent B or an agent with a tool go in a loop as it is not able to understand if the context is good enough to move forward. This would keep on using LLMs and cost you money. Add recursion limits like 3 retries or max steps allowed for agents and LLMs. If it is tending to loop then we redirect to an exit path.

## Evaluation & Testing

### Q.  What are different types of evaluators for your AI agents? How will you evaluate the peroformane of your AI agent?

**A.** We can use LLM-as-a-judge, Heuristics meaning we can hard code the truth and lastly human in the loop. All of these evaluators can be used in different way like LLM-as-a-judge would be used to compare the generated answer with the ground truth if we have defined that or compare it with a pair of generations called the pairwise evaluations and take the best of it.

### Q. What are different types of RAG evaluations?

**A.** We can evaluate answers from RAG either with a reference answer that we created meaning the answer given by LLM is it the ground truth answer or not. Another way would be check for hallucnation as in is the answer present in the retrieved documents or not. Further, we can check if retreived documents itself are related to the user query or not.

### Q. How would you decide that changing the LLM for your use case would give better or worse answers?

**A.** We can perform regression testing across various models and use LLM as a judge to evaluate it based on a base model as reference that tells us whether it better or worse.

### Q. Agents and tool evaluation ?

**A.** Evaluate the final response that you get from the LLM after running the tool. Another way would be to check if the correct tool was called or not.
And lastly check the entire trajectory that when a user asked a query, did it follow the path it should to give you the answer based on the input query.

Q. 