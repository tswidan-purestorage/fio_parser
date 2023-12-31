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
## FIO Basic Terminologs and Operations

Every I/O activity created by FIO (called a job) has five data points associated with it, Bandwidth, IOPS, Total Latency, Submission Latency, Completion Latency (called log types). 
Left at default, FIO will name the log files as follows: {Jobe Name}_{log Type}.{job Number}.log. 
If fio is running in client/server mode, log files will be further named as follows: {Jobe Name}_{Log Type}.{Job Number}.log.{Worker IP Address}.

**Badwidth** is the amount of data that was read or written to the device. It is measured in Kibibytes per second (KiB/s).

**IOPS** is the number of I/O operations that were completed per second. It is measured in operations per second (ops/s).

**Submission Latency (slat)** is the amount of time from when fio initialized the I/O to submission to the operating system. It is measured in nanoseconds (ns).

**Completion Latency (clat)** is the amount of time from when the I/O was submitted to the operating system to when it was completed. It is measured in nanoseconds (ns).

**Total Latecy (lat)** is the amount of time the time from when fio created the I/O unit to completion of the I/O operation. It is the sum of submission and completion latency. 
  It is measured in nanoseconds (ns).

## Script Operations

**Assumptionns:**
- Log files were generated using fio's default output format which is CSV. The script does *NOT* recognize json, or json+ format.
- Log file names are automatically generated by fio, when using the keywords *write_bw_log*, *write_iops_log*, *write_lat_log* with *no custom name*, please see fio config example below.
- Script does not care about the transport protocol being used, it only looks for fio log files, therefore FC, iSCSI, NFS, S3 fio log files can be processed.
- The keyword "log_avg_msec" is used to set the interval for the log file. It is not necessary for the script execution, however, it is necesssary to set it to 1000 (1 second), otherwize, fio will log an entry in the logs every I/O. The impact is that the IOPS log file will have 1 I/O per line, and the all other data points will be based on single I/O, which is not useful.

- The script will extract the job names, and log types from the log file names, and also look inside each log file to determine if the job was read, write, or delete workload.
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

Lets say we ran the above FIO configuration file,in standalone mode. The resulting FIO logs will be located in the current directory. 
```bash
% ls
randread-512k_bw.4.log		read-512k_bw.3.log
randread-512k_clat.4.log	read-512k_clat.3.log
randread-512k_iops.4.log	read-512k_iops.3.log
randread-512k_lat.4.log		read-512k_lat.3.log
randread-512k_slat.4.log	read-512k_slat.3.log
randwrite-512k_bw.2.log		write-512k-seq_bw.1.log
randwrite-512k_clat.2.log	write-512k-seq_clat.1.log
randwrite-512k_iops.2.log	write-512k-seq_iops.1.log
randwrite-512k_lat.2.log	write-512k-seq_lat.1.log
randwrite-512k_slat.2.log	write-512k-seq_slat.1.log
% 
```

To process log files in the `./` directory and export the results to an Excel file:

```bash
./parsefio.py ./
```
Example Excel output: [fio-results-2023-10-30-21-35.xlsx](fio-results-2023-10-30-21-35.xlsx)

To process log files in the `./` directory and export the results to a CSV file:

```bash
./parsefio.py ./ -f csv
```
Example CSV output: [fio-results-2023-10-30-21-45.csv](fio-results-2023-10-30-21-45.csv)


## Features

- Automatically detects whether the logs are in standalone or client/server mode.
- When using client/server mode, the script will process all workers logs, and aggregate the data into a single table.
- The script will extract the job names, and log types from the log file names, and also look inside each log file to determine if the job was read, write, or delete workload.
- Column names are comprised of the job name, job type, and any of "Read", "Write", or "Trim" words, added as a safety in case the job name was not discriptive.
- When processing log files for fio client/server mode. bandwidth, and IOPS data reporting are the *accumulation* of all fio workers workload. Latency data reporting are the *average* of all fio workers workload.

- Converts the bandwidth to MiB/s.
- Converts the latency reports to milliseconds.
- Exports the aggregated data to either an Excel spreadsheet or a CSV file.

## Logging

The script logs its operations to a file named `fio_processing.log`. This can be useful for debugging or understanding the script's operations. An example can be found [here](fio_processing.log).


## License
Freeware  -  Use at your own risk.  No warranty expressed or implied.

## Updates
If you would like to update the script, please fork the repository and submit a pull request.

## Author Information
Tamer Swidan, tswidan@purestorage.com
Solutions Architect at Pure Storage


