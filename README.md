```markdown
# BitTorrent Traffic Blocker using iptables + ipset

# NO BLOCK GAMMING

This Bash script is designed to **block BitTorrent traffic** on Linux systems using iptables and ipset. It employs **Deep Packet Inspection (DPI)** to detect BitTorrent patterns and temporarily block suspicious IP addresses. **Importantly, it does not block any ports**—it only monitors the port range `6881:65535` for BitTorrent-related traffic, leaving all online gaming ports completely unaffected.

## Features

- **Deep Packet Inspection (DPI):**  
  Inspects the first 1500 bytes of TCP/UDP packets on the port range `6881:65535` to detect BitTorrent patterns without blocking the ports.

- **Dynamic Blocking:**  
  Automatically adds suspicious IPs to an ipset, temporarily blocking them if BitTorrent traffic is detected.

- **Gaming Traffic Unaffected:**  
  The script solely monitors the specified port range for BitTorrent activity. **Gaming ports remain open and fully functional**.

- **Intelligent Exclusions:**  
  Excludes local server IPs, known DNS servers, and specific IP ranges to avoid unintended disruptions.

- **Multi-Interface Monitoring:**  
  Automatically detects and monitors multiple network interfaces, including the default interface, ensuring comprehensive traffic supervision.

- **Automatic Cleanup:**  
  On termination, the script cleans up iptables rules and destroys the ipset, restoring the system’s original configuration.

## Requirements

- **Operating System:** Linux (Debian/Ubuntu or similar)
- **Permissions:** Must be run as `root`
- **Dependencies:** `iptables`, `ipset`, `ipcalc`, `rsyslog`, `grep`, `awk`, `coreutils`
- **Kernel Module:** `xt_string` (required for string matching in iptables)

## Installation

Update your system and install the required packages:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y iptables ipset ipcalc rsyslog grep awk coreutils
```

Load the `xt_string` module if it’s not already loaded:

```bash
sudo modprobe xt_string
lsmod | grep xt_string
```

Ensure that `/var/log/kern.log` exists; otherwise, the script will use `/var/log/messages`.

## Usage

1. **Make the script executable:**

   ```bash
   chmod +x torrent_block.sh
   ```

2. **Run the script as root:**

   ```bash
   sudo ./torrent_block.sh
   ```

3. **Monitor the ipset:**

   ```bash
   sudo ipset list torrent_block
   ```

4. **Stop the script:**  
   Press `Ctrl+C` or send a termination signal to clean up the iptables rules and ipset.

## Configuration

- **IPSET_NAME:** Name of the ipset (default: `torrent_block`)
- **INTERFACES:** List of network interfaces to monitor (default: `tun0`, `tun1`, `eth0`, plus the detected default interface)
- **BLOCK_DURATION:** Duration (in seconds) for which an IP remains blocked (default: 18000 seconds or 5 hours)
- **HIGH_PORTS:** Port range to inspect (default: `6881:65535`)
- **Exclusions:**  
  - Local server IPs  
  - Known DNS servers (`8.8.8.8`, `8.8.4.4`, `1.1.1.1`, `1.0.0.1`)  
  - Specific IP ranges (`10.9.0.0/22` and `10.8.0.0/22`)
- **DPI Patterns:** Modify or extend the list of BitTorrent-related strings as needed.

## How It Works

1. **Interface Detection:**  
   Automatically detects the default interface and adds it to the monitoring list.

2. **Setup:**  
   Creates or cleans the ipset and inserts iptables rules to block traffic from/to IPs in the set.

3. **Deep Packet Inspection:**  
   Uses iptables rules to inspect TCP/UDP traffic for BitTorrent patterns and logs any matches.

4. **Blocking Offenders:**  
   Monitors logs, extracts source and destination IPs, and adds non-excluded IPs to the ipset. Unblocks them after the defined `BLOCK_DURATION`.

5. **Cleanup:**  
   On termination, removes iptables rules and destroys the ipset to restore the system’s original state.

## Contributing

Contributions are welcome! Fork the repository and submit pull requests with your improvements.

## License

Distributed under the [MIT License](LICENSE).

## Security Notice

**Warning:** This script modifies firewall rules in real-time. Ensure you understand the security implications and have recovery options available. Test in a safe environment before using in production.
```
