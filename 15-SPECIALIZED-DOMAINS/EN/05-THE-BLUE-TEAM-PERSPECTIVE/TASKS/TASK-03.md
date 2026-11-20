# Task 03: Your First SIEM: Navigating Splunk

## Stage A: Original Task Content & Educational Context
Security Information and Event Management (SIEM) systems serve as the central nervous system of modern SOC operations. A SIEM performs five vital functions:
1. **Aggregation:** Collecting raw log streams across endpoints, domain controllers, firewalls, web servers, and cloud audit trails.
2. **Normalization:** Parsing diverse log schemas (JSON, W3C, CEF, Syslog, XML) into unified key-value fields.
3. **Indexing:** Compressing, indexing, and storing high-volume log data for ultra-fast text search.
4. **Correlation:** Linking disparate events across systems (e.g., matching a VPN connection from IP X with a failed database login from IP X).
5. **Alerting:** Triggering automated notices or SOAR playbooks when specific threat conditions are met.

### The Splunk Architecture & Data Model:
Splunk is the industry-standard big data SIEM platform. Its data structure relies on core metadata fields:
- **`index`:** The logical database partition where data is stored (e.g., `index=botsv1`).
- **`sourcetype`:** The format specification that tells Splunk how to interpret and parse the incoming event structure into fields (e.g., `WinEventLog:Security`, `iis`, `suricata`, `stream:http`, `fgt_utm`).
- **`source`:** The specific origin file path or network port from which the data arrived (e.g., `/var/log/apache2/access.log`, `WinEventLog://Security`).
- **`host`:** The hostname, FQDN, or IP address of the machine that generated the log.
- **`_time`:** The UNIX timestamp of the event, extracted and normalized during ingestion.
- **`_raw`:** The original, unparsed ASCII log string.

### The Boss of the SOC (BOTSv1) Dataset & The Time Picker Trap:
The lab provides an active Splunk instance pre-loaded with Splunk's famous **Boss of the SOC Version 1 (BOTSv1)** CTF dataset.
- A critical trap encountered by analysts is searching with Splunk's default time picker: `Last 24 hours`. Because BOTSv1 is a captured CTF dataset containing simulated attacks against a fictional company ("Wayne Corp") recorded on **August 10-11, 2016**, searching the last 24 hours returns zero results.
- The time range must always be adjusted to `All time` or set explicitly to **August 2016**.

---

## Stage B: Independent Thinking & OpSec Considerations
1. **Understanding Ingestion Delays:** Logs do not appear in SIEMs instantaneously; network forwarding, batch compression, and parsing pipelines introduce latency (typically 30 seconds to several minutes).
2. **Sourcetype Identification:** When testing a network, knowing which sourcetypes are actively ingested tells you where defenders are blind. If an organization ingests `WinEventLog:Security` but lacks endpoint process monitoring (`Sysmon`) or web application proxy logs (`stream:http`), attacks executed via PowerShell or web shells may go completely undetected.

---

## Stage C: Guided Hints (Tiered Progression)
- **Q1 Tier 1 Hint:** What Splunk field name tells the parser how to interpret the event format?
- **Q1 Tier 2 Hint:** One word: sourcetype.
- **Q2 Tier 1 Hint:** Execute the SPL query: `index=botsv1 | stats count by sourcetype`. Look at the total count of rows in the Statistics tab.
- **Q2 Tier 2 Hint:** 22 distinct sourcetypes.
- **Q3 Tier 1 Hint:** Inspect the timestamp of the earliest and latest events in the BOTSv1 dataset. In which month and year did the incident occur?
- **Q3 Tier 2 Hint:** Format: Month YYYY (e.g., August 2016).

---

## Stage D: Complete Practical Solution & Technical Deep Dive
### Executing Splunk Search Processing Language (SPL) Queries:

#### 1. Enumerating Distinct Sourcetypes in BOTSv1:
```spl
index=botsv1
| stats count by sourcetype
```
In the Splunk Web UI Statistics tab, the search outputs a table listing 22 distinct sourcetypes (including `WinEventLog:Security`, `WinEventLog:System`, `iis`, `suricata`, `stream:http`, `fgt_utm`, `fgt_traffic`, `ossec`, etc.).
- **Total distinct sourcetypes:** `22`

#### 2. Determining Dataset Generation Date:
```spl
index=botsv1
| stats earliest(_time) as first_event, latest(_time) as last_event
| eval first=strftime(first_event, "%B %Y"), last=strftime(last_event, "%B %Y")
| table first, last
```
Output confirms all events occurred between August 10, 2016 and August 11, 2016.
- **Date:** `August 2016`

---

## Stage E: Final Answer & Key Values
- **Question 1:** *What Splunk field classifies the format of ingested data and controls how Splunk parses events?*
  - **Answer:** `sourcetype`
- **Question 2:** *Log in to the Splunk VM and access the Search tab. Run: `index=botsv1 | stats count by sourcetype`. How many distinct sourcetypes appear in the results?*
  - **Answer:** `22`
- **Question 3:** *The BOTSv1 dataset returns zero results when searched with the default `Last 24 hours` time range. When was the dataset generated? (Answer Format: Month YYYY)*
  - **Answer:** `August 2016`

---

## Stage F: Practical Verification & Proof of Work
1. `sourcetype` is the foundational metadata field controlling parsing in Splunk.
2. The query `index=botsv1 | stats count by sourcetype` confirms exactly 22 distinct sourcetype entries.
3. The dataset timestamp spans `August 2016`. All three verified and validated on the platform.

---

## Stage G: Key Educational Takeaway
Navigating a SIEM requires mastering basic data model metadata (`index`, `sourcetype`, `source`, `host`). In forensic investigations and CTF environments, always ensure your search time range encompasses the target historical data window.
