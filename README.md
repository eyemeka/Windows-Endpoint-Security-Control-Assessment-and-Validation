# Windows Endpoint Security Control Assessment and Validation

## 1. Project Overview

## 1.1 Purpose
This project assessed selected Windows 11 endpoint security controls through hands-on testing and event log validation. The assessment focused on logon activity, failed authentication attempts, user account management, security group membership changes, and process creation auditing.

## 1.2 Objectives
The objectives were to:
<ul>
 	<li>Verify that Windows records selected security events in its event logs.</li>
 	<li>Examine audit policy settings for key security activities.</li>
 	<li>Validate process monitoring using Sysmon and Windows Security logs.</li>
 	<li>Identify a security logging configuration gap and apply a corrective action.</li>
 	<li>Document the results and recommend improvements to endpoint security monitoring.</li>
</ul>

## 1.3 Scope
The assessment was conducted in an isolated Windows 11 Home virtual machine running in Oracle VirtualBox. Testing covered selected Windows Security event IDs, Sysmon process creation events, and relevant audit policy settings.

The project used controlled activities, including a failed logon attempt, test account creation, a security group membership change, and execution of standard Windows commands.

This was a personal lab assessment. It did not represent a formal compliance audit or an assessment of an enterprise production environment.

# 2. Lab Environment and Tools

## 2.1 Lab Environment
The assessment was performed in an isolated Windows 11 Home virtual machine hosted in Oracle VirtualBox. A baseline snapshot and Windows restore point were created before testing to support recovery.
<table>
<tbody>
<tr>
<th>Component</th>
<th>Configuration</th>
</tr>
<tr>
<td>Operating system</td>
<td>Windows 11 Home, 64-bit</td>
</tr>
<tr>
<td>Virtualisation platform</td>
<td>Oracle VirtualBox</td>
</tr>
<tr>
<td>Allocated memory</td>
<td>4 GB RAM</td>
</tr>
<tr>
<td>Process monitoring</td>
<td>Microsoft Sysmon</td>
</tr>
<tr>
<td>Event log analysis</td>
<td>Windows Event Viewer</td>
</tr>
<tr>
<td>Audit policy verification</td>
<td>Windows <code>auditpol</code> command-line utility</td>
</tr>
</tbody>
</table>

## 2.2 Tools Used
<strong>Sysmon:</strong> Recorded process creation activity and provided process details, including executable paths, command lines, parent processes, and user context.

<strong>Windows Event Viewer:</strong> Used to examine security events related to logons, failed authentication, account creation, group membership changes, and process creation.

<strong>Windows Audit Policy Utility (</strong><code><strong>auditpol</strong></code><strong>):</strong> Used to inspect existing audit policy settings and enable successful process creation auditing.

<strong>Command Prompt:</strong> Used to execute controlled test commands, create a temporary local test account, and verify audit policy configurations.

## 2.3 Safety and Recovery
The assessment was performed in a virtual machine. A VirtualBox snapshot named <code>Baseline - Before Security Monitoring Lab</code> and a Windows restore point named <code>Before Windows Security Monitoring Lab</code> were created before testing.

These recovery points provided options for returning the environment to its earlier state after the assessment.

# 3. Sysmon Installation and Validation

## 3.1 Objective
The objective was to install Microsoft Sysmon on the Windows 11 virtual machine and verify that it recorded process creation activity.

## 3.2 Installation Procedure
Sysmon was downloaded from the official Microsoft Sysinternals website. The extracted files were stored in the <code>Downloads\sysmon</code> directory.

An elevated Command Prompt was opened, and the following command was executed from the Sysmon directory:
<pre><code>Sysmon64.exe -accepteula -i</code></pre>
The command accepted the Sysmon licence agreement and installed the Sysmon service with its default configuration.

## 3.3 Validation Procedure
After installation, Windows Event Viewer was opened. The following path was used to access the Sysmon event log:

<code>Applications and Services Logs → Microsoft → Windows → Sysmon → Operational</code>

The log was checked for Event ID 1, which records process creation. A process creation event was located and examined to confirm that Sysmon was collecting process activity.
IMG 1

