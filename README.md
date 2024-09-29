# Snakes and Ladders Analysis Using Monte Carlo Simulation


## Table of Contents

1. Introduction
2. Snakes and Ladders Explanation
3. Methodology
4. Creating the Game
5. Monte Carlo Simulation
6. Analysis
7. Markov Matrix Approach
8. Multiple Player Game

IMG

### Introduction and Motivation
This project analyzes the game Snakes and Ladders, which originates all the way back to ancient India in the 2nd century CE. 
Now, my motivation for this project was inspired by a heated game of Monopoly between my friends and me that lasted nearly five hours. During this five-hour span, I noticed a couple of things. One being the fact that the game never ended no matter how many times we rolled the dice. And the second, more interestingly, was the fact that I felt that we were always landing on the same few squares and narrowly dodging others. This made me wonder if there is a statistical reasoning for this. Monopoly is an extremely complicated game with many variables like Chance cards, Jail squares, and repeated turns on doubles. Snakes and Ladders, on the other hand, is far simpler, which allows for some cool analysis that we can do.

### Snakes and Ladders Explanation
Snakes and Ladders is a rather simple game by nature. The game consists of a game board. The dimensions of the board vary, but most commonly, it is 10 x 10. On the board, there are various snakes and ladders starting and ending on two different squares. 

IMG

The game can be played with one or more players. Each player will roll a single die to determine how many squares the player moves. If a player lands on a snake, they must slide down the snake to the respective square it connects to. If a player lands on a ladder, the ladder will be taken upwards to the connecting square. The goal of the games is to reach the final square at the end of the board before the other players. 

### Methodology
From Wikipedia: "Monte Carlo methods, or Monte Carlo experiments, are a broad class of computational algorithms that rely on repeated random sampling to obtain numerical results. The underlying concept is to use randomness to solve problems that might be deterministic in principle."

Since a player's wins or losses in this game are purely determined by the dice rolls rather than any type of human decision-making, skill, or strategy, we can use this randomness to get an idea of how the game works. The random dice rolls and the unpredictable impact of the snakes and ladders make it challenging to get any insights analytically. By running a Monte Carlo simulation, we can model thousands of simulated game playthroughs, which allows us to draw insights into the data. 


### Creating the Game

```Python
#Creates a fair dice roller

def roll_dice():
    """
    Simulates a fair dice roll with
    Returns: Integer from 1 to 6
    """
    return random.randint(1,6)
```
```Python
#Creates a single game simulation
#9 ladders, 10 snakes
#Ladders on 1,4,9,21,28,36,51,71,80 (non-indexing)
#Snakes on 16, 47, 49, 56, 62, 64, 87, 93, 95, 98 (non-indexing)

def play_game_board():
    
    """
    Simulates a game of snakes and ladders
    Returns: player_position, turns, chutes_hit, ladders_hit, tracking_board_before, tracking_board_after
    """
    
    game_board = [37, 0, 0, 10, 0, 0, 0, 0, 22, 0,
                  0, 0, 0, 0, 0, -10, 0, 0, 0, 0,
                  21, 0, 0, 0, 0, 0, 0, 56, 0, 0,
                  0, 0, 0, 0, 0, 8, 0, 0, 0, 0, 
                  0, 0, 0, 0, 0, 0, -21, 0, -38, 0,
                  16, 0, 0, 0, 0, -3, 0, 0, 0, 0,
                  0, -43, 0, -4, 0, 0, 0, 0, 0, 0,
                  20, 0, 0, 0, 0, 0, 0 , 0, 0, 20,
                  0, 0, 0, 0, 0, 0, -63, 0, 0, 0,
                  0, 0, -20, 0 ,-20, 0, 0, -20, 0, 0]
    
    tracking_board_before = np.zeros(100)
    tracking_board_after = np.zeros(100)
    
    #Player position starts on -1 because it is on the 0th square on an actual board 
    #but since we are using Python indexing it still be -1
    player_position = -1
    turns = 0 
    chutes_hit = 0
    ladders_hit = 0

    while player_position < 99:
        #Moving the player after rolling the dice
        player_position += roll_dice()
        
        # Ensure player_position stays within bounds
        player_position = min(player_position, 99)
        
        
        #Creating a counter for each square on the board before chutes and ladders
        tracking_board_before[player_position] += 1

        #Counting the total number of chutes and ladders hit per game
        if game_board[player_position] > 0:
            ladders_hit += 1
        if game_board[player_position] < 0:
            chutes_hit += 1
        
        #Moving the player through any potential chutes and ladders
        player_position += game_board[player_position]
        
        #Creating counter for each square after chutes and ladders
        tracking_board_after[player_position] +=1 
        
        turns += 1
    
    tracking_board_before[-1] = 1
    tracking_board_after[-1] = 1
    

    return player_position, turns, chutes_hit, ladders_hit, tracking_board_before, tracking_board_after
```


### Monte Carlo Simulation
### Analysis
### Markov Matrix Approach
### Multiple Player Game
