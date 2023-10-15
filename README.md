# SudokuGame
#include <iostream>
#include <vector>

const int N = 9;
const int UNASSIGNED = 0;

class Sudoku {
public:
    Sudoku() : grid(N, std::vector<int>(N, UNASSIGNED)) {}

    void printGrid() {
        for (int row = 0; row < N; row++) {
            for (int col = 0; col < N; col++) {
                std::cout << grid[row][col] << " ";
            }
            std::cout << std::endl;
        }
    }

    void setInitialValues() {
        initializeGrid();
    }

    bool isSafe(int row, int col, int num) {
        return !usedInRow(row, num) && !usedInCol(col, num) && !usedInBox(row - row % 3, col - col % 3, num);
    }

    bool solve() {
        int row, col;

        if (!findUnassignedLocation(row, col))
            return true;

        for (int num = 1; num <= N; num++) {
            if (isSafe(row, col, num)) {
                grid[row][col] = num;
                if (solve())
                    return true;
                grid[row][col] = UNASSIGNED; // Backtrack
            }
        }
        return false;
    }

protected:
    std::vector<std::vector<int>> grid;

    virtual void initializeGrid() = 0;

private:
    bool findUnassignedLocation(int& row, int& col) {
        for (row = 0; row < N; row++) {
            for (col = 0; col < N; col++) {
                if (grid[row][col] == UNASSIGNED)
                    return true;
            }
        }
        return false;
    }

    bool usedInRow(int row, int num) {
        for (int col = 0; col < N; col++) {
            if (grid[row][col] == num)
                return true;
        }
        return false;
    }

    bool usedInCol(int col, int num) {
        for (int row = 0; row < N; row++) {
            if (grid[row][col] == num)
                return true;
        }
        return false;
    }

    bool usedInBox(int boxStartRow, int boxStartCol, int num) {
        for (int row = 0; row < 3; row++) {
            for (int col = 0; col < 3; col++) {
                if (grid[row + boxStartRow][col + boxStartCol] == num)
                    return true;
            }
        }
        return false;
    }
};

class EasySudoku : public Sudoku {
protected:
    void initializeGrid() override {
        // Initialize the Sudoku grid with an easy puzzle
        grid = {
            {0, 0, 0, 0, 0, 0, 2, 0, 9},
            {0, 0, 5, 6, 0, 0, 0, 7, 0},
            {9, 3, 8, 0, 5, 7, 4, 6, 0},
            {6, 0, 9, 0, 8, 0, 3, 2, 7},
            {0, 0, 7, 0, 0, 0, 6, 0, 0},
            {3, 8, 4, 0, 2, 0, 9, 0, 5},
            {0, 6, 2, 8, 9, 0, 1, 3, 4},
            {0, 9, 0, 0, 0, 1, 7, 0, 0},
            {8, 0, 1, 0, 0, 0, 0, 0, 0}
        };
    }
};

class MediumSudoku : public Sudoku {
protected:
    void initializeGrid() override {
        // Initialize the Sudoku grid with a medium puzzle
        grid = {
            {0, 0, 1, 6, 4, 7, 0, 0, 8},
            {4, 8, 0, 0, 1, 0, 7, 0, 2},
            {0, 0, 6, 9, 2, 0, 0, 1, 3},
            {8, 9, 0, 1, 6, 0, 0, 0, 5},
            {0, 0, 0, 7, 5, 9, 2, 8, 0},
            {6, 5, 0, 0, 8, 0, 0, 9, 0},
            {0, 0, 0, 0, 0, 0, 5, 3, 9},
            {7, 4, 3, 0, 9, 0, 0, 2, 0},
            {0, 0, 0, 2, 3, 6, 8, 0, 7}
        };
    }
};

class HardSudoku : public Sudoku {
protected:
    void initializeGrid() override {
        // Initialize the Sudoku grid with a hard puzzle
        grid = {
            {0, 0, 0, 0, 5, 0, 0, 0, 0},
            {0, 0, 0, 0, 0, 0, 0, 9, 6},
            {0, 7, 5, 0, 0, 0, 8, 0, 0},
            {0, 0, 0, 0, 6, 3, 0, 0, 2},
            {0, 0, 0, 0, 0, 0, 0, 4, 0},
            {0, 0, 6, 5, 0, 0, 0, 0, 8},
            {0, 0, 0, 0, 0, 0, 0, 1, 9},
            {5, 3, 0, 0, 0, 0, 0, 0, 0},
            {1, 0, 0, 4, 0, 0, 0, 0, 0}
        };
    }
};

int main() {
    int difficulty;
    std::cout << "Sudoku Solver" << std::endl;
    std::cout << "1. Easy" << std::endl;
    std::cout << "2. Medium" << std::endl;
    std::cout << "3. Hard" << std::endl;
    std::cout << "Enter the difficulty level (1/2/3): ";
    std::cin >> difficulty;

    Sudoku* sudoku;

    switch (difficulty) {
        case 1:
            sudoku = new EasySudoku();
            break;
        case 2:
            sudoku = new MediumSudoku();
            break;
        case 3:
            sudoku = new HardSudoku();
            break;
        default:
            std::cout << "Invalid choice. Exiting." << std::endl;
            return 0;
    }

    sudoku->setInitialValues();

    std::cout << "Sudoku Puzzle (0 represents empty cells):" << std::endl;
    sudoku->printGrid();

    if (sudoku->solve()) {
        std::cout << "Solved Sudoku:" << std::endl;
        sudoku->printGrid();
    } else {
        std::cout << "No solution exists." << std::endl;
    }

    delete sudoku; // Free allocated memory

    return 0;
}