## 3.4 Result
Sysmon was installed successfully, and Event ID 1 was observed in the Sysmon Operational log. This confirmed that the monitoring component was operational and recording process creation events.

The installation used Sysmon's default configuration. No custom Sysmon detection rules were configured as part of this test.

# 4. Logon Event Validation

## 4.1 Objective
The objective was to verify that Windows Security logs recorded successful and failed logon activity.

## 4.2 Successful Logon Event

## Procedure
<ol start="1">
 	<li>Open Windows Event Viewer.</li>
 	<li>Navigate to <code>Windows Logs → Security</code>.</li>
 	<li>Locate a successful logon event with Event ID 4624.</li>
 	<li>Open the event and examine its General tab.</li>
 	<li>Review the Logon Type field to understand the type of logon recorded.</li>
</ol>

## Result
Event ID 4624 was located in the Windows Security log. The event showed Logon Type 5, which indicates a service logon. Therefore, this event confirmed that Windows recorded a successful service logon, not an interactive user logon.

## Evidence
IMG 2

<strong>Figure: Successful Service Logon Event (Event ID 4624)</strong>

This evidence shows a successful logon event and its Logon Type value in the Windows Security log.

## 4.3 Failed Logon Event

## Procedure
<ol start="1">
 	<li>Lock the Windows virtual machine.</li>
 	<li>Enter an incorrect password once.</li>
 	<li>Enter the correct password to regain access.</li>
 	<li>Return to Event Viewer and open <code>Windows Logs → Security</code>.</li>
 	<li>Locate Event ID 4625 corresponding to the failed logon attempt.</li>
 	<li>Review the event details to confirm the recorded failure.</li>
</ol>

## Result
Event ID 4625 was located after a deliberate failed logon attempt. This demonstrated that Windows recorded the failed authentication event in its Security log.

## Evidence
IMG 3

<strong>Figure: Failed Logon Event (Event ID 4625)</strong>

This evidence shows the failed logon event generated during the controlled test.

## 4.4 Assessment Outcome
The test confirmed that the Windows Security log contained both a successful service logon event and a failed logon event. These records can support investigations into authentication activity.

The results apply to the events observed during this lab. They do not establish that every possible logon scenario was tested.

# 5. User Account Creation Validation
<h3>5.1 Objective</h3>
The objective was to verify that Windows recorded the creation of a local user account in the Security event log.

## 5.2 Test Procedure
<ol start="1">
 	<li>Open Command Prompt as an administrator.</li>
 	<li>Create a temporary local test account using the following command:</li>
</ol>
<pre><code>net user LabTestUser * /add</code></pre>
<ol start="3">
 	<li>Enter a temporary password when prompted. Do not include the password in the report or public screenshots.</li>
 	<li>Open Event Viewer and navigate to <code>Windows Logs → Security</code>.</li>
 	<li>Filter the log for Event ID 4720.</li>
 	<li>Locate the event corresponding to the creation of <code>LabTestUser</code>.</li>
 	<li>Examine the event details to verify that Windows recorded the account creation.</li>
</ol>
<strong>Note:</strong> The command above uses a password prompt to avoid exposing the password in the command itself. In the original lab, a temporary password was supplied directly in the command.

IMG 4

## 5.3 Results
Event ID 4720 was located in the Windows Security log for the <code>LabTestUser</code> account. This confirmed that Windows recorded the local account creation activity.

The result demonstrates that account management activity can be reviewed through Windows Security logs.

## 5.4 Evidence
IMG 5

<strong>Figure 5.1: Local User Account Creation Event (Event ID 4720)</strong>

This evidence shows the account creation event recorded in the Windows Security log.

If you also saved a screenshot of the Command Prompt confirming that the account was created successfully, insert it before Figure 5.1 as an additional piece of evidence. Otherwise, the Event ID 4720 screenshot is sufficient to document the logged event.

# 6. Security Group Membership Change Validation

## 6.1 Objective
The objective was to verify that Windows recorded changes to local security group membership in the Security event log.

