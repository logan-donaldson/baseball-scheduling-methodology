# baseball-scheduling-methodology

*Believe it or not the sports scheduling industry is highly competitive. To maintain the competitive advantage of the Johns Hopkins University Scheduling Group I am not at liberty to post the MATLAB code which houses our integer linear program model. Instead, I have prepared this document to detail my work as a schedule maker for Minor League Baseball.*

The broad integer linear programming methodology used to develop the Minor League Baseball schedules was developed by Dr. Anton Dahbura, an Associate Research Scientist in the Department of Computer Science and Executive Director of the Johns Hopkins University Information Security Institute and Dr. Donniell Fishkind, an Associate Research Professor in the Department of Applied Mathematics and Statistics. They layed the foundation for the optimization model. My job is to adapt the model to produce viable schedules for specific scenarios. 

## Summary of the Schedule Making Process
First I correspond with league officials to better understand their unique scheduling requests. I then create a schedule template (explained below). Next, I write new classes of constraints, alter existing constraints, and, massage the coefficients of the objective functionto best model the specific scheduling problem. The optimization model is created in MATLAB and is then handed over to Gurobi to be solved on a SGI UV2000 System running Linux. With a typical model consisting of over 10000 variables and constraints each these problems are seldom solved to optimality. Rather I typically let the supercomputer churn on the problem for 1-3 days before interrupting the solver and extracting the best solution found so far. The solution comes in the form of a vector which is then fed into a macro-enabled Excel spreadsheet which generates an asthetically pleasing calendar schedule. This schedule is then sent to league officials, along with notes from myself detailing the schedule's strengths and weaknesses. A schedule may give each team an equal number of weekend home games (a desirable charcteristic) but require excessive amounts of travel. My job is simply to bring these tradeoffs to the attention of the league official who then makes a desicion as to which charcertistics they wish to prioritize. I then take these new priorities, update the models, and produce another schedule. A single schedule can go through over a dozen iterations before being ultimately aproved the league for use.

# Schedule Template
When creating a schedule I always start with a template. A template is an array which specifies on which days a baseball game will be played and which days are off-days. The template also groups games into series. Each series typically consists of 3 -5 games. Template creation is as much an art as a science. The league will specify on which days the season will start and end along with how many games need to played but on which days the games are played is left largely to my discretion. It's like a brainteaser in which I must find a way to fit the series into the alloted dates while adhering to some basic rules such as only 12 off-days, and no off-days on weekends. Below I have included an example template in which 0s are off-days and positive inetegers represent baseball games grouped by series number.

M  T  W  T  F  S  S\
0  0  0  1  1  1  1   week of Apr 8                      
1  2  2  2  2  2  3   week of Apr 15\
3  3  3  0  4  4  4   week of Apr 22\
4  4  5  5  5  5  5   week of Apr 29\
0  6  6  6  6  6  7   week of May 6\
7  7  7  7  8  8  8   week of May 13\
8  8  0  9  9  9  9   week of May 20\
9  10 10 10 10 10 10  week of May 27\
11 11 11 11 11 12 12  week of Jun 3\
12 12 0  13 13 13 13  week of Jun 10\
13 14 14 14 14 14 14  week of Jun 17\
0  0  0  15 15 15 15  week of Jun 24\
15 16 16 16 16 16 17  week of Jul 1\
17 17 17 17 18 18 18  week of Jul 8\
18 18 0  19 19 19 19  week of Jul 15\
19 20 20 20 20 20 0   week of Jul 22\
21 21 21 21 21 22 22  week of Jul 29\
22 22 22 0  23 23 23  week of Aug 5\
23 23 24 24 24 24 24  week of Aug 12\
0  25 25 25 25 25 26  week of Aug 19\
26 26 26 26 27 27 27  week of Aug 26\
27 27 0  28 28 28 28  week of Sep 2\
28 0  0  0  0  0  0   week of Sep 9\


The baseball scheduling problem is severly over-constrained. Broad classes of constraints include but are not limited to restrictions on the number of miles a team can travel, limitations on the number of times a team must spend an off day away from their home park, and requirements for the number of times a team must play intra-divisional rivals. In total there ~20 broad classes of constraints.  If each constraint were to be adhered to the problem would be infeasible. Thus, 

