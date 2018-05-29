# Set up Splunk connector



>**Update:** A new version of the Splunk Connector (v.1.2.0) was released in October 2017. Changes include:

>*   Connector is now Java-based.
>*   You must now complete additional fields when creating a data input: Interval between fetch operations and Splunk username and password.
>*   Default limit now 150,000 records per call.
>*   Some minor changes in how you retrieve past security events.



The sample Splunk connector is a Splunk add-on that captures security events from the Akamai Security Events Collector, which exposes a RESTful API that lets the connector pull events in JSON format. The Splunk add-on converts security events data from JSON into CIM format. The Splunk instance then analyzes high volumes of data by indexing it.

![Splunk connector diagram](https://developer.akamai.com/tools/siem-integration/docs/Content/Resources/Images/splunk_diag.png)

> _**Full SIEM Setup:** There's more to SIEM setup than just installing this connector. You need to turn SIEM integration on, set up administrators, and provision the SIEM API.  [Learn more](https://developer.akamai.com/tools/siem-integration/index.html)_ 

## System Requirements

Akamai’s Splunk Connector requires Sun JDK 1.8 or later to be installed. Download the latest JDK from the Sun Java site (Java Platform, Standard Edition) or install from a software distribution package on Linux.

You must install KVStore on the host machine where you want to install your connector 

Check to make sure that splunk forwarder is NOT installed on your splunk enterprise host machine.

Akamai strongly recommends installing the Splunk add-on app_ **Lookup File Editor** from within Splunk Apps. You need this add-on to switch retrieval mode.


### Proxy server

To access the SIEM API from behind a proxy server, ensure that your proxy:

*   whitelists the domains `*.cloudsecurity.akamaiapis.net`
*   does not interfere with HTTP request headers for those domains. If, due to a strict enterprise security policy, your proxy does change these headers, make sure that at a minimum you allow and don't change the `Host` and `Authorization` headers.


## Hardware Requirements

This sample application has been tested with the following operating systems:

* CentOS 7
* Windows Server 2012 R2
* Mac OS X El Capitan Version 10.11.6

Some additional hardware requirements:

* 4 CPU cores
* 16 GB RAM
* 2GB Free Disk Space

## Install

To send Akamai security events to Splunk® instance, download and install the Akamai SIEM add-on:

1.  Go to [https://developer.akamai.com/tools/siem-integration](https://developer.akamai.com/tools/siem-integration) and download `TA-Akamai_SIEM_1_0_x.spl` ( `x` being the latest version available).
2.  In Splunk, on the upper left of the screen, click the **Splunk >** icon.
3.  Next to **Apps** at the top of the navigation bar, click the gear icon.

    ![Splunk gear icon](https://developer.akamai.com/tools/siem-integration/docs/Content/Resources/Images/splunk_211x130.png)

4.  Click **Install app from file**.
5.  Click **Choose File**.
6.  Browse to and select `TA-Akamai_SIEM_1_0_x.spl` then click **Open**.
7.  Click **Upload**.
8.  Navigate back to home and confirm the Akamai SIEM application has loaded. You see it in the list on the left.

    ![Akamai SIEM app in menu](https://developer.akamai.com/tools/siem-integration/docs/Content/Resources/Images/add-on_in_splunk_165x212.png)

9.  Restart Splunk.
10.  From the menu, choose **Settings > Data Inputs**.
11.  Click the **Akamai Security Incident Event Manager API**.
12.  Click **New** and complete the following fields:
    *   **Name**. Enter any name you want for the input.
    *   **rest_username**. Enter your Splunk username.
    *   **rest_password**. Enter your Splunk password.
    *   **Hostname**. Enter the host URL you copied when you provisioned the SIEM API.
    *   **Security Configuration(s)**. Enter the **Configuration ID** you copied when you turned on SIEM in the Luna portal (allowed SIEM data collection).
    *   **Client Token**, **Client Secret**, and **Access Token**. Enter the values you copied when you provisioned the SIEM OPEN API.
    *   **Initial Epoch Time** and **Final Epoch time**. Leave these fields blank. If you ever encounter an issue with your data field, you can use them to retrieve security event data for a set time period.
    *   **Limit**. If you want to limit the number of security events to pull, you can enter a number here to limit the results for each REST call. If not specified, the API retrieves a maximum of 150000 records per call.
    *   **Interval**. Number of seconds between fetch requests. Enter 60, unless you have entered values in both **Initial Epoch Time** and **Final Epoch Time** to retrieve security events from a set time period. In that case, enter 0\.
13.  Return to Splunk home and click **Akamai SIEM**. If you see data, setup was successful:

   ![Security event data in Splunk](https://developer.akamai.com/tools/siem-integration/docs/Content/Resources/Images/splunkreports.png)
   
14.  If you see no data, go to the menu and choose **Debug > Akamai Logging dashboard**. You see Akamai SIEM Errors on the right.

   ![logging](https://developer.akamai.com/tools/siem-integration/docs/Content/Resources/Images/Splunklogs.png)
   
   In the event of a fatal error prohibiting collection of data, you’d need to review these logs and take corrective actions. This log is also available in `/{splunk_home}/var/log/splunk`. [See the SIEM Integration Guide](https://developer.akamai.com/tools/siem-integration) to learn how to retrieve past security events.
   
15.  To search for SIEM data within Splunk search app (From Splunk home, click **Search and Reporting app**), enter the query `sourcetype=”akamaisiem”`


## Editing data inputs

Once a data input is enabled, you can't just edit it and run it again. If you try, you get the following error:

```
message from "/Applications/Splunk/etc/apps/TAAkamai_SIEM/darwin_x86_64/bin/TAAkamai_SIEM.sh" com.splunk.modularinput.XmlUtil.textInNode(XmlUtil.java:38)\\com.splunk.modularinput.Parameter.nodeToParameterList(Parameter.java:72)\\com.splunk.modularinput.InputDefinition.parseDefinition(InputDefinition.java:193)\\com.splunk.modularinput.Script.run(Script.java:65)\\com.splunk.modularinput.Script.run(Script.java:44)\\Main.main(Main.java:493)
```

To avoid this problem, disable it, clone the data input, make changes to the clone, then run that new input.
