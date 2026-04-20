# Test Environment Configuration with CSV in JMeter


## Purpose
This document describes, step by step and in detail, how to configure the BlazeDemo ticket purchase scenario in JMeter using CSV files as a data source.

The goals are:

- keep the functional flow stable;
- use dynamic data for cities, flights, and passengers;
- configure timeout to avoid tests getting stuck at the end;
- first validate the functional flow;
- then run the load and spike tests.

---


## Expected Data Files Structure
In the project's data folder, the files used are:

- departure_cities.csv
- destination_cities.csv
- flights.csv
- passengers_100.csv

These files must exist before starting the configuration.

---


## Step 1 - Restart JMeter and Prepare the Test Plan
If JMeter got stuck after a previous run, or if the reports were empty, restart JMeter before running again.

### How to restart JMeter
1. If a test is running, click the Stop or Stop Now button.
2. Wait a few seconds.
3. Click File.
4. Click Save or Save Test Plan As to save the plan.
5. Close the JMeter window.
6. Open JMeter again using the jmeter.bat file inside the bin folder of the installation.
7. In the File menu, click Open.
8. Select your test plan file.

### Now leave only the load group enabled
1. In the tree on the left, locate the spike group.
2. Right-click it.
3. Click Disable.
4. Make sure the load group remains enabled.

This prevents running two tests at the same time.

---

## Step 2 - Build and Configure the Entire Load Group
In this step, you prepare the entire structure of the main load group: initial rate, listeners, CSVs, and the purchase flow.

### 2.1 Configure the Ultimate Thread Group for Initial Validation
At first, do not use the final load. First, perform a controlled functional test.

1. Right-click your Test Plan > Add > Threads (Users) > jp@gc - Ultimate Thread Group.
2. Rename it to "jp@gc - Ultimate Thread Group - load".
3. Click "Add Row" in the "Threads Schedule" table.
4. Fill in as follows:
	- Start Threads Count: 1
	- Initial Delay (sec): 0
	- Startup Time (sec): 5
	- Hold Load For (sec): 10
	- Shutdown Time (sec): 0

These numbers are only to check if the flow is functional. They are not the final values for the performance test.

### 2.2 Add Listeners to the Load Group
In the load group, create these listeners:

- Summary Report
- Aggregate Report
- View Results Tree

#### How to add the View Results Tree
1. Right-click the load group.
2. Click Add.
3. Click Listener.
4. Click View Results Tree.

This listener should only be used during the functional validation phase.

### 2.3 Configure HTTP Request Defaults, including timeout
1. In the tree on the left, click HTTP Request Defaults.
2. In the Basic tab, fill in:

- Protocol: https
- Server Name or IP: www.blazedemo.com
- Port Number: leave blank
- Path: leave blank

#### Important
Do not enter the full URL.

#### Correct
- Protocol: https
- Server Name or IP: www.blazedemo.com


#### Incorrect
- https://www.blazedemo.com
- https://www.blazedemo.com/
- www.blazedemo.com/


3. Still in HTTP Request Defaults, click the Advanced tab.
4. Look for the timeout fields.
5. Fill in as follows:

- Connect Timeout: 5000
- Response Timeout: 5000

If you prefer a little more slack at first, you can use Response Timeout of 7000, but 5000 is a good setting for this scenario.


### 2.4 Configure the HTTP Cookie Manager
1. Click HTTP Cookie Manager.
2. Leave the default configuration.
3. No special settings are needed here.

#### Role of the Cookie Manager
Even without manual configuration, it stores and automatically resends session cookies, better simulating browser behavior. In this BlazeDemo scenario, it is optional because the flow is simple, but it is good practice to keep it enabled.


### 2.5 Add the CSVs in the Same Load Group
You will create 4 CSV readers within the same group.

#### CSV - Departure
1. Right-click the load group.
2. Click Add.
3. Click Config Element.
4. Click CSV Data Set Config.
5. Fill in as follows:

- Name: CSV - Departure
- Filename: select the departure_cities.csv file inside the data folder
- File Encoding: UTF-8
- Variable Names: leave blank
- Ignore first line: False
- Delimiter: ,
- Recycle on EOF: True
- Stop thread on EOF: False
- Sharing mode: All threads


#### CSV - Destination
1. Right-click the load group.
2. Click Add.
3. Click Config Element.
4. Click CSV Data Set Config.
5. Fill in as follows:

- Name: CSV - Destination
- Filename: select the destination_cities.csv file inside the data folder
- File Encoding: UTF-8
- Variable Names: leave blank
- Ignore first line: False
- Delimiter: ,
- Recycle on EOF: True
- Stop thread on EOF: False
- Sharing mode: All threads


#### CSV - Flights
1. Right-click the load group.
2. Click Add.
3. Click Config Element.
4. Click CSV Data Set Config.
5. Fill in as follows:

- Name: CSV - Flights
- Filename: select the flights.csv file inside the data folder
- File Encoding: UTF-8
- Variable Names: leave blank
- Ignore first line: False
- Delimiter: ,
- Recycle on EOF: True
- Stop thread on EOF: False
- Sharing mode: All threads


