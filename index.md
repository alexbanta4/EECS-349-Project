### **EECS 349 Water Pong Final Report (Group 36: Alex Banta, Kyle LaBrosse)

### Overview

Our task was to predict the win rate of a team of two people in the classic college game, water pong. In the game, two teams of two attempt to shoot ping pong balls into a pyramid of cups placed on the opposite side of a table.The first to shoot balls into all the cups on the opponent's side wins.  Over the course of this game, there is a chance for individual players to hit “skill shots” involving things like targeting a specific cup or shooting at the cups in an unconventional manner. In general, we expect the players that hit these skill shots more often to better, resulting in higher winning percentages for their teams. Strangely, for a game so commonly enjoyed across college campuses, little to no data has been collected on the game. Luckily, because we have data collected over a period of 2 years on over 250 games of pong, we are in a unique position to analyze this game with rigor that no other group has attempted. Future players can use our data to see which types of trick shots have the highest impact on win rate, so that they can focus their practice on those shots and improve quickly.

### Tools and Training Methods

We used two main tools to analyze the data. We started out using Weka to analyze the data and get a sense of general trends. When using Weka, we tried ZeroR as a baseline for comparison, followed by Linear Regression, Nearest Neighbor, and Multiple Perceptron algorithms. As the analysis proceeded, we switched to Matlab in order to customize our linear regression method in order to best fit the peculiarities of our data; this will be described in more detail below. Because of the small size of our dataset, we did not use a test set but used leave one out cross validation to measure success. 

### Dataset (Attributes and Features)

The initial data consists of the recorded match outcomes of 278 games of water pong, as well as individual statistics on “skill shots” for all of the players in the games. We processed that into a dataset of 90 teams. Each entry had a single classifier, Win Percentage (WPCT), and six attributes. WPCT is calculated from a team’s number of wins divided by their total games played. The six attributes were:
	-Total Games Played by each person on the team (GP) (measures experience)
	-Fires (after three shots are made in a row, the person who made them is considered to be ‘on fire’ and shoots until they miss)
	-Cups made after ‘Fire’ (the amount of cups made after initially making 3 in a row)
	-Islands (players can call a cup separated from the group, and shoot at that cup; if they make it into the called up, two cups are removed instead of one)
	-Behind the back shots (taken if a ball bounces off of a cup on the other side and is recovered by the team that threw the ball)
	-Middle Cups (Hitting the middle cup first is generally avoided, as it results in a rack of cups that is harder to shoot on. We 	expect players who make this shot more often to have lower win rates.)
Since different teams had different numbers of games played, we normalized the skill shot data per player by dividing the total number of skill shots made by the total number of games played, and then calculated skill shots per game (FPG, CAFPG, IPG, BBPG, MCPG). Since the examples in the dataset consist of teams, we added together each teammate’s individual performance metrics to be used in the model. Below is a sample of our dataset with the output, win percentage (WPCT) as the final column.



### Initial Results, Analysis, and Final Results

During our initial testing, we tested using ZeroR, K-Nearest-Neighbor, Multiple Perceptrons, and Linear Regression. We measured the error with Mean Absolute Error (MAE), which is the average of the differences between the learner’s guess of win percentage and the actual win percentage for a validation set example. ZeroR’s baseline absolute error was 0.2624, and we got initial Mean Absolute Errors of 21.1%, 23.9%, and 20.8% for 9NN, Multiple Perceptrons, and Linear Regression, respectively. Though linear regression did stick out as the best method, we didn’t yet have satisfactory results (~10-15% error, so that we could have 85-90% accuracy), and we suspected that this was due to the distribution of matches played. The data for teams with large total games played was a lot closer to the line than the data for teams without a lot of total games, suggesting that there was a significant luck component in data for low-TGP teams caused by low sample size.
	To counteract this, we expanded our dataset to 90 teams and wrote our own custom MATLAB linear regression program so that we could weight our regression by games played. To weight the data, we first copied examples in our database a number of times equal to the number of games played by the team (aka if a team had played 17 games, the data point appeared 17 times). This magnified the most reliable data points in our collection while still allowing us to include large numbers of the unreliable data points weighted towards their reliability, reducing our error significantly. This resulted in a dataset of 548 examples rather than the original 90. We also adjusted the linear regression so that predicted winning rates above 1 and below 0 (which are not possible) were rounded off to 1 and 0 respectively. On top of this, we created a “minimum number of games” variable that we could easily alter, causing the teams with too low of number of games played (i.e. an unreliable WPCT) to not even be trained on. After testing, we decided the optimal value without losing too many examples was 4 games, lowering our dataset to 39 teams. After running our program with leave-one-out cross validation, we ended with the following model with a mean absolute validation error of 0.1281, compared to 0.2155 from the original dataset.

WPCT = 0.0487 + 0.0005*GP + 0.0454*FPG + 0.7745*IPG + 0.2951*CAFPG + -0.3277*MCPG + 0.2481*BBPG

Below is a graph of actual winning percentages compared to our model’s predicted winning percentages.

As you can see, although it looks like there are a lot of outliers far from the 45 degree line, many of those outliers could still be from teams with low sample sizes of games. If we look at results of teams that have played at least 15 games (i.e. the most reliable WPCT data), we see much better results, with very few points straying from the 45 degree line.



We then wanted to find which attributes had the largest impact on WPCT, we calculated the average value of each attribute, multiplied it by its coefficient, and then normalized it. The following are the results of relative impacts:

        GP: 0.0410 
        FPG: 0.0104 
        IPG: 0.2539 
        CAFPG: 0.1057 
        MCPG: 0.2273 
        BBPG: 0.1321 

As you can see, the attribute with the largest impact on success is islands per game (IPG). From this result, we would suggest that teams going for the best chance of winning should attempt to hit a higher rate of “islands.” This is surprising since to us, it seems like fires are just as important. You can also see from the results, that all the attributes have a positive correlation with winning except for middle cups, as expected. This proves that hitting the middle cup first in a game of water pong should generally be avoided. 

### Suggestions for Future Work

Future work on this data would be extremely helpful if someone were to use this for personal improvement. The biggest weakness of our study was the difficulty of gathering large amounts of data; though we had a large amount of matches, they were mainly played by a small group of teams, and thus we didn’t have a large amount of reliable data points. Though we were able to compensate for this in large part with our weighting algorithm, future studies could get even smaller MAE values if they were to fill out the dataset to remove teams with less than ten games played. Also, there are many attributes that, due to the limitations of our starting dataset, we were not able to analyze. It is possible (and in fact likely) that statistics on hit percentage, as well as other statistics measuring performance in clutch moments, could greatly improve the accuracy of future regression studies, and future data collectors starting from scratch could incorporate these statistics from the get-go.




