#include <stdio.h>
#include <conio.h>
#include <windows.h>
#include <stdlib.h>
#include <string.h>

#define WIDTH 15
#define HEIGHT 10
#define MAX_HIGH_SCORES 5
#define MAX_USERS 10
#define USERNAME_LEN 20
#define PASSWORD_LEN 20  // Define max password length

typedef struct {
    char username[USERNAME_LEN];
    char password[PASSWORD_LEN];  // Add password field
    int highScores[MAX_HIGH_SCORES];
} User;
// Global variables
User users[MAX_USERS];
int userCount = 0;
User *currentUser;
char grid[HEIGHT][WIDTH];
int pacmanX, pacmanY, ghostX, ghostY, score = 0, level = 1, foodRemaining;

// Function prototypes
void loginScreen();
void initializeLevel();
void displayGrid();
void movePacman(int key);
void moveGhost();
int checkCollision();
void resetGame();
void updateHighScores();
void displayHighScores();
void drawMaze(int level);
User *findUser(const char *username);
void addUser(const char *username, const char *password);
void getPassword(char *password);

void loginScreen() {
    char username[USERNAME_LEN];
    char password[PASSWORD_LEN];

    printf("Enter your username: ");
    scanf("%s", username);

    currentUser = findUser(username);

    if (currentUser) {
        // Existing user: Ask for password
        printf("Enter your password: ");
        scanf("%s", password);

        if (strcmp(currentUser->password, password) == 0) {
            printf("Login successful! Welcome back, %s!\n", username);
        } else {
            printf("Incorrect password! Try again.\n");
            exit(1);
        }
    } else {
        // New user: Set up a password
        printf("Welcome, %s! Creating a new account.\n", username);
        printf("Set a password: ");
        getPassword(password);  // Secure password input

        addUser(username, password);  // ✅ Correct function call
        currentUser = &users[userCount - 1];  // Set new user as current user
    }
}

void addUser(const char *username, const char *password) {
    if (userCount >= MAX_USERS) {
        printf("User limit reached! Cannot add more users.\n");
        return;
    }

    strcpy(users[userCount].username, username);
    strcpy(users[userCount].password, password);  // Store password

    for (int i = 0; i < MAX_HIGH_SCORES; i++) {
        users[userCount].highScores[i] = 0;
    }

    userCount++;
}

void getPassword(char *password) {
    int i = 0;
    char ch;
    while (1) {
        ch = _getch();  // Read character
        if (ch == 13) break;  // Stop if Enter is pressed
        if (ch == 8 && i > 0) { // Handle Backspace
            printf("\b \b");
            i--;
        } else {
            password[i++] = ch;
            printf("*");  // Show '*' instead of the character
        }
    }
    password[i] = '\0';  // Null-terminate string
}
User *findUser(const char *username) {
    for (int i = 0; i < userCount; i++) {
        if (strcmp(users[i].username, username) == 0) {
            return &users[i];  // Return pointer to existing user
        }
    }
    return NULL;  // User not found
}



void initializeLevel() {
    drawMaze(level); // Use predefined maze layouts

    // Place Pac-Man
    pacmanX = HEIGHT / 2;
    pacmanY = WIDTH / 2;
    while (grid[pacmanX][pacmanY] != '.') { // Ensure Pac-Man starts in an open space
        pacmanX = rand() % (HEIGHT - 2) + 1;
        pacmanY = rand() % (WIDTH - 2) + 1;
    }
    grid[pacmanX][pacmanY] = 'P';

    // Place Ghost
    ghostX = 1;
    ghostY = 1;
    grid[ghostX][ghostY] = 'G';

    // Count remaining food
    foodRemaining = 0;
    for (int i = 1; i < HEIGHT - 1; i++) {
        for (int j = 1; j < WIDTH - 1; j++) {
            if (grid[i][j] == '.') {
                foodRemaining++;
            }
        }
    }
}

