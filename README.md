# Agentic-AI-App
Use Case: AI Shopping Assistant for Product Discovery

Scenario: A user chats with an AI assistant to:
- Discover products based on natural language queries
- Get personalized recommendations
- Ask follow-up questions with memory/context
- Perform CRUD operations on product catalog

We will have three Azure AI Agents:
- SearchAgent – Handles semantic product search using vector embeddings.
- RecommendationAgent – Suggests similar or trending products.
- CatalogAgent – Manages CRUD operations on the product catalog in Cosmos DB.

Each agent is defined in Azure AI Foundry and orchestrated using Semantic Kernel.


Create an app in your framework of choice (lang python)
Use Agentic frameworks available in Semantic Kernel, Langchain etc. and use 3 agents in your use-case
Create Cosmos DB in the backend, identify the collections and schema; properties that will be vectorized
Create Azure OpenAI endpoint service to be able to pass prompts and create embeddings
Next code up the application, to create the use case to light, making CRUD operations on the database
Use Vector Search capabilities (full text search and hybrid search are a bonus)
Ensure you are passing context, memory in your natural language conversations
