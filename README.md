Pouring Rain
============

Pouring Rain is a framework for distributed data transfer over unreliable network using Luby Transform codes.

See `test-client.py` and `test-server.py` for usage.

Introduction
============

**Problem:** How to transfer data over (unreliable) network as fast as possible?

**Solution:** Use distributed stateless transfer mechanism with error correction.

Key points:

* Transfer time T(K) = 1.25/K where K = number of servers. 
	* This means that T(5) = 0.25 (**four** times faster than from a single server!)
* M packets ( M is slighty larger than N ) needs to be received in order to construct the original message (composes of N packets).
* Total independece of transfer:
	* All packets are independent of each other so they don't have to come from the same machine.	
	* No ordering of packets is required.
	* Tolerates packet loss and unavailability of source machines
	* Only requirement is to receive enough unique packets to construct the original file.
	* Can be started, stopped and restarted arbitrarily. Servers doesn't need to know what you have received so far.
* Runs on UDP so no TCP overhead on the transfer.
* Server load (also disk io) is indepedent of how many clients it serves. 


Operating principles
============

1. Each resource in the network has a unique 64 bit identifier. 
2. When a client wants to retrieve that resource it sends a "subscribe" packet to each server it knows. 
3. If that resource is found on server, the server starts to stream packets to client. 
4. After a certain amount of time the server stops streaming to that client (UDP is used as protocol so there is no way to know is the client is still receiving).
5. In order to receive packets the client sends periodically the "subscribe" packet to each server. 
6. When the client is able to reconstruct the resource it sends a "unsubscribe" packet to each server and the servers stop to stream packets to client.



Technical notes
============

* Currently maximum transferred payload size is 4 Mb. Comes from 64 ipv4 datagrams.
* As there is no connection (udp), a heartbeat message is sent periodically in order to keep stream running



Future
============

* C / C++ implementation for more performance and smaller footprint


Murky Waters
============

Murky Waters is a distributed data store implemented using Pouring Rain. It adds "propagate" feature to Pouring Rain, which is used to replicate data over a 
PR cluster. It also caches locally all entries it fetches from the cluster. 
