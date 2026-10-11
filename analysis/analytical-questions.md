# Operational Stability (Real-Time Monitoring)

### Analytical Question: 

What are the current indicators of client crashes and network latency by region over the past 15 minutes to trigger automated DevOps alerts?

### Metric and Grain:

Measure: Crash rate percentage and average network ping latency (ms).

Grouping Dimensions: Region/Country, Device OS Version, Game Build Version.

Grain: One aggregated metric row per region and game version per 5-minute time window.

### Required Data: 

game_events (Bronze/Silver tables), fields: event_type (crash, ping), timestamp, player_id, region, device_os, joined with static geo-reference data.

### Freshness: 

Real-time view needed within a maximum acceptable delay of 5 minutes.

### Decision and User: 

Acted upon by DevOps Engineers to instantly deploy hotfixes, restart failing backend microservices, or rollback corrupted client updates. Success is measured by reducing incident detection time below 10 minutes.

### Limitations: 

Client crash reports may be delayed if the user's device loses internet connection immediately after the crash, leading to a temporary undercounting in the active window.

# Financial Performance (Daily Monetization)

### Analytical Question: 

What is the total gross revenue and Average Revenue Per Paying User (ARPPU) across different paying segments over the past completed day?

### Metric and Grain:

Measure: Gross Revenue ($), ARPPU ($), total successful transaction count

Grouping Dimensions: Currency, Payment Channel (Google Play / App Store), User Spend Tier (Whales, Minnows)

Grain: One summary row per transaction category and segment per day

### Required Data: 

in_app_purchases and user_profiles tables, fields: purchase_id, amount_usd, status, user_id, purchase_timestamp

### Freshness: 

Required daily, with a maximum acceptable delay of 24 hours (next-day financial report)

### Decision and User: 

Acted upon by the Finance Department and Monetization Managers to evaluate daily revenue performance, adjust in-game pricing, or plan promotional events. Success is measured by tracking daily financial targets and identifying revenue drops early.

### Limitations: 

Delayed webhooks from app stores for offline purchases may result in transactions rolling over into the subsequent day's report.

# Product Churn Analysis (Historical Progression)

### Analytical Question: 

At what specific campaign level does the highest player drop-off occur among users from the EU region over the last quarter?

### Metric and Grain:

Measure: Churn rate percentage, absolute drop-off count, average time spent on level.

Grouping Dimensions: Campaign Level Number, Device Type, User Acquisition Channel.

Grain: One row per campaign level ID for the selected quarterly cohort.

### Required Data: 

Historical aggregated logs from the Gold Zone, fields: level_id, event_type (level_start, level_fail, level_complete), player_id, region, timestamp.

### Freshness: 

Historical analysis with a refresh cadence of 1 week / monthly.

### Decision and User: 

Acted upon by Game Designers and Level Balance Teams to redesign difficult bottlenecks, adjust enemy difficulty scaling, and improve early-game retention. Success is measured by a 15% increase in level completion rates after design adjustments.

### Limitations: 

Churn caused by external factors (for example, internet outages, device upgrades) cannot be fully isolated from difficulty-based churn, introducing minor confounding bias.