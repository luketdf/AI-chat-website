ai-chat-website/
├─ public/
│  ├─ index.html
│  ├─ style.css
│  └─ script.js
├─ server.js
├─ package.json
├─ .env   ← (You won’t upload this one, you’ll add it manually on Render)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Chat</title>
<link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="chat-container">
    <h2>AI Chat</h2>
    <div id="chat-box"></div>
    <div class="input-area">
      <input type="text" id="user-input" placeholder="Type a message..." />
      <button onclick="sendMessage()">Send</button>
    </div>
  </div>
<script src="script.js"></script>
</body>
</html>
body {
  font-family: Arial, sans-serif;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: #eaeaea;
  margin: 0;
}

.chat-container {
  background: #fff;
  border-radius: 10px;
  padding: 20px;
  width: 400px;
  box-shadow: 0 0 10px rgba(0,0,0,0.2);
}

#chat-box {
  height: 300px;
  overflow-y: auto;
  border: 1px solid #ccc;
  padding: 10px;
  margin-bottom: 10px;
}

.input-area {
  display: flex;
  gap: 10px;
}

#user-input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

button {
  padding: 10px 15px;
  border: none;
  background: #007bff;
  color: #fff;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background: #0056b3;
}
async function sendMessage() {
    const input = document.getElementById("user-input");
    const chatBox = document.getElementById("chat-box");
    const userMessage = input.value.trim();
    if (!userMessage) return;

    chatBox.innerHTML += `<div><b>You:</b> ${userMessage}</div>`;
    input.value = "";

    const response = await fetch("/chat", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ message: userMessage })
    });

    const data = await response.json();
    chatBox.innerHTML += `<div><b>AI:</b> ${data.reply}</div>`;
    chatBox.scrollTop = chatBox.scrollHeight;
}
import express from 'express';
import fetch from 'node-fetch';
import dotenv from 'dotenv';
dotenv.config();

const app = express();
app.use(express.json());
app.use(express.static('public'));

app.post('/chat', async (req, res) => {
    const userMessage = req.body.message;

    try {
        const response = await fetch('https://api.openai.com/v1/chat/completions', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${process.env.OPENAI_API_KEY}`
            },
            body: JSON.stringify({
                model: "gpt-5-mini",
                messages: [{ role: "user", content: userMessage }]
            })
        });

        const data = await response.json();
        const reply = data.choices[0].message.content;
        res.json({ reply });
    } catch (error) {
        console.error(error);
        res.json({ reply: "Sorry, something went wrong." });
    }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
{
  "name": "ai-chat-website",
  "version": "1.0.0",
  "type": "module",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "dotenv": "^16.3.1",
    "express": "^4.18.2",
    "node-fetch": "^3.3.2"
  }

