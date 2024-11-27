# How to monitor your CPU & Memory

```
#!/bin/bash

BOT_TOKEN="<telegram-bot-token>"
CHAT_ID="<telegram-chat-id>"

SAMPLE_RATE=5 # in seconds
SAMPLE_COUNT=12
CPU_THRESHOLD=80
MEM_THRESHOLD=80

send_telegram_message() {
    local message="$1"
    curl -s -X POST "https://api.telegram.org/bot$BOT_TOKEN/sendMessage" \
        -d chat_id="$CHAT_ID" \
        -d text="$message" \
        -d parse_mode="HTML"
}

check_system_utilization() {
    sar -u -r $SAMPLE_RATE $SAMPLE_COUNT > /tmp/sar-output.tmp

    cpu_util=$(awk '/^Average:.*[1-9]/{print 100-$NF}' /tmp/sar-output.tmp | tail -n 1)
    mem_util=$(awk '/^Average:/{print $5}' /tmp/sar-output.tmp | tail -n 1)

    if (( $(echo "$cpu_util > $CPU_THRESHOLD" | bc -l) )); then
        local message="⚠️ <b>High CPU Usage Alert</b> ⚠️
<b>CPU Utilization:</b> ${cpu_util}%"
        send_telegram_message "$message"
    fi

    if (( $(echo "$mem_util > $MEM_THRESHOLD" | bc -l) )); then
        local message="⚠️ <b>High Memory Usage Alert</b> ⚠️
<b>Memory Utilization:</b> ${mem_util}%"
        send_telegram_message "$message"
    fi

    rm -f /tmp/sar-output.tmp
}

while true; do
    check_system_utilization
done
```
