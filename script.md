# Basic htop script -

#!/bin/bash

while true; do
    # Get CPU usage
    CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print 100 - $8}')
    
    # Get Memory usage
    MEMORY_USAGE=$(free | awk '/Mem:/ {printf "%.2f", $3/$2 * 100}')
    
    # Print CPU and Memory usage
    echo "$(date) - CPU Usage: $CPU_USAGE% | Memory Usage: $MEMORY_USAGE%"
    
    # Refresh every 5 seconds
    sleep 5
Done


# Threshold checking  script - 

import psutil
import time
import csv

Set threshold for alert
CPU_THRESHOLD = 75  # in percentage
MEMORY_THRESHOLD = 75  # in percentage

CSV file to log usage
LOG_FILE = "resource_monitor.csv"

 Initialize CSV file
with open(LOG_FILE, mode="w", newline="") as file:
    writer = csv.writer(file)
    writer.writerow(["Timestamp", "CPU_Usage(%)", "Memory_Usage(%)", "Alert"])

 Function to monitor system resources
def monitor_resources():
    while True:
        timestamp = time.strftime("%Y-%m-%d %H:%M:%S")
        cpu_usage = psutil.cpu_percent(interval=1)
        memory_usage = psutil.virtual_memory().percent

        # Check if usage exceeds threshold
        alert = ""
        if cpu_usage > CPU_THRESHOLD:
            alert += "High CPU Usage! "
        if memory_usage > MEMORY_THRESHOLD:
            alert += "High Memory Usage!"

        # Log data to CSV
        with open(LOG_FILE, mode="a", newline="") as file:
            writer = csv.writer(file)
            writer.writerow([timestamp, cpu_usage, memory_usage, alert])

        # Print status to console
        print(f"{timestamp} | CPU: {cpu_usage}% | Memory: {memory_usage}% | {alert}")

        # Wait for next check (5 seconds)
        time.sleep(5)

 Run the monitor
if __name__ == "__main__":
    monitor_resources()








# Connection setup Script - 


#!/bin/bash


 Variables 
GCP_INSTANCE_NAME="autoscale-instance-20250322191857"
GCP_PROJECT="solid-bliss-452121-e4"
GCP_ZONE="asia-east1-a"
GCP_USER="your-gcp-user"
SSH_KEY_PATH="$HOME/.ssh/id_rsa"


if [ ! -f "$SSH_KEY_PATH" ]; then
    echo " Generating SSH Key..."
    ssh-keygen -t rsa -b 4096 -C "$USER@$(hostname)" -f "$SSH_KEY_PATH" -N ""
fi


 Step 2: Add the public key 
echo " Adding SSH key to GCP metadata..."
gcloud compute instances add-metadata "$GCP_INSTANCE_NAME" \
    --project="$GCP_PROJECT" \
    --zone="$GCP_ZONE" \
    --metadata "ssh-keys=$GCP_USER:$(cat ${SSH_KEY_PATH}.pub)"


 Step 3: Test SSH connection
echo " Testing SSH connection..."
ssh -o StrictHostKeyChecking=no "$GCP_USER"@"$(gcloud compute instances describe $GCP_INSTANCE_NAME --zone=$GCP_ZONE --format='get(networkInterfaces[0].accessConfigs[0].natIP)')"


echo " SSH Setup Complete!"





# Tower of Hanoi Programme - 

import time
import multiprocessing

def tower_of_hanoi(n, source, auxiliary, target):
    if n == 1:
        return
    tower_of_hanoi(n - 1, source, target, auxiliary)
    tower_of_hanoi(n - 1, auxiliary, source, target)

def run_hanoi(n):
    print(f"Solving Tower of Hanoi for {n} disks...\n")
    start_time = time.time()
    tower_of_hanoi(n, 'A', 'B', 'C')
    end_time = time.time()
    print(f"Completed {n} disks in {end_time - start_time:.2f} seconds\n")

if __name__ == "__main__":
    n = 3  # Start from 3 disks
    cpu_count = multiprocessing.cpu_count()  
    print(f"Using {cpu_count} CPU cores\n")

    while True:
        processes = []
        for _ in range(cpu_count): 
            p = multiprocessing.Process(target=run_hanoi, args=(n,))
            p.start()
            processes.append(p)

        for p in processes:
            p.join() 
       
        n += 1  # Increase disk count for the next iteration