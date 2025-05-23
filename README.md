# Qualys integration with Atlassian Jira

## Highlights
- Common visibility between IT and Security Teams  
- Bring the vulnerability context in Jira tickets
	- Helping you make better decisions
- Close vulnerabilities faster with Automation
	- Auto ticket creation, update and closure of the tickets based on the scan

## Features
- Support for Jira on Cloud and On-premises
- Seamlessly automate end-to-end SecOps ticketing
- Ability to create tickets based on specific severity thresholds, asset groups, asset tags, QIDs or QDS
- Turnkey solution with bare minimum configuration
- Supports two ticketing schemes
	- One ticket per detection
	- Vulnerability ticket linked to Vulnerable Host ticket
- Reporting using Jira’s native filters and dashboards capabilities

Qualys has extensive data of hosts and vulnerabilities detected on them, and this integration helps you bring it to Atlassian Jira to create actionable SecOps tickets. This pull-based integration downloads the detection data and automatically creates or resolves tickets based on status of each detection. Qualys Integration with Atlassian Jira allows customers to create customized rules for creating actionable tickets in desired projects assigned to the desired owner. This also enables users to build Jira filters, reporting dashboards natively in Jira using JQL capabilities to simplify the operationalization.

 - To know more about this integration, please contact your Technical Account Manager.  

 - To know more on how to setup and use this integration, please read the [user guide](https://www.qualys.com/docs/qualys-jira-connector-user-guide.pdf).

# Upgrade

Note: These upgrade instructions are provided in incremental manner. If you are using really old version of Jira connector, to make sure the connnector runs smoothly please check the upgrade instruction for other versions in [guide](https://docs.qualys.com/en/integration/jira_connector/#t=upgrade_connector_app%2Fupgrade_the_app.htm&rhsearch=upgrade) as well. This will prevent potential upgrade issues due to missing changes in config/templates.	

## Intructions to upgrade the App to v1.3.1.1

For v1.3.1.1, we have introduced additional parameters for configuring connection retries and timeouts to the Jira connector in case of 429 error scenarios.
The newly introduced parameters in config.json are,
 1. numberOfRetriesForJiraApiCall
 2. connectionRequestTimeOut
 3. frequencyToProcessOutputFiles
    
To Upgrade to v1.3.1.1 from v1.3.1 follow the instructions listed below, 
 1. Stop your containers
 2. Update the latest images in docker-compose.yml. You can get the latest docker-compose.yml [here](https://github.com/QIntegration/Qualys-Jira-Connector/blob/main/docker-compose.yml)
 3. The newly introduced parameters described above are enabled with their default values if not manually updated in config.json. See [Configuration](https://docs.mp02.eng.sjc01.qualys.com/en/integration/jira_connector/#t=get_started%2Fconfiguration.htm) section in user guide to learn more about the parameters and their default values. To modify the values, add these parameters to the config.json file and set your preferred value.
 4. Restart your containers.

## Intructions to upgrade the App to v1.3.1
 
For v1.3.1, we have introduced 
- 'priorityMapping' parameter to all the ticketing schemes to map the issues created in Jira with appropriate priority based on their risk scores or severity level. 
- For WAS module we are adding 'result' data as attachment in Jira issues (Please check Pre-requisites section in the user guide for this change)
 
To Upgrade to v1.3.1, 
1. Stop your containers
2. Update the latest images in docker-compose.yml. 
 
If you don't want to opt for 'priorityMapping' then, 
3. you can restart your containers and Jira Connector will assign default Jira instance priority to all the issues just as earlier.
 
If you want to opt for 'priorityMapping' then, follow the instructions provided below to update template and then restart the containers.
 
For 'priorityMapping' to be effective in Jira issues please update the 'metadata' section of the ticketing template as per your ticketing scheme and profile with priorityMapping json. Please refer below to priorityMapping for each ticketing scheme.
Once template is updated manually, make sure its a valid json, you can check that using online json validator
 
Further, if you don't want to make any manual changes and don't have any customization added in them, then you can simply delete the template file and restart the connector it should bring the template with latest changes.
 
priorityMapping per ticketing scheme -
1. VMDR - Host_Vuln_Linking_Ticket_Scheme.json (Ticketing scheme 1)
   ```
      "priorityMapping": {
        "parent":{
          "matchWith": "${TRURISK_SCORE}",
          "criteria": {
            "Highest": "850-1000",
            "High":"700-849",
            "Medium": "500-699",
            "Low":"0-499"
          }
        },
        "relatedTicket":{
          "matchWith": "${QDS.content}",
          "criteria": {
            "Highest":"90-100",
            "High": "70-89",
            "Medium": "40-69",
            "Low": "1-39"
          }
        }
      }
   ```

3. VMDR - Per_Detection_Separate_Ticket_Scheme.json (Ticketing scheme 2)
 ```
      "priorityMapping": {
        "matchWith": "${QDS.content}",
        "criteria": {
          "Highest": "90-100",
          "High": "70-89",
          "Medium": "40-69",
          "Low": "1-39"
        }
      }
```
3. WAS - WAS_Finding_Vuln_Linking_Ticket_Scheme.json (Ticketing scheme 3)
 ```
      "priorityMapping": {
        "parent":{
          "matchWith": "Jira default",
          "criteria": {}
        },
        "relatedTicket":{
          "matchWith": "${SEVERITY}",
          "criteria": {
            "Highest": 5,
            "High": 4,
            "Medium": 3,
            "Low": 2,
            "Lowest": 1
          }
        }
      }
```
4. WAS - Per_Web_App_Finding_Ticket_Scheme.json (Ticketing scheme 4)

 ```
      "priorityMapping": {
        "matchWith": "${SEVERITY}",
        "criteria": {
          "Highest": 5,
          "High": 4,
          "Medium": 3,
          "Low": 2,
          "Lowest": 1
        }
      }
```
5. CS - CS_Container_Link_Ticket_Scheme.json (Ticketing scheme 5), CS_Container_Sub_Ticket_Scheme.json (Ticketing scheme 6), CS_Image_Link_Ticket_Scheme.json (Ticketing scheme 7), CS_Image_Sub_Ticket_Scheme.json (Ticketing scheme 8)
```
   "priorityMapping": {
                "parent":{
                    "matchWith": "Jira default",
                    "criteria": {}
                },
                "relatedTicket":{
                    "matchWith": "${vuln.severity}",
                    "criteria": {
                        "Highest": 5,
                        "High": 4,
                        "Medium": 3,
                        "Low": 2,
                        "Lowest": 1
                    }
                }
            }
```

## Intructions to upgrade the App to v1.3.2
Steps to Upgrade to v1.3.2,
1. Stop your containers
2. Update the latest images in docker-compose.yml. You can get the latest docker-compose.yml [here](https://github.com/QIntegration/Qualys-Jira-Connector/blob/main/docker-compose.yml).
3. Update the Ticketing template file with 1.3.2 Ticketing template file. You can get the latest Ticketing files from.
[Ticketing templates](https://github.com/QIntegration/Qualys-Jira-Connector/tree/main/1.3.2%20Ticketing%20templates)

In v1.3.2, we have added support to handle mandatory custom fields of type - label & Select List (Single & multi choice), Number, Component system field, Paragraph, Read Only, Short text (plain text only). You can set mandatory field in any Ticketing Template.

Example:
```
"Component":
	{
        	"value": "test",
        	"customFieldId": "customfield_10036"
	}
```
4. In config.json set the below parameters,
   
   i. logLevel -
   In “Profile” section set log Level according to what information should be logged for each profile.
   Example: "logLevel" : "INFO"

   ii. resposeTimeout-
   In “global” section replace “connectionRequestTimeOut” parameter with “resposeTimeout”. You can set the value upto 5 minutes.

   iii. numberOfConcurrentTask -
   In “moduleConfiguration” section set concurrent task for each profile to run jira client in multithreading mode. You can set value upto 5 concurrent tasks for each profile.
   Example:
   ```
   "moduleConfiguration":{
   	"HD":
   		{
   		"numberOfConcurrentTask": 2
                },
   	"WAS":
   		{
                "numberOfConcurrentTask": 2
                },
   	"CS-Container":
   		{
                "numberOfConcurrentTask": 2
                },
   	"CS-Image":
   		{
         	"numberOfConcurrentTask": 2
                }
   },
   ```
   iv. createTicketsOnlyForRunningContainer - 
   This parameter is only applicable for CS-Image profile. In “CS-Image” profile section, set value to true to create image ticket for running container only.

5. Restart your containers.

## Intructions to upgrade the App to v1.3.2.1
Steps to upgrade to v1.3.2.1,
1. Stop your Containers
2. Delete **qualys_jira_connector.rdb** file from db directory.

   Navigation - docker Volume > _data > db > **Delete qualys_jira_connector.rdb**
4. Delete **knowledgebase.txt** file from checkpoint directory.

   Navigation - docker Volume > _data > checkpoint > Delete **knowledgebase.txt**
6. Update the latest images in docker-compose.yml. You can get the latest YAML file from [GitHub](https://github.com/QIntegration/Qualys-Jira-Connector).
7. Restart your Containers.
   
### Why Do we Delete the Redis DB?
When upgrading to a new Redis version, the existing database file (qualys_jira_connector.rdb) must be deleted. When you restart the Jira Connector after deletion, it automatically creates a new database file in the db directory. This process ensures that your connector starts with a fresh, compatible database aligned with the new Redis version.

### Why Do we Delete the Checkpoint?
After deleting the database file, we also need to manage the checkpoint to maintain data consistency. 
Here's why:
1. The checkpoint file (knowledgebase.txt) stores the last processed data point.
2. Deleting this file allows the Jira Connector to reset its data collection process.
3. When the Jira Connector restarts, a new knowledgebase.txt is created with a new checkpoint date.
4. The connector starts pulling data from the new checkpoint date.

**Important** - The Jira Connector comes with pre-bundled knowledgebase data from before the checkpoint date. This means you won't lose historical data during this process.

## Intructions to upgrade the App to v1.3.2.2
To upgrade to v1.3.2.2
1. Stop your Containers
2. Update the latest images in docker-compose.yml. You can get the latest YAML file [here](https://github.com/QIntegration/Qualys-Jira-Connector/blob/main/docker-compose.yml).
3. Restart your Containers.

**Note**: This release contains updates to the Jira Client Image Only.
