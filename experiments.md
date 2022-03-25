# Threaded Merge Sort Experiments


## Host 1: 2018 Macbook laptop

- CPU: Intel Core i5
- Cores: 2 real cores, 4 logical cores
- Cache size (if known): 4MB
- RAM: 16GB
- OS: MacOS
- Running Processes: ~1500

### Input data

The input data for this experiment was 4 different text files, each with 100,000,000 integers in it.
The files were created using the following command:
shuf -i1-100000000 hund-mil-shufX.txt
where X is the number of the file, between 1 and 4. 

At each thread count, each of the four files was run once. This was to show how the algorithm performed
not only in different thread counts, but also different preconditions on the sort.

The times for msort to sort the number files were as follows:
1. 24.38
2. 25.78
3. 26.42
4. 24.06

### Experiments

*Replace X, Y, Z with the number of threads used in each experiment set.*

#### 1 Thread

Command: MSORT_THREADS=1 ./tmsort 100000000 < hund-mil-shufX.txt > /dev/null 

Sorting portion timings:

1. 24.36 seconds
2. 24.12 seconds
3. 23.91 seconds
4. 23.83 seconds

#### 2 Threads

Command: MSORT_THREADS=2 ./tmsort 100000000 < hund-mil-shufX.txt > /dev/null 

Sorting portion timings:

1. 13.20 seconds
2. 13.07 seconds
3. 12.90 seconds
4. 12.67 seconds

#### 3 Threads

Command: MSORT_THREADS=3 ./tmsort 100000000 < hund-mil-shufX.txt > /dev/null 

Sorting portion timings:

1. 13.86 seconds
2. 13.87 seconds
3. 13.78 seconds
4. 13.89 seconds

#### 4 Threads

Command: MSORT_THREADS=4 ./tmsort 100000000 < hund-mil-shufX.txt > /dev/null

Sorting portion timings:

1. 9.39 seconds
2. 9.38 seconds
3. 9.56 seconds
4. 9.41 seconds

#### 5 Threads

Command: MSORT_THREADS=5 ./tmsort 100000000 < hund-mil-shufX.txt > /dev/null

Sorting portion timings:

1. 9.87 seconds
2. 9.95 seconds
3. 9.84 seconds
4. 9.87 seconds

#### 6 Threads

Command: MSORT_THREADS=6 ./tmsort 100000000 < hund-mil-shufX.txt > /dev/null

Sorting portion timings:

1. 10.17 seconds
2. 13.15 seconds
3. 10.92 seconds
4. 10.46 seconds

#### 8 Threads

Command: MSORT_THREADS=8 ./tmsort 100000000 < hund-mil-shufX.txt > /dev/null

Sorting portion timings:

1. 9.42 seconds
2. 9.52 seconds
3. 9.39 seconds
4. 9.68 seconds

#### 16 Threads

Command: MSORT_THREADS=16 ./tmsort 100000000 < hund-mil-shufX.txt > /dev/null

Sorting portion timings:

1. 9.37 seconds
2. 9.51 seconds
3. 9.66 seconds
4. 9.60 seconds

## Host 2: Custom Desktop PC

 - CPU: AMD Ryzen 3700X
 - Cores: 8 real cores, 4 logical cores
 - Cache: L3=32MB, L2=4MB, L1=0.5MB
 - RAM: 32GB
 - OS: Arch Linux
 - Running processes: 402

#### 1 Thread

Command: MSORT_THREADS=1 ./tmsort 10000000 < random.txt > /dev/null

1. 1.46 sec
2. 1.45 sec
3. 1.49 sec
4. 1.43 sec

#### 2 Threads

Command: MSORT_THREADS=2 ./tmsort 10000000 < random.txt > /dev/null

1. 0.783 sec
2. 0.761 sec
3. 0.779 sec
4. 0.793 sec

#### 4 Threads

Command: MSORT_THREADS=4 ./tmsort 10000000 < random.txt > /dev/null

1. 0.443 sec
2. 0.446 sec
3. 0.442 sec
4. 0.440 sec

#### 8 Threads


Command: MSORT_THREADS=8 ./tmsort 10000000 < random.txt > /dev/null

1. 0.272 sec
2. 0.272 sec
3. 0.308 sec
4. 0.262 sec

#### 16 Threads

Command: MSORT_THREADS=16 ./tmsort 10000000 < random.txt > /dev/null

1. 0.220 sec
2. 0.223 sec
3. 0.209 sec
4. 0.226 sec

#### 32 Threads

Command: MSORT_THREADS=32 ./tmsort 10000000 < random.txt > /dev/null

1. 0.222 sec
2. 0.217 sec
3. 0.230 sec
4. 0.220 sec

## Observations and Conclusions


For host one, major improvements were found only at the jump from 1->2 threads and the jump from 3->4 threads.
Beyond that, no further improvements were seen with more threads, and some thread counts were actually slow. 
This seems to align with the number of threads available on the host. The first jump from 1 thread to 2 can 
be scheduled on the two cores, to the computation can happen truly in true parallel and a significant speedup was observed. 
At the step from 2 to 3 threads, one core got two threads while the other got one. The core with only one thread 
became the bottleneck, as the two threads on the other core couldn't return until that one did.  Thus no improvement was 
observed. The final improvement from three threads to four was due to the use of all four of the CPU's threads. Each core 
has two threads, so running four threads in the sort used all four threads of the CPU. That was the maximum parallelization seen, 
and any further threads did not result in any further paralling because the additional threads competed for time in the same 
CPU threads, and thus the process did not get more CPU time overall. 

The same was observed for host two. From 1 to 8 threads, the runtime about halved in every step with some minor falloff,
and while there were still improvements at 16 threads, they were only about 20%. This makes sense, as going from 1 to 8 threads
saturates the real cores, and the jump from 8 to 16 threads put the remaining tasks on the remaining logical cores on the real 
cores. The 20% performance improvement on the logical cores corresponds to that found by Intel for their Hyper-threading technology.
32 threads led to minor performance degradation possibly caused by constant context switches, but they were minor.


