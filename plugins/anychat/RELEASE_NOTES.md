# AnyChat 0.1.63

## User-visible changes

- Opening a Windows local archive now stops as soon as the first verified key is found, then fills in the remaining databases from that key. Typical setups finish sooner.
- If that first pass finds nothing, Windows does one extra search pass. Machines that already succeeded are not slowed down.
