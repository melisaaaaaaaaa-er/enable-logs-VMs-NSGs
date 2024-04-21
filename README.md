# Enable Log Collection for VMs and NSGs

<h2>Purpose</h2>

- Enable log collection for virtual machines (VMs) and network security groups (NSGs)
- Analyze some logs on LAW using KQL queries.

<img src ="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/Log%20Analystics%20Workspace%20ingestion%20diagram.png"/>

#
<h3>Create a Storage Account</h3>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/1.png"/>

Create a Storage Account on Azure. Make sure to place it in the same resource group and location as your VMs as this Storage Account is where the Network Security Group (NSG) flow logs will be stored and it will not work if it is in another region.

#
<h3>Set up the NSG Flow Log</h3>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/2.png"/>

Navigate to the windows-vm NSG, then to the NSG flow logs panel.

Select “Create” in the middle of the page or on the top-left corner.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/3.png"/>

Choose “+ Select target resource”.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/4.png"/>

Select both “linux-vm-nsg” and “windows-vm-nsg”.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/5.png"/>

For “Storage accounts” choose the SA created prior (here it is sacyberlab2024). As mentioned previously, this SA is where the flow logs will be stored.

For “Retention (days)” input 0. This will store the NSG flow logs indefinitely.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/6.png"/>

Under the “Analytics” panel, select “Version 2” for Flow log version. 

Click “Enable traffic analysis”. Make the Traffic analytics processing interval every 10 minutes.

Select the Log Analytics Workspace you are working with.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/7.png"/>

Here is the NSG flow log set up.

#
<h3>Configure Data Collection Rules</h3>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/8.png"/>

Go to the LAW interface → Agents → Data Collection Rules

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/9.png"/>

Select “+Create”.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/10.png"/>

Give the rule a name and place it in the same region as your resource group.

Select “All” for Platform Type.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/11.png"/>

In Resources, select “+ Add resources”.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/12.png"/>

Expand the Resource Group options and choose both the windows and linux VMs. Click “Apply”. 

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/13.png"/>

Go to the “Collect and deliver” panel.

#
<h4>Linux</h4>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/14.png"/>

Click “+Add”.

In the data source panel, choose “Linux Syslog” as the data source type.

Choose LOG_AUTH for the log type and LOG_DEBUG for the log level. The log level indicates the minimum severity level logs should be collected. LOG_DEBUG indicates essentially all logs should be collected.

Go to the Destination panel.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/15.png"/>

Select “Add destination”.

Choose “Azure Monitor Logs” for the destination type and the LAW you are using for the account/namespace.

Click “Add data source”.

#
<h4>Windows</h4>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/17.png"/>

Select “+Add”.

Choose “Windows Event Logs” for the data source group. 

Choose the Basic event log collection option.

Select “Information” for Application and select all Security options. The application option will collect logs from the SQL server installed in windows-vm while the security option will collect authentication/login logs.

Go to the Destination panel.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/18.png"/>

Select “Add destination”.

Choose “Azure Monitor Logs” for the destination type and the LAW you are using for the account/namespace.

Click “Add data source”.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/19.png"/>

Click “Review + create”.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/20.png"/>

Here are the the configured resources for our data collection rule (i.e. where the DCR takes logs from).

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/21.png"/>

Navigate to “Data sources” and select “Windows Event Logs”.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/22.png"/>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/23.png"/>

From https://github.com/joshmadakor1/Cyber-Course-v2/blob/main/Special-Windows-Event-Data-Collection-Rules/Rules.txt, copy the outlined xpath rules and add them to the Custom Data source list.

Click “Save”.

#
<h2>Adding LAW Agents Manually</h2>

While monitoring/log collection agents are installed on the VMs automatically by Azure (Azure Monitor agent), it’s possible to manually install agents as well (Log Analytics agent).

However, as of this lab, Log Analytics agent support is set for deprecation by August 2024. I’ll still showcase how to do the setup manually.

#
<h4>Windows</h4>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/24.png"/>

Navigate to the Agents panel of the LAW page. 

Select Windows servers at the top. Notice how there is “1 Windows computers connected”. This is the Azure Monitor Agent that was intalled automatically by Azure. To the right of this is “0 Windows computers connected”, referring to how there are no manually installed Log Analytics agents.

Click “Log Analytics agent instructions”. Here you will see the instructions for how to manually install the agent on Windows. 

Right-click the “Download Windows Agent (64-bit)” and select “Copy Link”.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/25.png"/>

RDP into your windows-vm and paste the link into the URL bar of the browser (Microsoft Edge). The agent setup wizard will install automatically. Select it once it’s done.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/26.png"/>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/27.png"/>

Follow the installation wizard.

When done, open the Control Panel and configure the large icon view. Select “Microsoft Monitoring Agent”.

Select “Add...” on the Azure Log Analytics (OMS) panel and copy+paste the Workspace ID from the agent installation instructions page on Azure. The Workspace Key should fill automatically. Choose “Azure Commercial” for Azure Cloud and click OK.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/28.png"/>

Here is the agent configuration. Click Apply and OK. 

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/29.png"/>

Now the Log Analytics agent has been manually installed.

#
<h4>Linux</h4>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/30.png"/>

Installing the Log Analytics Agent manually on Linux follows a similar process.

Expand the instructions panel and copy the “Download and onboard agent for Linux” installation command.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/31.png"/>

SSH into linux-vm and paste the command into the shell and press enter on your keyboard.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/32.png"/>

Once the installation is finished, it should end with “status code 0” if successful.

The red text is a warning about the Log Analytics Agent deprecation planned for August 2024 mentioned previously.

#
<h2>Analyzing some logs on LAW using KQL</h2>

NOTE: it may take a few hours for LAW to fully start ingesting logs from the various sources configured previously. For this lab, the Linux Syslogs took 2-3 hours to show up. You may need to wait a bit if experiencing the same issue.

#
<h4>Windows</h4>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/34.png"/>

Type “SecurityEvent” into the query panel to bring up the Windows authentication logs. If the configuration up til now have been successful, the results should show up in the table below. The same goes for logs from linux-vm and the NSG.

#
<h4>Azure NSG</h4>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/35.png"/>

#
<h4>Linux</h4>
<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/enable-logs-VMs-NSGs-images/main/36.png"/>
