#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/wait.h>

#define PORT 8080
#define MAX_CLIENTS 5

void handle_client(int client_socket) {
    char buffer[1024];
    char response[] = "Hello from the telnet server!";
    
    memset(buffer, 0, sizeof(buffer));
    read(client_socket, buffer, sizeof(buffer));
    write(client_socket, response, strlen(response));
    
    close(client_socket);
}

int main() {
    int server_socket, client_socket;
    struct sockaddr_in server_addr, client_addr;
    socklen_t client_len;
    
    server_socket = socket(AF_INET, SOCK_STREAM, 0);
    if (server_socket < 0) {
        perror("Error in socket");
        exit(EXIT_FAILURE);
    }
    
    server_addr.sin_family = AF_INET;
    server_addr.sin_addr.s_addr = INADDR_ANY;
    server_addr.sin_port = htons(PORT);
    
    if (bind(server_socket, (struct sockaddr *)&server_addr, sizeof(server_addr)) < 0) {
        perror("Error in bind");
        exit(EXIT_FAILURE);
    }
    
    if (listen(server_socket, MAX_CLIENTS) < 0) {
        perror("Error in listen");
        exit(EXIT_FAILURE);
    }
    
    printf("Server is running on port %d\n", PORT);
    
    while (1) {
        client_len = sizeof(client_addr);
        client_socket = accept(server_socket, (struct sockaddr *)&client_addr, &client_len);
        if (client_socket < 0) {
            perror("Error in accept");
            exit(EXIT_FAILURE);
        }
        
        printf("Connection from %s:%d\n", inet_ntoa(client_addr.sin_addr), ntohs(client_addr.sin_port));
        
        pid_t pid = fork();
        if (pid < 0) {
            perror("Error in fork");
            exit(EXIT_FAILURE);
        }
        else if (pid == 0) {
            close(server_socket);
            handle_client(client_socket);
            exit(EXIT_SUCCESS);
        }
        else {
            close(client_socket);
            wait(NULL);
        }
    }
    
    close(server_socket);
    
    return 0;
}
