# Windows Endpoint Security Control Assessment and Validation

1. Project Overview
1.1 Purpose

This project assessed selected Windows 11 endpoint security controls through hands-on testing and event log validation. The assessment focused on logon activity, failed authentication attempts, user account management, security group membership changes, and process creation auditing.

## 1.2 Objectives

The objectives were to:

Verify that Windows records selected security events in its event logs.
Examine audit policy settings for key security activities.
Validate process monitoring using Sysmon and Windows Security logs.
Identify a security logging configuration gap and apply a corrective action.
Document the results and recommend improvements to endpoint security monitoring.
1.3 Scope

The assessment was conducted in an isolated Windows 11 Home virtual machine running in Oracle VirtualBox. Testing covered selected Windows Security event IDs, Sysmon process creation events, and relevant audit policy settings.

The project used controlled activities, including a failed logon attempt, test account creation, a security group membership change, and execution of standard Windows commands.

This was a personal lab assessment. It did not represent a formal compliance audit or an assessment of an enterprise production environment.

# 2. Lab Environment and Tools

## 2.1 Lab Environment

The assessment was performed in an isolated Windows 11 Home virtual machine hosted in Oracle VirtualBox. A baseline snapshot and Windows restore point were created before testing to support recovery.

Component	Configuration
Operating system	Windows 11 Home, 64-bit
Virtualisation platform	Oracle VirtualBox
Allocated memory	4 GB RAM
Process monitoring	Microsoft Sysmon
Event log analysis	Windows Event Viewer
Audit policy verification	Windows auditpol command-line utility

## 2.2 Tools Used

Sysmon: Recorded process creation activity and provided process details, including executable paths, command lines, parent processes, and user context.

Windows Event Viewer: Used to examine security events related to logons, failed authentication, account creation, group membership changes, and process creation.

Windows Audit Policy Utility (auditpol): Used to inspect existing audit policy settings and enable successful process creation auditing.

Command Prompt: Used to execute controlled test commands, create a temporary local test account, and verify audit policy configurations.

## 2.3 Safety and Recovery

The assessment was performed in a virtual machine. A VirtualBox snapshot named Baseline - Before Security Monitoring Lab and a Windows restore point named Before Windows Security Monitoring Lab were created before testing.

These recovery points provided options for returning the environment to its earlier state after the assessment.

# 3. Sysmon Installation and Validation

## 3.1 Objective

The objective was to install Microsoft Sysmon on the Windows 11 virtual machine and verify that it recorded process creation activity.

## 3.2 Installation Procedure

Sysmon was downloaded from the official Microsoft Sysinternals website. The extracted files were stored in the Downloads\sysmon directory.

An elevated Command Prompt was opened, and the following command was executed from the Sysmon directory:

Sysmon64.exe -accepteula -i

The command accepted the Sysmon licence agreement and installed the Sysmon service with its default configuration.

## 3.3 Validation Procedure

After installation, Windows Event Viewer was opened. The following path was used to access the Sysmon event log:

Applications and Services Logs → Microsoft → Windows → Sysmon → Operational

The log was checked for Event ID 1, which records process creation. A process creation event was located and examined to confirm that Sysmon was collecting process activity.

## 3.4 Result

Sysmon was installed successfully, and Event ID 1 was observed in the Sysmon Operational log. This confirmed that the monitoring component was operational and recording process creation events.

The installation used Sysmon's default configuration. No custom Sysmon detection rules were configured as part of this test.

# 4. Logon Event Validation

## 4.1 Objective

The objective was to verify that Windows Security logs recorded successful and failed logon activity.

## 4.2 Successful Logon Event

Procedure
Open Windows Event Viewer.
Navigate to Windows Logs → Security.
Locate a successful logon event with Event ID 4624.
Open the event and examine its General tab.
Review the Logon Type field to understand the type of logon recorded.
Result

Event ID 4624 was located in the Windows Security log. The event showed Logon Type 5, which indicates a service logon. Therefore, this event confirmed that Windows recorded a successful service logon, not an interactive user logon.

