# TA-smartermail

Splunk Technology Add-on for SmarterMail (Windows) logs with CIM Email alignment.

## Supported Log Types
- `smtpLog.log`
- `delivery.log`
- `imapLog.log`
- `popLog.log`
- `administrative.log`
- `generalErrors.log`
- `spamChecks.log`
- `activation.log`

## Installation
1. Copy the `TA-smartermail` folder to `$SPLUNK_HOME/etc/apps/`.
2. Restart Splunk on the Search Head.
3. (Optional) Deploy to Universal Forwarders via Deployment Server if you need the `props.conf` line-breaking rules applied at index time.

## Configuration
This TA assumes logs are ingested with:
- `sourcetype = smartermail:log`
- `index = mail` (adjust as needed)

Ensure your `inputs.conf` on the UF uses the correct sourcetype.

## CIM Alignment & Splunk ES Compatibility
This TA is designed to work with Splunk Enterprise Security (ES) by mapping key fields to the **Email Data Model** of the Splunk Common Information Model (CIM).

### Key CIM Fields
| Field | Description | CIM Alignment | Notes |
|-------|-------------|---------------|-------|
| `action` | Result of the delivery attempt | ✅ Yes | Normalized to CIM values: `delivered`, `blocked`, `failed` |
| `protocol` | Email protocol involved | ✅ Yes | Extracted from the log filename and mapped to: `smtp`, `imap`, `pop3` |
| `src` | Client/Sender IP address | ✅ Yes | Extracted from `_raw` or `Binding to local IP` in delivery logs |
| `src_user` | Sender / login user | ✅ Yes | |
| `recipient` | Email recipient | ✅ Yes | |
| `dest` | Destination host | ✅ Yes | Aliased from `dest_host` |
| `message_id` | Globally unique message ID | ✅ Yes | |
| `size` | Message size in bytes | ✅ Yes | Extracted from `SIZE=` |
| `subject` | Message subject | ✅ Yes | Extracted from `Subject:` header if present |

### Tagging
The TA applies the `email` tag to all SmarterMail events, enabling the CIM Email Data Model to recognize and index them. This is essential for ES correlation searches and dashboards to work correctly.

## Extracted Fields (Full List)
| Field | Description | CIM Alignment |
|-------|-------------|---------------|
| `session_id` | SmarterMail session ID | ❌ No |
| `status_code` | SMTP response code | ✅ Yes (as `status_code`) |
| `status_message` | SMTP response message | ❌ No |
| `error_domain` | Domain with DKIM error | ❌ No |
| `dkim_selector` | DKIM selector | ❌ No |
| `dest_host` | Remote MX hostname | ✅ Yes (aliased to `dest`) |
| `mx_preference` | MX record preference | ❌ No |
| `user` | Login username | ✅ Yes (as `user`) |

## Lookup Files
The TA uses two CSV lookup files located in the `lookups/` directory for value normalization:
- `smartermail_cim_action_map.csv`: Maps raw delivery statuses (e.g., `Delivered`, `Bounced`) to CIM-compliant `action` values.
- `smartermail_cim_protocol_map.csv`: Maps log filename types (e.g., `delivery`, `smtpLog`) to CIM-compliant `protocol` values.

## Saved Searches
This TA includes pre-built saved searches to help you monitor security and operational events. After installation, you can find them under **Settings > Searches, reports, and alerts**.

| Report Name | Description | Schedule |
|-------------|-------------|----------|
| `SmarterMail - Failed Login Attempts by IP and User` | Identifies brute-force login attempts by correlating source IP and targeted user within a session. | Hourly |
| `SmarterMail - Top Source IPs for Failed Logins` | Shows the top source IPs with the most failed login attempts. | Every 6 hours |

## Important: Data Model Acceleration
After installing or updating this TA, you must rebuild the **Email** Data Model Acceleration in Splunk ES:
1. Go to **Settings > Data Models > Email**.
2. Set the **Summary Range** to a reasonable period (e.g., 7 Days) to speed up the process.
3. Click **Rebuild** and wait for the status to reach 100%.

## Version
1.0.3

---

## 📦 Installation from Splunkbase

The official, validated version of this Add-on is available on Splunkbase:
[https://splunkbase.splunk.com/app/10111](https://splunkbase.splunk.com/app/10111)

## 💻 Source Code

The source code for this Add-on is available on GitHub:
[https://github.com/your-username/smartermail-addon](https://github.com/your-username/smartermail-addon)

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](https://github.com/your-username/smartermail-addon/issues).

## 📄 License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.
