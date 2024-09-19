# **Advanced Threat Detection with Wazuh and Sysmon**

## **Project Overview**

This project demonstrates how to enhance threat detection capabilities using Wazuh, an open-source security monitoring platform, in combination with Sysmon, a Windows system monitoring tool. The focus of this lab is to create a rule in Wazuh that detects the presence of Mimikatz, a well-known credential-stealing tool, even if an attacker changes its file name. This is achieved by leveraging Sysmon logs and the original file name data.

The Wazuh instance was deployed in the cloud using DigitalOcean.

## **Objective**

- Set up a Wazuh instance on DigitalOcean.
- Configure a Windows agent to send Sysmon logs to Wazuh.
- Create a Wazuh rule to detect Mimikatz activity based on the original file name.

## **Prerequisites**

- A running Wazuh instance on DigitalOcean.
- A Windows system with Sysmon installed and configured.
- Basic knowledge of Wazuh and Sysmon configuration.

## **Setup Instructions**

### **1. Setting Up Wazuh on DigitalOcean**

1. **Create a DigitalOcean Droplet**: Log in to your [DigitalOcean account](https://www.digitalocean.com/) and create a new droplet. Select an appropriate size and image (e.g., Ubuntu).

2. **Install Wazuh**: SSH into your droplet and follow the [official Wazuh documentation](https://documentation.wazuh.com/current/installation-guide/) to install and configure Wazuh on your server.

3. **Configure Wazuh Manager**: Ensure the Wazuh manager is properly configured to accept logs from the Windows agent.

### **2. Configuring Sysmon on Windows**

1. **Install Sysmon**: Download and install Sysmon from the [Microsoft Sysinternals website](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon).
   
2. **Configure Sysmon**: Use a configuration file to capture relevant events. You can use the example configuration file from the [SwiftOnSecurity GitHub repository](https://github.com/SwiftOnSecurity/sysmon-config).

<img width="572" alt="Installing sysmon" src="https://github.com/user-attachments/assets/c5291c4b-f46c-494d-aaec-c0816c724c4f">

*Figure 1: Installing sysmon on the Windows Agent.*


### **3. Ingesting Sysmon Logs into Wazuh**

1. **Configure the Windows Agent**: Set up the Windows agent to send Sysmon logs to the Wazuh manager. Follow the [Wazuh documentation for Windows agent configuration](https://documentation.wazuh.com/current/installation-guide/windows/windows-agent.html).

2. **Verify Log Ingestion**: Ensure that Sysmon logs are being successfully ingested into Wazuh. You can check this by looking at the Wazuh dashboard or logs.

### **4. Creating a Custom Wazuh Rule**

1. **Create a Custom Rule**: Add a custom rule in Wazuh to detect Mimikatz activity. Create a new file in the Wazuh rules directory, for example: `/var/ossec/etc/rules/local_rules.xml`.

   ```xml
   <group name="sysmon,">
       <rule id="100002" level="15">
           <if_group>sysmon_event1</if_group>
           <field name="win.eventdata.originalFileName" type="pcre2">(?i)mimikatz\.exe</field>
           <description>Mimikatz Usage Detected</description>
           <mitre>
             <id>T1003</id>
           </mitre>
       </rule>
   </group>
   ```
<img width="593" alt="Adding local rules in Wazuh" src="https://github.com/user-attachments/assets/667fd8d7-348f-4258-9e76-773b8ef6a376">

*Figure 2: Adding local rule in Wazuh (hosted in the cloud).*
   

2. **Reload Wazuh Rules**: Apply the new rule by reloading the Wazuh rules.

   ```bash
   sudo systemctl restart wazuh-manager
   ```

## **Testing and Validation**

1. **Generate Test Alerts**: Simulate Mimikatz activity or use a known sample to trigger the rule and verify that the alert is generated in Wazuh.

2. **Review Alerts**: Check the Wazuh dashboard or log files to ensure that the rule is working as expected.

## **Screenshots from the project**

<img width="571" alt="Wazuh Dashboard" src="https://github.com/user-attachments/assets/8604a5bc-60b8-4c8c-b5e2-ea7fc3a2fe1c">

*Figure 3: Wazuh dashboard showing the overview of active alerts and system status.*

<img width="496" alt="Run Catch me if you can" src="https://github.com/user-attachments/assets/ace86dbd-55d9-43a3-bfce-262a4c2250f0">

*Figure 4: Running Mimikatz in the Windows machine, only this time we change the file name to "catchmeifyoucan".*


<img width="572" alt="No Alerts" src="https://github.com/user-attachments/assets/3a666857-0d2e-4e51-9edb-3fd586300d0e">
<img width="574" alt="After Alert" src="https://github.com/user-attachments/assets/8daabb20-8437-447b-8bc5-3518a59d7a34">


*Figure 5 & 6: Before and after creation of the rule. Figure 6 shows that there is a new alert picked up that uses Mimikatz.*

<img width="572" alt="Mimikatz after alertds" src="https://github.com/user-attachments/assets/b1214ffa-0f6d-44c7-8c91-238e6c7cc27d">
<img width="402" alt="Original Files" src="https://github.com/user-attachments/assets/731c97ff-09bb-4d30-88cd-80f29bd521fc">

*Figure 7 & 8: Detecting Mimikatz using the OriginalFileName data.*



## **Conclusion**

This project successfully demonstrates how to use Wazuh and Sysmon to detect the presence of Mimikatz by leveraging original file name data, even if the file name is changed by an attacker. This approach enhances the ability to detect and respond to sophisticated threats in a Windows environment.

---


## **References**

- [Wazuh Documentation](https://documentation.wazuh.com/current/)
- [Sysmon Documentation](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [SwiftOnSecurity Sysmon Config](https://github.com/SwiftOnSecurity/sysmon-config)

---

