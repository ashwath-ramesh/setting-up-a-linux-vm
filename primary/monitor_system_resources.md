# Monitoring system resources

1. Disk usage
   1. Overview
      1. `df -h`
      2. `df -i`
   2. Disk usage by directory (Directory permissions matters ...)
      1. Manual traversing & checking:
         1. `du -hc` (or) `du -hsc` (h: human readable, s: summary, c: cumulative total space used)
         2. `du -h --max-depth=1`
         3. `du -h --max-depth=1 | sort -h`
      2. Using `ncdu` (ncurses disk usage)
         1. `sudo apt install ncdu`
         2. `ncdu`
2. Memory usage
   1. Check column 'additional': `free -m` (or) `free`
   2. Managing swap
3. Load average
   1. View load average (1-5-15 mins): `uptime` (or) `cat /proc/loadavg`
4. Resource usage
   1. `sudo apt install htop`
   2. `htop`