void drawMaze(int level) {
    // Clear grid
    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            if (i == 0 || i == HEIGHT - 1 || j == 0 || j == WIDTH - 1) {
                grid[i][j] = '#'; // Outer walls
            } else {
                grid[i][j] = '.'; // Default food
            }
        }
    }

    // Define maze patterns
    if (level == 1) {
        for (int i = 2; i < HEIGHT - 2; i++) {
            grid[i][WIDTH / 2] = '#'; // Vertical wall
        }
    } else if (level == 2) {
        for (int i = 2; i < HEIGHT - 2; i++) {
            grid[i][WIDTH / 3] = '#'; // Vertical wall
            grid[i][2 * WIDTH / 3] = '#'; // Another vertical wall
        }
        for (int j = 2; j < WIDTH - 2; j++) {
            grid[HEIGHT / 2][j] = '#'; // Horizontal wall
        }
    } else if (level == 3) {
        for (int i = 1; i < HEIGHT - 1; i += 2) {
            for (int j = 1; j < WIDTH - 1; j += 2) {
                grid[i][j] = '#'; // Complex pattern
            }
        }
    }
}

void displayGrid() {
    system("cls");
    printf("User: %s\n", currentUser->username);
    printf("Score: %d\tLevel: %d\n", score, level);
    printf("Top 5 High Scores for %s:\n", currentUser->username);
    displayHighScores();

    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            printf("%c", grid[i][j]);
        }
        printf("\n");
    }
}

void movePacman(int key) {
    if (key == 0) return; // No input

    grid[pacmanX][pacmanY] = ' '; // Clear current position

    if (key == 72 && grid[pacmanX - 1][pacmanY] != '#') pacmanX--; // Up
    else if (key == 80 && grid[pacmanX + 1][pacmanY] != '#') pacmanX++; // Down
    else if (key == 75 && grid[pacmanX][pacmanY - 1] != '#') pacmanY--; // Left
    else if (key == 77 && grid[pacmanX][pacmanY + 1] != '#') pacmanY++; // Right

    if (grid[pacmanX][pacmanY] == '.') {
        score++;
        foodRemaining--;
    }

    grid[pacmanX][pacmanY] = 'P';
}

void moveGhost() {
    grid[ghostX][ghostY] = '.'; // Clear current position

    int direction = rand() % 4;
    if (direction == 0 && grid[ghostX - 1][ghostY] != '#') ghostX--;
    else if (direction == 1 && grid[ghostX + 1][ghostY] != '#') ghostX++;
    else if (direction == 2 && grid[ghostX][ghostY - 1] != '#') ghostY--;
    else if (direction == 3 && grid[ghostX][ghostY + 1] != '#') ghostY++;

    grid[ghostX][ghostY] = 'G';
}

int checkCollision() {
    return pacmanX == ghostX && pacmanY == ghostY;
}

void updateHighScores() {
    for (int i = 0; i < MAX_HIGH_SCORES; i++) {
        if (score > currentUser->highScores[i]) {
            for (int j = MAX_HIGH_SCORES - 1; j > i; j--) {
                currentUser->highScores[j] = currentUser->highScores[j - 1];
            }
            currentUser->highScores[i] = score;
            break;
        }
    }
}

void displayHighScores() {
    for (int i = 0; i < MAX_HIGH_SCORES; i++) {
        printf("%d. %d\n", i + 1, currentUser->highScores[i]);
    }
}

void resetGame() {
    updateHighScores();

    printf("Game Over! Final Score: %d\n", score);
    printf("Press Enter to restart or Escape to quit.\n");

    int key;
    while (1) {
        key = _getch();
        if (key == 13) {
            level = 1;
            score = 0;
            initializeLevel();
            return;
        } else if (key == 27) {
            exit(0);
        }
    }
}

int main() {
    srand(time(0));

    loginScreen();
    initializeLevel();

    while (1) {
        displayGrid();

        if (_kbhit()) {
            int key = _getch();
            movePacman(key);
        }

        moveGhost();

        if (checkCollision()) {
            resetGame();
        }

        if (foodRemaining == 0 || score >= 30) {
            printf("\nLevel %d complete! Press Enter to proceed.\n", level);
            if (_getch() == 13) {
                level++;
                if (level > 3) {
                    printf("\nCongratulations! You completed all levels!\n");
                    break;
                }
                initializeLevel();
            }
        }

        Sleep(100); // Faster response
    }

    return 0;
}
