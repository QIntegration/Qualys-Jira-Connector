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

## Upgrade
- To upgrade the existing Jira connector to the latest version, please read and follow the instructions given in the "Frequently Asked Questions" section of the [user guide](https://www.qualys.com/docs/qualys-jira-connector-user-guide.pdf).

- Upgrade the App to v1.3.1
 
For v1.3.1, we have introduced 
- 'priorityMapping' parameter to all the ticketing schemes to map the issues created in Jira with appropriate priority based on their risk scores or severity level. 
- For WAS module we are adding 'result' data as attachment in Jira issues (Please check Pre-requisites for this change)
 
To Upgrade to v1.3.1, 
1. Stop your containers
2. Update the latest images in docker-compose.yml. 
 
If you don't want to opt for 'priorityMapping' then, 
3. you can restart your containers and Jira Connector will assign default Jira instance priority to all the issues just as earlier.
 
If you don't want to opt for 'priorityMapping' then, follow the instructions provided below to update template and then restart the containers
 
For 'priorityMapping' to be effective in Jira issues please update the 'metadata' section of the ticketing template as per your ticketing scheme and profile with priorityMapping json. Please refer below to priorityMapping for each ticketing scheme.
Once template is updated manually, make sure its a valid json, you can check that using online json validator
 
Further, if you don't want to make any manual changes and don't have any customization added in them, then you can simply delete the template file and restart the connector it should bring the template with latest changes.
 
priorityMapping per ticketing scheme -
1. VMDR - Host_Vuln_Linking_Ticket_Scheme.json (Ticketing scheme 1)
 
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

2. VMDR - Per_Detection_Separate_Ticket_Scheme.json (Ticketing scheme 2)
 
      "priorityMapping": {
        "matchWith": "${QDS.content}",
        "criteria": {
          "Highest": "90-100",
          "High": "70-89",
          "Medium": "40-69",
          "Low": "1-39"
        }
      }
3. WAS - WAS_Finding_Vuln_Linking_Ticket_Scheme.json (Ticketing scheme 3)
 
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
3. WAS - Per_Web_App_Finding_Ticket_Scheme.json (Ticketing scheme 4)
 
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
4. CS - CS_Container_Link_Ticket_Scheme.json (Ticketing scheme 5), CS_Container_Sub_Ticket_Scheme.json (Ticketing scheme 6), CS_Image_Link_Ticket_Scheme.json (Ticketing scheme 7), CS_Image_Sub_Ticket_Scheme.json (Ticketing scheme 8)

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
           
Note: These upgrade instructions are provided in incremental manner. If you are using really old version of Jira connector, to make sure the connnector runs smoothly please check the upgrade instruction for other versions in guide as well. This will prevent potential upgrade issues due to missing changes in config/templates. https://docs.qualys.com/en/integration/jira_connector/#t=upgrade_connector_app%2Fupgrade_the_app.htm&rhsearch=upgrade		
