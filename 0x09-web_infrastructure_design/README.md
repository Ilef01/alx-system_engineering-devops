# 0x09. Web infrastructure design

For the ALX project `0x09. Web infrastructure design`.

## Tasks explanations, notes and answers

## 0. Simple web stack

### Diagram link and reference image

**Google Drive link to the diagram:** [Click here](https://drive.google.com/file/d/1mVbaj-w7oi3bqx1obdjcSpy-2gHZIQX0/view?usp=drive_link)

Here is the same diagram for easy reference:

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
`5. Response to the client`
\
After the static and dynamic content has been generated, the full response is sent back to the client
\
via the TCP/IP channel that was established earlier.
