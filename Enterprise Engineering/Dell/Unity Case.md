
ps -ef | grep svc_dc => data collection error


svc_dc --status => status of data collection

set the rescue counter => **svc_rescue_state -s**
shutdown command => svc_shutdown -r sp{a/b}

list service counters => svc_rescue_state -l
clear service counters => svc_rescue_state -c
clears all degraded state indicators => svc_rescue_state -d



system info => svc_diag
peer info =>  **svc_diag --monitor-peer**


svc_mount -m => check SSD mounting

svc_mount -s -w SSD in write mode



svc_cache --query
svc_cache --done
svc_cache --clear-lost


inval