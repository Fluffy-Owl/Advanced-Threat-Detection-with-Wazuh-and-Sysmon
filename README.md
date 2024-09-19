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

   ```xml
   <Sysmon schemaversion="4.30">
       <EventFiltering>
           <RuleGroup name="Default" groupRelation="or">
               <FileCreateTime onmatch="exclude">
                   <Image condition="end with">mimikatz.exe</Image>
               </FileCreateTime>
           </RuleGroup>
       </EventFiltering>
   </Sysmon>
   ```

### **3. Ingesting Sysmon Logs into Wazuh**

1. **Configure the Windows Agent**: Set up the Windows agent to send Sysmon logs to the Wazuh manager. Follow the [Wazuh documentation for Windows agent configuration](https://documentation.wazuh.com/current/installation-guide/windows/windows-agent.html).

2. **Verify Log Ingestion**: Ensure that Sysmon logs are being successfully ingested into Wazuh. You can check this by looking at the Wazuh dashboard or logs.

### **4. Creating a Custom Wazuh Rule**

1. **Create a Custom Rule**: Add a custom rule in Wazuh to detect Mimikatz activity. Create a new file in the Wazuh rules directory, for example: `/var/ossec/etc/rules/local_rules.xml`.

   ```xml
   <group name="sysmon,">
       <rule id="100001" level="10">
           <decoded_as>sysmon</decoded_as>
           <field name="original_file_name">mimikatz.exe</field>
           <description>Detect Mimikatz activity based on original file name</description>
       </rule>
   </group>
   ```

2. **Reload Wazuh Rules**: Apply the new rule by reloading the Wazuh rules.

   ```bash
   sudo systemctl restart wazuh-manager
   ```

## **Testing and Validation**

1. **Generate Test Alerts**: Simulate Mimikatz activity or use a known sample to trigger the rule and verify that the alert is generated in Wazuh.

2. **Review Alerts**: Check the Wazuh dashboard or log files to ensure that the rule is working as expected.

## **Screenshots**

(Add your screenshots here to illustrate the setup, configuration, and results.)

## **Conclusion**

This project successfully demonstrates how to use Wazuh and Sysmon to detect the presence of Mimikatz by leveraging original file name data, even if the file name is changed by an attacker. This approach enhances the ability to detect and respond to sophisticated threats in a Windows environment.

## **References**

- [Wazuh Documentation](https://documentation.wazuh.com/current/)
- [Sysmon Documentation](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [SwiftOnSecurity Sysmon Config](https://github.com/SwiftOnSecurity/sysmon-config)

---

Let me know if you need any more adjustments!
