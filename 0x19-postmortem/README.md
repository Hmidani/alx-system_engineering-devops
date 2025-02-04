#0x19-postmortem task using webstack debugging #1
![Technical Issue Resolution](./0x19-postmortem/API_outage_image.jpg.webp)

# Issue Summary
Duration of the Outage: January 24, 2025, from 13:15 UTC to 14:45 UTC (1 hour 30 minutes)

# Impact:
Picture this: You're about to log in, but the API says, "Nope!" That was the reality for 60% of our users. Web and mobile apps slowed to a crawl or stopped working altogether. Our customer support inbox turned into a digital bonfire of complaints, while our monitoring system screamed in panic.

# Root Cause:
A well-meaning but overly aggressive rate-limiting rule decided that too many legitimate requests were suspicious, throttling everyone into oblivion. The API Gateway choked, requests piled up, and the system tapped out.

# Timeline

13:15 UTC - Monitoring alerts started flashing red like a Christmas tree, indicating sluggish API response times.

13:20 UTC - Engineers jumped into action, initially suspecting a network issue.

13:30 UTC - Users began complaining en masse: "Hey, why can't I log in?"

13:40 UTC - Focus shifted to database performance, but everything looked normal (cue confusion).

13:50 UTC - Load balancer logs checked – still no culprit in sight.

13:55 UTC - A flood of HTTP 429 errors ("Too Many Requests") was discovered, pointing toward rate limiting.

14:05 UTC - A deep dive revealed that a new rate-limiting rule was treating everyone like an evil bot.

14:10 UTC - Escalated to the infrastructure team. Panic mode activated.

14:15 UTC - Rolled back the problematic rate-limiting configuration.

14:30 UTC - The system breathed a sigh of relief as request queues cleared.

14:45 UTC - Full service restored, and our engineers took a well-deserved coffee break.

# Root Cause and Resolution

Root Cause and ResolutionThe culprit? An overzealous rate-limiting rule designed to curb excessive API requests. Instead of blocking just the bad actors, it swung its mighty hammer at everyone, causing legitimate traffic to be throttled into oblivion. This led to a backlog of requests, which in turn exhausted API Gateway resources.

# Resolution:

Rolled back the faulty rate-limiting configuration.

Restarted API Gateway instances to flush out clogged request queues.

Monitored system recovery and confirmed that normal request handling resumed.

# Corrective and Preventative Measures

Corrective and Preventative MeasuresTo prevent another "APIpocalypse," we’re implementing the following:

# Sanity Check Before Deploying Limits: 
Test rate-limiting changes in staging before they wreak havoc in production.

# Enhanced Monitoring: 
Custom alerts for unexpected HTTP 429 spikes – because no one likes surprises.

# Automated Rollback Mechanism: 
If rate limits tank traffic unexpectedly, roll back immediately.

# Engineer Training: 
Ensure on-call teams can recognize and diagnose throttling issues faster than you can say "rate limit exceeded."

# Better Logging: 
Differentiate between legitimate and suspicious traffic, so debugging isn’t an episode of CSI: API Gateway.

By implementing these measures, we aim to keep our API Gateway from turning into a digital gatekeeper gone rogue. Lessons learned, battle scars earned – on to the next deployment (hopefully with fewer surprises).

