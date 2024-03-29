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

---
---
---

## `2. Secured and monitored web infrastructure`

### Diagram link and reference image for task 2

🔗 **Link to the diagram:** [Click here](https://github.com/josfam/ALX-screenshots/blob/main/0x09.Web-Infrastructure-Design/Ilef-Bchini_Joseph-Amukun_2-Secured-and-monitored-web-infrastructure.png)

Here is the same diagram for easy reference
![1. Distributed web infrastructure](https://raw.githubusercontent.com/josfam/ALX-screenshots/main/0x09.Web-Infrastructure-Design/Ilef-Bchini_Joseph-Amukun_2-Secured-and-monitored-web-infrastructure.png)

### Changes made from previous model

_(The numbering here closely matches the numbering in the diagram)_
\
\
`4`, `5` **Three firewalls on load balancer and two servers**:
\
The firewalls on each of the components filter out particular network traffic,
\
and make this architecture more secure from many attack surfaces like SQL injections.
\
\
`3`, `6` **Communication over HTTPS.**:
\
Unlike HTTP, HTTPS makes sure that the communications between the client and the
\
server happen over an encrypted channel.
\
This prevents web traffic from being extracted (and understood) by a middle Man / unauthorized third party.
\
\
`5` **Monitoring with Sumologic**:
\
Monitoring is used to track the performance (and other statistics about the system),
\
in order to check If there is a problem with any of the components, which can help find a resolution
\
sooner rather than later.

- **Overall picture of monitoring:**
\
The monitoring system comprises of a client that collects the data, and then sends said data
\
to a monitoring system that can crunch the data, and present it in a manner that is useful
\
for decision-making.

- **Monitoring the web server's Queries Per Second(QPS)**
\
Monitoring QPS with sumologic is as easy as specifying the conditions that should occur
\
for there to be a trigger, and then waiting to see if the system does get triggered.
\
Any remedies can then be issued as per policy.

### Remaining issues

- **SSL termination at the load balancer**
\
Terminating SSL at the load balancer means that the HTTPS encrypted channel stops at the load balancer,
\
and therefore any communication between the load balancer and the servers is unencrypted.
\
Any malicious attacker who was operating between the load End the server will ,therefore, be able to
\
access the communication in the clear.
- **Single points of failure (SPOF)**
\
Because still only one of them exists, the load balancer remains a single point of failure in the
\
architecture.

- **Only one MYSQL server is capable of writing to the database (only one master)**
\
Having only one master means that the system is suddenly inoperable (in terms of writing data) to the
\
database, if that singular master goes down.
\
This is also a single point of failure.

- **Identical servers with identical components**
\
Having identical servers, while promoting redundancy, also limits configuration in terms of custom
\
resource distribution.
\
If users tend to request a lot of data from server 2 over the weekend for example, then it may require
\
more application servers and more database servers than server 1.
\
The imbalance in resource distribution cannot be achieved if the servers are identical.

---
---
---

## `3. Scale up`

### Diagram link and reference image for task 3

🔗 **Link to the diagram:** [Click here](https://raw.githubusercontent.com/josfam/ALX-screenshots/main/0x09.Web-Infrastructure-Design/Ilef-Bchini_Joseph-Amukun_3-Scale-Up.png)

Here is the same diagram for easy reference
![1. Distributed web infrastructure](https://raw.githubusercontent.com/josfam/ALX-screenshots/main/0x09.Web-Infrastructure-Design/Ilef-Bchini_Joseph-Amukun_3-Scale-Up.png)

### Changes made from the second model

_(The numbering here closely matches the numbering in the diagram)_
\
\
`4` **Load balancers arranged in a cluster**
\
Instead of just one load balancer, there are now two load balancers,arranged in a cluster
\
such that if one fails, the other can take over.
\
\
`5`, `6`, `7` **All components now belong in there own server**
\
Instead of the web server, application server, code base, and Mysql databases being contained in
\
the same server, they have now been spread out into their own servers, to make sure that each component
\
can scale individually without affecting necessarily anything else.
