import random
import os

# Function to initialize a 4x4 grid
def initialize_game():
    grid = [[0] * 4 for _ in range(4)]
    add_new_tile(grid)
    add_new_tile(grid)
    return grid

# Function to add a new 2 or 4 tile in an empty spot
def add_new_tile(grid):
    empty_cells = [(i, j) for i in range(4) for j in range(4) if grid[i][j] == 0]
    if not empty_cells:
        return
    i, j = random.choice(empty_cells)
    grid[i][j] = 2 if random.random() < 0.9 else 4

# Function to compress the grid 
def compress(grid):
    new_grid = [[0] * 4 for _ in range(4)]
    for i in range(4):
        pos = 0
        for j in range(4):
            if grid[i][j] != 0:
                new_grid[i][pos] = grid[i][j]
                pos += 1
    return new_grid

# Function to merge tiles in a row
def merge(grid):
    for i in range(4):
        for j in range(3):
            if grid[i][j] == grid[i][j+1] and grid[i][j] != 0:
                grid[i][j] *= 2
                grid[i][j+1] = 0
    return grid

# Function to reverse the grid (used for right and down moves)
def reverse(grid):
    new_grid = []
    for i in range(4):
        new_grid.append(grid[i][::-1])
    return new_grid

# Function to transpose the grid (used for up and down moves)
def transpose(grid):
    new_grid = [[0] * 4 for _ in range(4)]
    for i in range(4):
        for j in range(4):
            new_grid[i][j] = grid[j][i]
    return new_grid

# Function to move the grid in a particular direction
def move_left(grid):
    new_grid = compress(grid)
    new_grid = merge(new_grid)
    new_grid = compress(new_grid)
    return new_grid

def move_right(grid):
    new_grid = reverse(grid)
    new_grid = move_left(new_grid)
    return reverse(new_grid)

def move_up(grid):
    new_grid = transpose(grid)
    new_grid = move_left(new_grid)
    return transpose(new_grid)

def move_down(grid):
    new_grid = transpose(grid)
    new_grid = move_right(new_grid)
    return transpose(new_grid)

# Function to check if there are moves left
def check_game_over(grid):
    for i in range(4):
        for j in range(4):
            if grid[i][j] == 0:
                return False
            if i < 3 and grid[i][j] == grid[i+1][j]:
                return False
            if j < 3 and grid[i][j] == grid[i][j+1]:
                return False
    return True

# Function to print the grid
def print_grid(grid):
    os.system('cls' if os.name == 'nt' else 'clear')
    for row in grid:
        print(f"{row[0]:<5} {row[1]:<5} {row[2]:<5} {row[3]:<5}")
    print()

# Main game function
def play_game():
    grid = initialize_game()
    print("Welcome to 2048! Use ABCD keys to move the tiles.")
    
    while True:
        print_grid(grid)
        
        # Get user input
        move = input("Make your move (ABCD): ").upper()
        
        if move not in 'ABCD':
            continue
        
        if move == 'A':
            new_grid = move_up(grid)
        elif move == 'B':
            new_grid = move_down(grid)
        elif move == 'C':
            new_grid = move_left(grid)
        elif move == 'D':
            new_grid = move_right(grid)
        
        if new_grid != grid:
            add_new_tile(new_grid)
            grid = new_grid
        
        if check_game_over(grid):
            print_grid(grid)
            print("Game Over!")
            break

# Start the game
play_game()
