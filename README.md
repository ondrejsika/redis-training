# Redis Training

    2019 Ondrej Sika <ondrej@ondrejsika.com>
    https://github.com/ondrejsika/redis-training

## Install Redis

### Ubuntu

```
sudo apt-get update
sudo apt-get install redis-server
```

### Mac

```
brew install redis
```

### Windows

```
choco install redis-64
```

### Docker

```
docker run --name redis -d redis
```

or

```
docker run --name redis -p 6379:6379 -d redis
```

## Connect Redis

### Local Server

If you want to connect redis, use:

```
redis-cli
```

You can check if the Redis responding:

```
PING
```

You will see:

```
ondrej@sika-macbookpro:~$ redis-cli
127.0.0.1:6379> PING
PONG
127.0.0.1:6379> PING hello
"hello"
127.0.0.1:6379>
```

### Remote Server

```
redis-cli [-h <host>] [-p <port>] [-a <password>]
```

### Example of Redis CLI

From Redis CLI:

```
SET message helloworld
GET message
```

From bash:

```
redis-cli SET message helloworld
redis-cli GET message
```

You will see:

```
ondrej@sika-macbookpro:~$ redis-cli
127.0.0.1:6379> SET message helloworld
OK
127.0.0.1:6379> GET message
"helloworld"
127.0.0.1:6379>
ondrej@sika-macbookpro:~$ redis-cli SET message helloworld
OK
ondrej@sika-macbookpro:~$ redis-cli GET message
"helloworld"
```

## Commands

Docummentation for all Redis commands is here: <https://redis.io/commands>

## Redis Keys

Those are commands for working with keys

- `DEL <key>` - This command deletes the key, if it exists.
- `DUMP <key>` - This command returns a serialized version of the value stored at the specified key.
- `EXISTS <key>` - This command checks whether the key exists or not.
- `EXPIRE <key> <seconds>` - Sets the expiry of the key after the specified time.
- `EXPIREAT <key> <timestamp>` - Sets the expiry of the key after the specified time. Here time is in Unix timestamp format.
- `KEYS <pattern>` - Finds all keys matching the specified pattern. SLOW, DON'T DO IT ON LARGE DATABASES
- `MOVE <key> <db>` - Moves a key to another database.
- `PERSIST <key>` - Removes the expiration from the key.
- `TTL <key>` - Gets the remaining time in keys expiry.
- `RENAME <key> <newkey>` - Changes the key name.
- `TYPE <key>` - Returns the data type of the value stored in the key.

## Redis Data Types

### Strings

- SET
- GET

```
SET msg hello
GET msg
```

All string commands: <https://www.tutorialspoint.com/redis/redis_strings.htm>

### Ingrements

- `INCR <key>`
- `INCRBY <key> <increment>`

```
ondrej@sika-macbookpro:~$ redis-cli
127.0.0.1:6379> incr counter
(integer) 1
127.0.0.1:6379> incr counter
(integer) 2
127.0.0.1:6379> incrby counter 10
(integer) 12
127.0.0.1:6379> incrby counter -1
(integer) 11
```

### Lists

- LPUSH / RPUSH
- LRANGE
- LLEN

```
LPUSH words hello
LPUSH words word
LPUSH words and redis
LRANGE words 0 10
LLEN words
```

You will see:

```
127.0.0.1:6379> LPUSH words hello
(integer) 1
127.0.0.1:6379> LPUSH words word
(integer) 2
127.0.0.1:6379> LPUSH words and redis
(integer) 4
127.0.0.1:6379> LRANGE words 0 10
1) "redis"
2) "and"
3) "word"
4) "hello"
127.0.0.1:6379> LLEN words
(integer) 4
```

All list commands: <https://www.tutorialspoint.com/redis/redis_lists.htm>

### HashMaps (Objects)

You can store objects in Redis using HashMaps

- HMSET
- HGETALL
- HGET

```
HMSET user:ondrej firstname Ondrej lastname Sika email ondrej@example.com
HGETALL user:ondrej
HGET user:ondrej email
```

```
ondrej@sika-macbookpro:~$ redis-cli
127.0.0.1:6379> HMSET user:ondrej firstname Ondrej lastname Sika email ondrej@example.com
OK
127.0.0.1:6379> HGETALL user:ondrej
1) "firstname"
2) "Ondrej"
3) "lastname"
4) "Sika"
5) "usename"
6) "ondrejsika"
7) "email"
8) "ondrej@example.com"
127.0.0.1:6379> HGET user:ondrej email
"ondrej@example.com"
```

All hash map commands: <https://www.tutorialspoint.com/redis/redis_hashes.htm>

## Publish Subscribe

Subsribe one client

```
SUBSCRIBE chat
```

Publish by another

```
PUBLISH chat "Hello World!"
```

Python Example of Pub/Sub: <https://gist.github.com/bamthomas/4165078>

All Pub/Sub commands: <https://www.tutorialspoint.com/redis/redis_pub_sub.htm>

## Transactions

Redis support transactions. Start your transaction using `MULTI` and commit it using `EXEC`. You can abort transaction using `DISCARD`.

```
ondrej@sika-macbookpro:~$ redis-cli
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> SET counter 1000
QUEUED
127.0.0.1:6379> INCR counter
QUEUED
127.0.0.1:6379> INCR counter
QUEUED
127.0.0.1:6379> EXEC
1) OK
2) (integer) 1001
3) (integer) 1002
```

## Persistence

Redis is in memory database but it can be persiatant. You can save dump of data to redis directory (see redis directory by command `CONFIG get dir`) using `SAVE`. Or you can save it on background using `BGSAVE`. Redis dump will be in file `dump.rdb`.

If you want to restore your data, just run redis server and redis automatically load dump (`dump.rdb`) to memory.

## Auth

Redis by default doesn't require any password.

You can set up auth using commands:

```
CONFIG set requirepass magicworld
```

You can also setup password in `redis.conf`

```
# redis.conf
requirepass magicworld
```

Then you have to call `AUTH <password>` on session or use `redis-cli -a <password>` to connect it.

See:

```
ondrej@sika-macbookpro:~$ redis-cli
127.0.0.1:6379> GET a
(error) NOAUTH Authentication required.
127.0.0.1:6379> AUTH magicworld
OK
127.0.0.1:6379> GET a
(nil)
127.0.0.1:6379>
ondrej@sika-macbookpro:~$ redis-cli -a magicworld
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> GET a
(nil)
127.0.0.1:6379>
```

## Databases

Redis supports multiple databases. Those databases dont have names, but indexes. Default database is `0`.

To select database, just call `SELECT <db>`.

You can swap database indexes using `SWAPDB <db1> <db2>`.
