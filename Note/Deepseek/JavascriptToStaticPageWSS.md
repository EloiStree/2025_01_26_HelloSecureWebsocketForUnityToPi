To create a JavaScript connection to a WebSocket Secure (WSS) server using a given certificate in a static page (without Node.js), you typically don't need to handle the certificate directly in the client-side code. The browser will handle the SSL/TLS handshake automatically when you connect to a `wss://` URL.

However, if you need to ensure that the certificate is trusted or if you have a self-signed certificate, you would typically configure the server to serve the certificate correctly, and the browser will handle the rest.

Here’s a basic example of how to connect to a WSS server using JavaScript in a static HTML page:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebSocket Connection</title>
</head>
<body>
    <h1>WebSocket Connection</h1>
    <div id="output"></div>

    <script>
        // WebSocket server URL
        const wsUrl = 'wss://your-websocket-server.com';

        // Create a new WebSocket connection
        const socket = new WebSocket(wsUrl);

        // Handle connection open
        socket.onopen = function(event) {
            console.log('WebSocket connection established.');
            document.getElementById('output').innerHTML += '<p>Connected to WebSocket server.</p>';
        };

        // Handle incoming messages
        socket.onmessage = function(event) {
            console.log('Message from server:', event.data);
            document.getElementById('output').innerHTML += `<p>Message from server: ${event.data}</p>`;
        };

        // Handle connection close
        socket.onclose = function(event) {
            console.log('WebSocket connection closed.');
            document.getElementById('output').innerHTML += '<p>Disconnected from WebSocket server.</p>';
        };

        // Handle errors
        socket.onerror = function(error) {
            console.error('WebSocket error:', error);
            document.getElementById('output').innerHTML += '<p>WebSocket error occurred.</p>';
        };

        // Send a message to the server
        function sendMessage(message) {
            if (socket.readyState === WebSocket.OPEN) {
                socket.send(message);
            } else {
                console.error('WebSocket is not open.');
            }
        }

        // Example: Send a message after 2 seconds
        setTimeout(() => {
            sendMessage('Hello, Server!');
        }, 2000);
    </script>
</body>
</html>
```

### Explanation:
1. **WebSocket URL**: Replace `'wss://your-websocket-server.com'` with the actual URL of your WebSocket server.

2. **WebSocket Object**: The `WebSocket` object is created with the `wss://` URL, which indicates a secure WebSocket connection.

3. **Event Handlers**:
   - `onopen`: Triggered when the connection is successfully established.
   - `onmessage`: Triggered when a message is received from the server.
   - `onclose`: Triggered when the connection is closed.
   - `onerror`: Triggered when an error occurs.

4. **Sending Messages**: The `sendMessage` function sends a message to the server if the connection is open.

### Important Notes:
- **Certificate Handling**: The browser will automatically handle the SSL/TLS certificate when connecting to a `wss://` URL. If you're using a self-signed certificate, you may need to ensure that the browser trusts it (e.g., by adding it to the browser's trusted certificates).
- **Security**: Ensure that your WebSocket server is properly configured to handle secure connections and that the certificate is valid and trusted by the browser.

This example assumes that the WebSocket server is already set up and running with a valid SSL/TLS certificate. If you need to handle custom certificate logic, that would typically be done on the server side, not in the client-side JavaScript.
