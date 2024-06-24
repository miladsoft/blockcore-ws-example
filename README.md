# Blockcore WebSocket Example

This project demonstrates a simple WebSocket client using the Blockcore WebSocket library. The application connects to a WebSocket server, sends messages, and logs the communication.

## Table of Contents

- [Installation](#installation)
- [Usage](#usage)
- [Features](#features)
- [Code Overview](#code-overview)
- [License](#license)

## Installation

No installation is required for this example. Just open the HTML file in a web browser to run the application.

## Usage

1. Open the `index.html` file in your web browser.
2. Click the "Connect" button to connect to the WebSocket server.
3. Once connected, you can:
   - See the connection status.
   - Send messages using the input box and "Send" button.
   - View messages and roundtrip times in the messages log.
4. Click the "Disconnect" button to close the connection.

## Features

- **Connect/Disconnect**: Connect to and disconnect from the WebSocket server.
- **Message Logging**: Logs messages sent and received, including the roundtrip time for messages.
- **Automatic Messages**: Sends a timestamp message to the server every two seconds when connected.

## Code Overview

### HTML Structure

- **Container**: Main UI container with connection status, buttons, message log, and input field.
- **Buttons**: Connect, Disconnect, and Send buttons to manage WebSocket actions.
- **Message Log**: Displays messages and roundtrip times.

### CSS Styling

The styling provides a clean and centered UI with a simple color scheme:

- **Container**: White background, rounded corners, and shadow for emphasis.
- **Buttons**: Different colors for connect (green), disconnect (red), and send (blue) actions.
- **Message Log**: Scrollable area with a light background for readability.

### JavaScript Logic

The JavaScript logic handles the WebSocket connection and message processing:

- **WebSocket Initialization**: Sets up the WebSocket connection to `wss://echo.websocket.org`.
- **Event Handlers**:
  - **onopen**: Updates status and starts sending periodic messages.
  - **onclose**: Updates status and stops interaction.
  - **onmessage**: Logs received messages and roundtrip times.
- **Message Sending**: Sends messages from the input field and logs them.
- **Button States**: Toggles button states based on connection status.

### Example Code

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Blockcore WebSocket Example</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background: #f0f0f0;
            margin: 0;
        }
        .container {
            text-align: center;
            background: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            width: 400px;
        }
        #status {
            margin-bottom: 20px;
            font-size: 1.2em;
        }
        #messages {
            border: 1px solid #ccc;
            padding: 10px;
            width: 100%;
            height: 200px;
            overflow-y: scroll;
            margin: 20px 0;
            border-radius: 5px;
            background: #fafafa;
        }
        .message {
            margin-bottom: 10px;
        }
        .input-container {
            display: flex;
            align-items: center;
            margin-bottom: 10px;
        }
        input[type="text"] {
            flex-grow: 1;
            padding: 10px;
            margin-right: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
            font-size: 1em;
        }
        button {
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1em;
            transition: background 0.3s;
        }
        #connectBtn {
            background: #28a745;
            color: white;
        }
        #connectBtn:hover {
            background: #218838;
        }
        #disconnectBtn {
            background: #dc3545;
            color: white;
        }
        #disconnectBtn:hover {
            background: #c82333;
        }
        #sendBtn {
            background: #007bff;
            color: white;
        }
        #sendBtn:hover {
            background: #0069d9;
        }
    </style>
</head>
<body>
    <div class="container">
        <div id="status">Disconnected</div>
        <button id="connectBtn">Connect</button>
        <button id="disconnectBtn" disabled>Disconnect</button>
        <div id="messages"></div>
        <div class="input-container">
            <input type="text" id="messageInput" placeholder="Enter message" disabled>
            <button id="sendBtn" disabled>Send</button>
        </div>
    </div>

    <script type="module">
        import WebSocket from 'https://unpkg.com/blockcore-ws@1.0.0/browser.js';

        let ws;
        const statusElement = document.getElementById('status');
        const messagesElement = document.getElementById('messages');
        const connectBtn = document.getElementById('connectBtn');
        const disconnectBtn = document.getElementById('disconnectBtn');
        const sendBtn = document.getElementById('sendBtn');
        const messageInput = document.getElementById('messageInput');

        connectBtn.addEventListener('click', () => {
            ws = new WebSocket('wss://echo.websocket.org');

            ws.onopen = () => {
                statusElement.textContent = 'Connected';
                logMessage('Connected to WebSocket');
                toggleButtons(true);

                // Send a message to WebSocket every two seconds
                setInterval(() => {
                    if (ws && ws.readyState === WebSocket.OPEN) {
                        const timestamp = Date.now();
                        ws.send(timestamp);
                    }
                }, 2000);
            };

            ws.onclose = () => {
                statusElement.textContent = 'Disconnected';
                logMessage('Disconnected from WebSocket');
                toggleButtons(false);
            };

            ws.onmessage = (event) => {
                const roundtripTime = Date.now() - parseInt(event.data, 10);
                if (!isNaN(roundtripTime)) {
                    logMessage(`Roundtrip time: ${roundtripTime} ms`);
                } else {
                    logMessage(`Received: ${event.data}`);
                }
            };

            ws.onpaused = () => {
                logMessage('Connection paused');
            };

            ws.onresumed = () => {
                logMessage('Connection resumed');
            };
        });

        disconnectBtn.addEventListener('click', () => {
            if (ws) {
                ws.close();
            }
        });

        sendBtn.addEventListener('click', () => {
            const message = messageInput.value;
            if (ws && message) {
                ws.send(message);
                logMessage(`Sent: ${message}`);
                messageInput.value = '';
            }
        });

        function logMessage(message) {
            const messageElement = document.createElement('div');
            messageElement.className = 'message';
            messageElement.textContent = message;
            messagesElement.appendChild(messageElement);
            messagesElement.scrollTop = messagesElement.scrollHeight;
        }

        function toggleButtons(isConnected) {
            connectBtn.disabled = isConnected;
            disconnectBtn.disabled = !isConnected;
            sendBtn.disabled = !isConnected;
            messageInput.disabled = !isConnected;
        }
    </script>
</body>
</html>
```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.