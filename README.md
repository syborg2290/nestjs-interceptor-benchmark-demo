# Improve response times 10x by introducing an interceptor in NestJS


`autocannon -d 10 -c 10 http://127.0.0.1:3000/before`

`autocannon -d 10 -c 10 http://127.0.0.1:3000/after`

## 5ms delay on a job 

┌─────────┬──────┬──────┬───────┬───────┬─────────┬─────────┬───────┐
│ Stat    │ 2.5% │ 50%  │ 97.5% │ 99%   │ Avg     │ Stdev   │ Max   │
├─────────┼──────┼──────┼───────┼───────┼─────────┼─────────┼───────┤
│ Latency │ 5 ms │ 7 ms │ 14 ms │ 18 ms │ 7.41 ms │ 3.01 ms │ 58 ms │
└─────────┴──────┴──────┴───────┴───────┴─────────┴─────────┴───────┘
┌───────────┬────────┬────────┬────────┬────────┬────────┬─────────┬────────┐
│ Stat      │ 1%     │ 2.5%   │ 50%    │ 97.5%  │ Avg    │ Stdev   │ Min    │
├───────────┼────────┼────────┼────────┼────────┼────────┼─────────┼────────┤
│ Req/Sec   │ 1080   │ 1080   │ 1264   │ 1364   │ 1247   │ 88.52   │ 1080   │
├───────────┼────────┼────────┼────────┼────────┼────────┼─────────┼────────┤

12k requests in 10.02s, 2.98 MB read




┌─────────┬──────┬──────┬───────┬──────┬─────────┬─────────┬───────┐
│ Stat    │ 2.5% │ 50%  │ 97.5% │ 99%  │ Avg     │ Stdev   │ Max   │
├─────────┼──────┼──────┼───────┼──────┼─────────┼─────────┼───────┤
│ Latency │ 1 ms │ 1 ms │ 5 ms  │ 7 ms │ 1.48 ms │ 1.31 ms │ 33 ms │
└─────────┴──────┴──────┴───────┴──────┴─────────┴─────────┴───────┘
┌───────────┬────────┬────────┬─────────┬─────────┬─────────┬────────┬────────┐
│ Stat      │ 1%     │ 2.5%   │ 50%     │ 97.5%   │ Avg     │ Stdev  │ Min    │
├───────────┼────────┼────────┼─────────┼─────────┼─────────┼────────┼────────┤
│ Req/Sec   │ 3135   │ 3135   │ 5159    │ 5775    │ 4997    │ 812.14 │ 3135   │
├───────────┼────────┼────────┼─────────┼─────────┼─────────┼────────┼────────┤

50k requests in 10.01s, 11.9 MB read


## 25ms delay on a job 

┌─────────┬───────┬───────┬───────┬───────┬─────────┬──────────┬────────┐
│ Stat    │ 2.5%  │ 50%   │ 97.5% │ 99%   │ Avg     │ Stdev    │ Max    │
├─────────┼───────┼───────┼───────┼───────┼─────────┼──────────┼────────┤
│ Latency │ 26 ms │ 29 ms │ 43 ms │ 50 ms │ 31.9 ms │ 15.49 ms │ 210 ms │
└─────────┴───────┴───────┴───────┴───────┴─────────┴──────────┴────────┘
┌───────────┬─────────┬─────────┬─────────┬─────────┬───────┬─────────┬─────────┐
│ Stat      │ 1%      │ 2.5%    │ 50%     │ 97.5%   │ Avg   │ Stdev   │ Min     │
├───────────┼─────────┼─────────┼─────────┼─────────┼───────┼─────────┼─────────┤
│ Req/Sec   │ 260     │ 260     │ 315     │ 335     │ 309.5 │ 25.63   │ 260     │
├───────────┼─────────┼─────────┼─────────┼─────────┼───────┼─────────┼─────────┤

3k requests in 10.04s, 740 kB read


┌─────────┬──────┬──────┬───────┬──────┬────────┬─────────┬───────┐
│ Stat    │ 2.5% │ 50%  │ 97.5% │ 99%  │ Avg    │ Stdev   │ Max   │
├─────────┼──────┼──────┼───────┼──────┼────────┼─────────┼───────┤
│ Latency │ 1 ms │ 1 ms │ 3 ms  │ 5 ms │ 1.3 ms │ 0.79 ms │ 16 ms │
└─────────┴──────┴──────┴───────┴──────┴────────┴─────────┴───────┘
┌───────────┬────────┬────────┬─────────┬─────────┬─────────┬────────┬────────┐
│ Stat      │ 1%     │ 2.5%   │ 50%     │ 97.5%   │ Avg     │ Stdev  │ Min    │
├───────────┼────────┼────────┼─────────┼─────────┼─────────┼────────┼────────┤
│ Req/Sec   │ 3507   │ 3507   │ 5815    │ 5939    │ 5483.55 │ 715.52 │ 3506   │
├───────────┼────────┼────────┼─────────┼─────────┼─────────┼────────┼────────┤

60k requests in 11.01s, 14.4 MB read


## test with redis real queue

first run redis with persistence
`docker run -p 6379:6379 --name redis -d redis --save 60 1 --loglevel warning`