## Evidence

Figure: Successful Service Logon Event (Event ID 4624)

This evidence shows a successful logon event and its Logon Type value in the Windows Security log.

## 4.3 Failed Logon Event

Procedure
Lock the Windows virtual machine.
Enter an incorrect password once.
Enter the correct password to regain access.
Return to Event Viewer and open Windows Logs → Security.
Locate Event ID 4625 corresponding to the failed logon attempt.
Review the event details to confirm the recorded failure.
Result

Event ID 4625 was located after a deliberate failed logon attempt. This demonstrated that Windows recorded the failed authentication event in its Security log.

## Evidence

## Figure: Failed Logon Event (Event ID 4625)

This evidence shows the failed logon event generated during the controlled test.

## 4.4 Assessment Outcome

The test confirmed that the Windows Security log contained both a successful service logon event and a failed logon event. These records can support investigations into authentication activity.

The results apply to the events observed during this lab. They do not establish that every possible logon scenario was tested.

# 5. User Account Creation Validation

## 5.1 Objective

The objective was to verify that Windows recorded the creation of a local user account in the Security event log.

## 5.2 Test Procedure

Open Command Prompt as an administrator.
Create a temporary local test account using the following command:
net user LabTestUser * /add
Enter a temporary password when prompted. Do not include the password in the report or public screenshots.
Open Event Viewer and navigate to Windows Logs → Security.
Filter the log for Event ID 4720.
Locate the event corresponding to the creation of LabTestUser.
Examine the event details to verify that Windows recorded the account creation.

Note: The command above uses a password prompt to avoid exposing the password in the command itself. In the original lab, a temporary password was supplied directly in the command.

## 5.3 Results

Event ID 4720 was located in the Windows Security log for the LabTestUser account. This confirmed that Windows recorded the local account creation activity.

The result demonstrates that account management activity can be reviewed through Windows Security logs.

## 5.4 Evidence

## Figure 5.1: Local User Account Creation Event (Event ID 4720)

This evidence shows the account creation event recorded in the Windows Security log.

If you also saved a screenshot of the Command Prompt confirming that the account was created successfully, insert it before Figure 5.1 as an additional piece of evidence. Otherwise, the Event ID 4720 screenshot is sufficient to document the logged event.

# 6. Security Group Membership Change Validation

## 6.1 Objective

The objective was to verify that Windows recorded changes to local security group membership in the Security event log.

## 6.2 Test Procedure

Open Command Prompt as an administrator.
Add the test account to the Remote Management Users group by running:
net localgroup "Remote Management Users" LabTestUser /add
Confirm that Windows reports the operation completed successfully.
Open Event Viewer and navigate to Windows Logs → Security.
Locate Event ID 4732, which records a member being added to a security-enabled local group.
Examine the event details to verify that LabTestUser was added to the group.

## 6.3 Results

Event ID 4732 was located in the Windows Security log. The event identified LabTestUser as the account added to the Remote Management Users group.

This confirmed that Windows recorded the group membership change.

Group membership changes can affect access to system resources and administrative capabilities. They should therefore be authorised, monitored, and reviewed.

## 6.4 Evidence

Figure 6.1: Security Group Membership Change (Event ID 4732)

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

##7.3 Results

Sysmon recorded Event ID 1 for ipconfig.exe. The event contained process information including the executable path, command line, parent process, user context, integrity level, and SHA-256 hash.

The event identified cmd.exe as the parent process and recorded the user context under which the command ran.

This information can help an analyst establish which process started, how it was launched, which user context it used, and which parent process initiated it.

The test used a legitimate Windows utility. It validated process telemetry collection but did not test the detection of malicious activity.

7.4 Evidence

Figure 7.1: Sysmon Process Creation Event for ipconfig.exe (Event ID 1)

This evidence shows Sysmon capturing process execution details for the ipconfig.exe command.

8. Windows Audit Policy Assessment
8.1 Objective

The objective was to review selected Windows audit policies and determine whether auditing was enabled for key security activities.

8.2 Assessment Procedure

