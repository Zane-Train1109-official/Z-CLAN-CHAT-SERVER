# Z-CLAN-CHAT-SERVER
The Official Z CLAN SERVER NETWORK
import socket
import threading

# Constants
HOST = '0.0.0.0'  # Listen on all available network interfaces
PORT = 12345  # Choose a port for your chat server

# Data structure to store connected clients
clients = []

def broadcast(message, client_socket):
    for client in clients:
        if client != client_socket:
            try:
                client.send(message)
            except:
                # If sending the message fails, assume the client is disconnected
                client.close()
                clients.remove(client)

def handle_client(client_socket):
    while True:
        try:
            message = client_socket.recv(1024)
            if not message:
                break
            # Broadcast the message to all clients
            broadcast(message, client_socket)
        except:
            break

def main():
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.bind((HOST, PORT))
    server_socket.listen(5)
    print(f"Chat server is listening on {HOST}:{PORT}")

    while True:
        client_socket, addr = server_socket.accept()
        clients.append(client_socket)
        print(f"Connection from {addr} established.")
        client_handler = threading.Thread(target=handle_client, args=(client_socket,))
        client_handler.start()

if __name__ == '__main__':
    main()
