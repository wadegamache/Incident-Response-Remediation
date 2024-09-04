# Incident Response Remediation



## Introduction and Objective

In this project, I use NIST 800-61 to do Incident Response on some incidents created in my [Creating a Live SOC/Honeynet in Azure](https://github.com/wadegamache/Azure-SOC-Honeynet) project.


## BRUTE FORCE Attempt - MEDIUM Priority

### Incident Description

This incident involves observation of potential brute force attempts against a windows VM. Custom analytic rules were deployed on Microsoft Sentinal to create alerts and incidents to potential malicious activity. This one below will alert on ten or more failed login attempts within one hour.

![image](https://github.com/user-attachments/assets/e61739c7-39c5-4e99-86a6-15450c20dfb6)

This is the initial page when investigating an incident in Sentinal. It gives you a great overview of the incident and alerts triggered. On the left side of the page we can see that 72 events and 8 alerts were created (9 events/alert). We can also see that 10 entities were involved. To get more context for the incident we can explore the tabs on the right side, starting with the timeline. 

All of these details are important to document for our final report and post-incident activity. By looking through the timeline tab we see that this incident occured on June 27th between 7:50pm-11:05pm. 

![image](https://github.com/user-attachments/assets/db75d921-24ba-4852-8e2e-4d669d1f3177)

This next image takes a look at our alerts tab. Here we can see all 8 of our alerts and the events that created them. We can choose to investigate them individually if needed. 

![image](https://github.com/user-attachments/assets/6df9f37c-70c8-40da-82cc-8e8d7bfdca0e)

This image focuses on the entity tab. We can see that we have 10 total, 1 host (windows-vm) and 9 attacker IP addresses. Here we can see that many of the IPs are similar and might come from the same region. The next step is to analyze the logs created by these alerts. We will go to our Log Analytics Workspace to query those logs.

![image](https://github.com/user-attachments/assets/979c045e-35ef-4f65-a4b6-161e0bb63857)

This query shows all logs with event id #4625 (failed logon attempts), where the amount exceeded 10 times. It also summarizes output into the feilds: Attacker IP, Event ID, Activity, Destination Host Name, and Failure Count.

![image](https://github.com/user-attachments/assets/eb0bdeaa-7638-4eb5-bc75-61b8eec7d336)

In this image we take a closer look at one of these logs. This was the largest of the brute force attempts with a 1588 Failure Count.

After confirming the authenticity of the alerts, we can go forward and investigate the attacker IP addresses. 

![image](https://github.com/user-attachments/assets/2895eb9c-0557-41d8-8378-7b98f035a651)

There are many resources available to help with this type of research. For this one I used a simple site called [WhatIsMyIP.com](https://www.whatismyip.com/).

The image above shows results from 4 of the attacker IPs. As we can see, the attacks are coming from multiple sources in Russia, Poland and the Netherlands. These locations are not typically associated with the host windows-vm and can be interpreted as malicious. 

To prevent further brute force attempts we will make some inbound firewall rules for those IP addresses. 

![image](https://github.com/user-attachments/assets/b6b50d06-920c-41ad-bd6a-43081c19b271)

The image above shows how to add an inbound security rule to the Network Security Group where the windows-vm resides. This is a broad rule designed to block all connection attempts regardless of source port ranges, destination ports or protocols. 

![image](https://github.com/user-attachments/assets/2a1642cb-0297-4248-b4f1-a1aedd03c777)

It is set to DENY all traffic from the selected IP. The priority level is set to 100 so that the rule will be checked first when a connection request comes in.

As we saw in an earlier image, many of the IP addresses in this incident were similar and started with the same three octets. This means they are from the same area or region. When making a firewall rule we can group these addresses together in an IP range.

![image](https://github.com/user-attachments/assets/00fcfd9d-f499-4956-96a8-7ea759be47a9)

The image above shows how to make an inbound rule using CIDR notation. The IP address range of 2.57.149.0 - 2.57.149.255 can be entered as 2.57.149.0/24. This eliminates the need to make many individual rules if attacking entites are related. 

![image](https://github.com/user-attachments/assets/470b7f70-d0b4-4d3a-a3c4-d7bb6ec6b384)

In this image we can see that we only needed to make 4 inbound rules for all 8 entities in this incident. IP ranges with CIDR can streamline remediation and make things more efficient. All of the rules created have a DENY action on all connection attempts It's also important to have the appropriate priortiy level with security rules so that a rule is not bypassed by another.

<!--

![image](https://github.com/user-attachments/assets/b1b3e750-81c1-4da9-b460-b580b120f365)

This image is from the close out of the incident. This is where to document all of our findings and close the incident




### Incident Response Actions
Verify the authenticity of the alerts
Isolate machine? Change password of affected user?
Identify the origin of attacks, determine if they are attacking or involved with anything else
Determine how and when ttack occured. Are the NSGs not beng locked down? If so check other NSGs.
Assess the potential impact of the incident. What type of account was it? Permissions?

### Containment and Recovery
Lock down the NSG assigned to that VM/Subnet, either entirely, or to allow only necessary trafic.

Reset the affected uer's password.

Enable MFA.

### Document Findings and Close out Incident

![image](https://github.com/user-attachments/assets/da9fc6b3-cc13-43df-865c-d1a59dbd2a8e)
Before investigating the attacker IPs, we want to see if there are any correlations to other incidents or entities on the network. In this image, we see our 10 entites from this incident and 

I think it is important to use multiple tools and sources to provide the best scope?

## Malware Detected - HIGH Priority

This incident 


-->
