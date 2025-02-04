Postmortem: API Gateway Outage

Issue SummaryDuration of the Outage: January 24, 2025, from 13:15 UTC to 14:45 UTC (1 hour 30 minutes)Impact: Our API Gateway became unresponsive, affecting approximately 60% of users. Web and mobile applications relying on the API experienced slow responses or complete downtime, preventing users from logging in and accessing key services. Customer complaints surged, and automated monitoring flagged high failure rates across multiple services.Root Cause: A misconfigured rate-limiting rule caused excessive throttling of legitimate traffic. This led to a cascade failure, overloading the API Gateway’s request queue and exhausting all available worker threads, rendering the service unresponsive.

Timeline

13:15 UTC - Monitoring alerts detected increased response times on the API Gateway.

13:20 UTC - Engineers began investigating API Gateway logs, suspecting a networking issue.

13:30 UTC - User reports started coming in about login failures and slow response times.

13:40 UTC - Assumed a database slowdown was contributing to API failures; checked database metrics but found no anomalies.

13:50 UTC - Investigated load balancer logs, suspecting uneven traffic distribution, but found no issues.

13:55 UTC - Noticed an increase in HTTP 429 (Too Many Requests) errors, indicating excessive throttling.

14:05 UTC - Identified a recent configuration change to the rate-limiting system that was incorrectly throttling legitimate traffic.

14:10 UTC - Escalated the issue to the infrastructure team for immediate rollback.

14:15 UTC - Rolled back the misconfigured rate-limiting rule.

14:30 UTC - API Gateway performance stabilized as request queues cleared.

14:45 UTC - Full service restored, and monitoring confirmed normal operation.

Root Cause and ResolutionThe outage stemmed from a misconfigured rate-limiting rule applied during a routine security update. Instead of throttling only excessive API calls from single clients, the rule mistakenly applied aggressive rate limits across all users. As a result, legitimate requests were blocked, causing a buildup of queued requests and ultimately leading to thread exhaustion.

Resolution:

Rolled back the faulty rate-limiting configuration.

Restarted API Gateway instances to clear queued requests.

Monitored system recovery to ensure normal request handling resumed.

Corrective and Preventative MeasuresTo prevent similar incidents in the future, the following measures will be implemented:

Improve Change Validation: Require all rate-limiting changes to be tested in a staging environment before deployment.

Enhance Monitoring: Add detailed alerts for abnormal increases in HTTP 429 errors to detect excessive throttling earlier.

Automated Rollback Mechanism: Implement automatic rollback procedures for API Gateway configuration changes if abnormal traffic drops are detected.

Incident Response Training: Conduct a training session for on-call engineers to better recognize and diagnose rate-limiting issues.

Configuration Management Review: Introduce stricter approval processes for API Gateway settings changes.

Add Logging Improvements: Enhance logging to differentiate between legitimate and excessive traffic, making debugging easier in future incidents.

By implementing these measures, we aim to enhance system reliability and prevent similar outages in the future.

Configuration Management Review: Introduce stricter approval processes for API Gateway settings changes.

By implementing these measures, we aim to enhance system reliability and prevent similar outages in the future.

