# Agentic-AI-App
Use case of ecommerce website is - 'Seller Onboarding & Product Management Assistant'
Agents:
- FormFillerAgent: Helps new sellers input structured product info via conversation.
- ValidatorAgent: Uses GPT to rephrase and clean descriptions.
- ProductCRUDManager: Interfaces with Cosmos DB for inserts, updates, deletes.
Scenario: A small business can upload a product by simply chatting: “I want to sell organic cotton t-shirts for $25 in the eco-friendly category.”

Create an app in **python**, Use Agentic frameworks available in **Semantic Kernel**, use 3 agents in your use-case. **Create Cosmos DB** in the backend, identify the collections and schema; properties that will be vectorized. Create **Azure OpenAI endpoint** service to be able to pass prompts and create embeddings.
Next code up the application, to create the use case to light, making CRUD operations on the database. Use Vector Search capabilities (full text search and hybrid search are a bonus). Ensure you are passing context, memory in your natural language conversations. Use tech stack Azure Cosmos DB, Azure Open AI, Azure AI Agents.

Use Case Overview
Goal: Enable sellers to onboard and manage products via natural language, using agents that interpret, validate, and store structured product information in a secure, vector-search-enabled Cosmos DB backend.

🧠 Step 1: Define Semantic Kernel Agents
We'll use three agents built with Semantic Kernel’s planner and function-based agent model:
- ProductFormFillerAgent
- Extracts structured fields (name, price, category, description) from unstructured input using GPT.
- ContentValidatorAgent
- Rewrites and checks product descriptions, ensuring compliance and consistency.
- ProductDBAgent
- Performs CRUD operations and embeds metadata for vector search in Azure Cosmos DB.
Each agent is registered in the Semantic Kernel with its own skill directory and memory context.

🗂️ Step 2: Cosmos DB Setup (NoSQL + Vector Search)
Database: ecommerce_db
Container: products
Partition key: /category

Document Schema
{
  "id": "product-001",
  "title": "Eco-friendly yoga mat",
  "description": "Non-slip mat made from recycled materials.",
  "category": "Fitness",
  "price": 45.99,
  "embedding": [0.254, -0.129, ...],
  "createdAt": "2025-06-27T10:00:00Z"
}


🔹 Vectorized: title + description
🔹 Use a vector index for semantic + keyword (hybrid) queries
🔹 Use Azure Cosmos DB Mongo API or Azure Vector DB (preview)


🤖 Step 3: Azure OpenAI Setup
- Deploy GPT Model:
- gpt-4, gpt-35-turbo, or gpt-4o for prompt completion and agent reasoning
- Deploy Embedding Model:
- text-embedding-3-small for metadata embeddings
Collect deployment names, API keys, and endpoint URLs for SK config


🐍 Step 4: Python + Semantic Kernel App Architecture
🧰 Install Dependencies
pip install semantic-kernel azure-cosmos openai numpy


🧪 Agent Initialization in Semantic Kernel
from semantic_kernel import Kernel
from semantic_kernel.connectors.openai import OpenAITextCompletion, OpenAIEmbedding

kernel = Kernel()
kernel.add_text_completion_service(OpenAITextCompletion(...))
kernel.add_embedding_service(OpenAIEmbedding(...))


📤 Register Skills per Agent
- ProductFormFillerSkill/parse_product_info.json
- ContentValidatorSkill/validate_description.json
- ProductDBSkill/create_update_product.json

🧬 Step 5: Generate Embeddings + Save to Cosmos DB
def get_embedding(text):
    response = openai.Embedding.create(model="text-embedding-3-small", input=text)
    return response['data'][0]['embedding']


def create_product_document(product):
    embedding = get_embedding(product['title'] + ' ' + product['description'])
    product['embedding'] = embedding
    cosmos_container.upsert_item(product)



🔎 Step 6: Vector Search + Hybrid Query Example
Hybrid retrieval with Cosmos DB Vector Search (preview):
query_vector = get_embedding("natural rubber yoga mat")
results = cosmos_container.search_vectors(
    vector=query_vector,
    filter="category = 'Fitness'",
    top_k=5
)


Add CONTAINS clauses or prefiltering to boost hybrid precision.


🧠 Step 7: Context & Memory Chaining
Use Semantic Kernel memory to retain seller preferences or infer past input:
memory.save_information(
    collection="seller_memory",
    key="preferred_category",
    text="Fitness"
)

Agents read/write memory for grounding interactions (“Add another product in the same category”).

🏗️ Full System Flow
- User Input: “I want to sell bamboo water bottles for $18 in eco-home”
- ProductFormFillerAgent: Parses fields → structured data
- ContentValidatorAgent: Polishes the description
- ProductDBAgent: Embeds content → saves to Cosmos DB
- Buyer query: “Find eco-friendly drinkware” → → Vector Search triggers

✅ Summary Table
| Layer | Service / Tech | 

| NLP + Reasoning | Azure OpenAI + GPT via SK | 

| Agent Framework | Semantic Kernel Agents + Memory | 

