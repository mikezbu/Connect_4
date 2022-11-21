# Mike Bu's Connect 4 Game

#importing different projects
import numpy as np  #numpy used for setting up arrays
import pygame   #pygame used for setting up video games
import sys     #sys used for settings
import math     #math used for arithmetic calculations

#creating colors from variable names
BLUE = (0, 0, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
YELLOW = (255, 255, 0)

#create size of board
ROW_COUNT = 6
COLUMN_COUNT = 7

#create pixel size of squares
SQUARESIZE = 100

#function that creates the board graphics interface
def create_board():
    board = np.zeros((ROW_COUNT, COLUMN_COUNT)) #np is numpy
    return board

#function that defines which column/row the piece lands in
def drop_piece(board, row, col, piece): #uses these four variables to drop a piece
    board[row][col] = piece #board pulls row and col from piece

#function that prevents a piece drop in a full column
def is_valid_location(board, col):  #uses these two variables to drop a piece
    return board[ROW_COUNT - 1][col] == 0   #pulls the row and column of the space that has a legal drop and makes it an open space (0)

#function that calculates the next open row on the board
def get_next_open_row(board, col):
    for r in range(ROW_COUNT):
        if board[r][col] == 0: #checks to see if space has piece in it
            return r

#function that flips the matrix so that the pieces fall on the bottom
def print_board(board):
    print(np.flip(board, 0))

#function that decides whether there is a winning move
def winning_move(board, piece):
    # Check horizontal locations for win
    for c in range(COLUMN_COUNT - 3):
        for r in range(ROW_COUNT):
            if board[r][c] == piece and board[r][c + 1] == piece and board[r][c + 2] == piece and board[r][c + 3] == piece: #checks if all the pieces are horizontally next to one another
                return True

    # Check vertical locations for win
    for c in range(COLUMN_COUNT):
        for r in range(ROW_COUNT - 3):
            if board[r][c] == piece and board[r + 1][c] == piece and board[r + 2][c] == piece and board[r + 3][c] == piece: #checks if all the pieces are vertically next to one another
                return True

    # Check positively sloped diagonals
    for c in range(COLUMN_COUNT - 3):
        for r in range(ROW_COUNT - 3):
            if board[r][c] == piece and board[r + 1][c + 1] == piece and board[r + 2][c + 2] == piece and board[r + 3][c + 3] == piece:
                return True

    # Check negatively sloped diagonals
    for c in range(COLUMN_COUNT - 3):
        for r in range(3, ROW_COUNT):
            if board[r][c] == piece and board[r - 1][c + 1] == piece and board[r - 2][c + 2] == piece and board[r - 3][c + 3] == piece:
                return True

#function that draws the board background with holes
def draw_board(board):

    for c in range(COLUMN_COUNT):
        for r in range(ROW_COUNT):
            pygame.draw.rect(screen, BLUE, (c * SQUARESIZE, r * SQUARESIZE + SQUARESIZE, SQUARESIZE, SQUARESIZE))
            pygame.draw.circle(screen, BLACK, (int(c * SQUARESIZE + SQUARESIZE / 2), int(r * SQUARESIZE + SQUARESIZE + SQUARESIZE / 2)), RADIUS)

    for c in range(COLUMN_COUNT):
        for r in range(ROW_COUNT):
            if board[r][c] == 1:
                pygame.draw.circle(screen, RED, (int(c * SQUARESIZE + SQUARESIZE / 2), height - int(r * SQUARESIZE + SQUARESIZE / 2)), RADIUS)
            elif board[r][c] == 2:
                pygame.draw.circle(screen, YELLOW, (int(c * SQUARESIZE + SQUARESIZE / 2), height - int(r * SQUARESIZE + SQUARESIZE / 2)), RADIUS)
    pygame.display.update()

#set initial conditions for board
board = create_board()
print_board(board)
game_over = False
turn = 0

pygame.init()
#set width and height for board
width = COLUMN_COUNT * SQUARESIZE
height = (ROW_COUNT + 1) * SQUARESIZE

#set initial conditions for size
size = (width, height)

#set radius of space square size
RADIUS = int(SQUARESIZE/2 - 5)

#set screen to display size
screen = pygame.display.set_mode(size)
draw_board(board)
pygame.display.update()

myfont = pygame.font.SysFont("monospace", 75)

while not game_over:

    for event in pygame.event.get():
        if event.type == pygame.QUIT:   #if hitting quit
            sys.exit()

        if event.type == pygame.MOUSEMOTION:    #if doing mouse motion different from other
            pygame.draw.rect(screen, BLACK, (0, 0, width, SQUARESIZE))
            posx = event.pos[0] #pos starts at space 0
            if turn == 0:
                pygame.draw.circle(screen, RED, (posx, int(SQUARESIZE / 2)), RADIUS) #draws red piece to drop if red's turn
            else:
                pygame.draw.circle(screen, YELLOW, (posx, int(SQUARESIZE / 2)), RADIUS) #draws yellow piece to drop if yellow's turn
        pygame.display.update()

        if event.type == pygame.MOUSEBUTTONDOWN:    #draw pygame mousebutton
            pygame.draw.rect(screen, BLACK, (0, 0, width, SQUARESIZE))
            # print(event.pos)
            # Ask for Player 1 Input
            if turn == 0:
                posx = event.pos[0] #pos is mouse cursor location
                col = int(math.floor(posx / SQUARESIZE))

                if is_valid_location(board, col):   #draw
                    row = get_next_open_row(board, col)
                    drop_piece(board, row, col, 1)

                    if winning_move(board, 1):
                        label = myfont.render("Player 1 wins!", True, RED)
                        screen.blit(label, (40, 10))
                        game_over = True


            # # Ask for Player 2 Input
            else:
                posx = event.pos[0]
                col = int(math.floor(posx / SQUARESIZE))

                if is_valid_location(board, col):
                    row = get_next_open_row(board, col)
                    drop_piece(board, row, col, 2)

                    if winning_move(board, 2):
                        label = myfont.render("Player 2 wins!", True, YELLOW)
                        screen.blit(label, (40, 10))
                        game_over = True

            print_board(board)
            draw_board(board)

            turn += 1
            turn = turn % 2

            if game_over:
                pygame.time.wait(5000)