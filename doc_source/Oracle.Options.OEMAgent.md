# Oracle Management Agent for Enterprise Manager Cloud Control<a name="Oracle.Options.OEMAgent"></a>

Oracle Enterprise Manager \(OEM\) Management Agent is a software component that monitors targets running on hosts and communicates that information to the middle\-tier Oracle Management Service \(OMS\)\. For more information, see [Overview of Oracle Enterprise Manager cloud control 12c](http://docs.oracle.com/cd/E24628_01/doc.121/e25353/overview.htm) and [Overview of Oracle Enterprise Manager cloud control 13c](http://docs.oracle.com/cd/E63000_01/EMCON/overview.htm#EMCON109) in the Oracle documentation\.

Amazon RDS supports Management Agent through the use of the `OEM_AGENT` option\. Management Agent requires an Amazon RDS DB instance running any of the following releases: 
+ Oracle Database 19c \(19\.0\.0\.0\)
+ Oracle Database 12c Release 2 \(12\.2\.0\.1\)
+ Oracle Database 12c Release 1 \(12\.1\.0\.2\)

Amazon RDS supports Management Agent for the following versions of OEM: 
+ Oracle Enterprise Manager Cloud Control for 13c
+ Oracle Enterprise Manager Cloud Control for 12c

**Topics**
+ [Prerequisites for Management Agent](#Oracle.Options.OEMAgent.PreReqs)
+ [Limitations for Management Agent](#Oracle.Options.OEMAgent.limitations)
+ [Option settings for Management Agent](#Oracle.Options.OEMAgent.Options)
+ [Adding the Management Agent option](#Oracle.Options.OEMAgent.Add)
+ [Using the Management Agent](#Oracle.Options.OEMAgent.Using)
+ [Modifying Management Agent settings](#Oracle.Options.OEMAgent.ModifySettings)
+ [Performing database tasks with the Management Agent](#Oracle.Options.OEMAgent.DBTasks)
+ [Removing the Management Agent option](#Oracle.Options.OEMAgent.Remove)

## Prerequisites for Management Agent<a name="Oracle.Options.OEMAgent.PreReqs"></a>

To use Management Agent, ensure that you meet the following prerequisites\.

### General prerequisites<a name="Oracle.Options.OEMAgent.PreReqs.general"></a>

Following are general prerequisites for using Management Agent: 
+ You need an Oracle Management Service \(OMS\) that is configured to connect to your Amazon RDS DB instance\. 
+ In most cases, you must configure your VPC to allow connections from OMS to your DB instance\. If you aren't familiar with Amazon Virtual Private Cloud \(Amazon VPC\), we recommend that you complete the steps in [Tutorial: Create an Amazon VPC for use with a DB instance](CHAP_Tutorials.WebServerDB.CreateVPC.md) before continuing\. 
+ Management Agent version 13\.5\.0\.0\.v1 requires OMS version 13\.5\.0\.0 or later\.
+ Management Agent version 13\.4\.0\.9\.v1 requires OMS version 13\.4\.0\.9 or later and patch 32198287\.
+ Ensure that you have sufficient storage space for your OEM release:
  + At least 8\.5 GiB for OEM 13c Release 5
  + At least 8\.5 GiB for OEM 13c Release 4
  + At least 8\.5 GiB for OEM 13c Release 3
  + At least 5\.5 GiB for OEM 13c Release 2
  + At least 4\.5 GiB OEM 13c Release 1
  + At least 2\.5 GiB for OEM 12c 
+ If you are using Management Agent versions `OEM_AGENT 13.2.0.0.v3` and `13.3.0.0.v2`, and if you want to use TCPS connectivity, follow the instructions in [Configuring third party CA certificates for communication with target databases](https://docs.oracle.com/cd/E73210_01/EMSEC/GUID-8337AD48-1A32-4CD5-84F3-256FAE93D043.htm#EMSEC15996) in the Oracle documentation\. Also, update the JDK on your OMS by following the instructions in the Oracle document with the Oracle Doc ID 2241358\.1\. This step ensures that OMS supports all the cipher suites that the database supports\.
**Note**  
TCPS connectivity between the Management Agent and the DB instance is only supported for Management Agent versions `OEM_AGENT 13.2.0.0.v3` and `13.3.0.0.v2`\.

### Oracle Database release prerequisites<a name="Oracle.Options.OEMAgent.PreReqs.db-release"></a>

Following are the supported Oracle Database versions for each Management Agent version\.


****  

| Management Agent version | Oracle Database 19c | Oracle Database 12c Release 2 \(12\.2\) | Oracle Database 12c Release 1 \(12\.1\) | 
| --- | --- | --- | --- | 
|  13\.5\.0\.0\.v1  |  Supported  |  Supported  |  Supported  | 
|  13\.4\.0\.9\.v1  |  Supported  |  Supported  |  Supported  | 
|  13\.3\.0\.0\.v2  |  Supported  |  Supported  |  Supported  | 
|  13\.3\.0\.0\.v1  |  Supported  |  Supported  |  Supported  | 
|  13\.2\.0\.0\.v3  |  Supported  |  Supported  |  Supported  | 
|  13\.2\.0\.0\.v2  |  Supported  |  Supported  |  Supported  | 
|  13\.2\.0\.0\.v1  |  Supported  |  Supported  |  Supported  | 
|  13\.1\.0\.0\.v1  |  Supported  |  Supported  |  Supported  | 
|  12\.1\.0\.5\.v1  |  Not supported  |  Supported  |  Supported  | 
|  12\.1\.0\.4\.v1  |  Not supported  |  Supported  |  Supported  | 

Following are prerequisites for different database versions:
+ For an Amazon RDS DB instance running Oracle Database 19c \(19\.0\.0\.0\), the minimum `AGENT_VERSION` is 13\.1\.0\.0\.v1\. 
+ For an Amazon RDS DB instance running Oracle Database Release 2 \(12\.2\.0\.1\) or lower, meet the following requirements:
  + For OMS 13c Release 2 with Oracle patch 25163555 applied, use OEM Agent 13\.2\.0\.0\.v2 or later\.

    Use OMSPatcher to apply the patch\.
  + For unpatched OMS 13c Release 2, use OEM Agent 13\.2\.0\.0\.v1\.

  Use OMSPatcher to apply patches\.

### OMS host communication prerequisites<a name="Oracle.Options.OEMAgent.PreReqs.host"></a>

Make sure that your OMS host and your Amazon RDS DB instance can communicate\. Do the following: 
+ To connect from the Management Agent to your OMS, if your OMS is behind a firewall, add the IP addresses of your DB instances to your OMS\. 

  Make sure the firewall for the OMS allows traffic from both the DB listener port \(default 1521\) and the OEM Agent port \(default 3872\), originating from the IP address of the DB instance\.
+ To connect from your OMS to the Management Agent, if your OMS has a publicly resolvable host name, add the OMS address to a security group\. Your security group must have inbound rules that allow access to the DB listener port and the Management Agent port\. For an example of creating a security and adding inbound rules, see [Tutorial: Create an Amazon VPC for use with a DB instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. 
+ To connect from your OMS to the Management Agent, if your OMS doesn't have a publicly resolvable host name, use one of the following: 
  + If your OMS is hosted on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance in a private VPC, you can set up VPC peering to connect from OMS to Management Agent\. For more information, see [A DB instance in a VPC accessed by an EC2 instance in a different VPC](USER_VPC.Scenarios.md#USER_VPC.Scenario3)\. 
  + If your OMS is hosted on\-premises, you can set up a VPN connection to allow access from OMS to Management Agent\. For more information, see [A DB instance in a VPC accessed by a client application through the internet](USER_VPC.Scenarios.md#USER_VPC.Scenario4) or [VPN connections](https://docs.aws.amazon.com/vpc/latest/userguide/vpn-connections.html)\. 

## Limitations for Management Agent<a name="Oracle.Options.OEMAgent.limitations"></a>

Following are some limitations to using Management Agent: 
+ Administrative tasks such as job execution and database patching, that require host credentials, aren't supported\. 
+ Host metrics and the process list aren't guaranteed to reflect the actual system state\. Thus, you shouldn't use OEM to monitor the root file system or mount point file system\. For more information about monitoring the operating system, see [Monitoring the OS by using Enhanced Monitoring](USER_Monitoring.OS.md)\.
+ Autodiscovery isn't supported\. You must manually add database targets\. 
+ OMS module availability depends on your database edition\. For example, the database performance diagnosis and tuning module is only available for Oracle Database Enterprise Edition\. 
+ Management Agent consumes additional memory and computing resources\. If you experience performance problems after enabling the `OEM_AGENT` option, we recommend that you scale up to a larger DB instance class\. For more information, see [DB instance classes](Concepts.DBInstanceClass.md) and [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 
+ The user running the `OEM_AGENT` on the Amazon RDS host doesn't have operating system access to the alert log\. Thus, you can't collect metrics for `DB Alert Log` and `DB Alert Log Error Status` in OEM\.

## Option settings for Management Agent<a name="Oracle.Options.OEMAgent.Options"></a>

Amazon RDS supports the following settings for the Management Agent option\.


****  

| Option setting | Required | Valid values | Description | 
| --- | --- | --- | --- | 
| **Version** \(`AGENT_VERSION`\) | Yes |  `13.5.0.0.v1` `13.4.0.9.v1` `13.3.0.0.v2` `13.3.0.0.v1` `13.2.0.0.v3` `13.2.0.0.v2` `13.2.0.0.v1` `13.1.0.0.v1` `12.1.0.5.v1` `12.1.0.4.v1`  |  The version of the Management Agent software\.  The AWS CLI option name is `OptionVersion`\.  In the AWS GovCloud \(US\) Regions, 12\.1 and 13\.1 versions aren't available\.   | 
| **Port** \(`AGENT_PORT`\) | Yes | An integer value |  The port on the DB instance that listens for the OMS host\. The default is 3872\. Your OMS host must belong to a security group that has access to this port\.  The AWS CLI option name is `Port`\.  | 
| **Security Groups** | Yes | Existing security groups |  A security group that has access to **Port**\. Your OMS host must belong to this security group\.  The AWS CLI option name is `VpcSecurityGroupMemberships` or `DBSecurityGroupMemberships`\.  | 
| **OMS\_HOST** | Yes |  A string value, for example *my\.example\.oms*   |  The publicly accessible host name or IP address of the OMS\.  The AWS CLI option name is `OMS_HOST`\.  | 
| **OMS\_PORT** | Yes | An integer value |  The HTTPS upload port on the OMS Host that listens for the Management Agent\.  To determine the HTTPS upload port, connect to the OMS host, and run the following command \(which requires the `SYSMAN` password\): emctl status oms \-details  The AWS CLI option name is `OMS_PORT`\.  | 
| **AGENT\_REGISTRATION\_PASSWORD** | Yes | A string value |  The password that the Management Agent uses to authenticate itself with the OMS\. We recommend that you create a persistent password in your OMS before enabling the `OEM_AGENT` option\. With a persistent password you can share a single Management Agent option group among multiple Amazon RDS databases\.  The AWS CLI option name is `AGENT_REGISTRATION_PASSWORD`\.  | 
| **ALLOW\_TLS\_ONLY** | No | `true`, `false` \(default\) |  A value that configures the OEM Agent to support only the `TLSv1` protocol while the agent listens as a server\. This setting is only supported for 12\.1 agent versions\. Later agent versions only support Transport Layer Security \(TLS\) by default\.   | 
| **MINIMUM\_TLS\_VERSION** | No | `TLSv1` \(default\), `TLSv1.2` |  A value that specifies the minimum TLS version supported by the OEM Agent while the agent listens as a server\. This setting is only supported for agent versions 13\.1\.0\.0\.v1 and higher\. Earlier agent versions only support the `TLSv1` setting\.   | 
| **TLS\_CIPHER\_SUITE** | No |  `TLS_RSA_WITH_AES_128_CBC_SHA` \(Default supported by all agent versions\) `TLS_RSA_WITH_AES_128_CBC_SHA256` \(Requires version 13\.1\.0\.0\.v1 or above\) `TLS_RSA_WITH_AES_256_CBC_SHA` \(Requires version 13\.2\.0\.0\.v3 or above\) `TLS_RSA_WITH_AES_256_CBC_SHA256` \(Requires version 13\.2\.0\.0\.v3 or above\)  |  A value that specifies the TLS cipher suite used by the OEM Agent while the agent listens as a server\.   | 

## Adding the Management Agent option<a name="Oracle.Options.OEMAgent.Add"></a>

The general process for adding the Management Agent option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

If you encounter errors, check [My Oracle Support](https://support.oracle.com/) documents for information about resolving specific problems\.

After you add the Management Agent option, you don't need to restart your DB instance\. As soon as the option group is active, the OEM Agent is active\. 

If your OMS host is using an untrusted third\-party certificate, Amazon RDS returns the following error\.

```
You successfully installed the OEM_AGENT option. Your OMS host is using an untrusted third party certificate. 
Configure your OMS host with the trusted certificates from your third party.
```

If this error is returned, the Management Agent option isn't enabled until the problem is corrected\. For information about correcting the problem, see the My Oracle Support document [2202569\.1](https://support.oracle.com/epmos/faces/DocContentDisplay?id=2202569.1)\.

### Console<a name="Oracle.Options.OEMAgent.Add.Console"></a>

**To add the Management Agent option to a DB instance**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine** choose the oracle edition for your DB instance\. 

   1. For **Major engine version** choose the version of your DB instance\. 

   For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **OEM\_AGENT** option to the option group, and configure the option settings\. For more information about adding options, see [Adding an option to an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. For more information about each setting, see [Option settings for Management Agent](#Oracle.Options.OEMAgent.Options)\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

### AWS CLI<a name="Oracle.Options.OEMAgent.Add.CLI"></a>

The following example uses the AWS CLI [add\-option\-to\-option\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/add-option-to-option-group.html) command to add the `OEM_AGENT` option to an option group called `myoptiongroup`\. 

For Linux, macOS, or Unix:

```
aws rds add-option-to-option-group \
    --option-group-name "myoptiongroup" \
    --options OptionName=OEM_AGENT,OptionVersion=13.1.0.0.v1,Port=3872,VpcSecurityGroupMemberships=sg-1234567890,OptionSettings=[{Name=OMS_HOST,Value=my.example.oms},{Name=OMS_PORT,Value=4903},{Name=AGENT_REGISTRATION_PASSWORD,Value=password}] \
    --apply-immediately
```

For Windows:

```
aws rds add-option-to-option-group ^
    --option-group-name "myoptiongroup" ^
    --options OptionName=OEM_AGENT,OptionVersion=13.1.0.0.v1,Port=3872,VpcSecurityGroupMemberships=sg-1234567890,OptionSettings=[{Name=OMS_HOST,Value=my.example.oms},{Name=OMS_PORT,Value=4903},{Name=AGENT_REGISTRATION_PASSWORD,Value=password}] ^
    --apply-immediately
```

## Using the Management Agent<a name="Oracle.Options.OEMAgent.Using"></a>

After you enable the Management Agent option, take the following steps to begin using it\. 

**To use the Management Agent**

1. Unlock and reset the DBSNMP account credential\. Do this by running the following code on your target database on your DB instance and using your master user account\. 

   ```
   1. ALTER USER dbsnmp IDENTIFIED BY new_password ACCOUNT UNLOCK;
   ```

1. Add your targets to the OMS console manually: 

   1. In your OMS console, choose **Setup**, **Add Target**, **Add Targets Manually**\. 

   1. Choose **Add Targets Declaratively by Specifying Target Monitoring Properties**\.

   1. For **Target Type**, choose **Database Instance**\.

   1. For **Monitoring Agent**, choose the agent with the identifier that is the same as your RDS DB instance identifier\. 

   1. Choose **Add Manually**\.

   1. Enter the endpoint for the Amazon RDS DB instance, or choose it from the host name list\. Make sure that the specified host name matches the endpoint of the Amazon RDS DB instance\.

      For information about finding the endpoint for your Amazon RDS DB instance, see [Finding the endpoint of your Oracle DB instance](USER_ConnectToOracleInstance.md#USER_Endpoint)\.

   1. Specify the following database properties: 
      + For **Target name**, enter a name\. 
      + For **Database system name**, enter a name\. 
      + For **Monitor username**, enter **dbsnmp**\. 
      + For **Monitor password**, enter the password from step 1\. 
      + For **Role**, enter **normal**\. 
      + For **Oracle home path**, enter **/oracle**\. 
      + For **Listener Machine name**, the agent identifier already appears\. 
      + For **Port**, enter the database port\. The RDS default port is 1521\. 
      + For **Database name**, enter the name of your database\. 

   1. Choose **Test Connection**\. 

   1. Choose **Next**\. The target database appears in your list of monitored resources\. 

## Modifying Management Agent settings<a name="Oracle.Options.OEMAgent.ModifySettings"></a>

After you enable the Management Agent, you can modify settings for the option\. For more information about how to modify option settings, see [Modifying an option setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\. For more information about each setting, see [Option settings for Management Agent](#Oracle.Options.OEMAgent.Options)\. 

## Performing database tasks with the Management Agent<a name="Oracle.Options.OEMAgent.DBTasks"></a>

You can use Amazon RDS procedures to run certain EMCTL commands on the Management Agent\. By running these procedures, you can do the tasks listed following\.

**Note**  
Tasks are executed asynchronously\.

**Topics**
+ [Getting the status of the Management Agent](#Oracle.Options.OEMAgent.DBTasks.GetAgentStatus)
+ [Restarting the Management Agent](#Oracle.Options.OEMAgent.DBTasks.RestartAgent)
+ [Listing the targets monitored by the Management Agent](#Oracle.Options.OEMAgent.DBTasks.ListTargets)
+ [Listing the collection threads monitored by the Management Agent](#Oracle.Options.OEMAgent.DBTasks.ListCollectionThreads)
+ [Clearing the Management Agent state](#Oracle.Options.OEMAgent.DBTasks.ClearState)
+ [Making the Management Agent upload its OMS](#Oracle.Options.OEMAgent.DBTasks.ForceUploadOMS)
+ [Pinging the OMS](#Oracle.Options.OEMAgent.DBTasks.PingOMS)
+ [Viewing the status of an ongoing task](#Oracle.Options.OEMAgent.DBTasks.ViewTaskStatus)

### Getting the status of the Management Agent<a name="Oracle.Options.OEMAgent.DBTasks.GetAgentStatus"></a>

To get the status of the Management Agent, run the Amazon RDS procedure `rdsadmin.rdsadmin_oem_agent_tasks.get_status_oem_agent`\. This procedure is equivalent to the `emctl status agent` command\.

The following procedure creates a task to get the Management Agent's status and returns the ID of the task\.

```
SELECT rdsadmin.rdsadmin_oem_agent_tasks.get_status_oem_agent() as TASK_ID from DUAL;            
```

To view the result by displaying the task's output file, see [Viewing the status of an ongoing task](#Oracle.Options.OEMAgent.DBTasks.ViewTaskStatus)\.

### Restarting the Management Agent<a name="Oracle.Options.OEMAgent.DBTasks.RestartAgent"></a>

To restart the Management Agent, run the Amazon RDS procedure `rdsadmin.rdsadmin_oem_agent_tasks.get_status_oem_agent`\. This procedure is equivalent to running the `emctl stop agent` and `emctl start agent` commands\.

The following procedure creates a task to restart the Management Agent and returns the ID of the task\.

```
SELECT rdsadmin.rdsadmin_oem_agent_tasks.restart_oem_agent() as TASK_ID from DUAL;    
```

To view the result by displaying the task's output file, see [Viewing the status of an ongoing task](#Oracle.Options.OEMAgent.DBTasks.ViewTaskStatus)\.

### Listing the targets monitored by the Management Agent<a name="Oracle.Options.OEMAgent.DBTasks.ListTargets"></a>

To list the targets monitored by the Management Agent, run the Amazon RDS procedure `rdsadmin.rdsadmin_oem_agent_tasks.list_targets_oem_agent`\. This procedure is equivalent to running the `emctl config agent listtargets` command\.

The following procedure creates a task to list the targets monitored by the Management Agent and returns the ID of the task\.

```
SELECT rdsadmin.rdsadmin_oem_agent_tasks.list_targets_oem_agent() as TASK_ID from DUAL;
```

To view the result by displaying the task's output file, see [Viewing the status of an ongoing task](#Oracle.Options.OEMAgent.DBTasks.ViewTaskStatus)\.

### Listing the collection threads monitored by the Management Agent<a name="Oracle.Options.OEMAgent.DBTasks.ListCollectionThreads"></a>

To list of all the running, ready, and scheduled collection threads monitored by the Management Agent, run the Amazon RDS procedure `rdsadmin.rdsadmin_oem_agent_tasks.list_clxn_threads_oem_agent`\. This procedure is equivalent to the `emctl status agent scheduler` command\.

The following procedure creates a task to list the collection threads and returns the ID of the task\.

```
SELECT rdsadmin.rdsadmin_oem_agent_tasks.list_clxn_threads_oem_agent() as TASK_ID from DUAL;          
```

To view the result by displaying the task's output file, see [Viewing the status of an ongoing task](#Oracle.Options.OEMAgent.DBTasks.ViewTaskStatus)\.

### Clearing the Management Agent state<a name="Oracle.Options.OEMAgent.DBTasks.ClearState"></a>

To clear the Management Agent's state, run the Amazon RDS procedure `rdsadmin.rdsadmin_oem_agent_tasks.clearstate_oem_agent`\. This procedure is equivalent to running the `emctl clearstate agent` command\.

The following procedure creates a task that clears the Management Agent's state and returns the ID of the task\.

```
SELECT rdsadmin.rdsadmin_oem_agent_tasks.clearstate_oem_agent() as TASK_ID from DUAL;
```

To view the result by displaying the task's output file, see [Viewing the status of an ongoing task](#Oracle.Options.OEMAgent.DBTasks.ViewTaskStatus)\.

### Making the Management Agent upload its OMS<a name="Oracle.Options.OEMAgent.DBTasks.ForceUploadOMS"></a>

To make the Management Agent upload the Oracle Management Server \(OMS\) associated with it, run the Amazon RDS procedure `rdsadmin.rdsadmin_oem_agent_tasks.upload_oem_agent`\. This procedure is equivalent to running the `emclt upload agent` command\.

The following procedure creates a task that makes the Management Agent upload its associated OMS and return the ID of the task\.

```
SELECT rdsadmin.rdsadmin_oem_agent_tasks.upload_oem_agent() as TASK_ID from DUAL;
```

To view the result by displaying the task's output file, see [Viewing the status of an ongoing task](#Oracle.Options.OEMAgent.DBTasks.ViewTaskStatus)\.

### Pinging the OMS<a name="Oracle.Options.OEMAgent.DBTasks.PingOMS"></a>

To ping the Management Agent's OMS, run the Amazon RDS procedure `rdsadmin.rdsadmin_oem_agent_tasks.ping_oms_oem_agent`\. This procedure is equivalent to running the `emctl pingOMS` command\.

The following procedure creates a task that pings the Management Agent's OMS and returns the ID of the task\.

```
SELECT rdsadmin.rdsadmin_oem_agent_tasks.ping_oms_oem_agent() as TASK_ID from DUAL;
```

To view the result by displaying the task's output file, see [Viewing the status of an ongoing task](#Oracle.Options.OEMAgent.DBTasks.ViewTaskStatus)\.

### Viewing the status of an ongoing task<a name="Oracle.Options.OEMAgent.DBTasks.ViewTaskStatus"></a>

You can view the status of an ongoing task in a bdump file\. The bdump files are located in the `/rdsdbdata/log/trace` directory\. Each bdump file name is in the following format\.

```
dbtask-task-id.log 
```

When you want to monitor a task, replace `task-id` with the ID of the task that you want to monitor\.

To view the contents of bdump files, run the Amazon RDS procedure `rdsadmin.rds_file_util.read_text_file`\. The following query returns the contents of the `dbtask-1546988886389-2444.log` bdump file\. 

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-1546988886389-2444.log'));
```

For more information about the Amazon RDS procedure `rdsadmin.rds_file_util.read_text_file`, see [Reading files in a DB instance directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles)\.

## Removing the Management Agent option<a name="Oracle.Options.OEMAgent.Remove"></a>

You can remove the OEM Agent from a DB instance\. After you remove the OEM Agent, you don't need to restart your DB instance\. 

To remove the OEM Agent from a DB instance, do one of the following: 
+ Remove the OEM Agent option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an option from an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ Modify the DB instance and specify a different option group that doesn't include the OEM Agent option\. This change affects a single DB instance\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 