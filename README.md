# chatbot-pdf-content
Criando um Chatbot Baseado em Conteúdo de PDFs

# Projeto: Integração de PDFs com Azure Foundry e OpenAI

Este projeto tem como objetivo processar PDFs e utilizar Inteligência Artificial para extrair informações e responder perguntas com base no conteúdo extraído. Utilizamos o **Azure Foundry**, **Azure Machine Learning**, **FAISS**, **OpenAI GPT-3**, entre outras tecnologias.

## Passo a Passo para Configuração no Azure

### 1. Criar Conta no Azure
Se ainda não tiver uma conta no Azure, acesse [portal.azure.com](https://portal.azure.com) e crie uma conta gratuita. O Azure oferece créditos gratuitos para novos usuários por 30 dias.

### 2. Configurar Ambiente no Azure

#### 2.1 Criar um Workspace no Azure Machine Learning
1. No Portal do Azure, pesquise por **"Azure Machine Learning"** e crie um novo **Workspace**.
2. Preencha as informações necessárias (Nome, Região, Grupo de Recursos).
3. Clique em **Revisar + Criar** e depois em **Criar**.

#### 2.2 Criar um Cluster de Computação
1. Dentro do Workspace, acesse **Compute** e clique em **New**.
2. Escolha uma máquina com GPU para otimizar o processamento.
3. Selecione uma imagem Ubuntu ou Windows, conforme preferência.

#### 2.3 Criar um Jupyter Notebook no Azure
1. No Workspace, acesse **Notebooks** e clique em **Novo**.
2. Escolha o nome do notebook e a máquina criada anteriormente.
3. Instale as bibliotecas necessárias:
   ```bash
   !pip install PyPDF2 transformers langchain openai faiss-cpu numpy sentence-transformers
   ```

### 3. Subir PDFs para o Azure
Usaremos o **Azure Blob Storage** para armazenar os PDFs.

#### 3.1 Criar um Contêiner de Blob Storage
1. No Portal do Azure, pesquise por **Storage Accounts** e crie uma nova conta de armazenamento.
2. Crie um **Blob Container** dentro dessa conta.
3. Faça o upload dos arquivos PDF para o container.

#### 3.2 Acessar PDFs via Python
Para acessar os arquivos PDF no Blob Storage:
```python
from azure.storage.blob import BlobServiceClient

connection_string = "Sua_Connection_String"
container_name = "nome-do-container"
blob_name = "nome-do-arquivo.pdf"

blob_service_client = BlobServiceClient.from_connection_string(connection_string)
container_client = blob_service_client.get_container_client(container_name)
blob_client = container_client.get_blob_client(blob_name)

with open("arquivo.pdf", "wb") as f:
    f.write(blob_client.download_blob().readall())
```

### 4. Processar PDFs
Para extrair texto dos PDFs:
```python
import PyPDF2

def extrair_texto_pdf(caminho_pdf):
    with open(caminho_pdf, 'rb') as file:
        reader = PyPDF2.PdfReader(file)
        texto = ''
        for page in reader.pages:
            texto += page.extract_text()
    return texto
```

### 5. Gerar Embeddings com Sentence Transformers
```python
from sentence_transformers import SentenceTransformer

modelo = SentenceTransformer('all-MiniLM-L6-v2')

def gerar_embeddings(texto):
    return modelo.encode(texto)
```

### 6. Busca Vetorial com FAISS
```python
import faiss
import numpy as np

def criar_indice_busca(embeddings):
    dim = len(embeddings[0])
    index = faiss.IndexFlatL2(dim)
    index.add(np.array(embeddings))
    return index

def buscar_indice(indice, query_embedding, k=5):
    D, I = indice.search(np.array([query_embedding]), k)
    return I
```

### 7. Integração com OpenAI GPT-3
```python
import openai

openai.api_key = "Sua_API_Key"

def gerar_resposta(pergunta):
    resposta = openai.Completion.create(
        model="text-davinci-003",
        prompt=pergunta,
        max_tokens=150
    )
    return resposta.choices[0].text.strip()
```

### 8. Testar e Validar a Solução
No **Azure Machine Learning Studio** ou no **Azure Notebooks**, execute o código para validar o funcionamento do sistema.

### 9. Subir o Projeto para o GitHub
1. Inicie um repositório no GitHub e clone para sua máquina.
2. Adicione os arquivos e faça commit:
   ```sh
   git add .
   git commit -m "Adicionando projeto Azure Foundry com OpenAI e FAISS"
   git push origin main
   ```

### 10. Excluir Recursos do Azure (Opcional)
Se for apenas um teste, exclua os recursos no portal do Azure para evitar cobranças.

---

## 🚀 Conclusão
Este projeto utiliza o poder do Azure para processamento de PDFs com IA. Agora é só testar e expandir conforme necessário!








