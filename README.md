import socket
import threading

# Server ka configuration
HOST = '127.0.0.1'  # Localhost
PORT = 5000         # Port number

# Connected clients list
clients = []

def handle_client(client_socket):
    while True:
        try:
            message = client_socket.recv(1024)
            if not message:
                break
            # Broadcast to all clients
            for client in clients:
                if client != client_socket:
                    client.send(message)
        except:
            clients.remove(client_socket)
            client_socket.close()
            break

def main():
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.bind((HOST, PORT))
    server.listen(5)
    print("Server started. Waiting for connections...")

    while True:
        client_socket, addr = server.accept()
        print(f"Connection from {addr}")
        clients.append(client_socket)
        thread = threading.Thread(target=handle_client, args=(client_socket,))
        thread.start()

if __name__ == "__main__":
    main()
    
import pygame
import socket
import threading

# Initialize Pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Metro Rail Multiplayer")

# Colors
WHITE = (255, 255, 255)
BLUE = (0, 0, 255)
RED = (255, 0, 0)

# Player positions
player_positions = {"player1": [100, 300], "player2": [700, 300]}

# Network setup
HOST = '127.0.0.1'
PORT = 5000

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect((HOST, PORT))

# Current player ID
player_id = None

def receive_data():
    global player_positions
    while True:
        try:
            data = client.recv(1024).decode()
            player_positions = eval(data)  # Convert string to dict
        except:
            print("Connection lost!")
            break

# Start receiving data in a new thread
thread = threading.Thread(target=receive_data)
thread.start()

def send_data():
    client.send(str(player_positions).encode())

# Main game loop
running = True
clock = pygame.time.Clock()

while running:
    screen.fill(WHITE)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
            client.close()
            break

    keys = pygame.key.get_pressed()
    if keys[pygame.K_w] and player_positions["player1"][1] > 0:
        player_positions["player1"][1] -= 5
    if keys[pygame.K_s] and player_positions["player1"][1] < HEIGHT - 50:
        player_positions["player1"][1] += 5

    # Send updated positions to the server
    send_data()

    # Draw players
    pygame.draw.rect(screen, BLUE, (*player_positions["player1"], 50, 50))
    pygame.draw.rect(screen, RED, (*player_positions["player2"], 50, 50))

    pygame.display.flip()
    clock.tick(30)

pygame.quit()

<!---
tidakev993/tidakev993 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
