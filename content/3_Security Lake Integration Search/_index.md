---
title: Security Lake Integration - Search and Send
chapter: true
weight: 2
---

## Narrative

In this section, we will leverage Cribl Search to collect and send data out of Amazon Security Lake and sent it to our SIEM, or in this case an S3 bucket configured to look like a SIEM. We will validate all of these steps by running Cribl Search on the SIEM S3 bucket to show the events landing with some enriched events.

## Workshop workflow

![Partner Logo](/images/cribl23.png)

## Setup Cribl Search Dataset Providers
In this step we are going to create two Dataset Providers, one for each of our S3 buckets we have deployed. 

### Security Lake Dataset Providers for Search
Let's setup two Cribl Dataset
1. Go to *Cribl.Cloud* --> *Cribl Search* --> *Data* --> *Data Providers* --> *Create Provider*

![Cribl Dataset Providers](/images/cribl60.png)

2. Enter following data on the screen:<br/>
   *ID:* securitylake_Search <br/>
   *Description File:* securitylake_Search <br/>
   *Dataset Provider Type:* Amazon S3<br/>
   *Authentication Method:* AssumeRole <br/>
   *AssumeRole ARN:* Get this from your AWS Account (see your Cloudformation **Output** tab)
![Partner Logo](/images/cribl59.png)

3. Click *Save*

### SIEM Dataset Providers for Search

1. Go to *Cribl.Cloud* --> *Cribl Search* --> *Data* --> *Data Providers* --> *Create Provider*

![Cribl Dataset Providers](/images/cribl60.png)

2. Enter following data on the screen:<br/>
   *ID:* SIEM_Search <br/>
   *Description File:* SIEM_Search <br/>
   *Dataset Provider Type:* Amazon S3<br/>
   *Authentication Method:* AssumeRole
   *AssumeRole ARN:* Get this from your AWS Account (see your Cloudformation Resource tab)
![Partner Logo](/images/cribl39.png)

3. Click *Save*

## Setup Cribl Datasets for Security Lake
1. Go to *Cribl.Cloud* --> *Cribl Search* --> *Data* --> *DataSets* --> *Add Dataset*

![Partner Logo](/images/cribl61.png)

2. Enter following data on the screen: <br/>
   *ID:* securitylake_Search_Dataset <br/>
   *Description File:* securitylake_Search_Dataset<br/>
   *Dataset Provider:* securitylake_Search <br/>
   *Bucket path:* [bucketname]/ext/palo-alto-traffic/ <br/>
   *Path Filter:* True  <br/>
   *Auto-detect region:* Yes

   
![Partner Logo](/images/cribl40-1.png)

3. Click *Save*

## Setup Cribl Datasets for SIEM
1. Go to *Cribl.Cloud* --> *Cribl Search* --> *Data* --> *DataSets* --> *Add Dataset*

![Cribl Datasets](/images/cribl61.png)

2. Enter following data on the screen: <br/>
   *ID:* SIEM_Search <br/>
   *Description File:* Search_Dataset<br/>
   *Dataset Provider:* SIEM_Search <br/>
   *Bucket path:* [bucketname]/output/ <br/>
   *Path Filter:* True <br/>
   *Auto-detect region:* Yes

![Partner Logo](/images/cribl62.png)
3. Click *Save*

## Search Security Lake

1. From the Datasets screen, click on the *Search* button by the *securitylake_Search_Dataset*

* Start with a simple search 
`dataset="securitylake_Search_Dataset" | limit 1000`

![Partner Logo](/images/cribl63.png)
2. Let the search run, you should see results from your S3 bucket that is simulating Amazon Security Lake. **If you don't see any results, change your time span to *All Time*.

![Partner Logo](/images/cribl70.png)


3. From within the search, navigate to the *dst_endpoint.vlan_uid* and click on *untrusted* and *Add field in search*. 

![Cribl Datasets](/images/cribl65.png)

4. This will show only the results for that one destination IP.

5. Let's send these events to our SIEM by _Sending_ them using Cribl Search. Start with setting up your search: 

* Gradually add filtering components such as looking at the _untrusted_ destinations 
`dataset="securitylake_Search_Dataset" dst_endpoint.vlan_uid="untrusted" | limit 1000` 

![Partner Logo](/images/cribl69.png)

* Let's see what destination IP shows up: 

![Partner Logo](/images/cribl71.png)

* Finally, let's preview what we are going to send as results to our SIEM (S3 bucket)
`dataset="securitylake_Search_Dataset" dst_endpoint.vlan_uid="untrusted" |  project _time, host, dst_ip=dst_endpoint.ip, dst_port=dst_endpoint.port, dst_interface_uid=dst_endpoint.interface_uid`

* Now let us send our results to the SIEM (S3 bucket).
`dataset="securitylake_Search_Dataset" dst_endpoint.vlan_uid="untrusted" | project _time, host, dst_ip=dst_endpoint.ip, dst_port=dst_endpoint.port, dst_interface_uid=dst_endpoint.interface_uid | send`



## Search the SIEM

1. From the Datasets screen, click on the *Search* button by the *securitylake_Search_Dataset*

![Cribl Datasets](/images/cribl67.png)

2. You should now see the results from what we sent into the SIEM from Security Lake, only transmitting the fields we wanted while enriching it with the GeoIP information. 

![Cribl Datasets](/images/cribl68.png)

3. You're now done.