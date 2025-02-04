Postmortem: API Gateway Outage

Issue SummaryDuration: January 24, 2025, from 13:15 UTC to 14:45 UTC (1 hour 30 minutes)Impact: Our API Gateway became unresponsive, leading to degraded performance and outright failures for approximately 60% of users. Web and mobile applications relying on the API experienced slow responses or complete downtime, preventing users from logging in and accessing key services.Root Cause: A misconfigured rate-limiting rule caused a cascade failure, leading to an overload of the API Gateway’s request queue and eventual exhaustion of available worker threads.

Timeline

13:15 UTC - Monitoring alerts detected increased response times on the API Gateway.

13:20 UTC - Engineers began investigating API Gateway logs, suspecting a networking issue.

13:30 UTC - User reports started coming in about login failures and slow response times.

13:40 UTC - Assumed a database slowdown was contributing to API failures; checked database metrics but found no anomalies.

13:55 UTC - Noticed an increase in HTTP 429 (Too Many Requests) errors, indicating rate-limiting was being triggered.

14:05 UTC - Identified a recent configuration change to the rate-limiting system that was incorrectly throttling legitimate traffic.

14:15 UTC - Rolled back the misconfigured rate-limiting rule.

14:30 UTC - API Gateway performance stabilized as request queues cleared.

14:45 UTC - Full service restored, and monitoring confirmed normal operation.

Root Cause and ResolutionThe outage was caused by a misconfigured rate-limiting rule applied during a routine security update. The rule mistakenly throttled API requests more aggressively than intended, preventing legitimate traffic from reaching backend services. As a result, requests began queuing up, leading to thread exhaustion and API unresponsiveness.

The resolution involved identifying the faulty configuration, rolling back the changes, and restarting the affected API Gateway instances to clear queued requests. Once the rollback was applied, normal traffic flow resumed, and service performance returned to expected levels.

Corrective and Preventative MeasuresTo prevent similar incidents in the future, we will implement the following actions:

Improve Change Validation: Require all rate-limiting changes to be tested in a staging environment before deployment.

Enhance Monitoring: Add detailed alerts for abnormal increases in HTTP 429 errors to detect excessive throttling earlier.

Automated Rollback Mechanism: Implement automatic rollback procedures for API Gateway configuration changes if abnormal traffic drops are detected.

Incident Response Training: Conduct a training session for on-call engineers to better recognize and diagnose rate-limiting issues.

Configuration Management Review: Introduce stricter approval processes for API Gateway settings changes.

By implementing these measures, we aim to enhance system reliability and prevent similar outages in the future.

