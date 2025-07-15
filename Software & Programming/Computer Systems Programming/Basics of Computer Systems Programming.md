## 1. Why Computer Systems Programming?
- You can write functional software, but can you write performant software? 
- Most software you interact with runs on cloud servers that:
	- Are very expensive, saving 2% of the costs is significant 
	- Use a lot of power
	- Are idle a significant amount of time
- In the past you could just wait for better hardware
	- Moore’s Law was in full swing doubling transistors every ~18 months, and all the extra transistors went to a single core speed 
	- That meant if you wanted your program to go twice as fast, just wait 18 months and buy new hardware
	- Moore’s law hasn’t hit a wall just yet (although physical limits are coming) 
	- Single core performance has leveled off, due to physics a CPU running at 10 GHz would be hotter than the surface of the sun 
	- Modern systems use many cores, and you need to use them 
		- You’ll need to program with parallelism in mind 
	- For large scale software, you’ll also have to use multiple machines
- There is also a trend to specialize hardware
	- Most CPUs now have dedicated hardware certain functions 
		- e.g. video encoding/decoding 
	- ASIC (application-specific integrated circuit) for less common functions 
		- e.g. bitcoin mining You can also program GPUs that are massively parallel

## 1.1 Latency Numbers

| Type                                  | Latency |
| ------------------------------------- | ------- |
| L1 cache reference (~ 80 KB)          | 1 ns    |
| Branch mispredict                     | 3 ns    |
| L2 cache reference (~ 2 MB)           | 4 ns    |
| [Mutex lock/unlock](Locks.md)         | 17 ns   |
| Send 1K bytes over 1 Gbps network     | 44 ns   |
| Main memory reference                 | 100 ns  |
| Compress 1K bytes with Zippy          | 2 μs    |
| Read 1 MB sequentially from memory    | 3 μs    |
| Read 4K randomly from SSD             | 16 μs   |
| Read 1 MB sequentially from SSD       | 49 μs   |
| Round trip within same datacenter     | 500 μs  |
| Read 1 MB sequentially from disk      | 825 μs  |
| Disk seek                             | 2 ms    |
| Send packet Cali → Netherlands → Cali | 150 ms  |
- Reducing latency requires you to find the bottleneck
- If you can avoid disk I/O, you could improve performance by 100 000 times! 
	- Memcached for example, caches requests to a database or API 
- You could also allocate your memory in a better way 
	- If you can fit the data entirely in cache, it’ll be much faster 
- This makes single-threaded applications faster, but we’ll also parallelize 
- We’ll start with single-threaded applications and than multi-threaded

### 1.2 Optimization

> Premature optimization is the root of all evil. 

- How do I know an optimization is premature?
- This is a bit trickier to answer... 
- What’s the purpose of the program? Are you only going to use it once? 
	- It likely doesn’t matter 
- Are you optimizing the bottleneck? 
	- If not, you don’t need to waste time
- Are you optimizing the common case or special case? 
	- If it’s the special case, you don’t need to worry 
- What price am I paying? 
	- Develop productivity? 
	- Readability? 
	- Program size?
#### 1.2.1 Speedup
- The easiest way to measure performance: Speedup
	- We’re used to latency, how long does it take to do a task
$$S_{latency} = \frac{L_{old}}{L_{new}}$$ where,
	$S_{latency}$ is the speedup in terms of latency
	$L_{old}$ is the latency of the old task
	$L_{new}$ is the latency of the new task

- You can measure latency of an executable with `time`
	- You can use` /usr/bin/time -p <executable>` (not time the shell built-in)
	- `real` is the number of seconds that actually passed (wall time) 
	- `user` is the number of seconds all cores execute for in user mode (for multicore this can be greater than real) 
	- `sys` is the number of seconds all cores execute for in kernel mode

#### 1.2.2 Throughput
**Throughput**: The number of tasks you can do per unit time
$$Q = \frac{P}{L}$$
where,
	$Q$ is the throughput
	$P$ is the number of tasks you can do simultaneously
	$L$ is the latency of a single task

- Higher throughput and lower latency - positive correlation
- Higher latency and higher throughput - negative correlation

#### 1.2.3 Amdahl's Law
- The law states that overall speedup you achieve depends on the fraction you can speedup
- Let $p$ be the proportion of the task you can speedup
- Let $s$ be the speedup of that proportion
$S_{latency}(s)  = \frac{1}{(1-p) + \frac{p}{s}}$ 
	which means
$$
\begin{equation*}
\left\{
\begin{aligned}
S_{\text{latency}}(s) &\leq \frac{1}{1 - p} \\
\lim_{s \to \infty} S_{\text{latency}}(s) &= \frac{1}{1 - p}.
\end{aligned}
\right.
\end{equation*}
$$
- In other words, if you can speed up 90% of your code (p = 0.9), the best overall speed you can achieve is 10
#### 1.2.4 Other Performance Considerations
- Utilization
- Goodput (only measure useful data)
- Jitter (consistency)
- Do we care about the best case, worse case, or average case? 
- Performance evlauation is an active field of research