Design and architect around knowing software is going to be maintained by someone else.

Awful Code is written by reasonable devs in awful circumstances

The hardest bugs are where your mental model of the situation is just wrong, so you can’t see the problems at all.

Everyone ones that debugging is twice as hard as writing a program in the first place. So if you are clever as you can be when you write it, how long will you ever debug it.

The correctness of the implementation is the most important concern, but there is no royal road to correctness. It involves diverse tasks such as thinking of invariants, testing and code reviews. Optimization should be done, but not prematurely.

Making things easy to do is a false economy Focus on making things easy to understand and the rest will follow.

Implicit conversion of types is the Halloween special of coding, whoever thought of them deserves their own special hell

Do not hide the cost of the program you are writing

Understand the cost of what you are writing and developing and the impact its going to have.


>[!info]
>It's pretty intuitive:
>A program is **CPU bound** if it would go faster if the CPU were faster, i.e. it spends the majority of its time simply using the CPU (doing calculations). **A program that computes new digits of π** will typically be CPU-bound, it's just crunching numbers.
>A program is **I/O bound** if it would go faster if the I/O subsystem was faster. Which exact I/O system is meant can vary; I typically associate it with the disk, but of course, networking or communication, in general, is common too. **A program that looks through a huge file for some data might become I/O bound since the bottleneck is then the reading of the data from disk** (actually, this example is perhaps kind of old-fashioned these days with hundreds of MB/s coming in from SSDs).


**Multi-threading is where it tends to matter the most**

In this answer, I will investigate one important use case of distinguishing between CPU vs IO bounded work: when writing multi-threaded code.

**RAM I/O bound example: Vector Sum**

Consider a program that sums all the values of a single vector:

```
#define SIZE 1000000000
unsigned int is[SIZE];
unsigned int sum = 0;
size_t i = 0;
for (i = 0; i < SIZE; i++)
    /* Each one of those requires a RAM access! */
    sum += is[i]
```

Parallelizing that by splitting the array equally for each of your cores is of limited usefulness on common modern desktops.

For example, on my Ubuntu 19.04, Lenovo ThinkPad P51 laptop with CPU: Intel Core i7-7820HQ CPU (4 cores / 8 threads), RAM: 2x Samsung M471A2K43BB1-CRC (2x 16GiB) I get results like this:

