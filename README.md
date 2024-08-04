# Understanding-BGP-Route-Reflector-Mysteries
In this blog we will learn the functionality of BGP Route Reflectors (RR) and BGP attributes set by RR while sending BGP updates. BGP RR are used to avoid need of having full IBGP peers thus helps in network scalability  and besides that RR sets BGP attributes which are inspected by receiving  router to avoid network loops. 

## BGP Route Reflection Rules 
* Any route received by RR client is re-advertised to non-client iBPG Peers, eBPG peers and other RR clients.. 
* Any route received by non-client IBGP Peer or eBGP peers will be re-advertised to non-client iBGP peers  , other eBGP peers and RR clients. 
## BGP Attributes Adjusted by RR
* Cluster ID and Origin ID are set by the RR, with routes being received on the RR before sending them to non-client IBGP peers or RR Client peers. 
* Cluster ID and Origin ID are not set by the RR, with routes being received on the RR while sending it to eBGP peers.

## Lab Topolopy 
Our lab topology is as under:-
![lab](./images/lab.png)

In our lab topology, we have 2 RRs in US West Coast (PE1 & PE2) with 1 client i.e PE5 and 2 RRs in US East Coast (PE3 & PE4) with 1 client i.e PE6.  We will take the example of PE6 subnet i.e 100.100.251.0/24 and will explore this subnet is being handled in our lab topology.