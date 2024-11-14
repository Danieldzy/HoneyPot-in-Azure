<h1>HoneyPots deployed in Azure and real-time attack monitored by Microsoft Sentinel</h1>
<br/><img src="https://imgur.com/2ANt7E6.png" height="80%" width="80%" alt="Attacker Heatmap"/><br/>
<h2>Intro</h2>
<br/>In this lab, I deployed a Windows HoneyPot in Azure—configured with minimal security settings. The VM has all ports open, the lowest security priority, and allows all network protocols and inbound traffic to simulate a highly vulnerable environment. The attacker heat map above is based on two hours of real-time monitoring.<br/>

<h2>Utility Used</h2>
Azure VM<br/>
Azure Log Analytic Workplace<br/>
Azure Defender For Cloud<br/>
Microsoft Sentinel<br/>
PowerShell<br/>
KQL<br/>

<h2>Process</h2>
<b>Deploy VMs in Azure</b>

Set up your Windows VMs by specifying the VM name and setting a secure password. Set up a strong username and password to prevent user and password enumeration attacks, as we expect to receive many failed login attempts once deployed.
<br/><img src="https://imgur.com/BlYZxwF.png" height="80%" width="80%" alt="Create VMs"/><br/>

Go to the Network tab, select the NSG (Network Security Group). If you don't have one already, create a new NSG. We'll configure the NSG rules later. 
<br/><img src="https://imgur.com/TT6J4SG.png" height="80%" width="80%" alt="Create VMs"/><br/>

<br/>To configure your Azure NSG, search for NSG in the search bar and go to the NSG settings. Delete any default NSG rule if it exists. Then, create your own rule (e.g., WindowsHoneyPot-nsg) and set the Destination Port Range from 8080 to "*", with the priority set to 100 (low priority). Choose any for the protocol. This will allow network traffic to visit all our VM ports, with a low priority setting, it makes the VM more vulnerable, effectively turning it into a honeypot designed to attract and observe malicious traffic.<br/>
<br/><img src="https://imgur.com/JypYAe8.png" height="80%" width="80%" alt="Create VMs"/><br/>

After setting up your VM and NSG, you'll need to deploy a Log Analytics Workspace (LAW). First, search for "Log Analytic Workspace" in the Azure portal search bar, and click "Create." You can place the LAW in the same resource group as your VM. Once it's created, navigate to the VM you created, select the Windows VM, and click "Connect."
<br/><img src="https://imgur.com/jHAObdq.png" height="80%" width="80%" alt="Create VMs"/><br/>
<br/><img src="https://imgur.com/QO7GPvc.png" height="80%" width="80%" alt="Create VMs"/><br/>
<br/><img src="https://imgur.com/intJJJD.png" height="80%" width="80%" alt="Create VMs"/><br/>

<br/>Setting up a Log Analytics Workspace (LAW) alone isn't enough to collect logs from your VM. To ensure a smooth log ingestion process and improve visibility, you need to integrate Microsoft Defender for Cloud and Microsoft Sentinel. These tools work together to optimize log collection, enhance security posture, and provide a comprehensive visual presentation of your data.<br/>

<br/>To get started, search for Defender for Cloud in the Azure portal and navigate to Environment Settings. Under Azure Subscription 1, select the LAW you created(This LAW has been connected with the Windows VM). In the Defender Plan, deselect SQL Server and select Servers, then click Save. Lastly, go to the Data Collection tab and choose All Events to capture every alert, ensuring that no critical events are missed for monitoring and analysis. Enable Microsoft Defender and choose the LAW associated with your WindowsVM, it will start ingesting logs from your Windows VM in Azure, <br/>
<br/><img src="https://imgur.com/bUg7y6j.png" height="80%" width="80%" alt="Create VMs"/><br/>
<br/><img src="https://imgur.com/E7ULxNX.png" height="80%" width="80%" alt="Create VMs"/><br/>
<br/><img src="https://imgur.com/vtAE75m.png" height="80%" width="80%" alt="Create VMs"/><br/>

