## Broadcast Chat

### How to Run

1.  **Start the Server:**
    Open a terminal and navigate to the project directory. Run the following command:

    ```bash
    cargo run --bin server
    ```

    The server will start and listen for incoming client connections on `127.0.0.1:8080`.

2.  **Start the Clients:**
    Open new terminal windows for each client we want to run. Navigate to the project directory in each terminal and run:
    ```bash
    cargo run --bin client
    ```
    We can run this command multiple times to simulate multiple clients. Each client will connect to the server.

### Client Interaction

Once the server is running and we have started a few clients (e.g., three clients as suggested), we can type messages into any of the client terminals.

![How It Runs](./images/1.png)

**What happens:**
When we type a message in one client and press Enter, that message is sent to the server. The server then broadcasts this message to all other connected clients. This means every client (except the one that sent the message initially) will display the message in its terminal. This demonstrates the "broadcast" nature of the chat application.

### Modifying the WebSocket Port

The application uses a WebSocket connection for communication between the server and clients. The default port in the original code is `2000`.

To change the port to `8080`, we need to modify the following files:

1.  **`src/bin/server.rs`**:

    - Update the `TcpListener::bind` address to `"127.0.0.1:8080"`.
    - Optionally, update the `println!` message to reflect the new port `8080`.

    ```rust
    // ... inside main fn ...
    let listener = TcpListener::bind("127.0.0.1:8080").await?;
    println!("listening on port 8080");
    // ... rest of the code ...
    ```

2.  **`src/bin/client.rs`**:

    - Update the URI in `ClientBuilder::from_uri` to `"ws://127.0.0.1:8080"`.

    ```rust
    // ... inside main fn ...
    let (mut ws_stream, _) =
        ClientBuilder::from_uri(Uri::from_static("ws://127.0.0.1:8080"))
            .connect()
            .await?;
    // ... rest of the code ...
    ```

The WebSocket protocol (`ws://`) is explicitly defined in the client-side code (`src/bin/client.rs`) when constructing the URI for the connection. The server side, while not explicitly stating `ws://` in the `TcpListener::bind` function, handles the WebSocket handshake and subsequent communication once a TCP connection is established on the specified port. Both sides need to agree on the same port number for the connection to be successful.

After making these changes, we can run the server and clients as described previously, and they will now communicate over port `8080`.

![Modifying the WebSocket Port](/images/2.png)

If we only change the port on one side, either the client or the server, the connection will fail because they will be trying to communicate on different ports. Both the client and server must use the same port number for the WebSocket connection to work properly.