[![](https://i.sstatic.net/tSHev.png)](https://i.sstatic.net/tSHev.png)

[Plot data](https://gist.github.com/cirosantilli/f3403af4bd1d2dd1bed042ac89f55150#file-pthread_array_sum-dat).

Note that there is a lot of variance between run however. But I can't increase the array size much further since I'm already at 8GiB, and I'm not in the mood for statistics across multiple runs today. This seemed however like a typical run after doing many manual runs.

Benchmark code:

- [POSIX C `pthread` source code](https://github.com/cirosantilli/cpp-cheat/blob/7253dd7fcd6f9923abbabafba4b7dd83b49b8af0/posix/interactive/pthread_array_sum.c) used in the graph.
    
    And here is a [C++ version](https://github.com/cirosantilli/algorithm-cheat/blob/7bf62a54f36544bd5a2967d29c495213c7d7eb1b/src/cpp/interactive/sum_array_parallel.cpp) that produces analogous results.
    
- [plot script](https://github.com/cirosantilli/cpp-cheat/blob/7253dd7fcd6f9923abbabafba4b7dd83b49b8af0/posix/interactive/pthread_array_sum.sh)
    

I don't know enough computer architecture to fully explain the shape of the curve, but one thing is clear: the computation does not become 8x faster as naively expected due to me using all my 8 threads! For some reason, 2 and 3 threads was the optimum, and adding more just makes things much slower.

Compare this to CPU bound work, which actually does get 8 times faster: [What do 'real', 'user' and 'sys' mean in the output of time(1)?](https://stackoverflow.com/questions/556405/what-do-real-user-and-sys-mean-in-the-output-of-time1/53937376#53937376)

The reason it is all processors share a single memory bus linking to RAM:

```
CPU 1   --\    Bus    +-----+
CPU 2   ---\__________| RAM |
...     ---/          +-----+
CPU N   --/
```

so the memory bus quickly becomes the bottleneck, not the CPU.

This happens because adding two numbers takes a single CPU cycle, memory reads take about [100 CPU cycles](http://www.eecs.berkeley.edu/%7Ercs/research/interactive_latency.html) in 2016 hardware.

So the CPU work done per byte of input data is too small, and we call this an IO-bound process.

The only way to speed up that computation further, would be to speed up individual memory accesses with new memory hardware, e.g. [Multi-channel memory](https://en.wikipedia.org/wiki/Multi-channel_memory_architecture).

Upgrading to a faster CPU clock for example would not be very useful.

**Other examples**

- matrix multiplication is CPU-bound on RAM and GPUs. The input contains:
    
    ```
    2 * N**2
    ```
    
    numbers, but:
    
    ```
    N ** 3
    ```
    
    multiplications are done, and that is enough for parallelization to be worth it for practical large N.
    
    This is why parallel CPU matrix multiplication libraries like the following exist:
    
    - [http://www.netlib.org/scalapack/pblas_qref.html](http://www.netlib.org/scalapack/pblas_qref.html)
    - [http://icl.cs.utk.edu/magma/software/](http://icl.cs.utk.edu/magma/software/)
    
    Cache usage makes a big difference to the speed of implementations. See for example this [didactic GPU comparison example](https://github.com/HandsOnOpenCL/Exercises-Solutions/tree/master/Solutions/Exercise08).
    
    See also:
    
    - [Why can GPU do matrix multiplication faster than CPU?](https://stackoverflow.com/questions/51344018/why-can-gpu-do-matrix-multiplication-faster-than-cpu/51345265)
    - [BLAS equivalent of a LAPACK function for GPUs](https://stackoverflow.com/questions/9165299/blas-equivalent-of-a-lapack-function-for-gpus)
- Networking is the prototypical IO-bound example.
    
    Even when we send a single byte of data, it still takes a large time to reach it's destination.
    
    Parallelizing small network requests like HTTP requests can offer a huge performance gains.
    
    If the network is already at full capacity (e.g. downloading a torrent), parallelization can still increase improve the latency (e.g. you can load a web page "at the same time").
    
- A dummy C++ CPU bound operation that takes one number and crunches it a lot:
    
    - [serial](https://github.com/cirosantilli/algorithm-cheat/blob/ea16f6bba12e7dcc32c0cbbbcdc74bcc2fd2d05b/src/cpp/interactive/cpu_bound.cpp)
    - [parallel](https://github.com/cirosantilli/algorithm-cheat/blob/ea16f6bba12e7dcc32c0cbbbcdc74bcc2fd2d05b/src/cpp/interactive/cpu_bound_parallel.cpp)
- Sorting appears to be CPU based on the following experiment: [Are C++17 Parallel Algorithms implemented already?](https://stackoverflow.com/questions/51031060/are-c17-parallel-algorithms-implemented-already/55989883#55989883) which showed a 4x performance improvement for parallel sort, but I would like to have a more theoretical confirmation as well
    
- The well known [Coremark](https://github.com/eembc/coremark-pro) benchmark from [EEMBC](https://en.wikipedia.org/wiki/EEMBC) explicitly checks how well a suite of problems scale. Sample benchmark result clearing showing that:
    
    ```
    Workload Name                                     (iter/s)   (iter/s)    Scaling
    ----------------------------------------------- ---------- ---------- ----------
    cjpeg-rose7-preset                                  526.32     178.57       2.95
    core                                                  7.39       2.16       3.42
    linear_alg-mid-100x100-sp                           684.93     238.10       2.88
    loops-all-mid-10k-sp                                 27.65       7.80       3.54
    nnet_test                                            32.79      10.57       3.10
    parser-125k                                          71.43      25.00       2.86
    radix2-big-64k                                     2320.19     623.44       3.72
    sha-test                                            555.56     227.27       2.44
    zip-test                                            363.64     166.67       2.18
    
    MARK RESULTS TABLE
    
    Mark Name                                        MultiCore SingleCore    Scaling
    ----------------------------------------------- ---------- ---------- ----------
    CoreMark-PRO                                      18743.79    6306.76       2.97
    ```
    
- the [linking of a C++ program](https://stackoverflow.com/questions/3322911/what-do-linkers-do/33690144#33690144) can be parallelized to a certain degree: [Can gcc use multiple cores when linking?](https://stackoverflow.com/questions/5142753/can-gcc-use-multiple-cores-when-linking/64128746#64128746)
    
- [rigid body dynamics](https://en.wikipedia.org/wiki/Rigid_body_dynamics), sometimes used in game engines, can be parallelized and sped up on the GPU, e.g. [MuJoCo](https://mujoco.readthedocs.io/en/stable/mjx.html) and [PhysX](https://en.wikipedia.org/wiki/PhysX) both support it
    

**How to find out if you are CPU or IO bound**

Non-RAM IO bound like disk, network: `ps aux`, then check if `CPU% / 100 < n threads`. If yes, you are IO bound, e.g. blocking `read`s are just waiting for data and the scheduler is skipping that process. Then use further tools like `sudo iotop` to decide which IO is the problem exactly.

Or, if execution is quick, and you parametrize the number of threads, you can see it easily from `time` that performance improves as the number of threads increases for CPU bound work: [What do 'real', 'user' and 'sys' mean in the output of time(1)?](https://stackoverflow.com/questions/556405/what-do-real-user-and-sys-mean-in-the-output-of-time1/53937376#53937376)

RAM-IO bound: harder to tell, as RAM wait time it is included in `CPU%` measurements, see also:

- [How to check if app is cpu-bound or memory-bound?](https://stackoverflow.com/questions/3156334/how-to-check-if-app-is-cpu-bound-or-memory-bound)
- [https://askubuntu.com/questions/1540/how-can-i-find-out-if-a-process-is-cpu-memory-or-disk-bound](https://askubuntu.com/questions/1540/how-can-i-find-out-if-a-process-is-cpu-memory-or-disk-bound)

Some options:

- Intel Advisor Roofline (non-free): [https://software.intel.com/en-us/articles/intel-advisor-roofline](https://software.intel.com/en-us/articles/intel-advisor-roofline) ([archive](https://web.archive.org/web/20190910150447/https://software.intel.com/en-us/advisor-user-guide-roofline-analysis)) "A Roofline chart is a visual representation of application performance in relation to hardware limitations, including memory bandwidth and computational peaks."

**GPUs**

GPUs have an IO bottleneck when you first transfer the input data from the regular CPU readable RAM to the GPU.

Therefore, GPUs can only be better than CPUs for CPU bound applications.

Once the data is transferred to the GPU however, it can operate on those bytes faster than the CPU can, because the GPU:

- has more data localization than most CPU systems, and so data can be accessed faster for some cores than others
    
- exploits data parallelism and sacrifices latency by just skipping over any data that is not ready to be operated on immediately.
    
    Since the GPU has to operate on large parallel input data, it is better to just skip to the next data that might be available instead of waiting for the current data to be come available and block all other operations like the CPU mostly does
    

Therefore the GPU can be faster then a CPU if your application:

- can be highly parallelized: different chunks of data can be treated separately from one another at the same time
- requires a large enough number of operations per input byte (unlike e.g. vector addition which does one addition per byte only)
- there is a large number of input bytes

These designs choices originally targeted the application of 3D rendering, whose main steps are as shown at [What are shaders in OpenGL and what do we need them for?](https://stackoverflow.com/questions/17789575/what-are-shaders-in-opengl-and-what-do-we-need-them-for/36211337#36211337)

- vertex shader: multiplying a bunch of 1x4 vectors by a 4x4 matrix
- fragment shader: calculate the color of each pixel of a triangle based on its relative position withing the triangle

and so we conclude that those applications are CPU-bound.

With the advent of programmable GPGPU, we can observe several GPGPU applications that serve as examples of CPU bound operations:

- [Image Processing with GLSL shaders?](https://stackoverflow.com/questions/13693946/image-processing-with-glsl-shaders/40641014#40641014)
    
    [![enter image description here](https://i.sstatic.net/gZ87k.gif)](https://i.sstatic.net/gZ87k.gif)
    
    Local image processing operations such as a blur filter are highly parallel in nature.
    
- [Is it possible to build a heatmap from point data at 60 times per second?](https://stackoverflow.com/questions/30864752/is-it-possible-to-build-a-heatmap-from-point-data-at-60-times-per-second/39839788#39839788)
    
    Plotting of heatmap graphs if the plotted function is complex enough.
    
    [![enter image description here](https://i.sstatic.net/3Khj7.gif)](https://i.sstatic.net/3Khj7.gif)
    
- [https://www.youtube.com/watch?v=fE0P6H8eK4I](https://www.youtube.com/watch?v=fE0P6H8eK4I) "Real-Time Fluid Dynamics: CPU vs GPU" by Jesús Martín Berlanga
    
    Solving partial differential equations such as the [Navier Stokes equation](https://en.wikipedia.org/wiki/Navier%E2%80%93Stokes_equations) of fluid dynamics:
    
    - highly parallel in nature, because each point only interacts with their neighbour
    - there tend to be enough operations per byte

See also:

- [Why are we still using CPUs instead of GPUs?](https://superuser.com/questions/308771/why-are-we-still-using-cpus-instead-of-gpus)
- [What are GPUs bad at?](https://cs.stackexchange.com/questions/121080/what-are-gpus-bad-at)
- [https://www.youtube.com/watch?v=_cyVDoyI6NE](https://www.youtube.com/watch?v=_cyVDoyI6NE) "CPU vs GPU (What's the Difference?) - Computerphile"

**CPython Global Intepreter Lock (GIL)**

As a quick case study, I want to point out to the Python Global Interpreter Lock (GIL): [What is the global interpreter lock (GIL) in CPython?](https://stackoverflow.com/questions/1294382/what-is-the-global-interpreter-lock-gil-in-cpython/55309364#55309364)

This CPython implementation detail prevents multiple Python threads from efficiently using CPU-bound work. The [CPython docs](https://docs.python.org/3.7/library/threading.html) say:

> CPython implementation detail: In CPython, due to the Global Interpreter Lock, only one thread can execute Python code at once (even though certain performance-oriented libraries might overcome this limitation). If you want your application to make better use of the computational resources of multi-core machines, you are advised to use `multiprocessing` or `concurrent.futures.ProcessPoolExecutor`. However, threading is still an appropriate model if you want to run multiple I/O-bound tasks simultaneously.

Therefore, here we have an example where CPU-bound content is not suitable and I/O bound is.

**JavaScript `async` and Node.js `worker_threads`**

The situation is similar to Python's.

JavaScript is fundamentally single threaded. Not sure if this is part of the language standard or not (for Python it is not, there isn't even a language standard besides the reference CPython implementation AFAIK).

JavaScript has the `async` keyword which allows execution to stall, and it then it start executing something else. You can write stuff like:

```
async function myfunc(init) {
  ret = init
  for (let i = 0; i < 1000000; i++) {
    ret += i*i + 2*i + 3
  }
  return ret;
}

async function doNetworkRequest(init) {
  // Some native method that gets network data.
}

const [first, second, myfunc_ret1, myfunc_ret2] = await Promise.all([
  doNetworkRequest('example.com'),
  doNetworkRequest('example2.com'),
  myfunc(1),
  myfunc(2),
])
```

where `await` says "wait for all these async things to finish before moving on".

However, only one of the `async` methods can run at a time on your CPU, so the CPU intensive work of `myfunc` does not get sped up by this at all.

The prototypically IO bound operation of network access however can get sped up, as this would fire both network requests one after the other, and just wait for both to return while the servers/network do the work.

The fact that there is an actual keyword in the language dedicated for this, `async`, is telling: unsurprisingly, network requests are very important in the browser dominated context of JavaScript.

With the advent of Node.js however, people started to want to parallelize CPU workload as well more and more, and they reached a similar solution to CPython: create separate processes rather than threads. This is done with the [`worker_threads` library](https://nodejs.org/docs/latest-v17.x/api/worker_threads.html), which:

- actually takes a JavaScript script path as input, and launches a new interpreter instance to run it
- since the processes don't share memory space, you pass messages between instances with a messaging system

[https://medium.com/@Trott/using-worker-threads-in-node-js-80494136dbb6](https://medium.com/@Trott/using-worker-threads-in-node-js-80494136dbb6) contains a good example.

The documentation of `worker_threads` once more states the difference as mentioned elsewhere in this answer:

> Workers (threads) are useful for performing CPU-intensive JavaScript operations. They do not help much with I/O-intensive work. The Node.js built-in asynchronous I/O operations are more efficient than Workers can be.

On the browser there's also Web Workers, not sure how it compares to Node's implementation: [What's the difference between Web Workers and Worker Threads?](https://stackoverflow.com/questions/60268604/whats-the-difference-between-web-workers-and-worker-threads)