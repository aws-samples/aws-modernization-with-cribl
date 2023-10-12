---
title: Security Lake Integration - Collect Custom Logs
chapter: true
weight: 2
---

## Narrative

In this workshop, the user is tasked with collecting data from a known source and using Cribl Stream to route it to the Amazon Security Lake. We will use a data generator from Cribl to simulate Palo Alto Logs flowing through Cribl Stream. Next, we will setup the Amazon S3 destination to mimick an Amazon Security Lake destination. Finally, we will leverage Cribl Search to collect and send data out of Amazon Security Lake and sent it to our SIEM, or in this case an S3 bucket configured to look like a SIEM. We will validate all of these steps by running Cribl Search on the SIEM S3 bucket to show the events landing with some enriched events.

## Workshop workflow
![Partner Logo](/images/cribl23.png)


## Setup Cribl.Cloud Account

1. Go to [Cribl.Cloud](https://cribl.cloud)
2. Create an account or sign in using Google account.
3. Create *Region* in US-East. Then click *+ Create Organization*
   ![Partner Logo](/images/cribl22.png)
4. Navigate to Cribl.Cloud-->*Network Settings*-->*Trust and Provision Infrastructure* (for new account)
   ![Partner Logo](/images/cribl24.png)
5. Click on the *Provisioned Now* button and wait. It will take 2-4 minutes for Cribl.Cloud to create objects.
6. Copy the Worker ARN.

![Cribl Trust](/images/cribl58.png)
## Deploy Workshop Assets
This Cloudformation template will create an IAM role in your AWS account that will trust the Cribl Cloud service and setup permissions to your S3 buckets.
Two S3 buckets will also be created for this lab to simulate an Amazon Security Lake and a SIEM. 

1. Log into your AWS Console, then click  [*here*](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/template?stackName=CriblCloudTrustSearch&templateURL=https://cribl-rapid-adoption.s3.amazonaws.com/templates/DevDayWorkshop.yaml
   ) to deploy the cloudformation template. This template will create IAM Policy, Role, and S3 buckets.
2. Click *Next* and then enter the (account number) that is part of copied arn in previous step and paste it  in the account placeholder for both *CriblSearchCloudTrust* and *CriblStreamCloudTrust*, update the *External ID* to 123456.
   ![Partner Logo](/images/cribl48.png) </br>

   ![Partner Logo](/images/cribl148-1.png)
3. Click *Next* and then click the checkbox at bottom of screen and then click *Submit* .
4. Once the CloudFormation completes, click on the *Resources* tab to view the name of S3 buckets and the CriblTrustCloud RoleName.

![Assets Added](/images/cribl56.png)

- _s3DefaultSecurityLake_ - This is the S3 bucket that will simulate the Amazon Security Lake destination.
- _s3DefaultSIEM_ - This is the S3 bucket that will mimic a SIEM destination.
- _CriblTrustCloud_ - the IAM Role which will be used to authenticate Cribl Stream to send data and search it.

## Setup OCSF Parquet Schema in Cribl Stream

Please review all the sections in the [Cribl Amazon Security Lake Docs](https://docs.cribl.io/stream/usecase-security-lake/ ) for Cribl Integration workflow. 

1. Navigate to the Cribl [GitHub repo](https://github.com/criblpacks/cribl-ocsf-parquet-schemas/ ) that is hosting the OCSF schemas
2. Select the appropriate *OCSF version* from the list. In our case, we will pick the latest version that doesn't have any suffix.
3. Select *Parquet Schema*
4. Select *OCSF class* (OCSF 4001 Network Activity 1.0.0.json)

![Cribl Schemas](/images/cribl57.png)

5. Copy the Raw JSON file
6. Go to [Cribl.Cloud](https://cribl.cloud)
7. Click on *Cribl Stream*
8. Select a Worker Group. Go to *Processing* --> *Knowledge* --> *Parquet Schema*
9. Click on *Add Parquet Schema*

    ![Partner Logo](/images/cribl26.png)
10. Enter *ID*: OCSF_4001_Network_Activity </br>
    Enter *Description*: OCSF 4001 Network Activity 1.0.0.json </br>
    Paste the Parquet Schema (from GitHub) </br>
    Click *Save*</br>
    ![Partner Logo](/images/cribl27.png)
11. Commit and Deploy Changes

## Install OCSF Post Processor

1. In your Cribl Cloud Instance, navigate to *Cribl Stream* --> *Processing* --> *Packs* --> *Add Pack* --> *Add from Dispensary*

![Add from Dispensary](/images/cribl49.png)

2. Search for *OCSF Post Processor*

![Post Processor Pack](/images/cribl50.png)

3. Click on the Pack and then Click on *Add Pack*

![Add Pack](/images/cribl51.png)

4. Once complete, it will show the pack has been installed.

![Pack Installed](/images/cribl52.png) 

![Pack Installed](/images/cribl53.png) 

## Setup Amazon Security Lake Destination

1. Go to *Cribl.Cloud* --> *Cribl Stream* --> *Data* --> *Destinations* -->*Amazon Security Lake*--> Click *Add Destination*
   ![Partner Logo](/images/cribl29.png)
2. Enter the following values: </br>
   *Output ID*: **s3dest**</br>
   *S3 bucket name* (**s3DefaultSecurityLake** value from Resources tab of CloudFormation)</br>
   *Region* (US-East)</br>
   *Account ID*(from the Account that contain S3 bucket created)</br>
   *Custom source name*: **palo-alto-traffic**</br>
   ![Partner Logo](/images/cribl30.png)
3. Assume Role Tab, copy the arn (**RoleArn** in output tab	) and external ID.
   ![Partner Logo](/images/cribl31.png)
4. Parquet settings tab: Select the parquet schema from the drop down and leave every other value as default
   ![Partner Logo](/images/cribl32.png)
5. Commit and Deploy Changes

## Setup S3 bucket for SIEM 
1. Go to *Cribl.Cloud* --> *Cribl Stream* --> *Data* --> *Destinations* -->*S3*--> Click *Add Destination* </br>
2. Enter the following values:</br>
Output ID: **s3SIEMdest** </br>
S3 bucket name* (**s3DefaultSecurityLake** value from Resources tab of CloudFormation)</br>
Region (US-East) </br>
Account ID(from the Account that contain S3 bucket created) </br>

3. Assume Role Tab, copy the arn (RoleArn in **output** tab of Cloudformation ) and external ID
   ![Partner Logo](/images/cribl31-1.png)
   ![Partner Logo](/images/cribl31-2.png)
4. Commit and Deploy Changes

## Setup Palo Alto Data Generator
1. *Cribl.Cloud* instance and navigate to the Datagen screen: *Cribl.Cloud* --> *Cribl Stream* -->  *Data* --> *Sources* scroll down and click on *Datagen*

![Datagen](/images/cribl54.png)

2. Click on *Add Source*
 - Name the Input ID : *palo-alto-datagen*
 - Select the Datagenerator File *palo_alto_traffic.log*
 - ![Datagen](/images/cribl54-2.png)
 - Add Fields: sourcetype = 'pan:traffic'
 - ![Datagen](/images/cribl54-1.png)
 - Click *Save*


## Set Up Routes to Amazon Security Lake

1. Go to *Cribl.Cloud* --> *Cribl Stream* --> *Routing* --> *Data Routes* --> Click *Add Routes*
2. Enter the following values: </br>
   Route Name: PaloAltoLogs </br>
   Filter: __inputId=='datagen:palo-alto-datagen' </br>
   Pipeline: Select from dropdown _PACK AWS_OCSF_Mapping (OCSF Post Processor)_ </br>
   Enable Expression: No </br>
   Output: Select from dropdown _security_lake:s3dest_</br>
   Final: Yes </br>

    ![Partner Logo](/images/cribl34-1.png)

3. Drag and drop the pipeline you created so it is before the **default** pipeline


## Set Up Pipeline for SIEM

1. Download the database file from this [GeoIP Link](https://cribl-rapid-adoption.s3.amazonaws.com/thirdparty/GeoLite2-City.mmdb). 
2. Go to *Cribl.Cloud* --> *Cribl Stream* --> *Processing* --> *Knowledge *
3. Click "Add Lookup File" , then upload the "GeoLite2-City.mmdb"
   ![Partner Logo](/images/cribl46.png)
6. Go to *Cribl.Cloud* --> *Cribl Stream* --> *Processing* --> *Pipeline* --> *Add Pipeline*
   ID: send_search_results_to_SIEM
   ![Partner Logo](/images/cribl35-1.png)
7. Click *Save*

9. Click *Add Function* --> *Standard* --> *GeoIP*
   ![Partner Logo](/images/cribl36-1.png)
10. Enter following data on the screen:  </br>
    Filter: True </br>
    GeoIP File: GeoLite2-City.mmdb </br>
    IP Field: dst_ip </br>
    Result Field: geoip </br>
    ![Partner Logo](/images/cribl37-1.png)
11. Click *Save*

## Set Up Route for SIEM

1. Go to *Cribl.Cloud* --> *Cribl Stream* --> *Routing* --> *Data Routes* --> Click *Add Routes*
2. Enter the following values: <br/>
   Route Name: RouteSearchtoSIEM  <br/>
   Filter: __inputId==‘cribl_http:in_cribl_http’  <br/>
   Pipeline: send_search_results_to_SIEM  <br/>
   Enable Expression: No  <br/>
   Output: s3SIEMdest  <br/>
   Final: Yes  <br/>

    ![Partner Logo](/images/cribl66-1.png)
3. Default pipeline should be at the end

    ![Partner Logo](/images/cribl66-2.png)
4. Click *Save*
5. Commit and deploy changes

## Route the Datagen to Amazon Security Lake



{{% notice warning %}}
The examples and sample code provided in this workshop are intended to be consumed as instructional content. These will help you understand how various AWS services can be architected to build a solution while demonstrating best practices along the way. These examples are not intended for use in production environments.
{{% /notice %}}