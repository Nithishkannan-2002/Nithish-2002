#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>

#define MAX_CMD 100

// Function to execute the command
void executeCommand(char *cmd) {
    pid_t pid = fork();
    if (pid == 0) {
        // Child process
        char *args[MAX_CMD];
        char *token = strtok(cmd, " ");
        int i = 0;

        // Tokenize the command into individual arguments
        while (token != NULL) {
            args[i++] = token;
            token = strtok(NULL, " ");
        }
        args[i] = NULL;  // NULL-terminate the arguments list
        
        execvp(args[0], args);  // Execute the command
        perror("execvp failed"); // If execvp fails
        exit(1);
    } else if (pid < 0) {
        // If fork fails
        perror("Fork failed");
        exit(1);
    } else {
        // Parent process waits for the child to finish
        wait(NULL);
    }
}

int main() {
    char cmd[MAX_CMD];
    
    while (1) {
        printf("my_shell> ");
        fgets(cmd, MAX_CMD, stdin);
        
        // Remove the newline character
        cmd[strcspn(cmd, "\n")] = 0;
        
        // If the user types "exit", break out of the loop
        if (strcmp(cmd, "exit") == 0) {
            break;
        }

        // Execute the command
        executeCommand(cmd);
    }

    return 0;
}

