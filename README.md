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

Enough theory, let's explore our lab and see what is happening there. 

PE6 is RR client with US East Coast Route Reflectors i.e PE3 (172.172.172.9 ) and PE4 (172.172.172.10), PE6 is sending 1 subnet to it's RR. 

```
root@PE6> show route advertising-protocol bgp 172.172.172.9 extensive 


prod.inet.0: 5 destinations, 18 routes (5 active, 0 holddown, 0 hidden)
* 100.100.251.0/24 (1 entry, 1 announced)
 BGP group rr type Internal
     Route Distinguisher: 172.172.172.11:65000
     VPN Label: 16
     Nexthop: Self
     Flags: Nexthop Change
     Localpref: 100
     AS path: [65000] I 
     Communities: target:65000:100

root@PE6> show route advertising-protocol bgp 172.172.172.10 extensive   

prod.inet.0: 5 destinations, 18 routes (5 active, 0 holddown, 0 hidden)
* 100.100.251.0/24 (1 entry, 1 announced)
 BGP group rr type Internal
     Route Distinguisher: 172.172.172.11:65000
     VPN Label: 16
     Nexthop: Self
     Flags: Nexthop Change
     Localpref: 100
     AS path: [65000] I 
     Communities: target:65000:100
```
Once the PE6 subnet i.e100.100.251.0/24 is is receievd on it's RR i.e (PE3 and PE4) that subnet has to be sent to US West Coast RR i.e (PE1 and PE2) as there is full mesh iBGP sessions are configured between US East Cosst RRs  and US West Coast RR .  US East Coast RRs will set cluster ID and Origin ID attributes with PE6 subnet, cluster ID value would be what ever is configured on US East Coas RRs and Origin ID would be vlaue of PE6 loopback IP (which is originator of the route). 

