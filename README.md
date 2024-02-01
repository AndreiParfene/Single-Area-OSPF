Hello world!

Story: Once upon a time, in the realm of network architecture, there was a visionary client who sought to connect four distant domains within their digital kingdom. The client's quest for seamless communication among these routers mirrored a desire for swift and reliable data exchange. In the heart of this realm, routers R1, R2, R3, and R4 were chosen to weave a robust network tapestry using OSPF  as the enchanting protocol and a Multilayer Switch emerged as the nexus, blending the realms of routers and switches.
		
To keep this simpler, no security configurations were done. 

I've crafted a .pkt file featuring a single-area OSPF network design. Within this topology there are 4 routers, each hosting its own network and to optimize cable connections between these routers, a multilayer switch is used.
	R4 = DR, connected to a Server-2 intented to simulate a webserver ( with a private IP address here ) - Client requested that some LANs should not have access to port 80 or any access at all
 	R3 = BDR, - Here, the client requested that LAN C should not see the real IPs of LAN B, so 192.168.2.0/24 was chosen to be used for NAT
  	R1, R2 = Drother

Step 1: Configured IP address for hosts, servers, switches, routers and activated all interfaces used in this topology. 

Step 2: Activated OSPF on the routers to establish adjacencies: 
	router ospf 1
 		network ( network IP ) ( Wildcard ) area 0
   		passive interface G0/0/1 -> as I used G0/0/0 on all routers for OSPF 
     		router-id 4.4.4.4 -> this will be the RID for R4 to become DR
       		auto-cost reference bandwidth 10000 -> this the gig interfaces I use will have a cost for 10, planning for future use of higher speeds
		clear ip ospf process -> in case something went wrong 
  		hello/dead timers will remain with default values
    
Step 3: Blocked http, LAN A and LAN B access to Server-2 using extended ACL
	access-list 101 deny tcp any host 10.0.4.2 eq 80
 	access-list 101 deny ip 10.0.1.0 0.0.0.255 host 10.0.4.2
  	access-list 101 deny ip 10.0.2.0 0.0.0.255 host 10.0.4.2
   	access-list 101 permit ip any any 
    - applied on interface R4 G0/0/0 in

Step 4: To bolster security measures, I implemented NAT on R3
	access-list 1 permit 10.0.2.0 0.0.0.255
 	ip nat pool NAT-B 192.168.2.1 192.168.2.254 netmask 255.255.255.0
  	ip nat inside source list 1 pool NAT-B overload
   - applied on R3 G0/0/0 in and G0/0/1 out
   - results for NAT working appear in file "Commands" at the start of 2nd page

Step 5: Verification
	- " R#show ip ospf neighbors " to verify adjacencies 
 	- " R#show run " to verify interfaces, NAT, ACL 
  	- pings between devices - some failed as the client requested no access 

In crafting this project, I've represented my technical skills and also showcased my interest to learn more. I am looking forwaard to contirbute to the dynamic landscapes of real-world projects. 
Thank you for exploring my journey in this way.
