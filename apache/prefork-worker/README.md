## Prefork vs Worker
Apache MPMs conceptual comparison between Prefork and Worker.

>###### Prefork is 1-thread per 1-child process and good at stability. Worker is N-threads per 1-child process and goot at scalability

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.24
ㅁ References:
 - http://httpd.apache.org/docs/current/mod/prefork.html
 - http://httpd.apache.org/docs/current/mod/worker.html
 - http://stackoverflow.com/questions/13883646/apache-prefork-vs-worker-mpm
 - https://kb.plesk.com/en/113007
```

The server ships with a selection of `Multi-Processing Modules (MPMs)` which are responsible for binding to network ports on the machine, accepting requests, and dispatching children to handle the requests.

The server can be better customized for the needs of the particular site. For example, sites that need a great deal of scalability can choose to use a threaded MPM like worker or event, while sites requiring stability or compatibility with older software can use a prefork.

#### 1. Prefork
Pros :
 - Developed in `Apache 1.x`
 - Multiple child processes, `1-thread per child process`, child processes handle requests
 - Better isolation and `stability`

Cons :
 - `Higher memory consumption` and lower performance over the threaded MPMs

This Multi-Processing Module (MPM) implements a `non-threaded`, pre-forking web server. **Each server process may answer incoming requests, and a parent process manages the size of the server pool.** It is appropriate for sites that need to avoid threading `for compatibility with non-thread-safe libraries`. It is also the best MPM for isolating each request, so that a problem with a single request will not affect any other.

###### 1.1. How it Works
A single control process is responsible for launching child processes which listen for connections and serve them when they arrive. Apache httpd always tries to maintain several spare or idle server processes, which stand ready to serve incoming requests. In this way, clients do not need to wait for a new child processes to be forked before their requests can be served.

The StartServers, MinSpareServers, MaxSpareServers, and `MaxRequestWorkers` regulate how the parent process creates children to serve requests. In general, Apache httpd is very self-regulating, so most sites do not need to adjust these directives from their default values. Sites which need to serve more than 256 simultaneous requests may need to increase MaxRequestWorkers.

> Sites with limited memory may need to decrease MaxRequestWorkers to keep the server from thrashing (swapping memory to disk and back). More information is provided in [performance hints](http://httpd.apache.org/docs/current/misc/perf-tuning.html).

```xml
<IfModule prefork.c>
  StartServers       8
  MinSpareServers    5
  MaxSpareServers   20
  ServerLimit      256
  MaxClients       256
  MaxRequestsPerChild 4000
</IfModule>
```

#### 2. Worker
Pros :
 - Developed in `Apache 2.x`
 - Multiple processes, `N-threads per child process`, threads handle requests
 - Uses `less memory` and provides higher performance - `scalability`

Cons :
- Does not provide the same level of isolation request-to-request as a Prefork does
- If single thread is suspended/out of control, the entire process will be terminated, affecting all of the threads
- Requires a thread-safe processor to handle dynamic content

This Multi-Processing Module (MPM) implements a hybrid `multi-process multi-threaded` server. By using threads to serve requests, it is able to serve a large number of requests with fewer system resources than a process-based server. However, it retains much of the stability of a process-based server by keeping multiple processes available, each with many threads.

The most important directives used to control this MPM are `ThreadsPerChild`, which controls the number of threads deployed by each child process and `MaxRequestWorkers`, which controls the maximum total number of threads that may be launched.

###### 2.1. How it Works
A single control process (the parent) is responsible for launching child processes. `Each child process creates a fixed number of server threads as specified in the ThreadsPerChild directive`, as well as a listener thread which listens for connections and passes them to a server thread for processing when they arrive.

Apache HTTP Server always tries to maintain a pool of spare or idle server threads, which stand ready to serve incoming requests. In this way, clients do not need to wait for a new threads or processes to be created before their requests can be served. The number of processes that will initially launch is set by the StartServers directive. During operation, the server assesses the total number of idle threads in all processes, and forks or kills processes to keep this number within the boundaries specified by MinSpareThreads and MaxSpareThreads. Since this process is very self-regulating, it is rarely necessary to modify these directives from their default values. The maximum number of clients that may be served simultaneously (i.e., the maximum total number of threads in all processes) is determined by the MaxRequestWorkers directive. The maximum number of active child processes is determined by the MaxRequestWorkers directive divided by the ThreadsPerChild directive.

```xml
<IfModule worker.c>
 StartServers         2
 MaxClients         150
 MinSpareThreads     25
 MaxSpareThreads     75
 ThreadsPerChild     25
 MaxRequestsPerChild  0
 </IfModule>
```
