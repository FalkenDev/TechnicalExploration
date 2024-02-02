# Technical study on Load Balancers for the REST API

During project planning we started to calculate how many requests the REST API will receive (traffic) from all our programs. For example, one program makes requests for information about a certain scooter that is active where the scooter's information is updated every 10 seconds while another program makes a request for all scooters and cities.

With this we came to the conclusion that more than 10,000 requests per minute will be made (if you think that more people will use the different programs). With this, we then needed to look more closely at how to optimize performance and also see how many requests a REST API can actually handle if you build it with nodejs and express without using a Load Balancer.

We started doing a benchmark test for the REST API with ApacheBench where we test how long it takes to make 100,000 requests to a page on localhost that shows the documentation for the REST API.

<img src="assets/loadBalancer/benchmark1.png" alt="Benchmark 1" width="400"/>

Here we can see that it took 28.074 seconds for all requests to go through when testing 100,000 requests where 1000 requests are made at a time.

## Load balancer

We decided to use a Load Balancer that acts as a traffic cop that sits in front of the application and directs client requests to all servers (jobbers) that take care of the requests and fulfill them. Using this ensures that no server (worker) is overworked which can degrade performance. Also, you maximize the speed of all requests where there are more workers who can work on different things (manage the workload) than without a load balancer, there is only one worker who can work on that particular thing. An example of a load balancer is NodeJS' own cluster module that we will use in our project. It took about 18.7 seconds for 100,000 requests where 1000 requests are made at a time. This is then about 10 seconds faster than the previous benchmark test with loading a page of information but will probably be faster when different types of requests are made.

Image of Benchmark when the Cluster module is used in the REST API.

<img src="assets/loadBalancer/benchmark2.png" alt="Benchmark 2" width="400"/>

## NodeJS own Cluster Module

Nodejs has its own built-in module called Cluster Module to take advantage of a multi-core system than what is preset for NodeJS which only uses a single thread (Core) on a multi-core system. To take advantage of the other available cores, you can start a cluster of Node.js processes and distribute the load between them.

Having multiple threads to handle requests improves the throughput (requests/second) of your server as multiple clients can be served simultaneously.

The NodeJS cluster module creates subordinate processes called workers that run simultaneously and share the same server port. Each created worker has its own event loop, memory and v8 instance that allows more requests to be processed simultaneously and if there is a long term/blocking operation on one worker, the other workers can continue to handle other incoming requests. Thus, the REST API will not be blocked/stop working until the blocked request is processed.

The master process listens for connections on a port and distributes them across workers in a round-robin fashion.
The master process creates a listening contact and sends it to interested workers who can then receive incoming connections directly.

Below I show an image where there is a primary (the Master process) and a number of workers working on the same port to handle all requests.

<img src="assets/loadBalancer/worker.png" alt="workers" width="400"/>

### Using the NodeJS CLuster Module

Documentation - https://nodejs.org/api/cluster.html

There are also several pages and videos showing how to implement it.

## Other Load Balancers

There are also other Load Balancers that can be used.
Examples of this are:

- Nginx
- Express Web Server Balancer
- HAProxy

We chose to use NodeJS's own module because it felt easiest to implement with the knowledge we have, but there are certainly better Load Balancers.
