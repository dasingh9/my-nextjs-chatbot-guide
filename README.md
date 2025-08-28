# My Next.js Chatbot - Mini Project Guide

## 📚 Project Objective

Create a **Next.js full-stack app** that securely calls the **Gemini 2.0 Flash API** for content generation. Students will:

- Use **Next.js App Router** with API routes as backend endpoints.  
- Securely use API keys via **environment variables**.  
- Implement a clear separation of concerns (**Controller + Service/DAO pattern** inside the `lib/` folder).  
- Validate user input (prompt) inside the controller logic.  
- Implement error handling (try/catch + global error page).  
- Create a customised **React frontend page** for user interaction.  

---

## ✅ Step-by-Step Instructions

### Step 1: Initialize Project

```bash
npx create-next-app@latest my-nextjs-chatbot
cd my-nextjs-chatbot
```

Choose **TypeScript: No** (for now we’ll keep it JS), **Tailwind CSS: No**, and **import alias: No**.

---

### Step 2: Install Dependencies

```bash
npm install axios
```

---

### Step 3: File Structure

```
my-nextjs-chatbot/
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   └── generate/
│   │   │       └── route.js        # API endpoint
│   │   ├── error.js                # Global error boundary page
│   │   └── page.js                 # Frontend page
│   ├── lib/
│   │   ├── controllers/
│   │   │   └── geminiController.js
│   │   ├── dao/
│   │   │   └── geminiDao.js
│   │   └── utils/
│   │       └── validatePrompt.js   # (optional helper for validation)
├── .env.local
├── .gitignore
├── package.json
└── README.md
```

---

### Step 4: Environment Setup

**`.env.local`** This file needs to be created at root level i.e. at the same level where your package.json file is.
```env
GEMINI_API_KEY=your_api_key_here
GEMINI_API_URL=https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent
```

✅ Remember: `.env.local` should be added to `.gitignore` (ideally it should already be in .gitignore as this is a next.js project).

---

### Step 5: API Route (Backend in Next.js)

**`app/api/generate/route.js`**
```js
import { NextResponse } from 'next/server';
import GeminiController from '../../../lib/controllers/geminiController';

const controller = new GeminiController();

export async function POST(request) {
  try {
    const body = await request.json();
    const result = await controller.generateContent(body.prompt);
    return NextResponse.json({ content: result });
  } catch (error) {
    return NextResponse.json({ error: error.message || 'Internal Server Error' }, { status: 500 });
  }
}
```

---

### Step 6: Controller (Class-Based)

**`lib/controllers/geminiController.js`**
```js
import GeminiDao from '../dao/geminiDao.js';

export default class GeminiController {
  async generateContent(prompt) {
    // TODO: validate prompt
    // a. Throw error if prompt is null or blank
    // b. Throw error if length > 1000 chars

    const dao = new GeminiDao();
    return await dao.callGeminiAPI(prompt);
  }
}
```

---

### Step 7: DAO (Gemini API Call)

**`lib/dao/geminiDao.js`**
```js
import axios from 'axios';

export default class GeminiDao {
  async callGeminiAPI(prompt) {
    const GEMINI_API_URL = process.env.GEMINI_API_URL;
    const GEMINI_API_KEY = process.env.GEMINI_API_KEY;
    const response = await axios.post(
      GEMINI_API_URL,
      { contents: [{ parts: [{ text: prompt }] }] },
      {
        headers: {
          'Content-Type': 'application/json',
          'X-goog-api-key': GEMINI_API_KEY
        }
      }
    );

    const candidates = response.data.candidates;
    return candidates[0]?.content?.parts[0]?.text || 'No response from Gemini.';
  }
}
```

---

### Step 8: Global Error Page

**`app/error.js`**
```js
'use client';

export default function GlobalError({ error, reset }) {
  return (
    <html>
      <body>
        TODO - Complete this section!
      </body>
    </html>
  );
}
```

---

### Step 9: Frontend Page

**`app/page.js`**
```js
"use client";

import { useState } from "react";

export default function Home() {
  const [prompt, setPrompt] = useState("");
  const [result, setResult] = useState("");

  function formatResult(geminiRawResult) {
    // Gemini may not return you the properly formatted text.
    // TODO: You need to update the code of this function to tranform the geminiRawResult to a well formatted result. Hint: You may use some regular expressions for find/replace. 
    const formattedResult = geminiRawResult;
    return formattedResult;
  }

  async function sendPrompt() {
    const res = await fetch("/api/generate", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ prompt }),
    });
    const data = await res.json();
    const formattedResult = formatResult(data.content || data.error);
    setResult(data.content || data.error);
  }

  return (
    //TODO: Make your UI look better by adding additional elements and styles as you wish! Possibly add your styles to a new css file called chatbot.css, and import that file in this page
    <main style={{ padding: "2rem" }}>
      <h1>My Next.js Chatbot</h1>
      <textarea
        rows="5"
        cols="50"
        value={prompt}
        onChange={(e) => setPrompt(e.target.value)}
        placeholder="Type your prompt..."
      />
      <br />
      <button onClick={sendPrompt}>Generate</button>
      <pre>{result}</pre>
    </main>
  );
}
```

➡️ **Customisation Requirement**:  
Students must **customise the UI look and feel** (colours, fonts, layout, animations, or any creative design). You can make it as **fancy** as you like.  

---

### Step 10: Testing

- Run your app:
```bash
npm run dev
```
- Visit [http://localhost:3000](http://localhost:3000)  
- Enter a prompt → Click Generate → See the Gemini response.  
- Test validation: blank prompt / too long prompt should throw errors.  

---

## 📌 Submission Requirements

Students must submit:
- ✅ GitHub repo link (with `.env.local` excluded).  
- ✅ Working Next.js demo (local).  
- ✅ Add a simple architecture diagram in README.md (Frontend + Next.js API routes + Controller + DAO + error.js).

---

## 🎯 Bonus Challenges

- Add tone/style selector (funny, poetic, formal).  
- Save prompt history on server side to include it in the on-going conversation.  
- Add **Bootstrap styling** for a polished look.
---

All the best! 🎉
