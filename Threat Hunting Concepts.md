#### <font color='cyan'>BEACONING 101 </font>

###### <font color=ADFF2F>What means ?</font>
When we have an internal host periodically making outbound egress requests through the network boundary to an external server.

If Endpoint is compromised will phone attacker server (C2 Server) could be an IP or Domain.
It will reach out to the attacker server and wait for few seconds and the it'll tear down the TCP connection. And wait few seconds and then again attempt to communicate with attacker server. Goes on …

###### <font color=ADFF2F>What's the point ?</font>
Attacker wants to gain contact with internal host but the firewall will reject arbitrary requests to connect.
So, they need to somehow coerce the internal host into initiating an outbound connection to the attacker. And, so if the system is running malware, it will initiate that outbound request.

Basically the outbound request means, the internal host is asking the C2 a question : 
DO YOU HAVE ANYTHING FOR ME TO DO ?

Example : Let's say the C2 wants to perform directory listing of all the files in the current directory, attacker can queue up this command on C2 so that the next time the victim beacons into the C2. The computer will ask the question "DO YOU HAVE ANYTHING FOR ME TO DO ?" and boom. The victim checks the queue and does it commanded action.
SO, next time the victim beacons into the C2, it will not ask the question instead it will return the directory listing as requested by attacker.

Important types of Beaconing : DNS & CDN

###### <font color=ADFF2F>Beaconing : DNS</font> 
DNS beaconing is a technique used by adversaries to establish and maintain communication with compromised systems in a network by abusing the DNS.
Often used by APTs

How DNS Beaconing works ?
1. Infection
2. Beaconing : The malware send periodic DNS queries to attacker's domain or IP. These queries act as beacons.
3. Communication : Since DNS traffic is allowed through firewalls, attackers can use DNS queries and responses to send and receive commands.
4. C2 : Once communication is established, attackers can control the infected system, retrieve data or execute further malicious activities.

Indicators of DNS Beaconing :
- Frequent DNS Requests.
- Unusual domain names.
- Large DNS responses, indicating hidden data in DNS traffic.

<font color=FF69B4>K3nelP4n!c </font> : Usually DNS works in a way that it first checks and resolves the domain using local cache. But to execute DNS beaconing, attacker has to bust through this locally cached DNS, since the DNS contains the commands for compromised host. So, the attacker has hundreds and thousands of sub-domains to by pass this problem.
Every time victim makes large DNS requests, it is basically asking the "Do you have anything for me to day ?" in those requests. And there are multiple pseudo random sub-domains that actually map to that question.
So the attacker's infrastructure knows what these pseudo random sub-domains mean. And when a task is queued up in C2 like return directory listing, when the DNS request comes in; it now has the commands of what it should do in the host and that result is returned as a series of sub-domain lookups. 
Thousands of these lookups present a detection opportunity.
These sub-domains are usually non human readable.

###### <font color=ADFF2F>Beaconing : CDN</font> 

What is a CDN ?
Whole purpose behind CDN is to reduce the latency when loading a webpage.
CDN is a system of ==distributed servers== that work together to deliver content to users quickly and reliably based on their geographic location. Basically, the content is delivered from the server that is physically closest to you.
Common examples of CDN include services like Cloudflare, Akamai and Amazon CloudFront.

Origin Server (Mumbai) [Actual Server] -> Edge Server (Austin, Texas) [Distributed Server] 
User (Houston, Texas) is returned response from Austin and not Mumbai.

The edge server will periodically cache/check-in with the origin server so that it has the most recent content. 
Now, the attackers can abuse this benign functionality. 
They can use this to hide their C2 IP Address. 
They also abuse the high reputation - as a SOC you will see traffic originating from an IP Address that belongs to trusted provider like Amazon or Akamai.

<font color=FF69B4>K3nelP4n!c </font> : The way this works is that the attacker stand up their C2 behind the CDN and they'll set the cache value really really low so that the CDN doesn't cache the response on the edge node. Instead it just forwards it to attacker C2.

How would you stop it ? 
Instead of looking for IPs, look for FQDN








