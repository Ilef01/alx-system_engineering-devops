# 0x09. Web infrastructure design

For the ALX project `0x09. Web infrastructure design`.

## Tasks explanations, notes and answers

## `0. Simple web stack`

### Diagram link and reference image for task 0

🔗 **Link to the diagram:** [Click here](https://imgur.com/As325hc)

Here is the same diagram for easy reference
![0. Simple web stack](https://i.imgur.com/As325hc.png)

---

### Explanation of steps and components encountered in the infrastructure when requests are made

(The numbered steps here closely match the numbered steps in the diagram)
\
\
`1.` The user types "www.foobar.com" into their browser, and initiates a DNS lookup when they hit
<kbd>ENTER</kbd>.
\
\
`2.` DNS (Domain Name System).

A DNS server translates the human-readable domain name "www.foobar.com", into an IP address (`8.8.8.8`
\
in our case), that is then returned to the client machine.
\
Because the domain name was translated into an IPV4 address, then the domain name was an <u>`A` record.</u>
\
\
`3.` An HTTP request is made to the `8.8.8.8` server over the internet, by the client machine.
\
Besides HTTP, the client and the server will communicate over TCP/IP as well (once the handshake has been
\
complete).

`4.` The server.
\
The server, that can either be physical or virtual (software) accepts requests from the client, and _serves_
\
back a response. The server itself runs in its own environment inside of an Operating System like Linux for example.
\
\
Our particular server is located inside of a data center somewhere, and itself consists of the following components:

- `The Web Server`
\
That serves back static HTML files to the client that requested the website, so they can be rendered on the
\
client's browser
- `The Application Server`
\
When non-static (dynamic) content is part of the response, it is the application server's job to generate
\
this content by communicating with the application's database for instance.
- `The database`
\
The database stores application data that can then be queried for by the application server.
\
This data can be images, text, and so forth.
- `The application files (codebase)`
\
The logic defined in the codebase determines the behavior of the other components.
\
Every one of the components listed above interact through a codebase of some sort in order to control
\
how their actions are performed. The codebase can be in any language, the popular of which include:
\
PHP, JavaScript, Python, Ruby, and so on.
\
Application files also involve the various configuration files that take part in the web serving process.
\

`5.` Response to the client
\
After the static and dynamic content has been generated, the full response is sent back to the client
\
via the TCP/IP channel that was established earlier.

### Single Points of Failure (SPOF)

In this architecture, the server itself is a single point of failure, because there currently is no backup
\
server in case the only one that is there fails.
\
A failure of this single server will predictably lead to the site being unavailable until the server has been
\
fixed and re-deployed.

### Scaling

This architecture, though sufficient enough for a few requests, does not scale at all if the number of requests
\
approaches the limit of the only server that is present at the moment.
\
This can be remedied by adding another server to distribute the load requests and—by extension—handle even more
\
requests from more clients.

---
---
---

## `1. Distributed web infrastructure`

### Diagram link and reference image for task 1

🔗 **Link to the diagram:** [Click here](https://github.com/josfam/ALX-screenshots/blob/main/0x09.Web-Infrastructure-Design/Ilef-Bchini_Joseph-Amukun_1-Distributed-Web-Infrastructure.png)

Here is the same diagram for easy reference
![1. Distributed web infrastructure](https://raw.githubusercontent.com/josfam/ALX-screenshots/main/0x09.Web-Infrastructure-Design/Ilef-Bchini_Joseph-Amukun_1-Distributed-Web-Infrastructure.png)

### Explanation of changes made from previous model

_(The numbering here closely matches the numbering in the diagram)_
\
\
`5` **An extra server**:
\
For redundancy and resilience in case one of the servers fails, or is overwhelmed
\
with requests. This setup allows for one server to fully take over in case of a complete failure of the
\
other server.
\
\
`4` **A load balancer (HAproxy)**:
\
This handles even distribution of requests between the two servers, thereby reducing load on the servers.

- `Load balancing distribution algorithm`:
\
**Least connections** is the algorithm we chose due to it's relative simplicity in terms of implementation.
\
Least connections sends an incoming request to the server with the least numbers of connections,
\
which causes an even load on the servers, as a currently overwhelmed server will not continue to be
\
overwhelmed by incoming requests if the other server is free to handle said requests.

- `Active-active scheme`:
\
The load balancer employs an active-active setup for the servers whereby each server handles requests
\
simultaneously, in that there is no server that will be idle.
\
This makes sure that the resources that are present in the setup are actively being utilized, and not
\
wasted.
\
Furthermore, should one server fail, the other server can take over the work of accepting requests
\
and sending back responses, until the failed server is up and running again.

`6` Databases setup in a primary-replica (master-replica) cluster.

- `Primary database` vs `Replica database`:
\
The primary database can read and write database records, and can communicate the new database state
\
to any replica databases in the cluster. This ensures that data is synchronized across all databases
\
in the arrangement.
\
The replicated data also provides data resilience in case the primary database fails.
\
\
The replica database can only read records of the database, and cannot write data there.
\
Replica databases receive updates from the primary database concerning the updated state of the
\
database, and therefore act as data backups.
\
Moreover, if the primary database fails, a replica database can be promoted to become the new primary
\
database, seeing as the state of the data should be identical.

### Remaining Single Points of Failure (SPOF)

Despite being more scalable than the previous architecture, there are still points of failure.

- The load balancer:
\
There is still just one load balancer which, when it does fail, will cause a disruption across the
\
servers as well, rendering the site unreachable.

### Security issues

- Communications are not happening over HTTPS:
\
Currently, the communication between client and server is not encrypted in transit, leaving the entire
\
path vulnerable to all sorts of attacks.

- There is no firewall:
\
All traffic to the server is not currently being filtered, and is let through as is.
\
This opens up servers and their components to all sorts of attacks including SQL injections, and the like.

### Monitoring

- There is no resource monitoring in this architecture, which means that impending overloads or other failure
\
types cannot currently be detected in time for a fix to be issued before the point of failure.
