## Overview

This repository contains rules to be used from within a Red Hat Process Automation Manager project. The rules update the Target Capacity field for the respective Fleet object input.

## Prerequisites

An instance of [Red Hat Process Automation Manager 7.0](https://access.redhat.com/documentation/en-us/red_hat_process_automation_manager/7.0/) with both Business Central and Process Server deployed.

## Deploying

Import the project into Business Central using the 'Add Project' feature.

Build and Deploy the project. A container should start in the Process Server from which the rules can be accessed.

## Using the rules

Use the BRMS REST API to access the rules. The command below will insert a fact from the input file and fire all rules within the container.

You should expect a JSON response containing the modified fact. The input file is required and all fields should be initialized. The rules will only fire for Fleet Ids that are contained within the rules.

[More information on the BRMS REST API](https://access.redhat.com/documentation/en-us/red_hat_decision_manager/7.0/html/installing_and_configuring_red_hat_business_optimizer/optimizer-rest-api-for-execution-server-con)
```
curl -X POST -H 'X-KIE-ContentType: JSON' -H 'Content-type: application/json' -u '{{USERNAME}}:{{PASSWORD}}' --data @{{INPUTFILE}}.json http://{{HOSTNAME}}/services/rest/server/containers/instances/{{CONTAINER_ID}}
```
### Input File Format:
```
{
      "commands" : [ {
        "insert" : {
          "disconnected" : false,
          "out-identifier" : "fleetRules",
          "return-object" : true,
          "entry-point" : "DEFAULT",
          "object" : {
           "com.cloudbalancer.balancerrules.Fleet":{
                        "id": "sfr-e297b726-fa8b-490e-acdd-2b6c2420549a",
                        "name": "fleetD",
                        "currentCapacity": 1,
                        "availabilityZone": "eu-west-1a",
                        "instanceType": "m4.xlarge",
                        "cpuLoad": 30,
                        "memoryLoad": 30,
                        "networkLoad": 30,
                        "currentTime": 1500,
                        "filter": false,
                        "targetCapacity": 2,
                        "price": [ {
                                "type": "onDemand",
                                "price": 0.45
                        }
                        ]
           }

          }
        }

      }, {
        "fire-all-rules" : { }
      } ]
    }
```
## Updating the Rules

### To Update the Rules:
* From within the project in Business Central, access the rulesPAM.xls file
* Download the current version
* Update the decision table with your changes
* Upload the new version
* Increment the project version within the Project Settings menu
* Build and Deploy the project
* The new rules should now be available within the newly deployed container
* **IMPORTANT:** The new container id (used in the REST call from above) will contain the incremented project version number. If you wish to maintain the same container name, you must set it manually from the Deploy -> Execution Servers menu


