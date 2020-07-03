<h1>Networking</h1>

<h3>VPCs</h3>
With Amazon Virtual Private Cloud, we have the ability to create logically isolated networks.

<ol>
  <li>A VPC is inherently, by default, isolated from every other network, including the internet, and so there is a security mechanism in that, in that the only way for VPC to communicate with any other network, another VPC, on premises, the internet, we have to explicitly put that in place and allow that kind of communication.</li>
  <li>VPCs are created per account per region. When we create a VPC, it does exist, and it spans a single region, so keep that in mind, that one VPC exists within the region that it's created in. It is possible to allow VPCs in different regions to communicate, but they still exist in their respective regions.</li>
  <li>VPCs can make use of all of the availability zones in that region, so if we were to create a VPC in Oregon in the US West 2 region, for example, then we would have three availability zones for us to use, and that VPC can leverage all three of those availability zones.</li>
  <li>VPCs can also peer with other VPCs, so when you allow two VPCs to communicate directly, that's called peering, and you can peer between VPCs in a single region, you can peer between VPCs in multiple regions.</li>
  <li>Gateways are a device that allows one network to communicate with another network, and so, if we wanted to communicate with the internet, then we would create and attach an internet gateway.</li>
</ol>

<h3>VPC Limits</h3>
<ul>
  <li>Default soft limit of five VPCs per region</li>
  <li>Default limit of 200 subnets per VPC</li>
</ul>

<h3>Subnets</h3>
Subnets are, as the name suggests, a subdivision of a network, and so it's a way of isolating different parts of our network to create, perhaps, functional placement of different devices for different purposes, and to create isolation for security. And so, subnets within Amazon VPC perform two primary, very important functions:
<ol>
  <li>Create isolation between tiers, so if you're designing a multi-tier system, then every subnet, perhaps every tier, would get its own subnet, so that you can have that isolation between tiers.</li>
  <li>Give us a physical mapping to an availability zone. When we create subnets, they are created specifically one to one with an availability zone, and so we have isolation between tiers and a mapping to an availability zone</li>
</ol>

