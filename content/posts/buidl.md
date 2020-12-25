---
title: "BUIDL Show & Tell - Advent of Code"
date: 2020-12-20T19:47:51+01:00
description: My BUIDL Grants writeup on the Advent of Code
showToc: true
math: true
---

# Introduction
Hi 👋! I'm Nufflee, a 17 yo zoome- I mean programmer from Croatia, and this is my BUIDL week writeup (show & tell). For my BUIDL week, I decided to focus on my participation in the Advent of Code and another smol project. 

You can also find me on [{{< social_icon "twitter" >}} Twitter](https://twitter.com/Nufflee), [{{< social_icon "github" >}} GitHub](https://github.com/nufflee/), or [{{< social_icon "twitch" >}} Twitch](https://www.twitch.tv/nuffleee).

## [BUIDL Grants](https://troynikov.io/buidl-grants/)

For the uninitiated, the idea of BUIDL Grants is best summarized by the following sentence:
> We pay zoomers to stop tweeting and BUIDL.

Essentially, it is an incentive for [zoomers](https://en.wikipedia.org/wiki/Generation_Z) like me to stop tweeting for a week and buidl (yes, I know, that *is* the right spelling) something instead, organized by [@atroyn](https://twitter.com/atroyn).

## [Advent of Code](https://adventofcode.com/)

Advent of Code is an advent calendar of 25 programming problems of increasing difficulty. The problems are two-part, where the second part is harder but builds on top of the first one. Solving each part grants you one gold star ({{< aoc_stars solvedCount=1 >}}), for a total of 50 {{< aoc_stars solvedCount=1 >}} by Christmas. 

They involve everything from basic brute forcing to dynamic programming, graphs, and trees to the [Chinese remainder theorem](https://en.wikipedia.org/wiki/Chinese_remainder_theorem).

{{< warning >}}
This post **does** contain Advent of Code spoilers for days 12 to 18 and may contain light spoilers for other days too.
{{< /warning >}}

## Goals

Going into Advent of Code, I had multiple goals:
- Mainly, spend more time practicing [Rust](https://www.rust-lang.org/) as I've been wanting to learn it for a while 
- Learn about various computer science concepts like time and memory complexity, trees and graphs, and, apparently, dynamic programming
- See how long I can stick with the daily schedule since I've always had issues with finishing projects 

---

I [started the week on December 11th](https://twitter.com/Nufflee/status/1337512892464291847), the 11th day of Advent of Code, and finished on December 18th. 

Although I didn't solve all of the problems, below are the breakdowns of what I did each day and how I approached them.

### Legend

|                                   |                          |
| --------------------------------- | ------------------------ |
| {{< aoc_stars solvedCount=1 >}}   | Part solved successfully |
| {{< aoc_stars unsolvedCount=1 >}} | Part not solved          |

# Day 1
## Advent of Code Day 12 -- Rain Risk ({{< aoc_stars solvedCount=2 >}})

[{{< social_icon "github" >}} Full Solution on GitHub](https://github.com/Nufflee/AdventOfCode-2020/tree/master/day12) | [{{< social_icon "twitch" >}} VOD on Twitch](https://www.twitch.tv/videos/845334812)

### [Problem](https://adventofcode.com/2020/day/12)
My BUIDL week started with a fairly straightforward problem, as expected for day 12. The problem revolved around parsing and interpreting a list of commands for a ship navigation system. 

There were 7 possible commands: North (`N`), South (`S`), West (`W`), East (`E`), Right (`R`), Left (`L`) and Forward (`F`) followed by a number. For example:

```plaintext
F10
N3
F7
R90
F11
```

Both parts required interpreting these commands, although with different rules, and outputting the [Manhattan distance](https://en.wikipedia.org/wiki/Taxicab_geometry) between the ship's starting and final position.

### Solution
The solution is pretty basic but there are a few interesting things about it.

#### Integer to Enum conversion in Rust
Rust may be a modern language that comes with a lot of batteries but this is not one of them. Converting an integer to an enum value requires several external dependencies, specifically [`num-derive`](https://crates.io/crates/num-derive) and [`num-traits`](https://crates.io/crates/num-traits). Even after that, I still had to derive a trait and use `FromPrimitive::from_i32<TEnum>(i32) -> TEnum` (not the actual function definition) for the conversion.

#### 2D [Rotation matrices](https://en.wikipedia.org/wiki/Rotation_matrices)
Part 2 of the problem required us to rotate a 2D vector around a point. Even though rotation matrices weren't exactly necessary since all rotations were multiples of $90°$ ($\frac{\pi}{2}$) and could be achieved by inverting and swapping coordinates, I was excited to finally use them in a real world application. [Here](https://github.com/Nufflee/AdventOfCode-2020/blob/a14581c2022df869df0c1e4728d4d39b99cb3715/day12/src/part2.rs#L15-L22)'s the piece of code in question:

```rust
let sin = angle.sin() as i32;
let cos = angle.cos() as i32;

position = Pos(
  position.x * cos - position.y * sin,
  position.x * sin + position.y * cos,
);
```

... or in math terms (because I didn't set up [MathJax](https://www.mathjax.org/) for nothing, right):
$$
\text{position} = \begin{bmatrix} 
  x\ \cos(\text{angle}) - y\ \sin(\text{angle}) \cr
  x\ \sin(\text{angle}) + y\ \cos(\text{angle}) \cr
\end{bmatrix}
$$

# Day 2
## Advent of Code Day 13 -- Shuttle Search ({{< aoc_stars solvedCount=1 unsolvedCount=1 >}})

[{{< social_icon "github" >}} Full Solution on GitHub](https://github.com/Nufflee/AdventOfCode-2020/tree/master/day13) | [{{< social_icon "twitch" >}} VOD on Twitch](https://www.twitch.tv/videos/845369228)

### [Problem](https://adventofcode.com/2020/day/13)
The 13th problem was a special one (notice the missing {{< aoc_stars solvedCount=1 >}}). 

In part 1 we had to, given a bus departure schedule, determine which bus departs next after a given timestamp. 

Part 2 was a lot more interesting, using the same schedule, we had to find the earliest timestamp at which each bus departs exactly 1 minute/time step after the previous one.  

### Solution
#### Part 1
This part was very straightforward as it didn't even require brute forcing and it was possible to come up with a [simple mathematical solution](https://github.com/Nufflee/AdventOfCode-2020/blob/a14581c2022df869df0c1e4728d4d39b99cb3715/day13/src/part1.rs#L8-L13).

#### Part 2
Unlike part 1, this one wasn't straightforward at all. I spent several hours malding[^1] trying to implement a brute force solution, but that failed horribly. My idea was to iteratively advance time and check if the required condition is met every iteration. This would work on a small dataset but would take multiple days on the actual dataset. 

People have come up with various clever tricks, such as advancing time by a timestamp bigger than 1, but the expected solution involved the [Chinese ~~clown~~ remainder theorem](https://en.wikipedia.org/wiki/Chinese_remainder_theorem).

I really disliked this problem as one basically had to reinvent the theorem if they weren't familiar with it previously, and I'm not alone here. Therefore, I did not solve this part of the problem, and even if I did, it wouldn't have helped me advance towards any of my goals anyway.

# Day 3
## Advent of Code Day 14 -- Docking Data  ({{< aoc_stars solvedCount=2 >}})

[{{< social_icon "github" >}} Full Solution on GitHub](https://github.com/Nufflee/AdventOfCode-2020/tree/master/day14) | [{{< social_icon "twitch" >}} VOD on Twitch](https://www.twitch.tv/videos/845369606)

### [Problem](https://adventofcode.com/2020/day/14)
Day 14 was a relatively easy problem. It described a simple computer with two operations, one that sets the bitmask (`mask`) and another which writes a value to a memory address taking the bitmask into account (`mem`). Interestingly, the said computer is 36-bit (addresses and values are 36 bits wide). For example:

```
mask = XXXXXXXXXXXXXXXXXXXXXXXXXXXXX1XXXX0X
mem[8] = 11
mem[7] = 101
mem[8] = 0
```

Part 1 required us to apply the given bitmask to the value being written, while part 2 required it to be applied to the memory address. But in part 2 there's a twist, the previously ignored `X` bits are now considered as floating -- they can be either `0` or `1`. Due to this, every write operation writes to many different addresses to cover all of the possible combinations of floating bits.

### Solution
#### Part 1
This one was still straightforward but required more work than before, the steepness of the difficulty curve is already showing. This was another problem where a part of the logic, in this case, the parsing of the instructions, was shared between the two parts of the problem and I was able to extract it into another Rust module, [`parser.rs`](https://github.com/Nufflee/AdventOfCode-2020/blob/master/day14/src/parser.rs). The fact that the emulated machine is 36-bit instead of 32-bit (or less) also had me use a 64-bit integer type (`u64`) instead of the usual 32-bit `u32`, but this is not the first time that was the case.

#### Part 2
But this part, again, required a bit of malding to come up with a way to generate all possible combinations of the floating bits. [My solution](https://github.com/Nufflee/AdventOfCode-2020/blob/a14581c2022df869df0c1e4728d4d39b99cb3715/day14/src/part2.rs#L33-L55) was slightly scuffed, I:
1. Counted the number of floating (`X`) bits
1. Created a bitmask based on them
1. Generated all numbers from $0$ to $2^{\text{number of floating bits}}$
1. Spread them onto the actual memory address based on the bitmask created previously

... but it worked out perfectly fine!

## ToF Camera

[{{< social_icon "github" >}} Code on GitHub](https://github.com/Nufflee/tof-camera)

Alongside the Advent of Code, I started another smol project. I got an Android phone with LIDAR, actually a scannerless Time of Flight (ToF) camera, and finally found some time to play around with it. I used Android Studio, Kok- Kotlin, and the [Camera2 API](https://developer.android.com/reference/android/hardware/camera2/package-summary) for this whole endeavor.

First, I enumerated all of the cameras on my phone to find out that the ToF one supports two modes, `640x480` at 5 FPS and `320x240` at 20 FPS. To start receiving frames from the camera, I of course had to jump through multiple hoops of Java ~~BS~~ abstractions, factories, and builders but once that was done, I was presented with a ~0.05 FPS depth map slideshow. That's when I realized how much Java/JVM suck and the amount of overhead there is to calling native methods in big loops. Having fixed that by lifting all native calls outside of the pixel-by-pixel loop, this is what I ended up with:

{{< video src="/blog/tof-camera1.webm" >}}

# Day 4
## Advent of Code Day 15 -- Rambunctious Recitation ({{< aoc_stars solvedCount=2 >}})

[{{< social_icon "github" >}} Full Solution on GitHub](https://github.com/Nufflee/AdventOfCode-2020/tree/master/day15) | [{{< social_icon "twitch" >}} VOD on Twitch](https://www.twitch.tv/videos/845389475)

### [Problem](https://adventofcode.com/2020/day/15)
In the 15th problem, we were given a list of starting numbers and a set of rules for generating a sequence. Once the starting numbers were appended to the sequence, rules for generating new numbers were as follows:
- If this is the first time the (previous) number has showed up, add 0
- Otherwise, add how many numbers ago it was last seen

Part 1 required us to calculate the `2020th` number of this sequence while part 2 required the... *drumroll please* `30000000th` (30 **millionth**).

### Solution
#### Part 1
For this part, I implemented a very simple brute force solution that worked right away.

#### Part 2
The description of part 2 immediately had me intimidated as I imagined that the old brute force solution would surely not work... but to my greatest surprise, it worked quite well, taking only a few seconds to complete. 

Having solved this problem in record time, I was extremely underwhelmed and decided to improve upon my solution. The first improvement was to, instead of linearly searching the list of all previous numbers, maintain a hash map of each previously seen number and its last index in the sequence. This greatly improved performance by decreasing the lookup time complexity from `O(n)` to `O(1)`. Quickly, I also realized that I don't even need to keep a list of all numbers in the sequence as the generation of the next one always only depends on the previous number (and the hash map). Only keeping track of one number instead of up to 30 million of them greatly reduced the memory footprint too.

## ToF Camera

[{{< social_icon "github" >}} Code on GitHub](https://github.com/Nufflee/tof-camera)

Spending little time on the Advent of Code problem, I decided to do a bit more work on this project. I did some more profiling and optimization and managed to get the time of processing a single `640x480` frame down to 60 ms on average, much less than 200 ms it takes to capture a new frame at 5 FPS. While that is great, I missed one critical detail -- the app **consumed** my battery. It [used 28.2% of it](https://cdn.discordapp.com/attachments/773886565449334785/788465098419208222/SmartSelect_20201215-185824_Device_care.jpg) in just 2.5h, ~11% per hour! I assume this is mainly because doing camera things, especially with lasers, is inherently power-hungry, but there are things that can be done to alleviate this. I also added a few more UI elements that can be seen here:

{{< video src="/blog/tof-camera2.webm" >}}

The app is currently obviously very basic but there are many things I'd like to add to it in the future:
- Firstly, to address excessive power usage, detecting when the user goes idle and disabling the camera should help a lot, together with more efficient resource usage by using C++
- 60 ms per frame may not sound like a lot, but it only performs the very basic processing operations, however, greater performance is required to run more advanced algorithms. The said performance can likely be gained by using C++ through the [Android NDK](https://developer.android.com/ndk) instead of Java (Kotlin)
- More advanced algorithms for edge detection, frame interpolation, fusion with the regular visible light camera(s) etc.

# Day 5
## Advent of Code Day 16 -- Ticket Translation ({{< aoc_stars solvedCount=2 >}})

[{{< social_icon "github" >}} Full Solution on GitHub](https://github.com/Nufflee/AdventOfCode-2020/tree/master/day16)

### [Problem](https://adventofcode.com/2020/day/16)
Day 16 was another data validation problem. First, given a list of validation rules in form `{field name}: {a}-{b} or {b}-{c}` and a list of tickets to be validated, we had to filter out the completely invalid ones that contain values which aren't valid for any field's validation rule. Tickets were given as lists of numbers in a random, yet consistent, order.

Once that was done, in part 2, we had to, based on the list of remaining valid tickets, deduce the correct order of fields so that all remaining tickets were valid. The problem was set up so that is always the case and there is only one solution. Once the order has been deduced, we had to decode our ticket and output the sum of specific fields.

I did not stream solving this problem on Twitch and therefore the VOD link is missing.

### Solution
#### Part 1
As in all problems so far, it was possible to solve part 1 with a simple brute force method.

#### Part 2
Part 2, on the other hand, was more interesting, although not due to it being an interesting problem, but due to the description being bad. I was able to solve all problems up until this point in one day but this one took two and caused me to fall behind. I first implemented a simple iterative solution, but due to the nature of hash maps, it was always returning a different result so it wasn't hard to notice that something was off. Using this method, there were multiple solutions because each field matched multiple different validator rules. After a fair amount of time, I couldn't figure out how to properly handle this conflict resolution and resorted to [the subreddit](https://www.reddit.com/r/adventofcode/) for hints.

One thing to notice was that there is always one field which matches only one validator. The correct algorithm for doing this conflict resolution was to find this one field, remove it from the list of candidates for other validators and add it into the final validator to field mapping to achieve a 1 validator to 1 field mapping instead of 1 validator to many fields. I still don't understand how I was supposed to come up with this on my own, but once I was aware of this pattern, it didn't take much time to come up with the solution. 

# Day 6
## Advent of Code Day 17 -- Conway Cubes ({{< aoc_stars solvedCount=2 >}})

[{{< social_icon "github" >}} Full Solution on GitHub](https://github.com/Nufflee/AdventOfCode-2020/tree/master/day17) | [{{< social_icon "twitch" >}} VOD on Twitch](https://www.twitch.tv/videos/845389155)

### [Problem](https://adventofcode.com/2020/day/17)
From the title, it was already obvious this problem was about [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life), the same as [day 11](https://adventofcode.com/2020/day/11). This one came with two big twists though. In the first part, we had to implement a 3-dimensional Game of Life in an infinite space, while in part 2 we had to do the same but in *4* spatial dimensions.

The goal in both parts was to output the number of active/alive (hyper)cubes after 6 cycles of evolution.

### Solution
The concept wasn't very complicated but the implementation was quite different from day 11 due to the infiniteness of the space. Instead of being able to use an n-dimensional array, like in day 11, I settled on using a hash set and only storing the positions of active cubes as those are the only ones we care about. The rest of the implementation was fairly similar to day 11, just with an added layer of loops, or two.

In the end, I didn't actually manage to solve this problem during the BUIDL week due to a very dumb error, and falling behind on the previous problem. The rules in the description of the problem read "... is active and exactly 2 *or* 3 ..." and my brain, being on autopilot, typed the `||` (or) operator instead of the `&&` (and) operator which was correct in the logical context. This two character difference took me almost 2 days to debug...

Once that was fixed, my solution worked perfectly and I was able to move onto part 2. For part 2, I just added another coordinate to my vectors and another layer of loops to my simulation. This certainly isn't the smartest solution and I believe it's possible to implement a general, n-dimensional, solution to this but I yet have to explore that.

# Day 7
## Advent of Code Day 18 ({{< aoc_stars unsolvedCount=2 >}})

### [Problem](https://adventofcode.com/2020/day/18)
I did not solve this problem yet, but due to it's very interesting nature, I think it is worth a mention. The goal of part 1 is to parse and evaluate a mathematical expression using only two operators, `+`, `*` and parenthesis. The catch is that both operators have the same precedence and the whole expression is evaluated from left to right, regardless of the operators used. Since I didn't solve part 1, I can't talk about part 2 here.

### Solution
The reason I found this problem so interesting is because I've always been interested in parsers, compilers, interpreters and programming language development in general. Even though I have implemented a few toy languages, I never really understood [ASTs (Abstract Syntax Trees)](https://en.wikipedia.org/wiki/Abstract_syntax_tree) and this is a great opportunity to learn more about them. In theory, evaluating these expressions should be even easier as I can traverse the AST without having to pay attention to precedence.

# Conclusion
In conclusion, I consider my BUIDL week and participation in the Advent of Code in general a great success. I have accomplished all of the goals mentioned at the beginning: I did become more familiar with Rust, I did learn about various computer science concepts and I managed to complete 17 days of puzzles. Considering day 2 is the furthest I've ever got, this certainly is progress.

And this is what the Advent of Code map looked like at the end of the week:
{{< figure src="https://i.imgur.com/TfC6KOQ.png" width="400" class="centered-image" >}}

## Next steps
Setting up a blog is something I've been planning to do for a while but kept putting off. Now that all of the boilerplate work has been done, I will have a much easier time motivating myself, and I certainly do have many topics I want to write about -- from Raspberry Pi camera muxing to G.Skill RGB RAM SMBus control protocol reverse engineering, SDRs (Software Defined Radios) and more.

Speaking of SDRs, a big part of the BUIDL grant will most likely be going towards getting a better, TX-capable SDR :^).

[^1]: malding /ˈmôldiNG/ -- zoomer verb, mad + bald (+ ing)