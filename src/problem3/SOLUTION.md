Provide your solution here:   
Step 1: Check Memory Usage  
 $\quad$    Run the following commands to analyze memory consumption:  
     $\quad$   $\quad$  free -h  
   $\quad$  This will show total, used, and available memory.  
  $\quad$ $\quad$       sudo smem -t -k | head -20  
  $\quad$   This command provides a breakdown of memory usage by processes.  
Step 2: Investigate NGINX Resource Usage  
   $\quad$  Check how much memory NGINX is consuming:  
   $\quad$ $\quad$      ps aux --sort=-%mem | head -10  
  $\quad$   If NGINX is consuming excessive memory, it might be due to:  
    $\quad$ $\quad$     A high number of worker processes.  
    $\quad$ $\quad$     Excessive caching or buffering.  
    $\quad$ $\quad$     Memory leaks or configuration issues.  
Step 3: Review NGINX Configuration  
   $\quad$  Check your NGINX worker processes and buffers:  
     $\quad$ $\quad$    cat /etc/nginx/nginx.conf | grep -E "worker_processes|worker_connections|client_body_buffer_size|proxy_buffer_size"  
    $\quad$ If worker_processes is set too high or buffers are consuming too much memory, try reducing them.  
     $\quad$ $\quad$    Example optimization:  
        $\quad$ $\quad$ $\quad$     worker_processes auto;  
         $\quad$ $\quad$ $\quad$    worker_rlimit_nofile 100000;  
         $\quad$ $\quad$ $\quad$    events {  
          $\quad$ $\quad$ $\quad$       worker_connections 10240;  
         $\quad$ $\quad$ $\quad$        multi_accept on;  
           $\quad$ $\quad$ $\quad$         }  

           $\quad$ $\quad$ $\quad$  http {  
           $\quad$ $\quad$ $\quad$      proxy_buffering off;  
        $\quad$ $\quad$ $\quad$         client_body_buffer_size 512k;  
        $\quad$ $\quad$ $\quad$         proxy_buffer_size 8k;  
        $\quad$ $\quad$ $\quad$         proxy_busy_buffers_size 16k;  
       $\quad$ $\quad$ $\quad$      }  
   $\quad$  Restart NGINX after changes:  
    $\quad$ $\quad$     sudo systemctl restart nginx  

Step 4: Check for Memory Leaks  
    $\quad$ If memory usage remains high after optimizing configurations:  
     $\quad$ $\quad$    Look for memory leaks in logs:  
        $\quad$ $\quad$ $\quad$     sudo journalctl -u nginx --since "1 hour ago"  
    $\quad$ Use valgrind to detect memory leaks:  
         $\quad$ $\quad$ $\quad$    valgrind --leak-check=full /usr/sbin/nginx  

Step 5: Check Swap Usage  
   $\quad$  If your VM has no swap, it might be running out of RAM too quickly.  
     $\quad$ $\quad$    swapon --show  
    $\quad$ If no swap is enabled, create one:  
     $\quad$ $\quad$    sudo fallocate -l 2G /swapfile  
       $\quad$ $\quad$  sudo chmod 600 /swapfile  
       $\quad$ $\quad$  sudo mkswap /swapfile  
       $\quad$ $\quad$  sudo swapon /swapfile  
       $\quad$ $\quad$  echo "/swapfile none swap sw 0 0" | sudo tee -a /etc/fstab  
    
Step 6: Consider Scaling or Using a Dedicated Load Balancer  
    $\quad$ If traffic is overwhelming the VM:  
       $\quad$ $\quad$  Increase VM resources (e.g., more RAM).  
       $\quad$ $\quad$  Use a managed load balancer (e.g., AWS ALB, Azure Load Balancer).  
       $\quad$ $\quad$  Introduce a secondary load balancer for redundancy.  
    