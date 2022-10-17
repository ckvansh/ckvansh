use Project_Analysis

select * from ipldetails
select * from iplmatch

-- Total Number of matches played per year --

select yr, count(distinct id) as num_match from 
(select year(Date) as yr, id from iplmatch)a
group by yr

-- most man of the matches --

select player_of_match,count(player_of_match) as total 
from iplmatch
group by player_of_match order by total desc

-- -- most man of the matches per year --

select * from
(select player_of_match,yr,total, rank() over(partition by yr order by total desc) as rnk from
(select player_of_match,year(date) as yr,count(player_of_match) as total 
from iplmatch
group by player_of_match, year(date))a)b where rnk = 1


-- most winner by any team --

select winner, count(winner) as winner from iplmatch group by winner 

-- Top 5 stadium where matches were played --

select top 5 venue , count(venue) as total_Match 
from iplmatch
group by venue order by total_Match desc

-- top run by any bestment --

select top 1 batsman, count(batsman_runs) as totalruns from ipldetails
group by batsman order by totalruns desc

-- percentage of total runs by each batsman --

select batsman, SUM(batsman_runs) as totalruns, 
(SUM(batsman_runs)*100)/Sum(SUM(batsman_runs)) over() as pr
from ipldetails 
group by batsman order by totalruns desc

-- most sixes by any batsman --

select batsman, count(batsman) as total_sixes from 
(select * from ipldetails  where batsman_runs=6)a 
group by batsman order by total_sixes desc

-- most Fours by any batsman --

select batsman, count(batsman) as total_sixes from 
(select * from ipldetails  where batsman_runs=4)a 
group by batsman order by total_sixes desc

-- 3000 runs club/Highest strikrate --

select batsman, total_runs, strikrate from
(select batsman, total_runs, ((total_runs*1.0)/total_balls) * 100 as strikrate from
(select batsman, SUM(batsman_runs) as total_runs, count(batsman) as total_balls
from ipldetails
group by batsman)a)b
where total_runs > 3000

-- bolwer details -- 
-
select bowler, total_ball, total_runs, (total_runs/(total_ball*1.0)) as 
eco_rate from
(select bowler, count(bowler) as total_ball, sum(total_runs) as total_runs
from ipldetails group by bowler)a
where total_ball > 300 order by total_runs 
