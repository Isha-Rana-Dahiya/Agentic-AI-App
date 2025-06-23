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


Task : Create an app using Agentic frameworks available in Semantic Kernel.
Create Cosmos DB in the backend, identify the collections and schema; properties that will be vectorized
Create Azure OpenAI endpoint service to be able to pass prompts and create embeddings
Code up the application, to create the use case to light, making CRUD operations on the database
Use Vector Search capabilities
Ensure you are passing context, memory in your natural language conversations
