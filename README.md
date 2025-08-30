🚀 Multi-Agent Chat App

A ChatGPT-style web app built with Next.js + TailwindCSS where users can interact with multiple AI agents (different personalities and roles). This project demonstrates how to build a modern AI chat UI, connect it to OpenAI’s API, and extend it with features like history, theming, and voice.

✨ Features

🔹 Clean chat interface with user & AI message bubbles

🔹 Multi-agent support (Professor, Buddy, Writer, Solver, Researcher, etc.)

🔹 Next.js API routes proxying requests to OpenAI

🔹 TailwindCSS styling with dark/light mode support

🔹 Easy extensibility: file uploads, voice input/output, and multi-agent debates

🛠️ Project Setup
1️⃣ Initialize Next.js with TailwindCSS
npx create-next-app@latest multi-agent-chat
cd multi-agent-chat

# Install TailwindCSS
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

2️⃣ Configure TailwindCSS

In tailwind.config.js add your file paths:

module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  theme: { extend: {} },
  plugins: [],
}


Add Tailwind directives to styles/globals.css:

@tailwind base;
@tailwind components;
@tailwind utilities;

💬 Step 2: Create Chat UI

Build a simple chat interface with bubbles and input. Example (components/Chat.js):

import { useState } from "react";

export default function Chat() {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState("");

  const sendMessage = async () => {
    if (!input.trim()) return;

    const userMsg = { role: "user", content: input };
    setMessages((prev) => [...prev, userMsg]);
    setInput("");

    const res = await fetch("/api/chat", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ message: input }),
    });

    const data = await res.json();
    setMessages((prev) => [...prev, { role: "assistant", content: data.reply }]);
  };

  return (
    <div className="flex flex-col h-screen p-4 bg-gray-100 dark:bg-gray-900">
      <div className="flex-1 overflow-y-auto space-y-2">
        {messages.map((m, i) => (
          <div
            key={i}
            className={`p-2 rounded-lg max-w-lg ${
              m.role === "user"
                ? "bg-blue-500 text-white self-end"
                : "bg-gray-300 dark:bg-gray-700 text-black dark:text-white self-start"
            }`}
          >
            {m.content}
          </div>
        ))}
      </div>
      <div className="flex mt-4">
        <input
          className="flex-1 p-2 border rounded-lg dark:bg-gray-800 dark:text-white"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          onKeyDown={(e) => e.key === "Enter" && sendMessage()}
        />
        <button
          className="ml-2 px-4 py-2 bg-blue-600 text-white rounded-lg"
          onClick={sendMessage}
        >
          Send
        </button>
      </div>
    </div>
  );
}

🤖 Step 3: Connect to OpenAI API

Create API route pages/api/chat.js:

import { Configuration, OpenAIApi } from "openai";

const config = new Configuration({ apiKey: process.env.OPENAI_API_KEY });
const openai = new OpenAIApi(config);

export default async function handler(req, res) {
  if (req.method !== "POST") return res.status(405).end();

  const { message } = req.body;

  try {
    const response = await openai.createChatCompletion({
      model: "gpt-3.5-turbo",
      messages: [{ role: "user", content: message }],
    });

    res.status(200).json({ reply: response.data.choices[0].message.content });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
}

🔗 Step 4: Wire Frontend to Backend

Use the Chat component in pages/index.js:

import Chat from "../components/Chat";

export default function Home() {
  return (
    <main>
      <Chat />
    </main>
  );
}

▶️ Step 5: Run the App

Add your API key to .env.local:

OPENAI_API_KEY=your_openai_api_key_here


Start the dev server:

npm run dev


Open 👉 http://localhost:3000

🌟 Next Steps

🌙 Dark/Light theme toggle

💾 Save chat history (localStorage or DB)

⌛ Typing indicator while waiting for AI

📂 File upload + integrate docs into prompts

🎤 Voice input/output via Web Speech API

🧑‍🤝‍🧑 Multi-agent debates with side-by-side chat windows

📌 License

MIT License – free to use and modify.
