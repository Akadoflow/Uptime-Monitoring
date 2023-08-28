# Uptime-Monitoring

#!/bin/bash

# Logfile path
LOGFILE="/mnt/c/Users/devon/OneDrive/Desktop/uptime_monitor/monitor.log"

# File containing list of sites to monitor
SITES_FILE=sites.txt

# Email to notify if site is down
NOTIFY_EMAIL=testemail@gmail.com

# Loop through all sites
while IFS= read -r site
do
    # Get the response from the site
    RESPONSE=$(curl -s --head --request GET "$site")
    
    # Log the site being checked and the received response
    echo "$(date) - Checking $site" | tee -a $LOGFILE
    echo "Response: $RESPONSE" | tee -a $LOGFILE

    # Check if the response contains the "200" status code
    if ! echo "$RESPONSE" | grep "200" > /dev/null
    then
        # If not, log the site as down and send an email
        echo "$(date) - $site looks down" | tee -a $LOGFILE | ssmtp $NOTIFY_EMAIL
    fi
done < "$SITES_FILE"
