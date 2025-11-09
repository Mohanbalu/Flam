# Flam

A CLI-based background job queue system.

## Features and Functionality

Flam provides a simple yet robust background job queue system controlled via a command-line interface. Key features include:

*   **Job Enqueueing:** Adds new jobs to the queue with a JSON specification.
*   **Worker Management:** Starts and stops worker processes to execute jobs.
*   **Job Status Monitoring:** Provides a summary of job states (pending, processing, completed, failed, dead) and active workers.
*   **Dead Letter Queue (DLQ):**  Automatically moves failed jobs to a DLQ after a configured number of retries.  Jobs can be retried from the DLQ.
*   **Configuration:**  Allows configuration of parameters like maximum retries and backoff base via the CLI.
*   **Concurrency:** Implements atomic job fetching using SQLite's `BEGIN IMMEDIATE` for safe concurrent processing.
*   **Graceful Shutdown:** Workers handle `SIGINT` and `SIGTERM` signals, allowing for graceful shutdown.

## Technology Stack

*   **Python 3.7+**
*   **Typer:**  For building the command-line interface.
*   **SQLite:**  For the job queue database.
*   **psutil:** For monitoring worker processes.
*   **dataclasses:** For defining job data structures.
*   **subprocess:** For executing job commands.

## Prerequisites

*   Python 3.7 or higher.
*   `pip` (Python package installer).

## Installation Instructions

1.  Clone the repository:

    ```bash
    git clone https://github.com/Mohanbalu/Flam.git
    cd Flam
    ```

2.  Create a virtual environment (recommended):

    ```bash
    python3 -m venv venv
    source venv/bin/activate  # On Linux/macOS
    # venv\Scripts\activate  # On Windows
    ```

3.  Install the required packages:

    ```bash
    pip install -e Backend
    ```
    or
     ```bash
    cd Backend
    pip install .
    cd ..
    ```


## Usage Guide

The main entry point is the `queuectl` command.

### Enqueueing Jobs

To add a job to the queue, use the `enqueue` command:

```bash
queuectl enqueue '{"command": "echo Hello World"}'
```

This command enqueues a job with the command `echo Hello World`.  You can optionally provide an `id` field in the JSON, or the system will generate a UUID:

```bash
queuectl enqueue '{"id": "my-unique-job", "command": "sleep 5 && echo Done"}'
```

**Important:** The command is executed using `shell=True`. Ensure that the commands being enqueued are trustworthy to prevent security risks.

### Managing Workers

*   **Starting Workers:**
    To start worker processes, use the `worker start` command:

    ```bash
    queuectl worker start --count 3
    ```

    This command starts 3 worker processes.

*   **Stopping Workers:**
    To stop all running worker processes, use the `worker stop` command:

    ```bash
    queuectl worker stop
    ```

### Monitoring Job Status

*   **Viewing Job Summary:**
    To see a summary of job states and active workers, use the `status` command:

    ```bash
    queuectl status
    ```

*   **Listing Jobs by State:**
    To list jobs in a specific state (e.g., pending), use the `list` command:

    ```bash
    queuectl list --state pending
    ```
    Valid states are `pending`, `processing`, `completed`, `failed`, and `dead`.

### Managing the Dead Letter Queue (DLQ)

*   **Listing DLQ Jobs:**
    To list all jobs in the DLQ, use the `dlq list` command:

    ```bash
    queuectl dlq list
    ```

*   **Retrying a DLQ Job:**
    To move a specific job from the DLQ back to the pending queue, use the `dlq retry` command:

    ```bash
    queuectl dlq retry <job_id>
    ```
    Replace `<job_id>` with the ID of the job to retry.

### Configuration

*   **Setting Configuration Values:**
    To set a configuration value (e.g., `max_retries`), use the `config set` command:

    ```bash
    queuectl config set max_retries 5
    ```

*   **Showing Current Configuration:**
    To view the current configuration, use the `config show` command:

    ```bash
    queuectl config show
    ```

The configuration file is stored at `Backend/.queuectl_data/config.json`.

## API Documentation

This project uses a CLI interface and does not expose a traditional API.  However, the `queuectl` command is the entry point, and the subcommands provide distinct functionalities.

*   **`queuectl enqueue`**: Adds a job to the queue based on the provided JSON specification. The JSON *must* contain the "command" field. An optional "id" field can be used to specify the job ID; otherwise, a UUID is automatically generated.
*   **`queuectl worker start`**: Starts worker processes based on the `--count` flag.
*   **`queuectl worker stop`**: Signals the currently running worker processes to shut down.
*   **`queuectl status`**: Displays a summary of jobs in each state as well as information about the currently running worker processes.
*   **`queuectl list`**: Lists the jobs that are currently in the queue, optionally filtered by their state.
*   **`queuectl dlq`**: Provides commands to interact with the dead letter queue, allowing to view and retry failed jobs.
*   **`queuectl config`**: Allows to manage the configuration of the job queue.

## Contributing Guidelines

Contributions are welcome! Please follow these guidelines:

1.  Fork the repository.
2.  Create a new branch for your feature or bug fix.
3.  Write tests for your code.
4.  Ensure that all tests pass.
5.  Submit a pull request.

## License Information

License not specified.

## Contact/Support Information

For questions or support, please open an issue on the GitHub repository:  [https://github.com/Mohanbalu/Flam](https://github.com/Mohanbalu/Flam)