---
title: Collections/Enumerables Deep dive - Part 1
description: "About Collectios/ Enumerables, deep dive with examples"
date: 2018-04-20
lastmod: 2018-04-20
tags: [enumerables, collections, select, flat_map, detect, arrays]

---
### Introduction
In this series I am going to do a deep dive into the Ruby Enumerables module. I am going to talk about problem you will face in everyday coding and how you can solve them using Ruby enumerables. I believe Ruby Enumerables module is extremely powerful. Enumerables along with ActiveRecord querying according to me are the 2 most important tools for any Ruby/Rails developer. I am going to do another series for ActiveRecord deep dive.

Fasten  your seat belts and take the dive.


### Problem 1

Given an array of 1000 random numbers (rand_arr). Split the array into 2 arrays, odd_arr  and even_arr. odd_arr will contain odd numbers while even_arr will contain even numbers. The challenge is to do it in a single ruby statement

Attempt 1
```ruby
odd_arr = []
even_arr = []
rand_arr.each do |elem|
  odd_arr << elem if elem % 2 == 0
  even_arr << elem if elem % 2 == 1
end
```
Attempt 2
```ruby
odd_arr = rand_arr.select{|elem| elem%2 ==0 }
even_arr = rand_arr.select{|elem| elem%2 ==0 }
```
Attempt 3
```ruby
odd_arr = rand_arr.select{|elem| elem.odd?}
even_arr = rand_arr.select{|elem| elem.even?}
```
Attempt 4
```ruby
odd_arr = rand_arr.select(&:odd?)
even_arr = rand_arr.select(&:even?)
```

Attempt 5
```ruby
odd_arr,even_arr = rand_arr.partition(&:odd?)
```

### Problem 2
You would want to create an array of random numbers, for trying out few of your ideas.
So here is the easiest way to do it. Believe me whenever you want to create an array, you wouldn't want to start typing an array of numbers, and even if you want to how many numbers can you type?

```ruby
Array.new(100){rand(1..1000)}
```
If you want to create a series of natural numbers.

```ruby
(1..30).to_a
#[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30]
```

If you want to create numbers with a step

```ruby
(0..30).step(2).to_a
#[0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30]
(0..100).step(5).to_a
#[0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95, 100]
```
Alternatively,

```ruby
3.step(30,3).to_a
#[3, 6, 9, 12, 15, 18, 21, 24, 27, 30]
```
And if you want to do more
```ruby
(1..10).to_a.map{|n| 2**n}
#[2, 4, 8, 16, 32, 64, 128, 256, 512, 1024]
(1..10).to_a.collect{|n| 2**n}
#[2, 4, 8, 16, 32, 64, 128, 256, 512, 1024]
```
### Problem 3
You have been given an array of strings(or numbers) and you want to find repetitions in the array. Also which string repeated itself how many times.

Lets generate some random numbers
```ruby
arr = Array.new(20){rand(1..5)}
#[1, 1, 3, 3, 4, 2, 5, 3, 1, 2, 1, 5, 1, 1, 4, 5, 5, 5, 4, 1]
```
lets attempt this problem.
```ruby
repetitions = {}
arr.each { |a|
    repetitions[a].nil? ? repetitions[a]=0 : repetitions[a]+=1
}
repetitions
# {5=>2, 2=>2, 4=>2, 3=>3, 1=>1}
```
A more elegant solution is
```ruby
repetitions = Hash.new(0)
arr.each{|n| repetitions.store(n, repetitions[n]+1)}
repetitions
# {5=>2, 2=>2, 4=>2, 3=>3, 1=>1}
```

This is a good solutions, but lets attempt it in a different way.

```ruby
arr.group_by{|n| n}.map{|k,v| {k=>v.count}}
#[{5=>2}, {2=>2}, {4=>2}, {3=>3}, {1=>1}]
```
What we have is an array of hashes, we can also get Array of arrays.
```ruby
repetitions = arr.group_by{|n| n}.map{|k,v| [k,v.count]}
#[[5, 2], [2, 2], [4, 2], [3, 3], [1, 1]]
```

Now if we want to convert an array of arrays to a Hash
```ruby
Hash[*repetitions.flatten]
#{5=>2, 2=>2, 4=>2, 3=>3, 1=>1}
```





