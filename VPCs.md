<h1>VPC</h1>
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