#### CSV - Passengers
1. Right-click the load group.
2. Click Add.
3. Click Config Element.
4. Click CSV Data Set Config.
5. Fill in as follows:

- Name: CSV - Passengers
- Filename: select the passengers_100.csv file inside the data folder
- File Encoding: UTF-8
- Variable Names: leave blank
- Ignore first line: False
- Delimiter: ,
- Recycle on EOF: True
- Stop thread on EOF: False
- Sharing mode: All threads


### 2.6 Configure the Entire Purchase Flow Inside the Transaction Controller
1. Click Transaction Controller.
2. Look for the option Generate parent sample.
3. Check this option.

This makes JMeter measure the entire purchase transaction as a single unit.


#### 01 - Home
1. Click 01 - Home.
2. In the right panel, configure:

- Name: 01 - Home
- Method: GET
- Path: /

Do not add parameters.


#### 02 - Find Flights
1. Click 02 - Find Flights.
2. Configure:

- Name: 02 - Find Flights
- Method: POST
- Path: /reserve.php

3. In the Send Parameters With the Request area, click Add.
4. Create the first row:

- Name: fromPort
- Value: ${departureCity}

5. Click Add again.
6. Create the second row:

- Name: toPort
- Value: ${destinationCity}


#### 03 - Choose Flight
1. Click 03 - Choose Flight.
2. Configure:

- Name: 03 - Choose Flight
- Method: POST
- Path: /purchase.php

3. In the parameters area, click Add several times and create the rows below:

- flight = ${flight}
- price = ${price}
- airline = ${airline}
- fromPort = ${departureCity}
- toPort = ${destinationCity}


#### 04 - Purchase Flight
1. Click 04 - Purchase Flight.
2. Configure:

- Name: 04 - Purchase Flight
- Method: POST
- Path: /confirmation.php

##### Very important
This request must be POST. If it is GET, the purchase will fail.

3. In the parameters area, click Add to create the following rows:

- _token = empty
- inputName = ${firstName}
- address = ${address}
- city = ${city}
- state = ${state}
- zipCode = ${zipCode}
- cardType = ${cardType}
- creditCardNumber = ${creditCardNumber}
- creditCardMonth = ${creditCardMonth}
- creditCardYear = ${creditCardYear}
- nameOnCard = ${nameOnCard}
- rememberMe = on

4. Make sure the Path field contains only /confirmation.php.
5. Do not enter the full URL in Path.
6. Do not manually build the query string in Path.


#### Assert Purchase Success
1. Right-click 04 - Purchase Flight.
2. Click Add.
3. Click Assertions.
4. Click Response Assertion.
5. Fill in as follows:

- Name: Assert Purchase Success
- Apply to: Main sample only
- Field to Test: Response Text
- Pattern Matching Rules: Contains

6. Click Add in the patterns area.
7. Enter exactly the phrase below:

Thank you for your purchase today!

---


## Step 3 - Run the Initial Functional Validation
1. In the top menu, click Run.
2. Click Clear All.
3. Click the Start button.
4. Wait for the test to finish.
5. Click View Results Tree.

### What should happen
All requests should be green:

- 01 - Home
- 02 - Find Flights
- 03 - Choose Flight
- 04 - Purchase Flight

If any are red, fix them before proceeding.

---


## Step 4 - Restart JMeter if Shutdown Freezes
If the test finishes the load but the timer keeps running, or if the reports are empty:

1. Save the plan.
2. Stop the test.
3. Close JMeter.
4. Open JMeter again.
5. Reopen the saved plan.

In practice, this restart usually solves the problem of empty reports or the interface freezing at shutdown.

---


## Step 5 - Prepare the Environment for the Real Load Test
Before running the real load:

1. Right-click View Results Tree.
2. Click Disable.

Leave only the following enabled:

- Summary Report
- Aggregate Report

---



## Step 6 - Configuration and Execution of the Official Load Test
Once the functional flow is stable, use these values in the load group. These were the values that produced a satisfactory result in the validated load test.

1. Click "jp@gc - Ultimate Thread Group - load".
2. In the "Threads Schedule" table, configure:
	- Start Threads Count: 75
	- Initial Delay (sec): 0
	- Startup Time (sec): 120
	- Hold Load For (sec): 300
	- Shutdown Time (sec): 0

If you want a step ramp-up, add multiple rows, for example:
	- 12 rows of 6 or 13 rows of 6 (adjust to total 75), each with increasing Initial Delay and lower Startup Time (e.g., 10s each), to simulate a progressive ramp-up.

### How to execute
1. In the top menu, click Run.
2. Click Clear All.
3. Click Start.
4. Wait for the test to finish by itself.
5. Do not click Stop unless it really freezes for a long time.

### Reference result obtained with this configuration
- Throughput: 65.8 per second
- Error %: 0.00%
- Average: 1212 ms
- 90% Line: 1345 ms
- 95% Line: 1410 ms
- 99% Line: 1642 ms

