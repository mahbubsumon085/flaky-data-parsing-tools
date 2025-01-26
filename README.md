# Project Test Automation and Statistics Generator

This repository primarily contains Python and Bash scripts for parsing and generating statistics for flaky unit tests across multiple iterations. It supports various flaky test types, including order-dependent (OD), time-dependent (TD), implementation-dependent (ID), and more.  

The scripts are adapted from the [Azure Tools repository](https://github.com/winglam/azure-tools) to extend functionality for diverse flaky test scenarios and provide enhanced automation and analysis.

## Important Files

The following are the key files for test analysis and in generating statistics:

- **`parse_surefire_report.py`**:  
  A python script that processes Maven Surefire XML reports to extract detailed test case results. It parses the report to identify the status of each test case (pass, failure, or error), along with execution time, and outputs the results in a structured format for further analysis.

- **`statistics_generator.sh (For timining dependent flaky test)`**:  
  A Bash script designed to automate the process of running a specified test iteratively, analyzing results, and generating comprehensive statistics for timing dependent flaky tests. The script:
  - Builds the project with Maven while skipping tests and unnecessary checks.
  - Executes the test for a given number of iterations (default: 100) and logs results for each run.
  - Uses the `parse_surefire_report.py` script to extract and format test results from Surefire XML reports.
  - Organizes logs and test result files into structured directories (`flaky-result`).
  - Calculates and summarizes the counts of test passes, failures, and errors in a summary file.
  - Saves the results in `rounds-test-results.csv` and outputs a summary to `flaky-result/summary.txt`.

- **`od_statistics_generator.sh (For order-dependent flaky tests)`**:  
  A Bash script designed to analyze and generate statistics for order-dependent flaky tests by running a flaky test along with its preceding test in a specific order for multiple iterations. The script:
  - Builds the Maven project with necessary options while skipping unnecessary checks.
  - Executes the specified tests (`precedingtest` and `flakytest`) in the desired order for a given number of iterations (default: 100).
  - Uses the `parse_surefire_report.py` script to process Surefire XML reports and extract detailed results for the flaky test.
  - Organizes logs, test results, and summaries into structured directories (`flaky-result`).
  - Outputs a summary of test passes, failures, and errors to `flaky-result/summary.txt`.

### Example Usage

To run timing dependent `SlowBookieTest#testSlowBookie` test in the `bookkeeper-server` module with 100 iterations for statistics_generator.sh run in your source code directory.

```bash
chmod +x statistics_generator.sh
./statistics_generator.sh bookkeeper-server scripts org.apache.bookkeeper.client.SlowBookieTest#testSlowBookie 100
```

To run order dependent flaky test `testGetServerSideGroups`  that depends on running earler `testLogin` from class `TestUserGroupInformation` in the `hadoop-common-project/hadoop-common` module with 100 iterations for statistics_generator.sh run in your source code directory.

```bash
chmod +x od_statistics_generator.sh
./od_statistics_generator.sh hadoop-common-project/hadoop-common org.apache.hadoop.security.TestUserGroupInformation#testGetServerSideGroups org.apache.hadoop.security.TestUserGroupInformation#testLogin 100
```