# FIO Log Processor

This script processes FIO log files and exports the processed data to either an Excel spreadsheet or a comma-separated values (CSV) file.

## Requirements

- Python 3
- pandas
- openpyxl

## Installation

1. Ensure you have Python 3 installed.
2. Install the required Python packages using pip:

```bash
pip install pandas openpyxl
```

## Usage

To use the script, navigate to the directory containing the script and execute it using the following command:

```bash
./parsefio.py [directory] [-f {excel,csv}]
```
## FIO basic terminologs and operations

Every work load in FIO (called a job) has three data points associated with it, Bandwidth, IOPS, and Latency (called job types). FIO will name the log files as follows: {Jobe Name}_{job Type}.{job Number}.log. If fio is running in client/server mode, the log files will be named as follows: {Jobe Name}_{job Type}.{job Number}.log.{worker IP address}.

**Badwidth** is the amount of data that was read or written to the device. It is measured in Kibibytes per second (KiB/s).
**Submission Latency (slat)** is the amount of time from when fio initialized the I/O to submission to the operating system. It is measured in nanoseconds (ns).
++Completion Latency (clat)++ is the amount of time from when the I/O was submitted to the operating system to when it was completed. It is measured in nanoseconds (ns).
**Total Latecy (lat)** is the amount of time the time from when fio created the I/O unit to completion of the I/O operation. It is the sum of submission and completion latency. 
  It is measured in nanoseconds (ns).

## Script Operations

**Assumptionns:**
- Log files were generated using fio's default output format which is CSV. The script does *NOT* recognize json, or json+ format.
- Log file names are automatically generated by fio, when using the keywords *write_bw_log*, *write_iops_log*, *write_lat_log* with *no custom name*, please see fio config example below.
- Does not care about the transport protocol being used, it only looks for fio log files, therefore FC, iSCSI, NFS, S3 fio log files can be processed.


**Workflow:**
- The script will extract the job name, and job type from the log file names, and also look inside each log file to determine if the job was read, write, or delete workload.
- Column names are comprised of the job name, job type, and any of "Read", "Write", or "Trim" words, added as a safety in case the job name was not discriptive.
- When processing log files for fio client/server mode. bandwidth, and IOPS data reporting are the *accumulation* of all fio workers workload. Latency data reporting are the *average* of all fio workers workload.

**Example fio configuration file**

```bash
[global]
invalidate=1
ramp_time=5
iodepth=1
runtime=180
time_based
size=10g
bs=512k
log_avg_msec=1000
filename=C\:\fio\fiofile.dat
write_bw_log
write_iops_log
write_lat_log

[write-512k-seq]
stonewall
rw=write

[randwrite-512k]
stonewall
rw=randwrite

[read-512k]
stonewall
rw=read

[randread-512k]
stonewall
rw=randread

```
**Arguments:**

- `directory`: (Required) The directory where the log files are located.
- `-f, --format`: (Optional) The export format. Options are `excel` (default) and `csv`.

**Example:**

To process log files in the `./logs` directory and export the results to an Excel file:

```bash
./parsefio.py ./logs
```

To process log files in the `./logs` directory and export the results to a CSV file:

```bash
./parsefio.py ./logs -f csv
```

## Features

- Automatically detects whether the logs are in standalone or client/server mode.
- Processes log files and aggregates the data.
- Exports the aggregated data to either an Excel spreadsheet or a CSV file.

## Logging

The script logs its operations to a file named `fio_processing.log`. This can be useful for debugging or understanding the script's operations.

---

You can save the above content to a file named `README.md` in the same directory as your script. This will provide users with instructions and information about how to use your script.