| Database | Azure Cosmos DB + Vector Search | 

| Embedding Engine | Azure OpenAI Embeddings | 

| Language | Python (with SK SDK) | 


Would you like a Copilot Page scaffolded with skill templates and Cosmos DB wrappers to jumpstart the codebase? I’d love to help you roll out a working prototype.

## Help me to build a working prototype for the above project
Project Structure
Here's a recommended directory structure:
ecommerce-agent-app/
├── agents/
│   ├── product_form_filler_skill/
│   ├── content_validator_skill/
│   └── product_db_skill/
├── config/
│   └── settings.env
├── services/
│   ├── cosmos_service.py
│   ├── embedding_service.py
├── main.py
├── semantic_kernel_runner.py
├── requirements.txt



📦 Step 1: Install Required Libraries
pip install semantic-kernel azure-cosmos openai python-dotenv numpy


⚙️ Step 2: Configure Your Environment (config/settings.env)
OPENAI_API_KEY=your-key-here
OPENAI_ENDPOINT=https://your-resource.openai.azure.com/
EMBEDDING_DEPLOYMENT=text-embedding-3-small
GPT_DEPLOYMENT=gpt-35-turbo
COSMOS_DB_URI=https://your-db.documents.azure.com:443/
COSMOS_DB_KEY=your-db-key
COSMOS_DB_NAME=ecommerce_db
COSMOS_DB_CONTAINER=products


Then load this with python-dotenv.

🧠 Step 3: Build Core Services
services/cosmos_service.py
from azure.cosmos import CosmosClient, PartitionKey
import os

class CosmosService:
    def __init__(self):
        self.client = CosmosClient(os.getenv("COSMOS_DB_URI"), credential=os.getenv("COSMOS_DB_KEY"))
        db_name = os.getenv("COSMOS_DB_NAME")
        self.container = self.client.get_database_client(db_name).get_container_client(os.getenv("COSMOS_DB_CONTAINER"))

    def upsert_product(self, doc):
        return self.container.upsert_item(doc)


services/embedding_service.py
import openai
import os

openai.api_key = os.getenv("OPENAI_API_KEY")

def get_embedding(text):
    resp = openai.Embedding.create(
        model=os.getenv("EMBEDDING_DEPLOYMENT"),
        input=text
    )
    return resp["data"][0]["embedding"]



🤖 Step 4: Define Agent Plugins (Skills)
agents/product_form_filler_skill/parse_product_info.json
{
  "schema": "1.0",
  "description": "Parse product info from free-form text",
  "parameters": [],
  "template": "Extract the title, price, category, and description from this seller's input: '{{ $input }}'. Return JSON.",
  "input_variables": ["input"]
}


Do the same for content validation and product DB wrapper templates (CRUD logic).


🔗 Step 5: Initialize Semantic Kernel
semantic_kernel_runner.py
from semantic_kernel import Kernel
from semantic_kernel.connectors.openai import OpenAITextCompletion, OpenAIEmbedding
import os

def get_kernel():
    kernel = Kernel()

    kernel.add_text_completion_service(
        OpenAITextCompletion(
            deployment_name=os.getenv("GPT_DEPLOYMENT"),
            endpoint=os.getenv("OPENAI_ENDPOINT"),
            api_key=os.getenv("OPENAI_API_KEY")
        )
    )

    kernel.add_embedding_service(
        OpenAIEmbedding(
            deployment_name=os.getenv("EMBEDDING_DEPLOYMENT"),
            endpoint=os.getenv("OPENAI_ENDPOINT"),
            api_key=os.getenv("OPENAI_API_KEY")
        )
    )

    # Register skills
    kernel.import_semantic_skill_from_directory("agents", "product_form_filler_skill")
    kernel.import_semantic_skill_from_directory("agents", "content_validator_skill")
    kernel.import_semantic_skill_from_directory("agents", "product_db_skill")

    return kernel



🚀 Step 6: main.py (Put It All Together)
from semantic_kernel_runner import get_kernel
from services.cosmos_service import CosmosService
from services.embedding_service import get_embedding

async def main():
    kernel = get_kernel()
    cosmos = CosmosService()

    seller_input = "I want to sell bamboo toothbrushes for $8. Sustainable and eco-friendly."

    # Step 1: Fill Product Fields
    skill = kernel.skills.get("product_form_filler_skill")
    parsed = await kernel.run_async(seller_input, skill["parse_product_info"])
    product = eval(str(parsed))  # Convert JSON string to dict

    # Step 2: Clean Description
    validator_skill = kernel.skills.get("content_validator_skill")
    validation_result = await kernel.run_async(product["description"], validator_skill["validate_description"])
    product["description"] = str(validation_result)

    # Step 3: Add Embedding
    product["embedding"] = get_embedding(product["title"] + " " + product["description"])
    product["id"] = product["title"].lower().replace(" ", "-")

    # Step 4: Upsert to Cosmos DB
    cosmos.upsert_product(product)
    print("Product saved:", product)