<br/>After setting up the VM, Log Analytics Workspace (LAW), and Defender for Cloud, configure the example logs and ensure the correct log path is set for LAW to extract data. To enhance alert visualization with geolocation data, visit the IP Geolocation API site (https://ipgeolocation.io/) to register and obtain an API key. Keep this key saved securely, as it will be used in the PowerShell script to visualize alerts with IP geolocation data.<br/>
<br/>On your host machine, use Remote Desktop Protocol (RDP) to connect to your Windows VM (you can locate the VM’s public IP address on its Azure page). To generate log data, try intentionally entering an incorrect password a few times. Once logged in, open Windows PowerShell ISE and run the provided command from this repository. This command saves all Event Viewer logs to a file named failed_rdp.log, including geolocation information for each entry. The file will be saved by default at C:\ProgramData\failed_rdp.log. Later, we'll use the Log Analytics Workspace (LAW) in Azure to ingest this log data and display it in Microsoft Sentinel for visualization. Remember to replace the $API_KEY variable to your API key obtained from IP Geo website.<br/>
<br/><img src="https://imgur.com/NGhy5z8.png" height="80%" width="80%" alt="Create VMs"/><br/>
<br/>After running the PS script, the failed_rdp.log will be automatically generated and saved. Copy this file path, we need to set this path to our LAW in Azure.<br/>
<br/><img src="https://imgur.com/8XQERvh.png" height="80%" width="80%" alt="Create VMs"/><br/>


<br/>Back in Azure, go to your Log Analytics Workspace (LAW), select Tables, and create a new custom log (MMA-based). Under the Sample tab, paste the log data from failed_rdp.log that you generated on the VM, then click Next. Choose Windows and in the Path field, accurately enter C:\ProgramData\failed_rdp.log to ensure LAW can properly extract data from this log file.<br/>
<br/><img src="https://imgur.com/U5DExJS.png" height="80%" width="80%" alt="Create VMs"/><br/>
<br/><img src="https://imgur.com/D7viX4k.png" height="80%" width="80%" alt="Create VMs"/><br/>


<br/>Now, let’s set up Microsoft Sentinel. In Azure, search for Sentinel and create a new instance. Select the Log Analytics Workspace (LAW) you previously set up, then add Microsoft Sentinel to this workspace. Once complete, the Sentinel dashboard will be available, providing centralized monitoring and analysis.<br/>
<br/><img src="https://imgur.com/XfQgTHQ.png" height="80%" width="80%" alt="Create VMs"/><br/>
<br/><img src="https://imgur.com/NCmjozN.png" height="80%" width="80%" alt="Create VMs"/><br/>

<br/>From the Sentinel dashboard, you can create custom workbooks with flexible layouts and visualizations to suit your monitoring needs. As you build out your workbook, you’ll see that logs are already being visualized in real time. You can also navigate to the Logs section to enter KQL (Kusto Query Language) queries for more detailed analysis. For example, using a query for Security Event ID 4625 will display failed RDP login attempts. Over time, as botnets worldwide attempt to access your honeypot server, additional failed login events will appear in the logs.<br/>
<br/><img src="https://imgur.com/29ZUjH6.png" height="80%" width="80%" alt="Create VMs"/><br/>
<br/><img src="https://imgur.com/Chjv4r1.png" height="80%" width="80%" alt="Create VMs"/><br/>

<br/>In Sentinel, you’ll also see detailed information like the attacker’s IP address and geolocation data, which makes it significantly more intuitive and visually informative than the standard Windows Event Viewer. This enhanced dashboard enables you to quickly assess security threats and understand where attacks are originating, providing a clear, actionable view of failed RDP attempts on your honeypot server. The image below displays the KQL (Kusto Query Language) query used to filter failed RDP login attempts, specifically by identifying Event ID 4625, which corresponds to failed logon events.<br/>
<br/><img src="https://imgur.com/CMRkI9N.png" height="80%" width="80%" alt="Create VMs"/><br/>

<br/>You can also display this data as geolocation points on a world map, showing where the botnet attackers are located. The image below is based on two hours of monitoring activity. If you keep your VM running, more results will accumulate, giving a broader picture of attack origins over time.<br/>
<br/><img src="https://imgur.com/2ANt7E6.png" height="80%" width="80%" alt="Create VMs"/><br/>
