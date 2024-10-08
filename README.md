# Snakes and Ladders Analysis Using Monte Carlo Simulation

![Intro_picture](https://github.com/user-attachments/assets/d04750c4-15dd-4d59-8080-0d1ad9d7306a)

## Table of Contents

1. [Introduction and Motivation](#introduction-and-motivation)
2. [Snakes and Ladders Explanation](#snakes-and-Ladders-explanation)
3. [Methodology](#methodology)
4. [Creating the Game](#creating-the-game)
5. [Monte Carlo Simulation](#monte-carlo-simulation)
6. [Analysis](#analysis)
7. [Markov Matrix Approach](#markov-matrix-approach)
8. [Multiple Player Game](#multiple-player-game)


### Introduction and Motivation
This project analyzes the game Snakes and Ladders, which originates all the way back to ancient India in the 2nd century CE. 
Now, my motivation for this project was inspired by a heated game of Monopoly between my friends and me that lasted nearly five hours. During this five-hour span, I noticed a couple of things. One being the fact that the game never ended no matter how many times we rolled the dice. And the second, more interestingly, was the fact that I felt that we were always landing on the same few squares and narrowly dodging others. This made me wonder if there is a statistical reason for this. Monopoly is an extremely complicated game with many variables like Chance cards, Jail squares, and repeated turns on doubles. Snakes and Ladders, on the other hand, is far simpler, which allows for some cool analysis that we can do.

### Snakes and Ladders Explanation
Snakes and Ladders is a rather simple game by nature. The game consists of a game board. The dimensions of the board vary, but most commonly, it is 10 x 10. On the board, there are various snakes and ladders starting and ending on two different squares. 

![snake_ladder_direction](https://github.com/user-attachments/assets/e4271eab-eacb-4295-a1fb-0acfc3f41513)

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
![snake_game_board](https://github.com/user-attachments/assets/8c4b7591-8692-4f64-a53a-943cf1579cc4)

We will now create an actual playthrough of a game defined as a function. We must first create a virtual board to be played on. Since the game only has one pathway to take (other than the snakes and ladders), we will use a list of length 100 (10 x 10 dimensions) to simulate the board. The index will represent the player's position on the board, and the value at each index of the list will represent where the snakes and ladders are located and where they connect. Spaces on the board without snakes or ladders will be represented by an integer value of 0 because we do not want to change the index (player position). A negative value will represent spaces on the board with a snake. Now, the magnitude of this value determines how many squares backward they will need to go (where the snake respectively connects). A positive value will represent spaces on the board with a ladder. This will determine how many squares forward they must go (where the ladder connects). These values are all determined by the location of snakes and ladders, as shown in the gameboard image below. For example, the first square of the board connects to square 38. In our list, the first value of the list is 37 (at index 0), which leads to square 38 (at index 37). Keep in mind Python indexing starts at 0.

There are a few more variables we define as well that assist in our analysis. We create two np arrays of length 100, all filled with zeros. These are to track the player's positions on the board after each dice roll, both before and after taking the snakes and ladders. We also track the number of turns a game takes, the number of snakes hit, and the number of ladders hit. Finally, we create a player position variable that starts at index -1, which does not exist, which ensures the player starts off the board. 

We now have the components ready to start the game; we can initialize a while loop that continues unless the player reaches the final square (index 99). We roll a dice and add that amount to the player's position to move the player across the board. Then, we track any of the variables defined before, depending on where the player lands. Additionally, if a player hits a position on the game board that contains a snake or ladder, we move the player up or down according to the snake and ladder, respectively. At the end, we return our variables.


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
Now that we have data from our Monte Carlo simulation, we can begin some analysis.

We can first take a look at the number of rolls it takes for a game to be completed. Since we have a lot of simulated data, we can show this data on a distribution built in matplotlib, as shown in the graph. We can also use our simulated data to calculate average turns per game's mean, median, and mode. 

```Python
plt.figure(figsize=(14, 8))
plt.hist(results_df['turns'], bins = range(0, 250, 5), density = True, rwidth= 0.8, color = 'cornflowerblue')
plt.xlabel('Number of Rolls', fontsize = 15)
plt.ylabel('Probability', fontsize = 15)
plt.title('Probability Distribution of Number of Rolls to Win', fontsize = 25)
plt.xticks(range(0,250,10), fontsize = 12)
plt.axvline(results_df['turns'].median(), color='blue', linestyle = "dashed", linewidth=2, label = f"Median: {results_df['turns'].median()}")
plt.axvline(results_df['turns'].mean(), color='red', linestyle = "dashed", linewidth=2, label = f"Mean: {results_df['turns'].mean()}")
plt.axvline(results_df['turns'].mode()[0], color='black', linestyle = "dashed", linewidth=2, label = f"Mode: {results_df['turns'].mode()[0]}")
plt.legend()
plt.grid()
plt.show()
```
![1](https://github.com/user-attachments/assets/9cab898e-8eeb-4a0b-966f-32a4d628f93e)

And we can calculate the probability of this mode to occur
```Python
# Calculates for the probability of the mode occuring before a game
x = results_df['turns'].mode()[0]
prob = len(results_df[results_df['turns'] == x]) / len(results_df['turns'])
print(f"Most like number of rolls is {x} with a probability of {prob}")
```
We get a probability of 0.02776

Now, let's examine some more interesting behaviors. We can examine how player positions are distributed throughout the game board by creating a function that takes our original list of indexes and turns them into an actual game board shape. Since we had tracked player positions earlier when doing the Monte Carlo simulation, we have all the necessary data. A heatmap can be a good way of depicting the number of players who land on each square.

```Python
def reshape_into_board(array):
    """
    Function that reshapes our array of player position on each square into the game board shape
    array: Numpt array of player position on each square
    returns: An transformed array in the shape of the game board
    """
    
    array = array.reshape(10,10)
    array[1::2, :] = array[1::2, ::-1]
    array = np.flipud(array)
    return array
```
```Python
#Transforms the gameboard before going down the snakes and ladders
heatmap_board = reshape_into_board(heatmap_board)
#Transforms the gameboard before going down the snakes and ladders
heatmap_board_after = reshape_into_board(heatmap_board_after)
```

```Python
#Create a heatmap of the player position on the gameboard

import matplotlib.patches as patches
#Ladders on 1,4,9,21,28,36,51,71,80 (non-indexing)
#Snakes on 16, 47, 49, 56, 62, 64, 87, 93, 95, 98
fig, ax = plt.subplots(figsize=(12,10))
sns.heatmap(heatmap_board, cmap = "Blues", annot = True, fmt = 'g')

#Patches add borders to where there are snakes and ladders
ax.add_patch(patches.Rectangle((2, 0), 1, 1, edgecolor='red', fill=False, lw=3))
ax.add_patch(patches.Rectangle((5, 0), 1, 1, edgecolor='red', fill=False, lw=3))
ax.add_patch(patches.Rectangle((7, 0), 1, 1, edgecolor='red', fill=False, lw=3))
ax.add_patch(patches.Rectangle((6, 1), 1, 1, edgecolor='red', fill=False, lw=3))
ax.add_patch(patches.Rectangle((1, 3), 1, 1, edgecolor='red', fill=False, lw=3))
ax.add_patch(patches.Rectangle((3, 3), 1, 1, edgecolor='red', fill=False, lw=3))
ax.add_patch(patches.Rectangle((4, 4), 1, 1, edgecolor='red', fill=False, lw=3))
ax.add_patch(patches.Rectangle((6, 5), 1, 1, edgecolor='red', fill=False, lw=3))
ax.add_patch(patches.Rectangle((8, 5), 1, 1, edgecolor='red', fill=False, lw=3))
ax.add_patch(patches.Rectangle((4, 8), 1, 1, edgecolor='red', fill=False, lw=3))

ax.add_patch(patches.Rectangle((0, 9), 1, 1, edgecolor='lime', fill=False, lw=3))
ax.add_patch(patches.Rectangle((3, 9), 1, 1, edgecolor='lime', fill=False, lw=3))
ax.add_patch(patches.Rectangle((8, 9), 1, 1, edgecolor='lime', fill=False, lw=3))
ax.add_patch(patches.Rectangle((0, 7), 1, 1, edgecolor='lime', fill=False, lw=3))
ax.add_patch(patches.Rectangle((7, 7), 1, 1, edgecolor='lime', fill=False, lw=3))
ax.add_patch(patches.Rectangle((4, 6), 1, 1, edgecolor='lime', fill=False, lw=3))
ax.add_patch(patches.Rectangle((9, 4), 1, 1, edgecolor='lime', fill=False, lw=3))
ax.add_patch(patches.Rectangle((0, 2), 1, 1, edgecolor='lime', fill=False, lw=3))
ax.add_patch(patches.Rectangle((9, 2), 1, 1, edgecolor='lime', fill=False, lw=3))

plt.xticks([])
plt.yticks([])
plt.title('Heatmap of the Board Player Positions Before Snakes and Ladders', fontsize = 20)
red_patch = patches.Patch(color='red', label='Snakes')
blue_patch = patches.Patch(color='lime', label='Ladders')
plt.legend(handles=[red_patch, blue_patch], loc = (-0.15, 0.9))
plt.show()
```
![2](https://github.com/user-attachments/assets/bd89963a-a656-4cff-b54e-8c54d9908831)![snake_game_board](https://github.com/user-attachments/assets/8c4b7591-8692-4f64-a53a-943cf1579cc4)



From the heatmap, we can see that some squares are landed on more often than others, and more importantly, some snakes and ladders are also landed on more often than others. Particularly, we can see that the snake on square 47 has the highest number of players that land on it after 100,000 simulations.

We can also take a look at the distribution of snakes and ladders.
```Python
plt.figure(figsize=(14, 8))
x = results_df['ladders_hit']
y = results_df['chutes_hit']
plt.hist([x, y], bins = range(0,25,1), label=['Ladders', 'Snakes'], color = ['cornflowerblue', 'orange'])
plt.title('Number of Snakes and Ladders Hit', fontsize = 20)
plt.xticks(range(0,25,1))
plt.xlabel('Snake/Ladder Hit Per Game', fontsize = 12)
plt.ylabel('Frequency', fontsize = 12)
plt.legend(loc='upper right', fontsize = 12)
plt.show()
```
![output](https://github.com/user-attachments/assets/f2a32a60-d184-42de-b7f8-6ab7ec9609a5)

```Python
print(f"Average ladders hit per game: {results_df['ladders_hit'].mean()}")
print(f"Average chutes hit per game: {results_df['chutes_hit'].mean()}")
print(f"Median ladders hit per game: {results_df['ladders_hit'].median()}")
print(f"Median chutes hit per game: {results_df['chutes_hit'].median()}")
print(f"Mode ladders hit per game: {results_df['ladders_hit'].mode()[0]}")
print(f"Mode chutes hit per game: {results_df['chutes_hit'].mode()[0]}")
```

Average ladders hit per game: 3.21554\
Average snakes hit per game: 3.94948\
Median ladders hit per game: 3.0\
Median snakes hit per game: 3.0\
Mode ladders hit per game: 3\
Mode snakes hit per game: 1

On average, more snakes are hit than ladders, and the distribution of the snakes/ladders hit follows the distribution of the number of rolls to finish a game. This makes sense because the games that take a long time are typically due to the unfortunate event where a player keeps landing on snakes, not ladders.

### Markov Matrix Approach
Everything mentioned so far was done using the Monte Carlo Simulation approach. But because of the nature of the game, we can also tackle the problem using Markov Matrices. A Markov process is a stochastic process where the probability of moving to a certain state only depends on the current state, not the history of how you got there. This fits well with Snakes and Ladders, where each player's next move depends solely on their current position and a die roll, without memory of past rolls. Essentially, the game is memoryless. 

We can start by creating a function that creates a Markov transition matrix, which is derived from the snakes and ladders and the probability of the dice roll. 
```Python
def markov_matrix(n):
    
    """
    Creates a markov transition matrix for snakes and ladders
    n: how many times you wnat the transition matrix applied (turns in a game)
    returns: markov transition matrix applied to the starting position n times
    """  
    
    board_snakes_ladders = {1:38, 4:14, 9:31, 16:6, 21:42, 28:84, 36:44,
                  47:26, 49:11, 51:67, 56:53, 62:19, 64:60,
                  71:91, 80:100, 87:24, 93:73, 95:75, 98:78}
    
    #Create a blank matrix of zeros
    matrix = np.zeros((101,101))
    
    #Add probability of 1/6 for 6 states after i 
    for i in range(101):
        matrix[i + 1: i + 7, i] = 1 / 6
    
    #Ensures the probabilites add to 1 in the columns
    matrix[range(101), range(101)] += 1 - matrix.sum(0)

    #Creating a matrix for the chutes and ladders
    jumping_matrix = np.zeros((101, 101))
    
    ind = [board_snakes_ladders.get(i, i) for i in range(101)]
    jumping_matrix[ind, range(101)] = 1
    
    #Multiplying the two matrices to get the transition matrix
    mkv_matrix =  jumping_matrix @ matrix
    
    #Starting state of a player represented as a vector
    starting_state = [1, *np.zeros(100)]
    
    return np.linalg.matrix_power(mkv_matrix, n) @ starting_state
```


We can take our function to simulate and use the Markov matrix to compute how long it takes, on average, to reach the final square (absorbing state) from any given starting square. By repeatedly multiplying the state vector (which tracks the current position on the board) by the transition matrix, we can simulate the game and analyze probabilities of being on certain squares after a given number of moves.

```Python
probability = [markov_matrix(i)[-1] for i in range(200)]
plt.figure(figsize=(14, 8))
plt.hist(results_df['turns'], bins = range(0, 250, 5), density = True, rwidth= 0.8, color = 'cornflowerblue')
plt.plot(np.arange(1, 200), np.diff(probability), color='black', label = 'Markov')
plt.legend()
plt.xlabel('Number of Rolls', fontsize = 15)
plt.ylabel('Probability', fontsize = 15)
plt.title('Distribution of Number of Rolls to Win', fontsize = 25)
plt.xticks(range(0,250,10), fontsize = 12)
plt.grid()
plt.show()
```

![4](https://github.com/user-attachments/assets/b7372a5f-e977-4063-aa42-105dc4c3cb0e)

We can see our Markov approach follows our Monte Carlo simulation approach quite closely. 

There are a lot of other cool things that we can do with the probabilities from a Markov matrix, but we will not go into detail for the sake of length.


### Multiple Player Game
Both our Monte Carlo simulation and the Markov matrix approach were under the assumption that only one player was playing. But of course, this is not the case in real life because nobody likes to play board games alone! Using the same core ideas, we can simulate the game for two or more players by adding a few variables. 

```Python
## situation with 2 players

def two_player_game():
    
    """
    Simulates a game of snakes and ladders for 2 players
    Returns: player_position1, player_position2 turns
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

    player_position1 = -1
    player_position2 = -1

    turns = 0 


    while player_position1 < 99 and player_position2 < 99:
        #Moving the player after rolling the dice
        player_position1 += roll_dice()
        
        # Ensure player_position stays within bounds
        player_position1 = min(player_position1, 99)
        player_position1 += game_board[player_position1]
        
        
        player_position2 += roll_dice()
        player_position2 = min(player_position2, 99)
        player_position2 += game_board[player_position2]
        
        turns += 1


    return player_position1, player_position2, turns
```
We will run 10,000 simulations instead of our original 100,000 for the sake of time.

```Python
# We will do 10,000 simulations
n = 10000

# Create an empty DataFrame to store the results
results_df2 = pd.DataFrame(columns=['turns','player_position1','player_position2'])


for i in range(n):
    
    player_position1, player_position2, turns = two_player_game()
    
    
    new_row2 = pd.Series({'turns': turns, 'player_position1': player_position1, 'player_position2': player_position2})
    results_df2 = pd.concat([results_df2, pd.DataFrame([new_row2])], ignore_index=True)
```

Our results_df2 data frame will look something like this:
| | turns | player_position1 |player_position2 |
|--|--|--|--|
|0| 22 | 96 | 99 |
|1| 17 | 52 | 99 |
|2| 15| 45 | 99 |


We can plot the distribution of the 2 player game:

```Python
plt.figure(figsize=(14, 8))
plt.hist(results_df2['turns'], bins = range(0, 160, 5), rwidth= 0.8, color = 'cornflowerblue')
plt.xlabel('Number of Rolls', fontsize = 15)
plt.ylabel('Frequency', fontsize = 15)
plt.title('Distribution of Number of Rolls to Win With 2 Players', fontsize = 25)
plt.xticks(range(0,160,10), fontsize = 12)
plt.axvline(results_df2['turns'].median(), color='blue', linestyle = "dashed", linewidth=2, label = f"Median: {results_df2['turns'].median()}")
plt.axvline(results_df2['turns'].mean(), color='red', linestyle = "dashed", linewidth=2, label = f"Mean: {results_df2['turns'].mean()}")
plt.legend()
plt.grid()
plt.show()
```
![5](https://github.com/user-attachments/assets/6efcd735-a9c9-4d13-99ca-a9b8c960a00d)


The distribution has less variance compared to the single-player game, and we don't get as many games that last a really long time. Logically, this makes sense since there is a higher probability that one of the players will end the game at a normal rate. 

```Python
We can measure the same important statistics from our single player game:
print(f"Mean of 2 player simulation is: {results_df2['turns'].mean()}")
print(f"Median of 2 player simulation is: {results_df2['turns'].median()}")
print(f"Mode of 2 player simulation is: {results_df2['turns'].mode()[0]}")
```

Mean of 2 player simulation is: 23.8462\
Median of 2 player simulation is: 21.0\
Mode of 2 player simulation is: 18


We can also repeat with 4 players (the more the merrier!).


```Python
def four_player_game():
    
    """
    Simulates a game of snakes and ladders for 4 players
    Returns: player_position1, player_position2, player_position3, player_position4, turns
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

    player_position1 = -1
    player_position2 = -1
    player_position3 = -1
    player_position4 = -1

    turns = 0 


    while player_position1 < 99 and player_position2 < 99 and player_position3 < 99 and player_position4 < 99:
        #Moving the player after rolling the dice
        player_position1 += roll_dice()
        player_position1 = min(player_position1, 99)
        player_position1 += game_board[player_position1]
        
        
        player_position2 += roll_dice()
        player_position2 = min(player_position2, 99)
        player_position2 += game_board[player_position2]
        
        player_position3 += roll_dice()
        player_position3 = min(player_position3, 99)
        player_position3 += game_board[player_position3]
        
        player_position4 += roll_dice()
        player_position4 = min(player_position4, 99)
        player_position4 += game_board[player_position4]
        
        turns += 1
    
    return turns
```


```Python
# We will do 10,000 simulations
n = 10000

# Create an empty DataFrame to store the results
results_df3 = pd.DataFrame(columns=['turns'])


for i in range(n):
    
    turns = four_player_game()
    
    
    new_row3 = pd.Series({'turns': turns})
    results_df3 = pd.concat([results_df3, pd.DataFrame([new_row3])], ignore_index=True)
```


```Python
plt.figure(figsize=(14, 8))
plt.hist(results_df3['turns'], bins = range(0, 160, 5), rwidth= 0.8, color = 'cornflowerblue')
plt.xlabel('Number of Rolls', fontsize = 15)
plt.ylabel('Frequency', fontsize = 15)
plt.title('Distribution of Number of Rolls to Win With 4 Players', fontsize = 25)
plt.xticks(range(0,160,10), fontsize = 12)
plt.axvline(results_df3['turns'].median(), color='blue', linestyle = "dashed", linewidth=2, label = f"Median: {results_df3['turns'].median()}")
plt.axvline(results_df3['turns'].mean(), color='red', linestyle = "dashed", linewidth=2, label = f"Mean: {results_df3['turns'].mean()}")
plt.legend()
plt.grid()
plt.show()
```

![7](https://github.com/user-attachments/assets/12bdaca4-cd9f-418d-b1d2-40790f5c5840)

Once again, our distribution follows the same trend; the more players we add to the game, the tighter our distribution gets and the less variation we have. Games tend to end at a quicker rate, and our measures of mean median and mode decrease.

