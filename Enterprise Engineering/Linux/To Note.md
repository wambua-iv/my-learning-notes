
SCP 
SSH
fscak

mounting



`daemon-reexec` command is used with `systemd` to **re-execute the systemd process itself**, without rebooting the machine

| Command         | Purpose                                                      |
| --------------- | ------------------------------------------------------------ |
| `daemon-reload` | Reloads systemd **unit files** from disk (`*.service`, etc.) |
| `daemon-reexec` | Re-executes the **systemd binary** itself                    |
