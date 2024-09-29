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
### Monte Carlo Simulation
### Analysis
### Markov Matrix Approach
### Multiple Player Game
