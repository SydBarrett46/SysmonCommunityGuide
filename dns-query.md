DNS Query
=========

Sysmon will log **EventID 22** to log all DNS Queries using the Windows
***DnsQuery\_\**** API calsl in **dnsapi.dll**. **Logging is supported
on Windows 8.1 or above** since it leverages new ETW functionality in
newer version of Windows. Programs that do their own DNS resolution and
do not use the Windows API calls will not be logged.

The fields for the event are:

* **RuleName**: Name of rule that triggered the event.

* **UtcTime**: Time in UTC when event was created

* **ProcessGuid**: Process Guid of the process that made the DNS query

* **ProcessId**: Process ID of the process that made the DNS query

* **QueryName**: DNS name that was queries

* **QueryStatus**: Query result status code

* **QueryResults**: Query results

* **Image**: File path of the process that made the DNS query

Exclude known destinations so as to focus on new unknown destinations.
This is a high-volume event generation filter so it is recommended to
experiment and build rules with filters for your specific environment if
implemented. Some examples can be found in
<https://github.com/olafhartong/sysmon-modular/tree/master/22_dns_query>

Example that excludes known update and telemetry domains.

```xml
<Sysmon schemaversion="4.22">
   <!-- special thanks to @SwiftOnSecurity for this -->
   <HashAlgorithms>*</HashAlgorithms>
   <CheckRevocation/>
   <EventFiltering>
      <RuleGroup name="" groupRelation="or">
      <DnsQuery onmatch="exclude">
            <!-- Browser Update Domains-->
            <QueryName condition="end with">.mozaws.net</QueryName> <!--Mozilla-->
            <QueryName condition="end with">.mozilla.com</QueryName> <!--Mozilla-->
            <QueryName condition="end with">.mozilla.net</QueryName> <!--Mozilla-->
            <QueryName condition="end with">.mozilla.org</QueryName> <!--Mozilla-->
            <QueryName condition="is">clients1.google.com</QueryName> <!--Google-->
            <QueryName condition="is">clients2.google.com</QueryName> <!--Google-->
            <QueryName condition="is">clients3.google.com</QueryName> <!--Google-->
            <QueryName condition="is">clients4.google.com</QueryName> <!--Google-->
            <QueryName condition="is">clients5.google.com</QueryName> <!--Google-->
            <QueryName condition="is">clients6.google.com</QueryName> <!--Google-->
            <!-- Microsoft Domains -->
            <QueryName condition="is">safebrowsing.googleapis.com</QueryName> <!--Google-->
            <QueryName condition="end with">-pushp.svc.ms</QueryName> <!--Microsoft: Doesn't appear to host customer content or subdomains-->
            <QueryName condition="end with">.b-msedge.net</QueryName> <!--Microsoft: Doesn't appear to host customer content or subdomains-->
            <QueryName condition="end with">.bing.com</QueryName> <!-- Microsoft | Microsoft default exclusion -->
            <QueryName condition="end with">.hotmail.com</QueryName> <!--Microsoft | Microsoft default exclusion-->
            <QueryName condition="end with">.live.com</QueryName> <!--Microsoft | Microsoft default exclusion-->
            <QueryName condition="end with">.live.net</QueryName> <!--Microsoft | Microsoft default exclusion-->
            <QueryName condition="end with">.s-microsoft.com</QueryName> <!--Microsoft-->
            <QueryName condition="end with">.microsoft.com</QueryName> <!--Microsoft | Microsoft default exclusion-->
            <QueryName condition="end with">.microsoftonline.com</QueryName> <!--Microsoft | Microsoft default exclusion-->
            <QueryName condition="end with">.microsoftstore.com</QueryName> <!--Microsoft | Microsoft default exclusion-->
            <QueryName condition="end with">.ms-acdc.office.com</QueryName> <!--Microsoft: Doesn't appear to host customer content or subdomains-->
            <QueryName condition="end with">.msedge.net</QueryName> <!--Microsoft: Doesn't appear to host customer content or subdomains-->
            <QueryName condition="end with">.msn.com</QueryName> <!--Microsoft | Microsoft default exclusion-->
            <QueryName condition="end with">.msocdn.com</QueryName> <!--Microsoft-->
            <QueryName condition="end with">.skype.com</QueryName> <!--Microsoft | Microsoft default exclusion-->
            <QueryName condition="end with">.skype.net</QueryName> <!--Microsoft | Microsoft default exclusion-->
            <QueryName condition="end with">.windows.com</QueryName> <!--Microsoft-->
            <QueryName condition="end with">.windows.net.nsatc.net</QueryName> <!--Microsoft-->
            <QueryName condition="end with">.windowsupdate.com</QueryName> <!--Microsoft-->
            <QueryName condition="end with">.xboxlive.com</QueryName> <!--Microsoft-->
            <QueryName condition="is">login.windows.net</QueryName> <!--Microsoft-->
      </DnsQuery>
</RuleGroup>
</EventFiltering>
</Sysmon>
```