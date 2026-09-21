# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!


---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

</br>

# AIOps Monitoring and Event Processing

## Scenario

This project monitors a synthetic payment service. It analyses response time,
CPU usage, memory usage, and application log levels to identify operational
problems.

## Operational Problem

The objective is to detect slow payment requests, high resource usage, and
error logs before they affect service reliability.

## Operational Data

The data is stored in data/service_data.json. Each record contains a timestamp,
service name, response time, CPU percentage, memory percentage, log level, and
message.

There are 10 records. Eight represent normal behavior. Two records, at
10:05 and 10:06, represent abnormal behavior.

## Observations

Normal records have response times between 120 and 150 ms, CPU usage between
42% and 50%, memory usage between 51% and 57%, and INFO log levels.

The 10:05 record has a response time of 610 ms and an ERROR log indicating a
payment service timeout.

The 10:06 record has a response time of 640 ms, CPU usage of 94%, memory usage
of 91%, and an ERROR log indicating a database connection timeout.

## Detection Results

The detector uses these thresholds:

- Response time greater than 500 ms
- CPU usage greater than 80%
- Memory usage greater than 80%
- ERROR log level

Two anomalies were detected:

1. 10:05: high response time and an error log.
2. 10:06: high response time, high CPU, high memory, and an error log.

No normal records were incorrectly flagged.

## Event Processing Flow

Operational data is read by the pipeline. The AnomalyDetector examines each
record. When an anomaly is found, an event is generated and sent to the
EventProducer.

The producer publishes the event to the in-memory anomaly-events topic. The
EventConsumer reads from the same topic and returns the processed events as
the final AIOps output.

## Problems Identified and Corrected

The detector originally checked for WARNING logs, but the data uses ERROR logs.
The condition was corrected to detect ERROR.

The producer and consumer originally used different EventTopic objects. The
consumer therefore received zero events. Both components were changed to use
the same anomaly-events topic.

## Final Execution Result

- Records processed: 10
- Anomalies detected: 2
- Events published: 2
- Events consumed: 2
- Final AIOps events: 2

## Limitation and Improvement

The detector uses fixed thresholds and does not learn normal behavior over
time. A possible improvement would be to calculate service-specific baselines
and detect deviations from historical patterns.

## Reproduction

Run:

    python3 -m pip install -r requirements.txt
    python3 -m pytest -q
    python3 src/aiops_pipeline.py