## 6.2 Test Procedure
<ol start="1">
 	<li>Open Command Prompt as an administrator.</li>
 	<li>Add the test account to the Remote Management Users group by running:</li>
</ol>
<pre><code>net localgroup "Remote Management Users" LabTestUser /add</code></pre>
<ol start="3">
 	<li>Confirm that Windows reports the operation completed successfully.</li>
 	<li>Open Event Viewer and navigate to <code>Windows Logs → Security</code>.</li>
 	<li>Locate Event ID 4732, which records a member being added to a security-enabled local group.</li>
 	<li>Examine the event details to verify that <code>LabTestUser</code> was added to the group.</li>
</ol>

## 6.3 Results
Event ID 4732 was located in the Windows Security log. The event identified <code>LabTestUser</code> as the account added to the Remote Management Users group.

This confirmed that Windows recorded the group membership change.

Group membership changes can affect access to system resources and administrative capabilities. They should therefore be authorised, monitored, and reviewed.

## 6.4 Evidence</h3>
IMG 6

<strong>Figure 6.1: Security Group Membership Change (Event ID 4732)</strong>

This evidence shows Windows recording the addition of the test account to the Remote Management Users group.

# 7. Sysmon Process Creation Monitoring

## 7.1 Objective
The objective was to validate Sysmon's ability to record process creation activity and capture details useful for security investigations.

## 7.2 Test Procedure

Open Command Prompt.
Run the following commands:
whoami
ipconfig

Open Event Viewer and navigate to Applications and Services Logs → Microsoft → Windows → Sysmon → Operational.
Locate Event ID 1 for the execution of ipconfig.exe.
Open the event and review the available process details.

## 7.3 Results

## 7.3 Results
Sysmon recorded Event ID 1 for <code>ipconfig.exe</code>. The event contained process information including the executable path, command line, parent process, user context, integrity level, and SHA-256 hash.

The event identified <code>cmd.exe</code> as the parent process and recorded the user context under which the command ran.

This information can help an analyst establish which process started, how it was launched, which user context it used, and which parent process initiated it.

The test used a legitimate Windows utility. It validated process telemetry collection but did not test the detection of malicious activity.

## 7.4 Evidence
IMG 8

<strong>Figure 7.1: Sysmon Process Creation Event for ipconfig.exe (Event ID 1)</strong>

This evidence shows Sysmon capturing process execution details for the <code>ipconfig.exe</code> command.

# 8. Windows Audit Policy Assessment

## 8.1 Objective
The objective was to review selected Windows audit policies and determine whether auditing was enabled for key security activities.

## 8.2 Assessment Procedure
An elevated Command Prompt was used to inspect the audit policy settings with the Windows <code>auditpol</code> utility.

The following commands were executed:
<pre><code>auditpol /get /subcategory:"Logon"
auditpol /get /subcategory:"User Account Management"
auditpol /get /subcategory:"Process Creation"
auditpol /get /subcategory:"Security Group Management"
auditpol /get /subcategory:"Other Account Management Events"</code></pre>
The reported settings were reviewed to identify which activities were configured for successful or failed event auditing.

## 8.3 Assessment Results
The following settings were observed during the assessment:
<table>
<tbody>
<tr>
<th>Audit subcategory</th>
<th>Observed setting</th>
<th>Assessment</th>
</tr>
<tr>
<td>Logon</td>
<td>Success and Failure</td>
<td>Both successful and failed logon auditing were enabled</td>
</tr>
<tr>
<td>User Account Management</td>
<td>Success</td>
<td>Successful account management events were audited</td>
</tr>
<tr>
<td>Security Group Management</td>
<td>Success</td>
<td>Successful security group management events were audited</td>
</tr>
<tr>
<td>Process Creation</td>
<td>No Auditing</td>
<td>Process creation auditing was disabled</td>
</tr>
<tr>
<td>Other Account Management Events</td>
<td>No Auditing</td>
<td>Additional account management event auditing was disabled</td>
</tr>
</tbody>
</table>

The results reflect the settings observed in this Windows 11 lab environment. They do not establish that every event within each subcategory was tested.

