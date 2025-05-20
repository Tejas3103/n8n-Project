## 📁 Files

- `BuildingAnRAG_AI_Agent.json`: This is the n8n workflow that connects Google Drive with Pinecone vector store and OpenAI for building a RAG AI agent.

## 🔧 Usage

1. Open n8n.
2. Go to "Import Workflow".
3. Upload the `BuildingAnRAG_AI_Agent.json` file.
4. Add your credentials (Google Drive, Pinecone, OpenAI).
5. Run it!

## ⚙️ Tools Used

- n8n
- Pinecone
- OpenAI Embeddings
- Google Drive


## 📝 Code Explanation

1. **Manual Trigger**  
   - **Node:** `When clicking ‘Test workflow’`  
   - **Type:** `manualTrigger`  
   - **Purpose:** Starts the pipeline on demand in the editor for testing.

2. **Google Drive Download**  
   - **Node:** `Google Drive`  
   - **Type:** `googleDrive` (v3)  
   - **Operation:** `download`  
   - **Parameters:**  
     - `fileId`: The Google Drive file’s ID (placeholder in JSON)  
   - **Purpose:** Fetches the source document from your Drive using OAuth2 credentials.

3. **Default Data Loader**  
   - **Node:** `Default Data Loader`  
   - **Type:** `documentDefaultDataLoader`  
   - **Parameters:**  
     - `dataType`: `binary`  
   - **Purpose:** Reads the downloaded file’s binary data and forwards it for text splitting.

4. **Recursive Character Text Splitter**  
   - **Node:** `Recursive Character Text Splitter`  
   - **Type:** `textSplitterRecursiveCharacterTextSplitter`  
   - **Parameters:**  
     - Default chunk size/settings  
   - **Purpose:** Breaks large document text into smaller chunks (~1,000 chars) while preserving sentence boundaries.

5. **Embeddings Creation**  
   - **Node:** `Embeddings OpenAI`  
   - **Type:** `@n8n/n8n-nodes-langchain.embeddingsOpenAi`  
   - **Purpose:** Sends each text chunk to OpenAI’s embeddings API and receives a high-dimensional vector.

6. **Pinecone Vector Store**  
   - **Node:** `Pinecone Vector Store`  
   - **Type:** `@n8n/n8n-nodes-langchain.vectorStorePinecone`  
   - **Operation:** `insert`  
   - **Parameters:**  
     - `pineconeIndex`: Name of your Pinecone index  
     - `pineconeNamespace`: Logical namespace for isolation  
   - **Connections:**  
     - **ai_document:** Receives binary data from Default Data Loader  
     - **ai_textSplitter:** Receives text chunks from Recursive Text Splitter  
     - **ai_embedding:** Receives embedding vectors from Embeddings OpenAI  
   - **Purpose:** Inserts each document chunk and its vector into Pinecone for semantic search.


