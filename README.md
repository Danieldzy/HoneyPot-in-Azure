<h1>Windows Server Active Directory Management Home Lab</h1>

<h2>Considerations</h2>
In a real office environment, best practice suggests that a Domain Controller (DC) should have only one internal-facing network interface card (NIC) for security purposes. Furthermore, DCs and workstations should be placed on different subnets to enhance network isolation. Internet access should be routed through a proxy server and a firewall or router, ensuring the DC remains securely dedicated to its internal role.<br/>


<br/>A Domain Controller (DC) should not be public-facing for these key reasons:<br/>

1. Security: DCs store sensitive data like user credentials, making them prime targets for attacks if exposed to the internet.<br/>
2. Expanded Attack Surface: Public exposure increases the risk of exploitation through vulnerabilities in services like LDAP or DNS.<br/>
3. Core Role: DCs handle critical internal tasks like authentication, and managing group policies. Which should remain within the internal network.<br/>
4. Performance Impact: External traffic can degrade the DC's ability to efficiently perform vital internal operations.<br/>

<br/>It is also advisable to keep services like Remote Access Services (RAS) and other non-core functions off the DC. RAS should be deployed on a dedicated server, allowing the DC to focus on its core responsibilities without being subjected to additional security risks or performance issues.

<br/>In a home lab environment, however, we have assigned two NICs to the DC (one internal and one external) to demonstrate the lab. This setup should never be used for real-world scenarios but serves to illustrate how to connect a workstation to the DC using Active Directory and allow the workstation to access the internet. Thus, the lab setup differs from the real-world configuration for educational purposes. Diagram 1 below should be the real-world scenario. Diagram 2 later is the demonstration of our home lab.<br/>
 
<p align="center">
Diagram 1: <br/>
<br/>
<img src="https://imgur.com/b94665x.png" height="80%" width="80%" alt=""/>
