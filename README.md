I AM NOT A WEB DEVELOPER AND I AM DONT KNOW WHAT I AM DONIG HERE:

![image](https://github.com/user-attachments/assets/5ca14c97-3739-46f7-8e1e-999f4ff30038)

I AM JUST A BUY THAT NEED TO USE WSS AND TRY THE DUMB EASY WAY TO HAVE SOME

-----------------------------------------


# Hello Secure WebSocket for Unity to Pi

Once again, I was blocked by a school's network security while using WebSockets. So, let's explore how to create a secure WebSocket server between a hosted Raspberry Pi and Unity3D.

Unity3D, Raspberry Pi, and WebSockets aren't the best of friends ^^.  
But it's the only cost-effective solution I found for hosting a mini server.

---

## Setting Up the Raspberry Pi

I have a Raspberry Pi hosted here:  
- [Best Hosting](https://best-hosting.cz/en/login)

To set up a secure WebSocket server, I had to create a private key and a certificate file. This is done by running the following command in the Raspberry Pi terminal. I executed this in the folder where I store my keys: `/token`.

```bash
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes \
-subj "/CN=193.150.14.47"
```

This command generates the following files:
- `/token/cert.pem` (certificate file)
- `/token/key.pem` (private key file)

**Important:**  
The private key (`key.pem`) must be kept secret, while the certificate (`cert.pem`) is shared with the client.

---

## Server Code (Python)

Here is the code for setting up a secure WebSocket server:

```python
import ssl
import websockets

# Paths to the certificate and private key
path_ssl_cert = "/token/cert.pem"
path_ssl_private_key = "/token/key.pem"

# Set up SSL context
ssl_context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
ssl_context.load_cert_chain(certfile=path_ssl_cert, keyfile=path_ssl_private_key)

# Debug: Display parts of the certificate and private key
with open(path_ssl_cert, "r") as file:
    data = file.read()
    print("Certificate Preview:", data[:50])

with open(path_ssl_private_key, "r") as file:
    data = file.read()
    print("Private Key Preview:", data[:50])

# Define WebSocket server (replace ws_handler, IP, and port with your setup)
async def start_server():
    ws_server = await websockets.serve(ws_handler, "193.150.14.47", 8765, ssl=ssl_context)
    await ws_server.wait_closed()
```

---

## Client Code (Python)

Here’s how to connect to the secure WebSocket server using Python:

```python
import ssl
import websockets

uri = "wss://193.150.14.47:8765"
cert_file_path = "/path/to/cert.pem"

# Set up SSL context
ssl_context = ssl.SSLContext(ssl.PROTOCOL_TLS_CLIENT)
ssl_context.check_hostname = False
ssl_context.load_verify_locations(cert_file_path)

# Debug: Verify the certificate content
with open(cert_file_path, "r") as file:
    cert_content = file.read()
    print("Certificate Content:", cert_content[:50])

# Connect to the WebSocket server
async def connect_to_server(uri):
    while True:
        try:
            async with websockets.connect(uri, ssl=ssl_context) as websocket:
                while True:
                    response = await websocket.recv()
                    print("Received:", response)
        except Exception as e:
            print("Connection error:", e)
```

---

## Observations

During a recent game jam, this setup allowed me to successfully connect my computer to a Raspberry Pi server hosted online without being blocked by network restrictions.  

---

## Next Steps: Running on Unity3D and JavaScript

### JavaScript  
JavaScript natively supports PEM files, so using the `cert.pem` file should work seamlessly. I'll explore this in more detail later.

### Unity3D  
Unity3D uses a different certificate format (`.pfx`). I'll need to figure out how to convert the PEM file to a PFX format and implement the connection in C#. This will require further research.

---

### Client Repository  
The client code I used during the game jam is available here:  
[https://github.com/EloiStree/2024_12_17_SingleTunnelMegaMaskWSUDP.git](https://github.com/EloiStree/2024_12_17_SingleTunnelMegaMaskWSUDP.git)

---

That's as far as I could get while on the train back from the Global Game Jam. I hope this write-up helps you set up your secure WebSocket connection. Good luck!

