# Manage processes

1. View jobs: `jobs`
2. Move to background: `<command> &`
3. Move to foreground: `fg <job_number>`
4. View running processes:
   1. All: `ps aux`
   2. Specific process: `ps aux  | grep <process_name>`
   3. Top 5 high CPU consuming processes: `ps aux --sort=-pcpu | head -n 5`
   4. Top 5 high Memory consuming processes: `ps aux --sort=-pmem | head -n 5`
5. Change priority of processes
6. Deal with misbehaving processes
   1. Kill process gracefully: `kill -15 <pid>`
   2. Kill process forcefully: `kill -9 <pid>`
   3. Kill all process by name: `killall <process_name>`
7. Manage system processes
   1. Find system process: `systemctl | grep <process_name>`
   2. Check status: `systemctl status <process_name>`
   3. Start a process: `systemctl start <process_name>`
   4. Stop a process: `systemctl stop <process_name>`
   5. Enable a process: `systemctl enable <process_name>`
   6. Disable a process: `systemctl disable <process_name>`
   7. Restart a process: `systemctl restart <process_name>`
8. Schedule tasks
   1. Edit: `crontab -e`
   2. View: `crontab -l`
