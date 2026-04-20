# Performance Test - BlazeDemo

## Scenario
Flow covered: airline ticket purchase with purchase confirmation on BlazeDemo.

## Tool used
- Apache JMeter 5.6.3
- Plugin bzm Arrivals Thread Group

## Test Data
The dynamic data used by the test is in the data folder:

- departure_cities.csv
- destination_cities.csv
- flights.csv
- passengers_100.csv

## Validated Load Test Configuration
The load configuration that produced the best validated result so far was:

- Start Threads Count: 75 (distributed in multiple rows)
- Initial Delay (sec): 0
- Startup Time (sec): 120
- Hold Load For (sec): 300
- Shutdown Time (sec): 0
- Connect Timeout: 5000 ms
- Response Timeout: 5000 ms

## Validated Spike Test Configuration
The spike test configuration used was:

- 7 rows, each with:
    - Start Threads Count: 17
    - Initial Delay (sec): 0, 2, 4, 6, 8, 10, 12 (each row adds +2)
    - Startup Time (sec): 20
    - Hold Load For (sec): 120
    - Shutdown Time (sec): 20

## Load Test Configuration Used
For the load test, the following configuration was used:

- 7 rows, each with:
    - Start Threads Count: 17
    - Initial Delay (sec): 0
    - Startup Time (sec): 120
    - Hold Load For (sec): 300
    - Shutdown Time (sec): 60

This setup ramps up to 119 concurrent threads over 2 minutes, holds the load for 5 minutes, and then ramps down over 1 minute. The choice of 17 threads per row and 7 rows was empirical, aiming to approach the throughput target while keeping the system stable. The configuration is not mathematically optimized, but was iteratively adjusted based on observed system behavior and resource constraints.

## Spike Test Configuration Used
For the spike test, the following configuration was used:

- 7 rows, each with:
    - Start Threads Count: 19
    - Initial Delay (sec): 0
    - Startup Time (sec): 20
    - Hold Load For (sec): 20
    - Shutdown Time (sec): 20

This configuration quickly ramps up to 133 concurrent threads in 20 seconds, holds the spike for 20 seconds, and then ramps down in 20 seconds. The intent was to simulate a sudden surge in users, but the specific values were chosen more for practical experimentation than for strict theoretical modeling. The numbers reflect a balance between pushing the system and avoiding total overload, based on trial and error.

## Chosen Values
The values above were selected based on iterative testing and practical constraints. They are not theoretically optimal, but reflect what was achievable and stable in the given environment. In this context, the configurations provided a reasonable balance between system stress and test reliability, but do not necessarily represent best practices for all scenarios.

## Load Test Result
With this configuration, the following metrics were observed:

- Throughput: 65.8 per second
- Error %: 0.00%
- Average: 1212 ms
- 90% Line: 1345 ms
- 95% Line: 1410 ms
- 99% Line: 1642 ms

## Load Test Conclusion
The load test result was satisfactory:

- the 90th percentile was below 2 seconds;
- there were no errors;
- the observed throughput was consistent with the expected target for the complete scenario.

## Test Execution
1. Open the test plan in JMeter.
2. Leave only the load group enabled for the load test, and only the spike group enabled for the spike test.
3. Disable the View Results Tree for the final execution.
4. Clear previous results with Run and Clear All.
5. Start the test with the Start button.
6. Analyze the Summary Report and Aggregate Report at the end.

## Note
If JMeter gets stuck at shutdown or if the reports are empty, restart JMeter and run the saved plan again.
