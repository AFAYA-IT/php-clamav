ClamAV uses a Unix domain socket for communication between its components. For building a similar socket server program for your file classification command-line program, you can follow these steps:

1. **Create the Socket Server Script**:
   Create a shell script named `socket_server.sh` on Alpine Linux to handle incoming socket connections and execute commands based on the received data. Here's an example:

   ```bash
   #!/bin/sh

   # Define the socket file path
   SOCKET_FILE="/tmp/file_classification.sock"

   # Clean up any existing socket file
   rm -f "$SOCKET_FILE"

   # Start the socket server
   socat UNIX-LISTEN:"$SOCKET_FILE",mode=777,fork EXEC:"./handle_command.sh"
   ```

2. **Handle Commands Script**:
   Create another shell script named `handle_command.sh` to handle different commands sent to the socket server:

   ```bash
   #!/bin/sh

   while read command; do
       case "$command" in
           "DOWNLOAD "*)
               # Extract the file URL from the command
               file_url=$(echo "$command" | cut -d' ' -f2-)

               # Download the file using curl or wget
               # Example: curl -O "$file_url"
               echo "File downloaded successfully!"
               ;;
           "INFO")
               # Return the socket server version
               echo "Socket server version 1.0"
               ;;
           *)
               echo "Unknown command: $command"
               ;;
       esac
   done
   ```

3. **Make Scripts Executable**:
   Make both scripts executable:
   ```bash
   chmod +x socket_server.sh handle_command.sh
   ```

4. **Run the Socket Server**:
   Run the socket server script to start listening for incoming connections:
   ```bash
   ./socket_server.sh &
   ```

5. **Client Communication**:
   In your PHP socket client, connect to the socket server using the server IP (`153.254.15.47`) and the socket file path (`/tmp/file_classification.sock`).

6. **Handling Commands in PHP**:
   Modify your PHP socket client to send commands to the server and process responses accordingly.

This setup enables communication between your socket server and PHP socket client over a Unix domain socket (`/tmp/file_classification.sock`) on the server IP `153.254.15.47`. The socket server script (`socket_server.sh`) uses `socat` to handle incoming connections and execute commands via `handle_command.sh`, which interprets the received commands and performs corresponding actions.