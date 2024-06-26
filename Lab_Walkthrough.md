# Setting Up a Home Lab for Cybersecurity Training with Elastic SIEM and Kali Linux

In the world of cybersecurity, staying ahead of potential threats is paramount. Understanding the tools and techniques used in security monitoring and incident response is crucial for safeguarding systems and data against malicious actors. In this blog post, I will provide a walk through outlining how I set up a home lab using Elastic SIEM and a Kali VM, which enabled me to gain hands-on experience with security monitoring and incident detection.

![Kali Linux Logo](./images/kali-logo.png)

## Setting Up Elastic SIEM

To start, it’s essential to create a free Elastic account to deploy a cloud-based Elastic instance for running the SIEM. You can do this by navigating to https://cloud.elastic.co/registration. Upon logging in, locate and click on the “Start your free trial” option. Next, select the “Create Deployment” button, and opt for “Elasticsearch” as the deployment type. Choose a region and deployment size that aligns with your requirements, and proceed by clicking on the “Create Deployment” button. Once the deployment setup is finalized, click on “continue” to proceed with the next steps.

## Setting Up Kali Linux VM

The next step is setting up your Kali VM. For this I used Oracle VirtualBox. Once VirtualBox is downloaded and set up on your machine you can proceed with downloading the Kali Linux ISO image from the official Kali website. Choose the appropriate version based on your system architecture (32-bit or 64-bit).

To create a new virtual machine you can use the following steps:

1. Launch VirtualBox and click on the “New” button in the toolbar.
2. Enter a name for your virtual machine (ex. “Kali”) and select the type as “Linux” and the version as “Debian (64-bit)”.
3. Allocate memory (RAM) to the virtual machine. A minimum of 2 GB is *recommended* for Kali Linux.
4. Create a virtual hard disk now and choose the default options for the hard disk file type and storage allocation.
5. Choose the hard disk file type. The default option (VDI) should suffice for most users.
6. Select “Dynamically allocated” for the storage on physical hard disk. This allows the virtual disk file to grow as needed.
7. Specify the size of the virtual hard disk. A minimum of 20 GB is recommended for installing Kali Linux.
8. Click “Create” to create the virtual machine.
9. Start the virtual machine by clicking on the “Start” button in VirtualBox.
10. The Kali Linux installer should boot from the ISO image. Follow the on-screen instructions to install Kali Linux on the virtual hard disk.
11. During the installation process, you will be prompted to configure various settings such as language, location, keyboard layout, and disk partitioning. Follow the prompts and choose the appropriate options.
12. Once the installation is complete, reboot the virtual machine and log in to your new Kali Linux environment.

**Note:** If you are only using this VM for this lab, the recommended RAM and CPU requirements may be able to be lessened a bit.

## Setting Up Elastic Agent

Next, we must set up an agent. An agent refers to a software application installed on a device, such as a server or endpoint, with the purpose of gathering and transmitting data to a centralized system for analysis and monitoring. Within the realm of Elastic SIEM, an agent serves as a mechanism for retrieving and relaying security-related events from endpoints to the Elastic SIEM instance. To configure the agent for collecting logs from your Kali VM and transmitting them to your Elastic SIEM instance, adhere to the following steps:

1. Access your Elastic SIEM instance by logging in and proceeding to the Integrations page. You can do this by clicking on the hamburger menu bar located at the top left corner of the interface. From there, select “Add Integrations” whic is located at the bottom of the menu.
![Add Integrations](./images/add-integrations.png)

2. Locate “Elastic Defend” and select it to access the integration page. Proceed to click on “Install Elastic Defend” and adhere to the instructions outlined on the integration page to install the agent on your Kali VM.
![Elastic Defend](./images/elastic-defend.png)

3. Select ‘Add Elastic Agents to your hosts’ and paste in the following command to you Kali Linux terminal.
![Install Code](./images/install-code.png)

4. The installation could take a few minutes, but once it is complete you will see a command that says “Elastic Agent has been successfully installed.” The agent will now commence collecting and transmitting logs to your Elastic SIEM instance automatically.
![Successful Install](./images/successful-install.png)

