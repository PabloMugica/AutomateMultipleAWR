GitHub Copilot: To automate Oracle AWR from multiple Oracle databases and hosts in a loop, you can use a combination of shell scripts and SQL scripts. Here's an overview of the steps involved:

1. Create a shell script to loop through the list of databases and hosts: The shell script should include a loop that iterates through a list of databases and hosts. You can use an array to store the database and host names, and a `for` loop to iterate through the array.

2. Create a SQL script to generate the AWR report: The SQL script should include the necessary commands to generate the AWR report, such as `SELECT` statements to retrieve the data and `DBMS_WORKLOAD_REPOSITORY.CREATE_*` procedures to create the report. You can customize the script to include specific time periods, snapshots, and other parameters as needed.

3. Modify the shell script to execute the SQL script for each database and host: Within the `for` loop, you can use the `sqlplus` command to connect to each database and host and execute the SQL script. You can use variables to store the database and host names, and pass them as parameters to the `sqlplus` command.

4. Schedule the shell script to run automatically: You can use a scheduling tool like `cron` to schedule the shell script to run automatically at specific intervals. This will allow you to generate the AWR reports on a regular basis without manual intervention.

Here's an example of what the shell script might look like:

```bash
#!/bin/bash

# Set the Oracle environment variables
export ORACLE_HOME=/u01/app/oracle/product/12.2.0/dbhome_1
export PATH=$ORACLE_HOME/bin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH

# Define the list of databases and hosts
databases=("database1" "database2" "database3")
hosts=("host1" "host2" "host3")

# Loop through the list of databases and hosts
for i in "${!databases[@]}"; do
  database=${databases[$i]}
  host=${hosts[$i]}
  
  # Connect to the Oracle database and execute the SQL script
  sqlplus -s username/password@$database @awr_report.sql $host
done
```

And here's an example of what the SQL script might look like:

```sql
-- Generate AWR report for the last 24 hours
DECLARE
  dbid NUMBER;
  instance_number NUMBER;
  begin_snap NUMBER;
  end_snap NUMBER;
BEGIN
  SELECT dbid, instance_number, snap_id
  INTO dbid, instance_number, begin_snap
  FROM dba_hist_snapshot
  WHERE begin_interval_time > SYSDATE - 1;
  
  SELECT snap_id
  INTO end_snap
  FROM dba_hist_snapshot
  WHERE begin_interval_time > SYSDATE - 1
  ORDER BY snap_id DESC
  FETCH FIRST 1 ROW ONLY;
  
  DBMS_WORKLOAD_REPOSITORY.CREATE_AWR_REPORT(
    dbid => dbid,
    instance_number => instance_number,
    begin_snap => begin_snap,
    end_snap => end_snap,
    report_type => 'html',
    report_level => 'typical',
    report_name => 'awr_report.html'
  );
END;
/
```

Note that this is just an example and you will need to customize it to fit your specific requirements. Additionally, you may need to modify the SQL script and shell script to handle errors, log output, and other issues that may arise during the automation process.