An elevated Command Prompt was used to inspect the audit policy settings with the Windows auditpol utility.

The following commands were executed:

auditpol /get /subcategory:"Logon"
auditpol /get /subcategory:"User Account Management"
auditpol /get /subcategory:"Process Creation"
auditpol /get /subcategory:"Security Group Management"
auditpol /get /subcategory:"Other Account Management Events"

The reported settings were reviewed to identify which activities were configured for successful or failed event auditing.

8.3 Assessment Results

The following settings were observed during the assessment:

Audit subcategory	Observed setting	Assessment
Logon	Success and Failure	Both successful and failed logon auditing were enabled
User Account Management	Success	Successful account management events were audited
Security Group Management	Success	Successful security group management events were audited
Process Creation	No Auditing	Process creation auditing was disabled
Other Account Management Events	No Auditing	Additional account management event auditing was disabled

The results reflect the settings observed in this Windows 11 lab environment. They do not establish that every event within each subcategory was tested.

8.4 Identified Configuration Gap

Process Creation auditing was set to No Auditing. At the Windows Security audit policy level, this meant successful process creation events were not configured to be audited through that policy.

This was a configuration gap because process creation records can help analysts investigate how programs are launched and identify activity that requires further review.

Sysmon was already recording process creation events through its own Operational log. Therefore, the gap concerned Windows Security process creation auditing, not a complete absence of process monitoring.

8.5 Evidence

The initial audit policy review identified that Process Creation auditing was set to No Auditing. The initial command output was not saved as a screenshot.

The finding was documented based on the setting observed during testing.

9. Remediation and Validation
9.1 Objective

The objective was to enable Windows Security auditing for successful process creation events and verify that the change produced the expected event records.

9.2 Remediation Procedure

An elevated Command Prompt was opened. The following command was executed to enable successful process creation auditing:

auditpol /set /subcategory:"Process Creation" /success:enable

Windows reported that the command completed successfully.

The audit policy was then checked using:

auditpol /get /subcategory:"Process Creation"

The resulting setting showed Success, confirming that successful process creation auditing was enabled.

9.3 Validation Procedure

After changing the policy, the whoami command was executed in Command Prompt.

Event Viewer was opened, and Windows Logs → Security was filtered for Event ID 4688, which records the creation of a new process.

An Event ID 4688 record corresponding to the test activity was located and reviewed.

9.4 Results

The audit policy changed from No Auditing to Success. Windows subsequently recorded an Event ID 4688 process creation event.

This confirmed that successful process creation auditing was enabled and producing records in the Windows Security log.

The remediation complemented Sysmon process monitoring by providing process creation records in a second Windows logging source. This may support investigation and cross-checking, although centralised collection and alerting were not tested.

9.5 Evidence




Figure 9.2: Windows Security Process Creation Event (Event ID 4688)

This evidence confirms that Windows recorded a process creation event after successful auditing was enabled.

10. Findings
10.1 Summary of Findings

The assessment produced the following findings:

ID	Finding	Result
F-01	Sysmon process monitoring	Event ID 1 confirmed process creation logging in the Sysmon Operational log
F-02	Logon event recording	Successful service logon and failed logon events were found in the Windows Security log
F-03	User account management	Event ID 4720 recorded creation of the test account
F-04	Security group management	Event ID 4732 recorded the addition of the test account to a local security group
F-05	Process creation audit policy	Windows Security Process Creation auditing was initially disabled
F-06	Remediation validation	Process Creation auditing was enabled for Success, and Event ID 4688 was observed afterward
F-07	Other Account Management Events	The subcategory was set to No Auditing. Further assessment is required to determine whether this setting is appropriate
10.2 Conclusion

This project demonstrated practical assessment and validation of selected Windows endpoint security controls. I examined security event logs, reviewed audit policy settings, identified a process auditing configuration gap, and applied and validated a corrective action.

The exercise strengthened my practical understanding of Windows security logging, audit policy configuration, event validation, and control assessment.

The assessment was conducted in a controlled Windows 11 virtual machine and did not represent a full enterprise security audit.
