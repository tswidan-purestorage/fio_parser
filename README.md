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