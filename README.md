<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Free GPT Chatbot</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; transition: background 2s; }
        #chatbox { width: 80%; height: 400px; border: 1px solid #ccc; overflow-y: scroll; margin: 20px auto; padding: 10px; }
        #userInput { width: 70%; padding: 10px; }
        button { padding: 10px; }
        .rainbow {
            animation: rainbow 1s infinite alternate;
        }
        @keyframes rainbow {
            0% { background: red; }
            20% { background: orange; }
            40% { background: yellow; }
            60% { background: green; }
            80% { background: blue; }
            100% { background: purple; }
        }
    </style>
</head>
<body>
    <h1>Chat with GPT</h1>
    <p>Chat Bucks: <span id="chatBucks">0</span></p>
    <div id="chatbox"></div>
    <input type="text" id="userInput" placeholder="Type your message...">
    <button onclick="sendMessage()">Send</button>
    <input type="file" id="fileInput">
    <button onclick="sendFile()">Send File</button>

    <script>
        let chatBucks = 0;

        async function sendMessage() {
            let inputField = document.getElementById("userInput");
            let message = inputField.value.trim();
            if (!message) return;

            let chatbox = document.getElementById("chatbox");
            chatbox.innerHTML += `<p><strong>You:</strong> ${message}</p>`;
            inputField.value = "";
            chatbox.scrollTop = chatbox.scrollHeight;

            if (message.toLowerCase() === "mango") {
                chatBucks += 1000;
            } else {
                chatBucks += 10;
            }
            document.getElementById("chatBucks").innerText = chatBucks;

            if (chatBucks >= 1000) {
                document.body.classList.add("rainbow");
                setTimeout(() => {
                    document.body.classList.remove("rainbow");
                }, 5000);
            }

            let response;
            try {
                let res = await fetch("https://api-inference.huggingface.co/models/bigscience/bloomz-560m", {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                        "Authorization": "Bearer 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"
                    },
                    body: JSON.stringify({ inputs: message })
                });
                
                if (!res.ok) {
                    throw new Error(`API Error: ${res.status} - ${res.statusText}`);
                }
                
                let data = await res.json();
                response = data[0]?.generated_text || "I'm sorry, I couldn't process your request.";
            } catch (error) {
                response = `Error: ${error.message}`;
            }
            
            chatbox.innerHTML += `<p><strong>GPT:</strong> ${response}</p>`;
            chatbox.scrollTop = chatbox.scrollHeight;
        }

        function sendFile() {
            let fileInput = document.getElementById("fileInput");
            let file = fileInput.files[0];
            if (file) {
                let chatbox = document.getElementById("chatbox");
                chatbox.innerHTML += `<p><strong>You sent a file:</strong> ${file.name}</p>`;
            } else {
                alert("Please select a file before sending.");
            }
        }
    </script>
</body>
</html>