## 8.4 Identified Configuration Gap
Process Creation auditing was set to <strong>No Auditing</strong>. At the Windows Security audit policy level, this meant successful process creation events were not configured to be audited through that policy.

This was a configuration gap because process creation records can help analysts investigate how programs are launched and identify activity that requires further review.

Sysmon was already recording process creation events through its own Operational log. Therefore, the gap concerned Windows Security process creation auditing, not a complete absence of process monitoring.

## 8.5 Evidence
<p dir="auto">The initial audit policy review identified that Process Creation auditing was set to <code dir="ltr">No Auditing</code>. The initial command output was not saved as a screenshot.</p>
<p dir="auto">The finding was documented based on the setting observed during testing.</p>

# 9. Remediation and Validation

## 9.1 Objective
The objective was to enable Windows Security auditing for successful process creation events and verify that the change produced the expected event records.

## 9.2 Remediation Procedure
An elevated Command Prompt was opened. The following command was executed to enable successful process creation auditing:
<pre><code>auditpol /set /subcategory:"Process Creation" /success:enable</code></pre>
Windows reported that the command completed successfully.

The audit policy was then checked using:
<pre><code>auditpol /get /subcategory:"Process Creation"</code></pre>
The resulting setting showed <code>Success</code>, confirming that successful process creation auditing was enabled.

## 9.3 Validation Procedure
After changing the policy, the <code>whoami</code> command was executed in Command Prompt.

Event Viewer was opened, and <code>Windows Logs → Security</code> was filtered for Event ID 4688, which records the creation of a new process.

An Event ID 4688 record corresponding to the test activity was located and reviewed.

## 9.4 Results
The audit policy changed from <code>No Auditing</code> to <code>Success</code>. Windows subsequently recorded an Event ID 4688 process creation event.

This confirmed that successful process creation auditing was enabled and producing records in the Windows Security log.

The remediation complemented Sysmon process monitoring by providing process creation records in a second Windows logging source. This may support investigation and cross-checking, although centralised collection and alerting were not tested.

## 9.5 Evidence
IMG

<strong>Figure 9.2: Windows Security Process Creation Event (Event ID 4688)</strong>

This evidence confirms that Windows recorded a process creation event after successful auditing was enabled.

# 10. Findings

## 10.1 Summary of Findings</h3>
The assessment produced the following findings:
<table>
<tbody>
<tr>
<th>ID</th>
<th>Finding</th>
<th>Result</th>
</tr>
<tr>
<td>F-01</td>
<td>Sysmon process monitoring</td>
<td>Event ID 1 confirmed process creation logging in the Sysmon Operational log</td>
</tr>
<tr>
<td>F-02</td>
<td>Logon event recording</td>
<td>Successful service logon and failed logon events were found in the Windows Security log</td>
</tr>
<tr>
<td>F-03</td>
<td>User account management</td>
<td>Event ID 4720 recorded creation of the test account</td>
</tr>
<tr>
<td>F-04</td>
<td>Security group management</td>
<td>Event ID 4732 recorded the addition of the test account to a local security group</td>
</tr>
<tr>
<td>F-05</td>
<td>Process creation audit policy</td>
<td>Windows Security Process Creation auditing was initially disabled</td>
</tr>
<tr>
<td>F-06</td>
<td>Remediation validation</td>
<td>Process Creation auditing was enabled for Success, and Event ID 4688 was observed afterward</td>
</tr>
<tr>
<td>F-07</td>
<td>Other Account Management Events</td>
<td>The subcategory was set to No Auditing. Further assessment is required to determine whether this setting is appropriate</td>
</tr>
</tbody>
</table>

## 10.2 Conclusion
This project demonstrated practical assessment and validation of selected Windows endpoint security controls. I examined security event logs, reviewed audit policy settings, identified a process auditing configuration gap, and applied and validated a corrective action.

The exercise strengthened my practical understanding of Windows security logging, audit policy configuration, event validation, and control assessment.

The assessment was conducted in a controlled Windows 11 virtual machine and did not represent a full enterprise security audit.
