Provide your solution here:
Step 1: Check Memory Usage  
    Run the following commands to analyze memory consumption:  
        free -h  
    This will show total, used, and available memory.  
        sudo smem -t -k | head -20  
    This command provides a breakdown of memory usage by processes.  
Step 2: Investigate NGINX Resource Usage  
    Check how much memory NGINX is consuming:  
        ps aux --sort=-%mem | head -10  
    If NGINX is consuming excessive memory, it might be due to:  
        A high number of worker processes.  
        Excessive caching or buffering.  
        Memory leaks or configuration issues.  
Step 3: Review NGINX Configuration  
    Check your NGINX worker processes and buffers:  
        cat /etc/nginx/nginx.conf | grep -E "worker_processes|worker_connections|client_body_buffer_size|proxy_buffer_size"  
    If worker_processes is set too high or buffers are consuming too much memory, try reducing them.  
        Example optimization:  
            worker_processes auto;  
            worker_rlimit_nofile 100000;  
            events {  
                worker_connections 10240;  
                multi_accept on;  
            }  

            http {  
                proxy_buffering off;  
                client_body_buffer_size 512k;  
                proxy_buffer_size 8k;  
                proxy_busy_buffers_size 16k;  
            }  
    Restart NGINX after changes:  
        sudo systemctl restart nginx  

Step 4: Check for Memory Leaks  
    If memory usage remains high after optimizing configurations:  
        Look for memory leaks in logs:  
            sudo journalctl -u nginx --since "1 hour ago"  
    Use valgrind to detect memory leaks:  
            valgrind --leak-check=full /usr/sbin/nginx  

Step 5: Check Swap Usage  
    If your VM has no swap, it might be running out of RAM too quickly.  
        swapon --show  
    If no swap is enabled, create one:  
        sudo fallocate -l 2G /swapfile  
        sudo chmod 600 /swapfile  
        sudo mkswap /swapfile  
        sudo swapon /swapfile  
        echo "/swapfile none swap sw 0 0" | sudo tee -a /etc/fstab  
    
Step 6: Consider Scaling or Using a Dedicated Load Balancer  
    If traffic is overwhelming the VM:  
        Increase VM resources (e.g., more RAM).  
        Use a managed load balancer (e.g., AWS ALB, Azure Load Balancer).  
        Introduce a secondary load balancer for redundancy.  
    