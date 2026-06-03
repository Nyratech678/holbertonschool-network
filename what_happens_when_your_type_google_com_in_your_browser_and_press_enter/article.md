# What happens when you type https://www.google.com in your browser and press Enter?

When you type a URL into your browser and hit Enter, a fascinating chain of events unfolds in a fraction of a second, spanning networking, security, and server infrastructure. This article breaks down each step of the journey, from the initial DNS request to the database that stores the data you see.

---

## 1. DNS Request: Turning a name into a number

Computers communicate via IP addresses (like `142.250.179.68`), not human-readable domain names. The Domain Name System (DNS) translates `www.google.com` into an IP address.

Here's how it works, step by step:

- **Browser Cache**: The browser checks its own memory for a recently visited IP.
- **OS Cache**: If not found, the operating system's DNS cache is checked (including the `hosts` file).
- **Router Cache**: The local router may also have a small cache.
- **ISP Resolver**: If all local caches miss, the request goes to your Internet Service Provider's recursive DNS server. This server does the heavy lifting.
- **Root Servers**: The resolver queries a root DNS server, which doesn't know the exact IP but points to the top-level domain (TLD) servers for `.com`.
- **TLD Servers**: The resolver asks a `.com` TLD server, which responds with the authoritative name servers for `google.com`.
- **Authoritative Servers**: Finally, the resolver queries one of Google's authoritative DNS servers, which returns the IP address for `www.google.com`.

The IP is cached at various levels (with a Time To Live, or TTL) and sent back to the browser. Now the browser knows where to send the request.

---

## 2. TCP/IP: Building a reliable connection

With the IP address in hand, the browser needs a reliable channel to communicate. This is where TCP/IP comes in.

- **IP (Internet Protocol)** handles addressing and routing – it ensures packets can find their way from your computer to Google's server.
- **TCP (Transmission Control Protocol)** ensures that data arrives intact, in the correct order, and without loss.

To establish a connection, TCP performs a **three-way handshake**:

1. Your browser sends a **SYN** packet to the server's IP address on port 443 (HTTPS).
2. The server replies with a **SYN-ACK** packet.
3. Your browser sends a final **ACK** packet.

At this point, a full-duplex connection is established, and data can flow reliably.

---

## 3. Firewall: The first line of defense

Before packets reach Google's servers, they must pass through one or more firewalls. A firewall is a network security system that monitors and controls incoming and outgoing traffic based on predefined rules.

For example, Google's firewall might allow inbound traffic on port 443 (HTTPS) but block everything else. Firewalls often keep track of connection state (stateful inspection), so once the TCP handshake is allowed, subsequent packets for that connection are automatically permitted.

This layer protects the infrastructure from unauthorized access and various network attacks.

---

## 4. HTTPS/SSL: Securing the conversation

Since we typed `https://`, the browser will now establish an encrypted connection using TLS (formerly SSL). This ensures confidentiality, integrity, and authenticity.

The **TLS handshake** proceeds as follows:

- **Client Hello**: The browser sends supported TLS versions, cipher suites, and a random number.
- **Server Hello**: The server chooses the cipher suite, sends its own random number, and provides its digital certificate (containing its public key).
- **Certificate Verification**: The browser checks the certificate chain to ensure it is signed by a trusted Certificate Authority (CA), is not expired, and matches the domain `www.google.com`.
- **Key Exchange**: The browser generates a pre-master secret, encrypts it with the server's public key, and sends it. Only the server can decrypt it with its private key. Both sides then compute a shared session key.
- **Finished Messages**: Both sides send a message encrypted with the session key, confirming the handshake is complete.

From now on, all HTTP traffic is encrypted with this symmetric session key, making it unreadable to eavesdroppers.

---

## 5. Load-Balancer: Distributing the traffic

Google receives billions of requests daily; a single server cannot handle that load. The encrypted request first hits a **load-balancer**.

A load-balancer sits in front of multiple backend servers and distributes incoming traffic using algorithms such as:

- **Round-robin** – one server after another.
- **Least connections** – send to the server with the fewest active connections.
- **IP hash** – ensure the same client always reaches the same server (session persistence).

Load-balancers ensure high availability and scalability. They also often handle **SSL termination**: they decrypt the request and forward it as plain HTTP to internal web servers, reducing processing overhead.

---

## 6. Web Server: Serving static content and routing

The decrypted request now reaches a **web server** like Nginx or Apache. The web server's primary role is to handle client connections and serve content.

- If the request is for a static file (image, CSS, JavaScript, HTML page), the web server reads it directly from disk and sends it back.
- If the request is dynamic (like the Google homepage, which is personalized), the web server acts as a **reverse proxy**. It forwards the request to an application server and returns the generated response to the client.

Web servers also handle caching, compression, and sometimes request filtering.

---

## 7. Application Server: The brain of the operation

The **application server** runs the core business logic. In Google's case, this might be custom software written in Python, C++, Java, or Go. 

When the web server passes the request, the application server:

- Reads the URL path, query parameters, and session cookies.
- Executes the appropriate logic (e.g., building the search page for a logged-in user).
- Communicates with various backend services and databases to fetch user preferences, recent searches, or personalized content.
- Generates an HTML page (or JSON for API calls) and sends it back to the web server.

The application server is where dynamic content creation happens – it's the heart of the web application.

---

## 8. Database: Storing and retrieving data

Finally, the application server often needs to query one or more **databases** to retrieve or store data. For a Google search page, this could include:

- User profile data (name, avatar, preferences).
- Indexed search results for trending queries.
- Session data and authentication tokens.

Databases can be relational (SQL) or non-relational (NoSQL), and large-scale systems like Google's often use custom distributed databases (e.g., Bigtable, Spanner). The application server queries the database, processes the data, and integrates it into the response.

---

## 9. The Return Journey

Once the application server generates the full HTML response, it passes it back to the web server. The web server sends it through the load-balancer, back over the encrypted TLS connection, through the firewall, and across the internet to your browser.

Your browser then parses the HTML, discovers additional resources (CSS, JavaScript, images), and requests them in parallel, repeating much of the same process for each. Finally, the page is rendered, and you see the familiar Google search interface.

---

## Conclusion

What seems like a simple action — typing a URL and pressing Enter — involves a complex dance of protocols, infrastructure, and security mechanisms. Understanding each layer, from DNS to databases, is fundamental for any software engineer. This journey illustrates how the modern web stack works, and why reliability, security, and scalability are designed into every step of the process.