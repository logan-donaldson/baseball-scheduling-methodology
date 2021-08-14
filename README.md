*Believe it or not the sports scheduling industry is highly competitive. To maintain the competitive advantage of the Johns Hopkins University Scheduling Group I am not at liberty to post the MATLAB code which houses our integer linear program model. Instead, I have prepared this document to detail my work as a schedule maker for Minor League Baseball.*

The broad integer linear programming methodology used to develop the Minor League Baseball schedules was developed by Dr. Anton Dahbura, an Associate Research Scientist in the Department of Computer Science and Executive Director of the Johns Hopkins University Information Security Institute and Dr. Donniell Fishkind, an Associate Research Professor in the Department of Applied Mathematics and Statistics. They laid the foundation for the optimization model. My job is to adapt the model to produce viable schedules for specific leagues.

### Summary of the Schedule Making Process
First, I correspond with league officials to better understand their unique scheduling requests. I then create a schedule template (explained below). Next, I write new classes of constraints, alter existing constraints, and massage the coefficients of the objective function to best model the specific scheduling problem. The optimization model is created in MATLAB and is then handed over to Gurobi to be solved on a SGI UV2000 System running Linux. With a typical model consisting of over 10,000 variables and constraints each of these problems are seldom solved to optimality. Rather I typically let the supercomputer churn on the problem for 1-3 days before interrupting the Gurobi solver and extracting the best current best solution. The solution comes in the form of a matrix which is then fed into a macro-enabled Excel spreadsheet which generates an aesthetically pleasing calendar schedule. This schedule is then sent to league officials, along with notes from myself detailing the schedule's strengths and weaknesses. A specific schedule variation may give each team an equal number of weekend home games (a desirable characteristic), but require excessive amounts of travel. My job is simply to bring these tradeoffs to the attention of the league official who then makes a decision as to which characteristics they wish to prioritize. I then take these new priorities, update the model and produce another schedule. A single schedule can go through over a dozen iterations before being ultimately approved by the league for use. It is important to manage the league's expectations and emphasize that satisfying all of their preferences is mathematically impossible. Much of my correspondence with league officials involves helping them understand the tradeoffs between their preferences.

### Schedule Template
When creating a schedule, I always start with a template. A template is an array which specifies on which days a baseball game will be played and which days are off days. The template also groups games into series. Each series typically consists of 3 -5 games. Template creation is as much an art as a science. The league will specify on which days the season will start and end along with how many games need to be played, but on which days the games are played is left largely to my discretion. I must find a way to fit the series into the allotted dates while adhering to some basic rules such as only 12 off-days and no off-days on weekends. Below I have included an example template in which 0s are off-days and positive integers represent baseball games grouped by series number.
```
M  T  W  T  F  S  S
0  0  0  1  1  1  1   week of Apr 8                      
1  2  2  2  2  2  3   week of Apr 15
3  3  3  0  4  4  4   week of Apr 22
4  4  5  5  5  5  5   week of Apr 29
0  6  6  6  6  6  7   week of May 6
7  7  7  7  8  8  8   week of May 13
8  8  0  9  9  9  9   week of May 20
9  10 10 10 10 10 10  week of May 27
11 11 11 11 11 12 12  week of Jun 3
12 12 0  13 13 13 13  week of Jun 10
13 14 14 14 14 14 14  week of Jun 17
0  0  0  15 15 15 15  week of Jun 24
15 16 16 16 16 16 17  week of Jul 1
17 17 17 17 18 18 18  week of Jul 8
18 18 0  19 19 19 19  week of Jul 15
19 20 20 20 20 20 0   week of Jul 22
21 21 21 21 21 22 22  week of Jul 29
22 22 22 0  23 23 23  week of Aug 5
23 23 24 24 24 24 24  week of Aug 12
0  25 25 25 25 25 26  week of Aug 19
26 26 26 26 27 27 27  week of Aug 26
27 27 0  28 28 28 28  week of Sep 2
28 0  0  0  0  0  0   week of Sep 9
```

### Constraints
The baseball scheduling problem is severely over-constrained. Broad classes of constraints include, but are not limited to, restrictions on the number of miles a team can travel, limitations on the number of times a team spends an off day away from their home park, and requirements for the number of times a team must play intra-divisional rivals. In total there are ~20 broad classes of constraints and ~10000 constraints total. Depending on the schedule I may need to write a new class of constraint or tweak an old one. If each constraint was to be adhered to, the problem would be infeasible. Thus, we use a form of slack variable to allow for constraint violations. If a slack variable is needed to satisfy a constraint it is 'turned on' and raises the objective function value. The model is a minimization problem, so the optimization algorithm is incentivized to utilize as few slack variables as possible.

### Objective Function
The variables corresponding to baseball games to be played have no influence over the objective function. Thus the objective function value is solely determined by the slack variables. Each class of constraint has a different penalty associated with its slack variables. Adjusting these penalty coefficients allows us to encode the league officials' preferences. For example, one league official may want to minimize travel distance as much as possible, in this case I would raise the associated penalty coefficient. Of course raising one penalty coefficient will cause more violations of other constraints. This give and take is at the core of the scheduling problem and accurately describing the tradeoffs to league official's is key to getting a schedule approved in as few iterations as possible.