These numbers were within the time goal and, considering the complete purchase flow, met the desired throughput.

---



## Step 7 - Configuration and Execution of the Spike Test
After the load test, run the spike test separately.

1. Right-click the load group and click Disable.
2. Right-click the spike group and click Enable.
3. Click "jp@gc - Ultimate Thread Group - spike".
4. In the "Threads Schedule" table, configure to simulate steps in ramp-up and ramp-down:

	 - For 7 steps, each row:
		 - Start Threads Count: 17
		 - Initial Delay (sec): 0, 2, 4, 6, 8, 10, 12 (each row adds +2)
		 - Startup Time (sec): 20
		 - Hold Load For (sec): 120
		 - Shutdown Time (sec): 20

	 - The "Expected parallel users count" chart will show the stepwise ramp-up and ramp-down.

	 - If you want 15 steps, use Start Threads Count: 5, Initial Delay: 0, 2, 4, ..., 28, Startup Time: 2, Hold Load For: 120, Shutdown Time: 2 for each row.

5. Click Run.
6. Click Clear All.
7. Click Start.

---


## Step 8 - Read the Reports and Conclude
### Summary Report
1. Click Summary Report.
2. Pay special attention to:

- Samples
- Average
- Error %
- Throughput

### Aggregate Report
1. Click Aggregate Report.
2. Pay special attention to:

- Average
- 90% Line
- 95% Line
- 99% Line
- Error %
- Throughput

### Most Important Metric for Acceptance
The 90% Line field represents the 90th percentile, which must be below 2000 ms to meet the time criterion.

### The challenge requires
- 250 requests per second
- 90th percentile response time below 2 seconds

### Therefore, in the final analysis you must check
- Sufficient throughput to meet the target
- 90% Line less than 2000 ms
- Error % ideally equal to 0

### If the environment meets the criterion
Write that:

- the ticket purchase scenario was executed successfully;
- the throughput reached the required target;
- the 90th percentile was below 2 seconds;
- the acceptance criterion was met.

### If the environment does not meet the criterion
Write that:

- the script worked correctly;
- the purchase flow was executed successfully;
- the environment did not sustain the required throughput or latency;
- the conclusion was based on throughput, 90th percentile, and error rate metrics.

---


## Quick Usage Order Summary
1. Restart JMeter if necessary.
2. Build the entire load group, including listeners, defaults, timeout, cookie manager, CSVs, and purchase flow.
3. Functionally validate with a low rate.
4. Restart JMeter if the interface freezes.
5. Disable the View Results Tree.
6. Run the load test.
7. Run the spike test.
8. Analyze the Summary Report and Aggregate Report.
9. Write the final conclusion.

### Most Important Metric for Acceptance
The 90% Line field represents the 90th percentile, which must be below 2000 ms to meet the time criterion.

---


## Step 24 - Understand the Acceptance Criteria
The challenge requires:

- 250 requests per second
- 90th percentile response time below 2 seconds

### Therefore, in the final analysis you must check
- Sufficient throughput to meet the target
- 90% Line less than 2000 ms
- Error % ideally equal to 0

---


## Step 25 - Configure the Spike Test
After the load test, run the spike test separately.

1. Right-click the load group.
2. Click Disable.
3. Right-click the spike group.
4. Click Enable.
5. Click the spike group.
6. Configure:

- Target Rate (arrivals/sec): 90
- Ramp Up Time (sec): 30
- Ramp-Up Steps Count: 3
- Hold Target Rate Time (sec): 120
- Time Unit: seconds
- Concurrency Limit: 1000
- Action to be taken after a Sampler error: Continue

7. Click Run.
8. Click Clear All.
9. Click Start.

---


## Step 26 - Best Practices During Execution
During the tests, follow these recommendations:

- run only one group at a time;
- leave the View Results Tree disabled during load and spike tests;
- keep the timeouts configured;
- use Clear All before each new run;
- restart JMeter if the interface freezes or if the reports do not load correctly.

---


## Step 27 - Test Conclusion
At the end, you should conclude based on the reports.

### If the environment meets the criterion
Write that:

- the ticket purchase scenario was executed successfully;
- the throughput reached the required target;
- the 90th percentile was below 2 seconds;
- the acceptance criterion was met.

### If the environment does not meet the criterion
Write that:

- the script worked correctly;
- the purchase flow was executed successfully;
- the environment did not sustain the required throughput or latency;
- the conclusion was based on throughput, 90th percentile, and error rate metrics.

---


## Quick Usage Order Summary
1. Restart JMeter if necessary.
2. Open the test plan.
3. Ensure only the load group is enabled.
4. Configure HTTP Request Defaults and timeouts.
5. Configure the four CSV Data Set Configs.
6. Review the four requests in the flow.
7. Functionally validate with a low rate.
8. Restart JMeter if the interface freezes.
9. Disable the View Results Tree.
10. Run the load test.
11. Run the spike test.
12. Analyze the Summary Report and Aggregate Report.
13. Write the final conclusion.