stop service
`docker stop redis`

start service
`docker start redis`

copy env values
`cp .env.example .env`

fill in your redis credentials if they are not `localhost:6379`

### before

┌─────────┬──────┬──────┬───────┬──────┬─────────┬─────────┬───────┐
│ Stat    │ 2.5% │ 50%  │ 97.5% │ 99%  │ Avg     │ Stdev   │ Max   │
├─────────┼──────┼──────┼───────┼──────┼─────────┼─────────┼───────┤
│ Latency │ 1 ms │ 2 ms │ 4 ms  │ 5 ms │ 1.76 ms │ 1.09 ms │ 19 ms │
└─────────┴──────┴──────┴───────┴──────┴─────────┴─────────┴───────┘
┌───────────┬────────┬────────┬─────────┬─────────┬─────────┬────────┬────────┐
│ Stat      │ 1%     │ 2.5%   │ 50%     │ 97.5%   │ Avg     │ Stdev  │ Min    │
├───────────┼────────┼────────┼─────────┼─────────┼─────────┼────────┼────────┤
│ Req/Sec   │ 2367   │ 2367   │ 4787    │ 5051    │ 4424.61 │ 872.51 │ 2367   │
├───────────┼────────┼────────┼─────────┼─────────┼─────────┼────────┼────────┤

44k requests in 10.01s, 10.6 MB read

### after (worse - interceptor ?)

┌─────────┬──────┬──────┬───────┬──────┬─────────┬─────────┬───────┐
│ Stat    │ 2.5% │ 50%  │ 97.5% │ 99%  │ Avg     │ Stdev   │ Max   │
├─────────┼──────┼──────┼───────┼──────┼─────────┼─────────┼───────┤
│ Latency │ 2 ms │ 2 ms │ 6 ms  │ 8 ms │ 2.42 ms │ 1.17 ms │ 19 ms │
└─────────┴──────┴──────┴───────┴──────┴─────────┴─────────┴───────┘
┌───────────┬────────┬────────┬────────┬────────┬─────────┬────────┬────────┐
│ Stat      │ 1%     │ 2.5%   │ 50%    │ 97.5%  │ Avg     │ Stdev  │ Min    │
├───────────┼────────┼────────┼────────┼────────┼─────────┼────────┼────────┤
│ Req/Sec   │ 1871   │ 1871   │ 3971   │ 4099   │ 3674.73 │ 649.61 │ 1871   │
├───────────┼────────┼────────┼────────┼────────┼─────────┼────────┼────────┤

40k requests in 11.01s, 9.66 MB read


## test with remote redis connection (EU to EU)

`autocannon -d 10 -c 10 http://127.0.0.1:3000/redis/before`

`autocannon -d 10 -c 10 http://127.0.0.1:3000/redis/after`

### before

┌─────────┬───────┬───────┬───────┬───────┬──────────┬─────────┬───────┐
│ Stat    │ 2.5%  │ 50%   │ 97.5% │ 99%   │ Avg      │ Stdev   │ Max   │
├─────────┼───────┼───────┼───────┼───────┼──────────┼─────────┼───────┤
│ Latency │ 63 ms │ 64 ms │ 71 ms │ 75 ms │ 64.36 ms │ 2.02 ms │ 79 ms │
└─────────┴───────┴───────┴───────┴───────┴──────────┴─────────┴───────┘
┌───────────┬─────────┬─────────┬─────────┬─────────┬─────────┬───────┬─────────┐
│ Stat      │ 1%      │ 2.5%    │ 50%     │ 97.5%   │ Avg     │ Stdev │ Min     │
├───────────┼─────────┼─────────┼─────────┼─────────┼─────────┼───────┼─────────┤
│ Req/Sec   │ 149     │ 149     │ 153     │ 157     │ 153.6   │ 2.58  │ 149     │
├───────────┼─────────┼─────────┼─────────┼─────────┼─────────┼───────┼─────────┤

2k requests in 10.03s, 367 kB read


### after

┌─────────┬──────┬──────┬───────┬──────┬─────────┬─────────┬───────┐
│ Stat    │ 2.5% │ 50%  │ 97.5% │ 99%  │ Avg     │ Stdev   │ Max   │
├─────────┼──────┼──────┼───────┼──────┼─────────┼─────────┼───────┤
│ Latency │ 1 ms │ 2 ms │ 6 ms  │ 8 ms │ 2.09 ms │ 1.62 ms │ 30 ms │
└─────────┴──────┴──────┴───────┴──────┴─────────┴─────────┴───────┘
┌───────────┬────────┬────────┬────────┬─────────┬────────┬────────┬────────┐
│ Stat      │ 1%     │ 2.5%   │ 50%    │ 97.5%   │ Avg    │ Stdev  │ Min    │
├───────────┼────────┼────────┼────────┼─────────┼────────┼────────┼────────┤
│ Req/Sec   │ 2281   │ 2281   │ 4143   │ 4527    │ 3866.8 │ 725.31 │ 2281   │
├───────────┼────────┼────────┼────────┼─────────┼────────┼────────┼────────┤

39k requests in 10.02s, 9.24 MB read