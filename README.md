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

We will create our game in Python. First, we have to download a couple of packages. Numpy will be used for the game creation and Monte Carlo simulation. Pandas will be used to store the data that our simulations produce.

```Python
#Importing Packages
import random
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
```

We create the dice for the game. Since the game relies on a single dice, we will create a function that chooses a random int between 1 and 6 to simulate our dice.

```Python
#Creates a fair dice roller

def roll_dice():
    """
    Simulates a fair dice roll with
    Returns: Integer from 1 to 6
    """
    return random.randint(1,6)
```


We will now create an actual playthrough of a game defined as a function. We must first create a virtual board to be played on. Since the game only has one pathway to take (other than the snakes and ladders), we will use a list of length 100 (10 x 10 dimensions) to simulate the board. The index will represent the position of the player on the board, and the value at each index of the list will represent where the snakes and ladders are located and where they connect. Spaces on the board without snakes or ladders will be represented by an integer value of 0 because we do not want to change the index (player position). Spaces on the board with a snake will be represented by a negative value. Now, the magnitude of this value determines how many squares backward they will need to go (where the snake respectively connects). A positive value will represent spaces on the board with a ladder. This will determine how many squares forward they must go (where the ladder connects). These values are all determined by the location of snakes and ladders, as shown in the gameboard image below. For example, the first square of the board connects to square 38. In our list, the first value of the list is 37 (at index 0), which leads to square 38 (at index 37). Keep in mind Python indexing starts at 0.

IMG of the game board to reference where the ladders are

There are a few more variables we define as well that assist in our analysis. We create two np arrays of length 100, all filled with zeros. These are to track the player's positions on the board after each dice roll, both before and after taking the snakes and ladders. We also track the number of turns a game takes, the number of snakes hit, and the number of ladders hit. Finally, we create a player position variable that starts at index -1, which does not exist, which ensures the player starts off the board. 

We now have the components ready to start the game; we can initialize a while loop that continues unless the player reaches the final square (index 99). We roll a dice and add that amount to the player's position to move the player across the board. Then, we track any of the variables defined before, depending on where the player lands. Additionally, if a player hits a position on the game board that contains a snake or ladder, we move the player up or down according to the snake and ladder respectively. At the end, we return our variables.


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
    #but since we are using Python indexing, it still is -1
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

Now that we have built a playthrough of a game, we can begin our Monte Carlo simulation. As mentioned before, the simplistic nature of Snakes and Ladders and its reliance on randomness makes it a good subject to simulate. For our purposes, we will simulate 100,000 game playthroughs. We simulate more or less depending on how precise we want our results to be. For our analysis, 100,000 will suffice.

To write this up, we create a data frame using pandas to store our data collected from each individual game. And then we simply simulate the game 100,000 times, each time we store the data into our data frame until all simulations are complete.

```Python
# We will do 100,000 simulations
n = 100000

random.seed(0)
# Create an empty DataFrame to store the results
results_df = pd.DataFrame(columns=["turns", "chutes_hit", "ladders_hit"])


#Create empty board for the player positions
heatmap_board = np.zeros(100)
#Create empty board for player positions after hitting the snakes and ladders 
heatmap_board_after = np.zeros(100)
# Repeat the simulation 'n' times and store the results in the DataFrame
for i in range(n):
    
    final_position, turns, chutes_hit, ladders_hit, tracking_board_before, tracking_board_after = play_game_board()
    
    
    new_row = pd.Series({'turns': turns, 'chutes_hit': chutes_hit, 'ladders_hit': ladders_hit})
    results_df = pd.concat([results_df, pd.DataFrame([new_row])], ignore_index=True)
    
    
    heatmap_board += tracking_board_before
    heatmap_board_after += tracking_board_after
    
```
The resulting data frame looks something like this:

| | turns | chutes_hit |ladders_hit|
|--|--|--|--|
|0| 47 | 6 | 5 |
|1| 53 | 7 | 4 |
|2| 15| 1 | 4 |


### Analysis
### Markov Matrix Approach
### Multiple Player Game
