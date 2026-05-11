# RateMyProf AI

RateMyProf AI is a web app that lets students ask an AI chatbot questions about professors and get answers grounded in real RateMyProfessors reviews. Instead of reading through hundreds of reviews yourself, you can just ask something like "Who's the best CS professor?" or "Is Professor Smith good for beginners?" and get a direct, informed response.

You can also add new professors to the database by pasting in their RateMyProfessors link. The app scrapes the page, generates embeddings from their reviews, and stores them in a vector database so the AI can reference them going forward.

---

## Features

- **AI chat powered by RAG** -- ask natural language questions about professors and get answers based on real student reviews
- **Add professors on the fly** -- paste any RateMyProfessors URL and the system ingests the professor's reviews automatically
- **Streaming responses** -- the chatbot streams its reply token by token so you're not staring at a loading spinner
- **Vector search** -- uses Pinecone to find the most relevant professor reviews for any query

---

## Tech Stack

- **Framework**: Next.js 14 (App Router)
- **UI**: React 18, Material UI v6, Tailwind CSS
- **AI**: OpenAI GPT-4o-mini (chat), OpenAI Embeddings (text-embedding-3-small)
- **Vector DB**: Pinecone
- **Scraping**: Cheerio
- **Analytics**: Vercel Analytics

---

## How It Works

### Chat (`/chatbot`)

1. You type a question about a professor or subject
2. The app embeds your question and runs a similarity search against the Pinecone index
3. The top 25 matching reviews are retrieved and appended to your message as context
4. GPT-4o-mini generates a response using that context and streams it back to you

### Add a Professor (`/addProfessor`)

1. Find the professor on [RateMyProfessors.com](https://www.ratemyprofessors.com)
2. Paste their profile URL into the input field
3. The app scrapes their name, subject, rating, and reviews from the page
4. The reviews are embedded and upserted into Pinecone so the AI can reference them in future chats

---

## Getting Started

### Prerequisites

- Node.js 18+
- An [OpenAI API key](https://platform.openai.com/account/api-keys)
- A [Pinecone](https://www.pinecone.io/) account and API key

### Installation

```bash
git clone https://github.com/your-username/RateMyProfessor-AI-Professor-Reviews.git
cd RateMyProfessor-AI-Professor-Reviews
npm install
```

### Environment Variables

Create a `.env.local` file in the root of the project:

```
OPENAI_API_KEY=your_openai_api_key
PINECONE_API_KEY=your_pinecone_api_key
```

### Seed the Database (first time only)

The `backend/` folder has a Python script that creates the Pinecone index and loads 10 seed professor reviews.

```bash
cd backend
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install pinecone-client openai python-dotenv
python setup_rag.py
```

Make sure your `.env` file is present in the `backend/` directory with the same keys before running this.

### Run the App

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) and you're good to go.

---

## Project Structure

```
.
+-- src/
|   +-- app/
|       +-- page.tsx              # Landing page
|       +-- chatbot/page.tsx      # Chat UI
|       +-- addProfessor/page.tsx # Add professor page
|       +-- api/
|           +-- chat/route.js     # RAG chat endpoint
|           +-- scrape/route.js   # Web scraping + Pinecone ingestion
+-- backend/
|   +-- reviews.json              # Seed professor data
|   +-- setup_rag.py              # One-time Pinecone setup script
|   +-- check.py                  # Pinecone index utility
+-- public/
    +-- images/                   # App icons and assets
```

---

## Deployment

This app is designed to deploy on [Vercel](https://vercel.com). Just connect the repo and add your environment variables in the Vercel dashboard. The Vercel Analytics integration is already wired up.

