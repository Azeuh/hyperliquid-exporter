# Hyperliquid Exporter

A Python script that collects and exports metrics for node operators to Prometheus. This exporter monitors various aspects of a Hyperliquid node, including block height, proposer counts, block metrics, jailed validator statuses, software version information, and more.

## Features

- **Proposer Counts**: Tracks the number of proposals made by each proposer.
- **Block Metrics**: Monitors block height and apply duration from the latest block time file.
- **Jailed Validator Status**: Reports the jailed status of validators with full addresses and names.
- **Software Version Monitoring**: Checks the current software version and whether it's up to date with the latest binary.
- **Validator Count**: Exposes the total number of validators on the network.

## Requirements

- Python 3.6 or higher
- `prometheus_client`
- `python-dotenv`
- `requests`

Install the required packages using:

```bash
pip install -r requirements.txt
```

## Setup

1. **Clone the Repository** 
```bash
git clone https://github.com/yourusername/hyperliquid-validator-metrics-exporter.git
cd hyperliquid-validator-metrics-exporter
```

2. **Configure Environment Variables**
Create a `.env` file in the project directory and configure variables:
```bash
cp .env.sample .env && nano .env
```

3. **Run the Script**
```bash
python hl_exporter.py
```

4. Optionally, set up a systemd service to manage the process
```bash
echo "[Unit]
Description=HyperLiquid Prometheus Exporter
After=network.target

[Service]
# The working directory where the script is located
WorkingDirectory=$HOME/hyperliquid-exporter

# Command to execute the script
ExecStart=/usr/bin/python3 $HOME/hyperliquid-exporter/hl_exporter.py

# Restart the service if it crashes
Restart=always
RestartSec=10

# Run the service as the current user
User=$USER
Group=$USER

[Install]
WantedBy=multi-user.target" | sudo tee /etc/systemd/system/hyperliquid-exporter.service

Reload the systemd manager configuration and start the service:
```bash
sudo systemctl daemon-reload
sudo systemctl start hyperliquid-exporter.service
```

The script will start a Prometheus HTTP server on port `8086` and begin exporting metrics.

## Metrics Exposed

- **Proposer Counts**
```
proposer_count_total{proposer="full_validator_address"} <value>
```
- **Block Metrics**
```
hl_block_height <value>
hl_apply_duration <value>
```
- **Jailed Validator Status**
```
validator_jailed_status{validator="full_address", name="validator_name"} <0|1>
```
- **Software Version Information**
```
hl_software_version_info{commit="commit_hash", date="build_date"} 1.0
hl_software_up_to_date <0|1>
```
- **Validator Count**
```
hl_validator_count <value>
```

## Customization

- **Endpoint Configuration**
    
    The script fetches validator summaries from the Hyperliquid testnet API. If needed, you can modify the endpoint URL in the `update_validator_mapping` function.
    
- **Logging Level**
    
    Adjust the logging level in the script if you need more or less verbosity.
    

## Contributing

We welcome contributions to enhance this exporter by adding more metrics, improving performance, or fixing issues. To contribute:

1. **Fork the Repository**
2. **Create a Feature Branch**
```bash
git checkout -b feature/your-feature-name
```
3. **Commit Your Changes**
```bash
git commit -am 'Add new feature'
```
4. **Push to Your Branch**
```bash
git push origin feature/your-feature-name
```
5. **Open a Pull Request**

