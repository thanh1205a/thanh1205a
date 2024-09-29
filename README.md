#include <iostream>
using namespace std;

#define PLAYER 1 // Người chơi (O)
#define AI 2     // Máy (X)

// Hàm in ra bàn cờ
void printBoard(char board[3][3]) {
    cout << "\n";
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            cout << board[i][j] << " ";
        }
        cout << "\n";
    }
}

// Hàm kiểm tra xem có ai thắng chưa
int checkWinner(char board[3][3]) {
    // Kiểm tra hàng
    for (int row = 0; row < 3; row++) {
        if (board[row][0] == board[row][1] && board[row][1] == board[row][2]) {
            if (board[row][0] == 'X') return +10;
            else if (board[row][0] == 'O') return -10;
        }
    }

    // Kiểm tra cột
    for (int col = 0; col < 3; col++) {
        if (board[0][col] == board[1][col] && board[1][col] == board[2][col]) {
            if (board[0][col] == 'X') return +10;
            else if (board[0][col] == 'O') return -10;
        }
    }

    // Kiểm tra đường chéo
    if (board[0][0] == board[1][1] && board[1][1] == board[2][2]) {
        if (board[0][0] == 'X') return +10;
        else if (board[0][0] == 'O') return -10;
    }

    if (board[0][2] == board[1][1] && board[1][1] == board[2][0]) {
        if (board[0][2] == 'X') return +10;
        else if (board[0][2] == 'O') return -10;
    }

    return 0;
}

// Hàm kiểm tra xem bàn cờ có đầy chưa
bool isMovesLeft(char board[3][3]) {
    for (int i = 0; i < 3; i++)
        for (int j = 0; j < 3; j++)
            if (board[i][j] == '_')
                return true;
    return false;
}

// Thuật toán Minimax
int minimax(char board[3][3], int depth, bool isMax) {
    int score = checkWinner(board);

    // Nếu AI thắng
    if (score == 10)
        return score - depth;

    // Nếu người chơi thắng
    if (score == -10)
        return score + depth;

    // Hòa
    if (isMovesLeft(board) == false)
        return 0;

    // Nếu là lượt AI
    if (isMax) {
        int best = -1000;
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (board[i][j] == '_') {
                    board[i][j] = 'X';
                    best = max(best, minimax(board, depth + 1, !isMax));
                    board[i][j] = '_';
                }
            }
        }
        return best;
    }
    // Nếu là lượt người chơi
    else {
        int best = 1000;
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (board[i][j] == '_') {
                    board[i][j] = 'O';
                    best = min(best, minimax(board, depth + 1, !isMax));
                    board[i][j] = '_';
                }
            }
        }
        return best;
    }
}

// Hàm tìm nước đi tốt nhất cho AI
pair<int, int> findBestMove(char board[3][3]) {
    int bestVal = -1000;
    pair<int, int> bestMove = {-1, -1};

    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (board[i][j] == '_') {
                board[i][j] = 'X';
                int moveVal = minimax(board, 0, false);
                board[i][j] = '_';
                if (moveVal > bestVal) {
                    bestMove = {i, j};
                    bestVal = moveVal;
                }
            }
        }
    }
    return bestMove;
}

// Hàm chính
int main() {
    char board[3][3] = {
        { '_', '_', '_' },
        { '_', '_', '_' },
        { '_', '_', '_' }
    };

    while (true) {
        int x, y;
        cout << "Bàn cờ hiện tại:\n";
        printBoard(board);

        if (checkWinner(board) == 10) {
            cout << "Máy thắng!\n";
            break;
        }
        if (checkWinner(board) == -10) {
            cout << "Người chơi thắng!\n";
            break;
        }
        if (!isMovesLeft(board)) {
            cout << "Hòa!\n";
            break;
        }

        cout << "Người chơi nhập nước đi (hàng cột): ";
        cin >> x >> y;

        if (board[x][y] == '_') {
            board[x][y] = 'O';
        } else {
            cout << "Vị trí này đã được chọn, chọn vị trí khác!\n";
            continue;
        }

        // Máy tìm nước đi tốt nhất
        pair<int, int> bestMove = findBestMove(board);
        board[bestMove.first][bestMove.second] = 'X';
    }

    return 0;
}