## Generating Security Events

You can now generate some security events on your Kali VM by running an Nmap scan. Nmap is a freely available open-source tool utilized extensively for network exploration, administration, and security assessment purposes. Its primary function revolves around uncovering hosts and services within a computer network, essentially crafting a comprehensive “map” of the network landscape. Nmap’s capabilities extend to scanning hosts to identify accessible ports, discerning the operating system and software running on target systems, and procuring additional network-related information.

You can run an Nmap scan on your Kali machine by opening up the terminal and entering some of the following commands:

- `nmap -p- localhost`
- `sudo nmap <vm-ip>`
- `nmap -sS <ip address>`
- `nmap -sT <ip address>`

![Nmap Scan](./images/nmap-scan1.png)
![Nmap Scan](./images/nmap-scan2.png)

## Querying and Analyzing Logs

Now that we have forwarded data from the Kali VM to the SIEM, we can start querying and analyzing the logs in the SIEM. To do this, click on the hamburger menu icon at the top-left and then under “Observability” select “Logs”.

![Logs View](./images/logs-view-elastic.png)

Next, in the search bar, search for all logs related to Nmap scans by entering the following query: process.args: “nmap_scan”. The results of the search query will be displayed in the table. You can click on the three dots next to each event to view more details. By generating and scrutinizing various security events within Elastic SIEM, such as the one described above, you can enhance your comprehension of the detection, investigation, and response processes to security incidents in real-world scenarios.

## Creating Visualizations and Alerts

Now, we will create a dashboard to visualize and analyze the log data more efficiently. We do this by once again clicking on the hamburger icon and selecting “Dashboards” under the “Analytics” section.

![Dashboard Menu](./images/dashboards-elastic.png)

 Click “Create dashboard” to create a new dashboard. Next, click on the “Create Visualization” button to add a new visualization to the dashboard. We can select “Bar chart” as the visualization type. For the Metrics section, we can select “Count” for the vertical field type and “Timestamp” for the horizontal field type. Now click ‘Save’ to complete the visualization. Here is an example of what mine looked like after running multiple nmap scans prior to creating it.

![Dashboard Visual](./images/dashboard-visual.png)

Now we can create an alert. Within a SIEM, alerts play a pivotal role in the identification and prompt response to security incidents. These alerts are formulated using predefined rules or customized queries and can be tailored to initiate specific actions upon meeting designated criteria. To create an alert, click on the hamburger icon then select “Alerts” under the “Security” section.

![Add Alerts](./images/alerts-elastic.png)

Next click on “Manage Rules” at the top right of the screen. Click on the “Create new rule” button at the top right. Under the “Define rule” section, select the “Custom query” option from the dropdown menu. Under “Custom query”, set the conditions for the rule. We will use the following query: event.action: “nmap_scan”. This will trigger an alert whenever an Nmap scan is run on the target machine, which in our case is our Kali VM. Give your rule a name and description as well as fill out the other relevant information required to help prioritize your alerts based on significance. Then click “Continue”.

In the “Actions” section you can chose a variety of actions to be taken when an alert is triggered. I decided to go with email, but some other options include sending a Slack message, creating a Jira ticket, or triggering a custom webhook. Now we can finally select “Create and enable rule” to create the alert.

![Alert Preview](./images/alert-preview.png)

## Conclusion

In this comprehensive guide, we’ve crafted a home lab environment leveraging Elastic SIEM and a Kali VM. Throughout the process, we channeled data from the Kali VM to the SIEM via the Elastic Beats agent, generated simulated security events using Nmap, and thoroughly explored querying and analyzing logs within the SIEM through the intuitive Elastic web interface. Additionally, we’ve gone a step further by crafting a dynamic dashboard to visually represent security events, complemented by the creation of a tailored alert system to promptly detect security incidents.

This home lab served as an avenue for honing and refining my skills in security monitoring and incident response with Elastic SIEM. By immersing myself in this hands-on exercise, I have gained practical experience in utilizing a SIEM but also nurturing the expertise necessary to thrive as a proficient security analyst or engineer, and you can too!