import asyncio
asyncio.run(main())



To set this up in a Copilot Page for edits and scaffolding? I can also help build hybrid search and buyer-side query flow next.
 I've created a Copilot Page with the full working prototype scaffold for your Seller Onboarding & Product Management Assistant project using Azure OpenAI, Cosmos DB, and Semantic Kernel.
You’ll find:
- Agent plugins for form filling, validation, and database ops
- Cosmos and embedding services
- A structured entry point in main.py
- Semantic Kernel initialization and environment config
Feel free to edit, run, or expand right from the page — we can build out the buyer-side vector search and planner chaining next if you'd like.
Click here to open the Copilot Page and start editing
Let me know if you want to add deployment instructions, visualization of agent interactions, or a guided walkthrough for testing.

Here's how you can deploy your Seller Onboarding & Product Management Assistant prototype in a structured, production-ready way using Azure services:
 Deployment Instructions
✅ 1. Provision Azure Resources
Use the Azure Portal or Azure CLI to create:
- Azure OpenAI Resource
- Models: gpt-35-turbo or gpt-4, text-embedding-3-small
- Azure Cosmos DB
- API: Core (SQL) or MongoDB (for compatibility with vector search)
- DB Name: ecommerce_db
- Container: products, partition key: /category
- (Optional) Azure Key Vault to securely store secrets

2. Local Setup
Clone your repo and set up your .env file (as previously scaffolded). Example:
OPENAI_API_KEY=xxx
OPENAI_ENDPOINT=https://your-endpoint.openai.azure.com/
EMBEDDING_DEPLOYMENT=text-embedding-3-small
GPT_DEPLOYMENT=gpt-35-turbo
COSMOS_DB_URI=https://your-cosmos-db.documents.azure.com/
COSMOS_DB_KEY=your-key
COSMOS_DB_NAME=ecommerce_db
COSMOS_DB_CONTAINER=products


Install dependencies:
pip install -r requirements.txt

Run the prototype locally:
python main.py

Containerize the App (Optional)
Create a Dockerfile:
FROM python:3.10
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "main.py"]


Build and run:
docker build -t ecommerce-agent-app .
docker run --env-file config/settings.env ecommerce-agent-app

 4. Deploy to Azure App Service
- Create an App Service Plan and Web App.
- Enable Continuous Deployment from GitHub or container registry.
- Set environment variables from your .env via the portal.

🧠 5. Test Conversation Flow
Use the existing main.py and simulate sample seller inputs. You’ll see:
- Parsed product info via ProductFormFillerAgent
- Cleaned descriptions via ContentValidatorAgent                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             
- Azure Bot Framework, or
- Flask / FastAPI REST layer

 Step-by-Step Deployment on Azure
 
🧭 Step 1: Provision Azure Resources
1.1 Azure OpenAI Service
- Go to: Azure OpenAI
- Create a Resource → Azure OpenAI
- Region: East US or compatible with embedding + GPT models
- Deploy Models:
- gpt-35-turbo or gpt-4o (for agent responses)
- text-embedding-3-small (for vector search)
- Capture:
- Resource name
- Endpoint URL
- Deployment names
- Keys
1.2 Azure Cosmos DB
- Go to: Create Resource → Azure Cosmos DB for NoSQL
- Name: ecommerce-db
- Create container:
- Name: products
- Partition key: /category
- Enable vector indexing (if available in preview region)
Optionally use MongoDB API if you're using preview vector search capabilities.


🔐 Step 2: Secure Your Secrets
- Use Azure Key Vault to store:
- OPENAI_API_KEY
- COSMOS_DB_KEY
- Set up access policies and integrate them into your app securely if running on Azure App Service or Functions.

🧪 Step 3: Local Test & Run
1. Clone your local repo
2. Create .env using keys and URIs
3. Install packages:
    pip install -r requirements.txt
4. Run locally:
python main.py

Step 4: Containerize the App (Optional)
If you'd like to deploy in a container:
- Create Dockerfile
- Build and tag your image:
docker build -t ecommerce-agent-app .
- Test locally:
docker run --env-file config/settings.env ecommerce-agent-app

 Step 5: Deploy on Azure
Option A: Azure App Service (Code-Based)
- Create Web App (Python 3.10+ runtime)
- Enable Deployment Center (from GitHub or local ZIP)
- Configure Application Settings:
- Add .env keys as environment variables
- Push code or ZIP deploy
Option B: Azure Container Apps
- Push Docker image to Azure Container Registry (ACR)
- Deploy to Azure Container App
- Map Secrets from Azure Key Vault or App Settings

Step 6: Validate on Azure
Test with inputs like:
“I want to sell a set of eco-friendly tea cups for $15. Handmade, ceramic.”

You should see:
- Structured JSON from the form-filler agent
- Cleaned description
- Vector-embedded metadata
- Cosmos DB record upserted

✅ Optional: Front-End Chat
Integrate via:
- Flask / FastAPI REST backend
- Azure Bot Service frontend with Web Chat or Teams channel

















