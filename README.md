# ratelimiter

# Features
* distributed rate limiter
* algorithm
  - sliding windows count
# storage 
  - Redis
    - Jedis/JedisCluster
    - Lettuce
# Reference
* Redis hash https://redis.io/docs/data-types/hashes/
  - HINCRBY https://redis.io/commands/hincrby/
  - HGET https://redis.io/commands/hget/  
* Redis sorted sets https://redis.io/docs/data-types/sorted-sets/
  - ZADD https://redis.io/commands/zadd/
  - ZRANGEBYSCORE https://redis.io/commands/zrangebyscore/
  - ZREMRANGEBYSCORE https://redis.io/commands/zremrangebyscore/
* Redis expire https://redis.io/commands/expire/

pseudo code
```
# we definc precision = {$id}:{$timestamp_sec}
# precision means the minimun unit in duration
HINCRBY {$id}:{$timestamp_sec} count 1
EXPIRE {$id}:{$timestamp_sec} {$duration_sec}
# add precision in sorted set with score
ZADD {$id} GT {$timestamp_sec} {$id}:{$timestamp_sec}
set = ZRANGEBYSCORE {$id} {$timestamp_sec}-{$duration_sec} {$timestamp_sec}
# caclute total precision's count in the last interval
int total = 0
for precision in set
  total += HGET precision count
# determine exceed limit or not
if(total > {$limit})
  return 0
else
  return 1
# remove the unused record
ZREMRANGEBYSCORE {$id}:{$timestamp_sec} -inf {$timestamp_sec}-{$duration_sec}

```
