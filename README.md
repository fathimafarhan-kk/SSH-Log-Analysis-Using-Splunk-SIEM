# SSH-Log-Analysis-Using-Splunk-SIEM

## Project Overview

This project demonstrates how to use Splunk for SSH log monitoring and security analysis.

The project simulates real-world SOC Analyst tasks by analyzing SSH authentication logs and creating dashboards and alerts for security monitoring.

---

#  Objective

The goal of this project is to analyze SSH authentication logs to detect:

- Successful SSH logins
- Failed login attempts
- Multiple failed authentication attempts (Brute-force indicators)
- Connections without authentication attempts
- Suspicious login behavior and attack patterns

By completing this project, I learned how to ingest logs into Splunk, write SPL queries, create visualizations, build dashboards, and configure alerts for security monitoring.

---

# Skills Learned

- SIEM Log Analysis using Splunk
- SSH Authentication Log Monitoring
- Detection of Brute-force Attacks
- Security Event Investigation
- Log Parsing and Field Extraction
- Writing SPL (Search Processing Language) Queries
- Dashboard Creation and Visualization
- Alert Configuration in Splunk
- Threat Detection and Monitoring
- SOC Analyst Workflow and Investigation Techniques

---

# Tools & Technologies Used

<div>
<img src="https://img.shields.io/badge/-Splunk-000000?&style=for-the-badge&logo=Splunk&logoColor=white" />
<img src="https://img.shields.io/badge/-Kali_Linux-557C94?&style=for-the-badge&logo=kalilinux&logoColor=white" />
<img src="https://img.shields.io/badge/-SSH-4D4D4D?&style=for-the-badge&logo=gnu-bash&logoColor=white" />
</div>

---

#  Lab Setup

Before starting this project:

- Install Splunk Enterprise or Splunk Free Edition
- Download the SSH log dataset (`ssh_log.json`)

---

#  Log Preparation & Ingestion

## Step 1: Upload SSH Logs into Splunk

1. Open Splunk Web Interface
2. Navigate to:

```text
Apps → Search & Reporting
```

3. Click:

```text
Add Data → Upload
```

4. Upload the file:

```text
ssh_log.json
```

5. Configure:
- **Sourcetype:** `_json`
- **Index Name:** `ssh_logs`

6. Review extracted fields and click:

```text
Start Searching
```

---

#  Task 1: Ingest and Parse Logs

## Validate Extracted Fields

Ensure the following fields are properly extracted:

- `event_type`
- `auth_success`
- `auth_attempts`
- `id.orig_h`
- `id.resp_h`

## Validation Search

```spl
index=ssh_logs
| stats count by event_type
```
````markdown id="plx83m"
## 📸 Project Screenshots & Explanation

### 1️⃣ SSH Log Data Successfully Ingested into Splunk

This screenshot shows the uploaded SSH log dataset inside Splunk after ingestion and field extraction. Splunk successfully parsed the JSON log file and categorized the events based on SSH activity types such as successful logins, failed logins, and authentication attempts.

<img width="1898" height="908" alt="Screenshot 2026-05-06 134237" src="https://github.com/user-attachments/assets/384993c8-800d-4b0f-9f61-273f7b0aec49" />

---

### 2️⃣ Validation Search for SSH Event Types

This search validates whether Splunk correctly extracted the `event_type` field from the SSH logs. The query groups all SSH activities into categories such as successful logins, failed login attempts, multiple failed authentications, and unauthenticated connections.

#### SPL Query Used

```spl
index=ssh_logs
| stats count by event_type
```

<img width="1909" height="971" alt="Screenshot 2026-05-06 134304" src="https://github.com/user-attachments/assets/907b82db-8f8c-4263-9784-19db7c68cf40" />

---

### 3️⃣ Failed SSH Login Attempt Analysis

This screenshot displays failed SSH login attempts grouped by source IP address. The analysis helps identify suspicious IPs attempting unauthorized access and can indicate brute-force or password-spraying attacks.

#### SPL Query Used

```spl
index=ssh_logs event_type="Failed SSH Login"
| stats count by id.orig_h
```

<img width="1918" height="971" alt="Screenshot 2026-05-06 134402" src="https://github.com/user-attachments/assets/9810ca55-7b08-4a54-a996-e87f99986536" />

---

### 4️⃣ Detection of Multiple Failed Authentication Attempts