![alt text](https://github.com/shakespete/aws/blob/master/img/subnets.png)

<h3>Route Tables</h3>
Routing is concerned with the flow of traffic, whether or not any and all traffic is allowed to move between two different ranges of IP addresses. Routing is not at all concerned with protocols and ports. Routing is not concerned with TCP versus UVP, port 22 versus port 443. It's only concerned about whether or not any and all traffic can move.

We don't need to create or manage the router, but we can configure routing by creating route tables. So all route tables that we create will ensure that traffic destined for the VPC remains local. And what that means is that every subnet in that VPC is inherently allowed to route traffic to other subnets. All of these subnets are inherently allowed to communicate with one another based on that local route. And without any other routes in place, that's the only communication that would be allowed. If we want to communicate with the internet, then first we would need an internet gateway. 

![alt text](https://github.com/shakespete/aws/blob/master/img/routing_2.png)

<h3>NACLs</h3>
<p>Network access control lists serves as a firewall for subnets.</p>

The public subnet is intended to allow our load balancer to communicate directly with the internet and to allow hosts on the internet to reach in and initiate new connections to our load balancer. Our private subnet is intended to keep our private application servers private. You'll notice that they can communicate with the load balancer but they don't communicate to the internet directly. We want to provide a firewall for our public subnet, for the subnet as a whole, no matter what devices might be in use, not matter what security groups are in use within that subnet, we want to protect the subnet as a whole. We can do that by applying a network access control list. So we create the network access control list and then we associate it with that particular subnet.

![alt text](https://github.com/shakespete/aws/blob/master/img/nacls.png)

We are allowing ports 80 and 443 to come into the subnet from anywhere. You'll see here that the source is all zeroes. That means any IP address whatsoever. By doing that, we allow our end users out there on the internet to initiate new connections, coming in on port 80 and 443. And then anything else, if someone tries to be malicious and come into that subnet on port 22, those requests would be denied. Those packets would be rejected by the firewall, by the network access control list acting as the firewall for the subnet. So if someone were to inadvertently open port 22 on an instance inside that subnet, it would still gain some protection by the fact that port 22 is blocked by the network access control list. We have to consider not only traffic that is coming in, but also traffic that is going out. So egress rules affect traffic that's going out. This particular rule says that we want to allow port 1024 through 75535. If you read the AWS documentation, it describes that the load balancers, elastic load balancers use ports 1024 through 65535 as the ephemeral port range. For those of us who might not be familiar with how networking works in this way, when the load balancer receives traffic on 80 or 443, it will send the response back on a different port. That port is generally chosen at random from a particular range. <b>In the case of elastic load balancers, that range is 1024 through 65535.</b> It's a very large range relative to other uses of ephemeral ports. By setting this rule here, port 80 and 443, we allow the load balancer to receive on the appropriate ports and we allow it to send responses back on the appropriate ports. It's necessary to specify both inbound and outbound traffic. Without this rule here at the bottom, we would receive requests, but our end users would never receive their responses. So we have to have both the ingress and the egress rules in place. You'll see here that by specifying 1024 through 65535 which are essentially non privileged ports, we could allow those ephemeral responses to go out but also block privileged ports. Any port less than 1024 is considered privileged. So port 22, port 21, and so on, those ports would not be able to go out.

Network Access Control Lists are stateless. They do not recognize responses as being responses. We have to open ports for every port that needed to go out.

<h3>Security Groups</h3>
<p>Security Groups are firewalls that are applied to individual instances or other devices like load balancers.</p>

<ul>
  <li>Stateful. They do recognize responses as being responses.</li>
  <li>Can specify ingress or egress.</li>
  <li>Default: all ingress traffic DENIED</li>
  <li>Default: all egress traffic ALLOWED</li>
</ul>

If something comes in on port 80, and then a response goes out, in response, the security group will recognize that as being a response. Which means that we don't need to open the ephemeral port range. <strong>Egress Rules apply to initiating new connections.</strong> So when we think about the relationship between the load balancer and our private application servers, our load balancer only needs to initiate new connections to one thing, our private application servers. The load balancer has no need whatsoever to establish a new connection to any other host on any other port. What we're saying here is that you are allowed to initiate a new connection on port 8080 so long as the destination instance is a member of this particular security group. We're not necessarily concerned with IP addresses because we could have different load balancers in the public subnet, we could have different application servers in the private subnet.

![alt text](https://github.com/shakespete/aws/blob/master/img/security_group_1.png)

<p>
We could take this a step further, we could also apply this to our database. And we could say, again using security group references, that our private application server is only allowed to initiate new connections on port 3306, so long as they are destined for a member of a particular database security group.
</p>

![alt text](https://github.com/shakespete/aws/blob/master/img/security_group_2.png)

<h3>VPC Peering</h3>

<ul>
  <li>Connection between 2 VPCs</li>
  <li>Helps segregate networks</li>
  <li>No bottleneck or single point of failure</li>
  <li>IP ranges must not overlap</li>
  <li>Can peer between accounts</li>
  <li>Always 1-1</li>
  <li>No transitive peering</li>
  <li>No edge-to-edge routing</li>
  <li>$/GB bandwidth</li>
</ul>

![alt text](https://github.com/shakespete/aws/blob/master/img/vpc_peering_1.png)

Peering 2 VPCs will need to update Route Table

![alt text](https://github.com/shakespete/aws/blob/master/img/vpc_peering_2.png)


<h2>Network Setup</h2>
<ol>
  <li>Create VPC</li>
  <li>Create at least one subnet in VPC</li>
  <li>Create Internet Gateway and attach to corresponding VPC</li>
  <li>Create and configure Route Table then associate to appropriate subnet</li>
  <li>Create NACL, edit Inbound and Outbound rules, and associate to appropriate subnet</li>
  <li>Create Security Group then edit Ingress and Egress rules</li>
</ol>
