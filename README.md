# undead
A parallelized tool for reporting statuses of network devices en masse.

## Why undead

Ad-hoc monitoring of groups of servers or network devices is something that can be put together using a variety of existing tools, but undead provides a simple interface to perform temporary live monitoring, or report generation, and does it faster than most other existing tools. An entire /24 can be ICMP scanned in approximately 2.1 seconds, including the time it takes to print the table of results. undead also supports report generation in CSV or JSON format.

## Usage
```
usage: undead [-h] (-H HOST_FILE | -n NODE [NODE ...]) (-i | -t TCP) [-m [MONITOR]] [-o {csv,json,stdout}]

options:
  -h, --help            show this help message and exit
  -H HOST_FILE, --host-file HOST_FILE
                        A newline separated file of DNS-resolvable hostnames.
  -n NODE [NODE ...], --node NODE [NODE ...]
                        A space separate list of DNS-resolvable hostnames.
  -i, --icmp            ICMP Ping targets. Calls system ping utility.
  -t TCP, --tcp TCP     TCP ping targets. Accepts a numeric port.

Optional Arguments:
  -m [MONITOR], --monitor [MONITOR]
                        Monitor mode with a polling interval in seconds. Defaults to 10.

Format Arguments:
  -o {csv,json,stdout}, --output-format {csv,json,stdout}
                        Write results to a file in CSV or JSON.
```

## Examples
Scan a single host, and exit.
```
nhix@dev-env:~$ undead --node exa01-rta01 --icmp
hostname         ip_addr          status           return_code      errs
exa01-rta01      192.168.1.1      undead           0
```

Scan three hosts, and exit.
```
nhix@dev-env:~$ undead --node shn01-rta-01 shn01-swa-01 shn01-vma-03 --icmp
hostname         ip_addr          status           return_code      errs
exa01-rta01      192.168.1.1      undead           0
exa01-swa01      192.168.1.10     undead           0
exa01-vma03      192.168.1.251    dead             1
```

Scan a subnet, and exit.
```
nhix@dev-env:~$ undead --node 192.168.1.{1..254} -i | head -n 15
hostname          ip_addr           status            return_code       errs
192.168.1.1       192.168.1.1       undead            0
192.168.1.2       192.168.1.2       dead              1
192.168.1.3       192.168.1.3       dead              1
192.168.1.4       192.168.1.4       dead              1
192.168.1.5       192.168.1.5       dead              1
192.168.1.6       192.168.1.6       dead              1
192.168.1.7       192.168.1.7       dead              1
192.168.1.8       192.168.1.8       dead              1
192.168.1.9       192.168.1.9       dead              1
192.168.1.10      192.168.1.10      undead            0
192.168.1.11      192.168.1.11      undead            0
192.168.1.12      192.168.1.12      undead            0
192.168.1.13      192.168.1.13      dead              1
192.168.1.14      192.168.1.14      dead              1
```

Scan a list of hosts, and write the results to a file.
```
nhix@dev-env:~$ undead --host-file host-list --icmp --output-format=csv
```
## Known Issues
undead is still young, so there are quite a few known issues which haven't been addressed yet.

### General
1. Reports printed to stdout are broken.
   - Text is not formatted to fit the terminal window.
   - There is no colorization, or other formatting of the text to visually distinguish dead from undead.
2. TCP ping isn't implemented yet.
3. DNS resolution relies on the host system's DNS facilities, without the ability to specify another DNS server.
4. Piping to `head` results in a `BrokenPipeError`.

### Windows
1. Text is colored using ANSI escape sequences, which do not display properly.
2. Arguments for ping delay much longer than their *NIX counterparts.
