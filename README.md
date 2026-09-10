<img width="1920" height="970" alt="cropped_Screenshot_2026-09-10_222017" src="https://github.com/user-attachments/assets/e80e9632-ace5-4f99-ab95-9c8b25ced29b" /># Local Llama 3.1 Chatbot with LangChain + Streamlit

A minimal Streamlit app that lets you chat with **Llama 3.1** running **entirely on your own machine**, using **Ollama** as the local model server and **LangChain** to wire up the prompt and invocation.

No API keys. No cloud calls. No data leaves your computer.

---

## How It Works

- **Ollama** runs the Llama 3.1 model locally and exposes it to your machine.
- **LangChain** (`langchain-ollama`) wraps the model and lets you build a simple prompt → model chain.
- **Streamlit** provides the chat UI in your browser.

The app uses a basic "think step by step" prompt template, sends your question through the chain, and streams back Llama 3.1's answer.

---


<img width="1920" height="970" alt="cropped_Screenshot_2026-09-10_222017" src="https://github.com/user-attachments/assets/8b8d8a5d-3e0c-482c-9197-dacb307fe526" />


--


<img width="1920" height="970" alt="cropped_Screenshot_2026-09-10_222031" src="https://github.com/user-attachments/assets/13b8a873-2a60-45aa-92e6-d3cc29ef4d90" />


--

<img width="1920" height="970" alt="cropped_Screenshot_2026-09-10_222136" src="https://github.com/user-attachments/assets/de532989-cdc5-45d2-af93-cd2d00400782" />

--

<img width="1920" height="970" alt="cropped_Screenshot_2026-09-10_222751" src="https://github.com/user-attachments/assets/0771e45d-3247-4f0a-b6ba-062709f868bd" />

--

<img width="1920" height="970" alt="cropped_Screenshot_2026-09-10_225016" src="https://github.com/user-attachments/assets/993c9ea6-19fd-4c79-a6a9-d42966988b0a" />


--

<img width="1920" height="870" alt="cropped_Screenshot_2026-09-10_224552" src="https://github.com/user-attachments/assets/f1d2e8c2-3f67-43b2-b480-9af8fdfafe1a" />

--

<img width="1920" height="870" alt="cropped_Screenshot_2026-09-10_224859" src="https://github.com/user-attachments/assets/5d3018e3-4c22-4e9d-bb06-290c9d78d3fb" />

<img width="1920" height="870" alt="cropped_Screenshot_2026-09-10_224958" src="https://github.com/user-attachments/assets/62438b7f-048b-4adf-b6b2-7b40177a0092" />


---

## Prerequisites

1. **Python 3.10** (via conda or any environment manager)
2. **Ollama** installed and running — [download here](https://ollama.com/download)
3. The **Llama 3.1** model pulled locally:
   ```bash
   ollama pull llama3.1
   ```

---

## Setup

Create and activate a dedicated environment:

```bash
conda create -n env_langchain1 python=3.10
conda activate env_langchain1
```

Upgrade pip and install dependencies:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

---

## Run the App

Make sure Ollama is running in the background, then start the Streamlit app:

```bash
streamlit run pajju_app.py
```

Streamlit will open the app in your browser (usually at `http://localhost:8501`). Type a question in the chat box and Llama 3.1 will respond.

---

## Project Structure

```
.
├── pajju_app.py            # Streamlit app: prompt template + LangChain + Ollama LLM
├── requirements.txt   # Python dependencies
└── README.md
```

---

## Code Overview (`pajju_app.py`)

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_ollama.llms import OllamaLLM
import streamlit as st

template = """Question: {question}

Answer: Let's think step by step."""

prompt = ChatPromptTemplate.from_template(template)
model = OllamaLLM(model="llama3.1")
chain = prompt | model

question = st.chat_input("Enter your question here")
if question:
    st.write(chain.invoke({"question": question}))
```

- `ChatPromptTemplate` defines the reasoning-style prompt sent to the model.
- `OllamaLLM(model="llama3.1")` connects to your locally running Ollama instance.
- `prompt | model` is LangChain's pipe syntax — it chains the prompt formatting directly into the model call.
- `chain.invoke({...})` runs the full chain and returns Llama 3.1's response.

---

## Notes & Tips

- Since everything runs locally, response speed depends on your machine's CPU/GPU and RAM. Larger context or longer answers will take more compute.
- If Ollama isn't running, the app will fail to connect — start it with `ollama serve` (or ensure the Ollama desktop app/service is active) before launching Streamlit.
- You can swap `"llama3.1"` for any other model tag you've pulled via Ollama (e.g. `llama3.1:70b`, `mistral`, `gemma2`) without changing any other code.
- `langchain_experimental` is listed in `requirements.txt` but isn't used by `app1.py` yet — safe to remove if you don't plan to use LangChain's experimental features, or keep it if you're planning to extend the app.

---

## Next Steps (Ideas 🤔)

- Add chat history so the conversation has memory across turns.
- Stream tokens as they're generated instead of waiting for the full response.
- Add a system prompt to control the model's tone/persona.
- Swap the plain prompt template for a RAG pipeline (retrieve context from your own documents before answering).
