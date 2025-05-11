# Snowflake Action System

A flexible framework that monitors Snowflake views on a schedule and triggers configurable actions based on the data.

## Overview

This system allows you to:
- Define Snowflake views to monitor
- Schedule when to check these views for new data
- Configure actions to trigger when data is available
- Extend with custom actions for your specific needs

## Installation

### Prerequisites

- Python 3.7+
- Access to a Snowflake instance

### Installing from source

```bash
# Clone the repository
git clone https://github.com/yourusername/snowflake-action-system.git
cd snowflake-action-system

# Install the package
pip install -e .
```

## Configuration

The system uses a TOML file for configuration. You can specify:

- Snowflake connection details
- Email and Slack notification settings
- View definitions (what data to monitor)
- Job schedules (when to check for data)
- Action mappings (what to do with the data)

### Example Configuration

```toml
# Snowflake connection settings
[snowflake]
user = "your_username"
password = "your_password"  # Better to use environment variables
account = "your_account"
warehouse = "your_warehouse"
database = "your_database"
schema = "your_schema"

# Email notification settings
[email]
smtp_server = "smtp.company.com"
smtp_port = 587
username = "alerts@company.com"
password = "your_password"  # Better to use environment variables
from_address = "alerts@company.com"
to_addresses = ["team@company.com"]

# View definitions
[[views]]
name = "billing_anomalies_view"
query = """
    SELECT * FROM BILLING_ANOMALIES 
    WHERE detected_at > DATEADD(minute, -15, CURRENT_TIMESTAMP())
"""
description = "Recent billing anomalies detected in the last 15 minutes"

# Job definitions
[[jobs]]
name = "billing_anomaly_alerts"
view = "billing_anomalies_view"
actions = ["send_alert"]
schedule = "every 15 minutes"
enabled = true
```

## Usage

Run the system with your configuration file:

```bash
# Using default config.toml in current directory
snowflake-actions

# Or specify a config file
snowflake-actions --config /path/to/your/config.toml

# To check your configuration file syntax
snowflake-actions --check-config

# To list available actions
snowflake-actions --list-actions

# For verbose logging
snowflake-actions --verbose
```

## Creating Custom Actions

You can extend the system with your own custom actions:

```python
from snowflake_action_framework import register

@register("my_custom_action", "Description of what this action does")
def my_custom_action(data):
    """
    Process data from Snowflake and perform a custom action.
    
    Args:
        data: List of records from Snowflake
        
    Returns:
        Dictionary with action results
    """
    # Your custom logic here
    return {"processed": len(data)}
```

## Environment Variables

- `CONFIG_PATH`: Path to the TOML configuration file
- `SNOWFLAKE_PASSWORD`: Snowflake password (recommended over storing in config)
- `LOG_LEVEL`: Set the logging level (DEBUG, INFO, WARNING, ERROR)

## Built-in Actions

The system comes with several built-in actions:

- `send_alert`: Sends email and Slack notifications
- `log_incident`: Records incidents in a tracking system
- `generate_report`: Creates summary reports from data

## License

This project is licensed under the MIT License - see the LICENSE file for details.