This screenshot highlights repeated failed authentication attempts between source and destination hosts. Multiple failures from the same source IP may indicate brute-force attack activity targeting SSH services.

#### SPL Query Used

```spl
index=ssh_logs event_type="Multiple Failed Authentication Attempts"
| stats count by id.orig_h, id.resp_h
```

<img width="1913" height="956" alt="Screenshot 2026-05-06 134454" src="https://github.com/user-attachments/assets/f8d3c89c-1c06-4dfc-ae0b-2d5183c09c98" />

---

### 5️⃣ Monitoring Connections Without Authentication

This screenshot shows SSH connections where no authentication was completed. These events may indicate SSH scanning, probing activity, or incomplete connection attempts from suspicious systems.

#### SPL Query Used

```spl
index=ssh_logs event_type="Connection Without Authentication"
| stats count by id.orig_h
```

<img width="1918" height="951" alt="Screenshot 2026-05-06 134531" src="https://github.com/user-attachments/assets/15df6ce5-48e8-4b8a-a9bb-03527a6ed30c" />

---
````





#  Task 2: Analyze Failed Login Attempts

## Search Failed SSH Logins

```spl
index=ssh_logs event_type="Failed SSH Login"
| stats count by id.orig_h
```

## Objective

- Identify IP addresses generating failed logins
- Detect suspicious authentication activity
- Highlight top attacking source IPs

## Visualization

Create a **Bar Chart** showing:

- Source IP Address
- Number of Failed Login Attempts

---




#  Task 3: Detect Brute-force Attempts

## Search Multiple Failed Authentication Attempts

```spl
index=ssh_logs event_type="Multiple Failed Authentication Attempts"
| stats count by id.orig_h, id.resp_h
```

## Detect Repeated Login Failures

```spl
index=ssh_logs event_type="Multiple Failed Authentication Attempts"
| where auth_attempts > 5
```

## Splunk Alert Configuration

### Create Alert Conditions

Trigger an alert when:

- Any source IP performs more than **5 failed login attempts**
- Within a **10-minute window**

### Alert Purpose

This helps detect:

- SSH brute-force attacks
- Password spraying attempts
- Unauthorized access attempts

---

#  Task 4: Track Successful Logins

## Search Successful SSH Logins

```spl
index=ssh_logs event_type="Successful SSH Login"
| stats count by id.orig_h, id.resp_h
```

## Investigation Goals

- Identify legitimate and suspicious logins
- Compare successful logins with previous failed attempts
- Detect potential compromised accounts

## Dashboard Visualization

Create a dashboard panel showing:

- Top source IPs
- Successful login counts
- Frequently targeted systems

---

#  Task 5: Detect Connections Without Authentication

## Search Unauthenticated SSH Connections

```spl
index=ssh_logs event_type="Connection Without Authentication"
| stats count by id.orig_h
```

## Time-based Monitoring

```spl
index=ssh_logs event_type="Connection Without Authentication"
| timechart count by id.orig_h
```

## Investigation Goals

- Detect SSH probing attempts
- Identify port scanning behavior
- Monitor repeated incomplete SSH sessions

---

#  Dashboard Components

The Splunk dashboard includes:

- Failed Login Attempts by IP
- Successful Login Activity
- Brute-force Detection Panel
- SSH Connections Without Authentication
- Timechart of Suspicious Activity

---

#  Alerts Configured

| Alert Name | Purpose |
|------------|----------|
| SSH Brute Force Detection | Detect excessive failed login attempts |
| Suspicious SSH Activity | Monitor repeated unauthenticated connections |
| High Failed Login Count | Detect password spraying attacks |

---

#  Key Outcomes

By completing this project, I was able to:

- Ingest and analyze SSH logs using Splunk
- Detect brute-force login attempts
- Investigate suspicious SSH activity
- Create visual dashboards for monitoring
- Configure real-time security alerts
- Gain hands-on SOC Analyst experience

---

#  SOC Analyst Skills Demonstrated

- Log Analysis
- Threat Detection
- Security Monitoring
- Incident Investigation
- SIEM Operations
- SPL Query Writing
- Dashboard Development
- Alert Engineering

---

# 📚 Conclusion

This project provided practical hands-on experience with Splunk SIEM and SSH log analysis. It strengthened my understanding of SOC operations, threat detection, and security monitoring workflows commonly used by cybersecurity analysts in real-world environments.

The project demonstrates foundational SOC Analyst skills and serves as a valuable cybersecurity portfolio project.

---
