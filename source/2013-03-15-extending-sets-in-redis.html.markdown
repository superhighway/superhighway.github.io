---
title: Extending Sets in Redis
date: 2013-03-15
tags: data, tools
---

I seem to encounter some patterns where I need to have Hash data structure in Redis. I sometimes need their values as a list or a set.

```ruby
# ruby
hash = {
  cheez: ['burger'],
  hello: Set.new(['world', 'yay'])
}
```

Hash in Redis does not support the above structure by default. However, by using Set and another data structure, we can simulate it.

## First Approach

First, we are going to implement naive approach like below.

```
HSET "hash", "cheez", "burger"
HSET "hash", "hello", "world,yay"
```

Alright, those can solve our problem here but... we need hack for the above structure to work. 

Let's look at `cheez`. Imagine we will have array of values of `"cheez"`, then we need long comma-separated values for that. Ok, maybe we can use JSON, but again, it's another hack just to parse the stored JSON. And those are not including some concurrency issues that might appear someday...

Now, let's check `hello`. It has the same problems with `cheez`, but with an addition. Anytime we need to add a new value, we need to make sure the added value does not exist in existing list.

## An Extensible Approach Using Sets

Let's go back to definition of Hash in Ruby. Hash contains arbitrary key and value pairs, and the keys are unique. In other words, we can represent Hash keys in Ruby as one Set in Redis.

```
SADD "hash", "cheez"
SADD "hash", "hello"

RPUSH "hash::cheez", "burger"
SADD "hash::hello", "world"
SADD "hash::hello", "yay"
```

So, what we did was storing Hash keys in Redis set, then we store the values using another Redis data structure. Not so bad, eh?

This approach looks complete. Though, sometimes we do need to care a bit about concurrent access and modification, especially when later on you want to change or delete the `hash` often.

## Ensuring Atomic Operation

This step is not necessary if your app does not need the above stuff as atomic operation.

Let's step back a bit. This is what we want.

```ruby
# ruby
hash = {
  cheez: ['burger'],
  hello: Set.new(['world', 'yay'])
}
```

Anytime you set key-value pair to hash, you want it to be (as if) one batch of operation. To make multiple commands work as one unit, use multi/exec.

```
MULTI
SADD "hash", "cheez"
LADD "hash::cheez", "burger"
EXEC

MULTI
SADD "hash", "hello"
SADD "hash::hello", "world"
SADD "hash::hello", "yay"
EXEC
```

There is another term called pipelining, which is similar to multi/exec. [Pipelining is for batch of Redis commands, multi/exec is good for atomicity](https://groups.google.com/forum/?fromgroups=#!topic/redis-db/zVXsg0bjrbg).
