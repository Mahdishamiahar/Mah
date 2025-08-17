<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>هوش مصنوعی + آپلود فایل</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            direction: rtl;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            height: 100vh;
        }
        #home {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100%;
            flex-direction: column;
        }
        #chatPage {
            display: none;
            flex-direction: column;
            height: 100%;
        }
        #chatWindow {
            flex: 1;
            overflow-y: auto;
            padding: 10px;
            background: #f4f4f4;
        }
        .message {
            margin: 5px 0;
            padding: 10px;
            border-radius: 10px;
            max-width: 70%;
            word-wrap: break-word;
        }
        .user {
            background-color: #0084ff;
            color: white;
            align-self: flex-end;
        }
        .bot {
            background-color: #e0e0e0;
            color: black;
            align-self: flex-start;
        }
        #inputArea {
            display: flex;
            padding: 10px;
            background: #ddd;
        }
        #inputArea input[type="text"] {
            flex: 1;
            padding: 10px;
            font-size: 16px;
        }
        #inputArea button {
            padding: 10px;
            font-size: 16px;
        }
        #uploadArea {
            padding: 10px;
            background: #f0f0f0;
            display: flex;
            align-items: center;
        }
        #uploadArea input[type="file"] {
            flex: 1;
        }
    </style>
</head>
<body>
    <!-- صفحه اصلی با دکمه ورود -->
    <div id="home">
        <button onclick="enterChat()">ورود به هوش مصنوعی</button>
        <p>آپلود فایل تا 10 گیگابایت و دریافت لینک مستقیم</p>
    </div>

    <!-- صفحه چت -->
    <div id="chatPage">
        <div id="chatWindow"></div>

        <div id="uploadArea">
            <input type="file" id="fileInput">
            <button onclick="uploadFile()">آپلود فایل</button>
        </div>

        <div id="inputArea">
            <input type="text" id="userInput" placeholder="پیام خود را وارد کنید">
            <button onclick="sendMessage()">ارسال</button>
        </div>
    </div>

    <script>
        function enterChat() {
            document.getElementById('home').style.display = 'none';
            document.getElementById('chatPage').style.display = 'flex';
        }

        async function sendMessage() {
            const input = document.getElementById('userInput');
            const text = input.value.trim();
            if (!text) return;

            addMessage(text, 'user');
            input.value = '';

            const apiUrl = `https://hoshi-app.ir/api/chat-gpt.php?text=${encodeURIComponent(text)}`;

            try {
                const res = await fetch(apiUrl);
                const data = await res.text();
                addMessage(data, 'bot');
            } catch (err) {
                addMessage("خطا در دریافت پاسخ!", 'bot');
                console.error(err);
            }
        }

        function addMessage(text, sender) {
            const chatWindow = document.getElementById('chatWindow');
            const msgDiv = document.createElement('div');
            msgDiv.className = 'message ' + sender;
            msgDiv.innerHTML = text; 
            chatWindow.appendChild(msgDiv);
            chatWindow.scrollTop = chatWindow.scrollHeight;
        }

        async function uploadFile() {
            const fileInput = document.getElementById('fileInput');
            const file = fileInput.files[0];
            if (!file) return alert("لطفاً یک فایل انتخاب کنید!");

            addMessage(`در حال آپلود فایل: ${file.name}`, 'user');

            const formData = new FormData();
            formData.append('file', file, file.name);

            try {
                const res = await fetch('https://vddaniyel.top/upload_api.php', {
                    method: 'POST',
                    body: formData
                });
                const data = await res.json();
                if(data.success) {
                    const msg = `آپلود موفق! لینک مستقیم: <a href="${data.direct_link}" target="_blank">${data.direct_link}</a>`;
                    addMessage(msg, 'bot');
                } else {
                    addMessage("آپلود موفق نبود!", 'bot');
                }
            } catch(err) {
                addMessage("خطا در آپلود فایل!", 'bot');
                console.error(err);
            }
        }
    </script>
</body>
</html>
