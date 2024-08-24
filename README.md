# SUDUKO.
#include <iostream>
#include <cstdlib>
#include <ctime>
#define UNASSIGNED 0
#define N 9

using namespace std;

// Function to check if it's safe to place a number in a particular position
bool isSafe(int grid[N][N], int row, int col, int num) {
    // Check if the number is not already in the current row
    for (int x = 0; x < N; x++)
        if (grid[row][x] == num)
            return false;

    // Check if the number is not already in the current column
    for (int x = 0; x < N; x++)
        if (grid[x][col] == num)
            return false;

    // Check if the number is not already in the 3x3 subgrid
    int startRow = row - row % 3, startCol = col - col % 3;
    for (int i = 0; i < 3; i++)
        for (int j = 0; j < 3; j++)
            if (grid[i + startRow][j + startCol] == num)
                return false;

    return true;
}

// Function to solve the Sudoku using backtracking
bool solveSudoku(int grid[N][N]) {
    int row, col;
    bool isEmpty = true;

    // Find an unassigned location (with value 0)
    for (row = 0; row < N; row++) {
        for (col = 0; col < N; col++) {
            if (grid[row][col] == UNASSIGNED) {
                isEmpty = false;
                break;
            }
        }
        if (!isEmpty) break;
    }

    // Puzzle is solved if no unassigned locations are left
    if (isEmpty)
        return true;

    // Try digits 1 to 9
    for (int num = 1; num <= 9; num++) {
        // If it's safe to place num, assign it
        if (isSafe(grid, row, col, num)) {
            grid[row][col] = num;

            // Recursively attempt to solve the rest of the grid
            if (solveSudoku(grid))
                return true;

            // If placing num didn't lead to a solution, reset and backtrack
            grid[row][col] = UNASSIGNED;
        }
    }

    return false; 
}

// Function to print the Sudoku grid
void printGrid(int grid[N][N]) {
    for (int row = 0; row < N; row++) {
        for (int col = 0; col < N; col++)
            cout << (grid[row][col] == UNASSIGNED ? "." : to_string(grid[row][col])) << " ";
        cout << endl;
    }
}

// Function to generate a Sudoku puzzle based on difficulty
void generatePuzzle(int grid[N][N], int difficulty) {
    // Seed random number generator
    srand(time(0));

    // Clear the grid
    for (int i = 0; i < N; i++)
        for (int j = 0; j < N; j++)
            grid[i][j] = UNASSIGNED;

    // Number of clues based on difficulty
    int clues;
    switch (difficulty) {
        case 1: clues = 40; break; // Easy
        case 2: clues = 30; break; // Medium
        case 3: clues = 20; break; // Hard
        default: clues = 30; break; // Default to Medium
    }

    // Randomly place clues in the grid
    for (int i = 0; i < clues; i++) {
        int row = rand() % N;
        int col = rand() % N;
        int num = rand() % N + 1;

        while (grid[row][col] != UNASSIGNED || !isSafe(grid, row, col, num)) {
            row = rand() % N;
            col = rand() % N;
            num = rand() % N + 1;
        }

        grid[row][col] = num;
    }
}

// Function to get user input and update the grid
void userInput(int grid[N][N]) {
    int row, col, num;
    char choice;

    while (true) {
        cout << "Current Sudoku grid:" << endl;
        printGrid(grid);
        
        cout << "Enter the row (1-9), column (1-9), and number (1-9) or '0' to exit: ";
        cin >> row;
        if (row == 0) break;
        cin >> col >> num;

        if (row < 1 || row > N || col < 1 || col > N || num < 1 || num > N) {
            cout << "Invalid input. Please try again." << endl;
            continue;
        }

        row--; // Convert to 0-based index
        col--;

        if (isSafe(grid, row, col, num) || grid[row][col] == UNASSIGNED) {
            grid[row][col] = num;
        } else {
            cout << "Cannot place " << num << " at (" << row + 1 << ", " << col + 1 << ")" << endl;
        }
    }
}

// Main function
int main() {
    int grid[N][N];
    int difficulty;

    cout << "Select difficulty level (1: Easy, 2: Medium, 3: Hard): ";
    cin >> difficulty;

    // Generate the Sudoku puzzle based on the selected difficulty
    generatePuzzle(grid, difficulty);

    cout << "Generated Sudoku Puzzle:" << endl;
    printGrid(grid);

    userInput(grid);

    cout << "Trying to solve the Sudoku Puzzle..." << endl;
    if (solveSudoku(grid)) {
        cout << "Solved Sudoku Puzzle:" << endl;
        printGrid(grid);
    } else {
        cout << "No solution exists" << endl;
    }

    return 0;
}
