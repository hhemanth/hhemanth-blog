---
title: Collections/Enumerables Deep dive - Part 1
description: "About Collectios/ Enumerables, deep dive with examples"
date: 2018-04-20
lastmod: 2018-04-20
tags: [sample post, code, highlighting]
image:
  feature: /images/abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---
### Introduction
In this series I am going to do a deep dive into the Ruby Enumerables module. I am going to talk about problem you will face in everyday coding and how you can solve them using Ruby enumerables. I believe Ruby Enumerables module is extremely powerful. Enumerables along with ActiveRecord querying according to me are the 2 most important tools for any Ruby/Rails developer. I am going to do another series for ActiveRecord deep dive.

Fasten  your seat belts and take the dive.

[^1]: <http://en.wikipedia.org/wiki/Syntax_highlighting>

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

Problem 2
You would want to create an array of random numbers, for tru
```ruby
Array.new(100){rand(1..1000)}
````

