---
layout: post
title: "Distributed Scheduled Queue With Redis"
description: ""
category: 
tags: ["redis","queue","distributed"]
date: 2012/10/20
---

Unlike the classic FIFO queue, jobs are scheduled for execution in the future, possibly on a specific day, week, month year.. similar to "cron" in a Unix OS. Unlike the normal Unix "cron" implementation, we want it to be distributed, so that several machines(workers) can query and then execute these jobs.

### Implementing

 * _Planning_ - The standart Unix cron format is very flexible and has been time tested for describing schedules
 * _Managing/Coordinating_ - The queues are stored in Redis Sorted Sets, Lua will be used for atomic execution
 * _Execution_ - Getting the pieces together

#### Planning 

"Unix Time" format, is the number of milliseconds since (*UTC*) 1 January 1970 
Example (* the actual unix time number is in UTC, while the formatted date is localized)

	1350745504313 == Sat Oct 20 2012 17:05:04 GMT+0200 (IST)

Using this format will make it possible to slice the queue with specific point in time in mind.

#### Managing/Coordinating

Redis'st will be holding the job IDs as members scored by their corresponding execution times(in unix time format). <BR/>

<img src="https://docs.google.com/drawings/pub?id=1Sc9DY0Ne4YZZeC8-R2WCTAQaJvnbM3pnpDPtQpSpEUU&amp;w=960&amp;h=359">

Redis commands that will come in handy
 
 * Scheduling job run: *ZADD cron:queue 1350745504313 1000*
 * Finding a job to run: *ZRANGEBYSCORE cron:queue 1350745504314 LIMIT 0 1*

#### Execution

Without going into any platform specific implementation details, this is what the process looks like

1. When a new schedule is added, jobs are created for upcoming execution times, which are calculated for the next two hours and added to our cron queue
2. A special job is added to be run every hour, it will run over all the schedules, calculate and add their execution time to the scheduled queue, two hours into the future
3. Workers will be polling the database every second to find jobs
4. Once an entry comes up, it is removed from the queue and added to another Sorted Set queue, so we dont "forget" any jobs..

As for items 3 and 4 above, it is not possible to implement this logic in the client, because for example when we find a job that needs to be executed, another worker might grab it, or worse they both "grabbed" it and are now working on the same job. To work around this, we need to write a little Lua script, since Lua scripts are executed serially, just like any other Redis command

```lua
local currentTimeMS = KEYS[1]
local workingQueue = "scheduler:working"
local scheduleQueue = "schedule"
-- for easier reading, epoch time is in milliseconds
local second = 1000

local function foundJob(jobID)
	-- implement removing the job from all other queues
	-- implement updating job status where the actual job object is stored
end

local function findNextJob()
	local result
	-- check if there are any "stale" jobs
	result = redis.call( 'ZRANGEBYSCORE', workingQueue, 0, currentTimeMS-60*second, 'LIMIT',0,1 )
	if result[1] ~= nil then return foundJob(result[1]) end
	-- check the normal queue
	result = redis.call( 'ZRANGEBYSCORE', scheduleQueue, 0, currentTimeMS, 'LIMIT',0,1 )
	if result[1] ~= nil then return foundJob(result[1]) end
	-- if we didnt find anything return nil
	return nil
end

return findNextJob()
```


And there you have it! a "Distributed scheduled queue" implemented on top of Redis.

If you are going to implement this with node, I created a little cron format parser you can find here:
[JSCron](https://github.com/romansky/JsCron)




	

