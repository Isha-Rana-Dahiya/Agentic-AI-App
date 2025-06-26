# Agentic-AI-App
Use case of ecommerce website is - 'Seller Onboarding & Product Management Assistant'
Agents:
- FormFillerAgent: Helps new sellers input structured product info via conversation.
- ValidatorAgent: Uses GPT to rephrase and clean descriptions.
- ProductCRUDManager: Interfaces with Cosmos DB for inserts, updates, deletes.
Scenario: A small business can upload a product by simply chatting: “I want to sell organic cotton t-shirts for $25 in the eco-friendly category.”

Create an app in **python**, Use Agentic frameworks available in **Semantic Kernel**, use 3 agents in your use-case. **Create Cosmos DB** in the backend, identify the collections and schema; properties that will be vectorized. Create **Azure OpenAI endpoint** service to be able to pass prompts and create embeddings.
Next code up the application, to create the use case to light, making CRUD operations on the database. Use Vector Search capabilities (full text search and hybrid search are a bonus). Ensure you are passing context, memory in your natural language conversations. Use tech stack Azure Cosmos DB, Azure Open AI, Azure AI Agents.
