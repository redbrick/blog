# NFS Out Of Sync: Apache Outage - Redbrick Blog
published on December 08, 2018  
author: greenday  
tags: [Apache](https://blog.redbrick.dcu.ie/tags/apache) [NFS](https://blog.redbrick.dcu.ie/tags/nfs)

Alert Recieved
--------------

*   A raintank alert was recieved @ 23:38 to inform that the website was down
*   A customer informed the site was down @ 00:38

Alert Validation
----------------

*   Exploration to the site revealed that there was in fact an apache error
*   The error was a 403 that apache couldn’t read the files
*   And interesting not is that the webserver could also not read the custom redbrick error page, another hint that this was bigger than just one folder

Fix
---

*   Error logs were investigated
    
    *   Apache error logs gave an error of the following
    
    ```
[Sat Dec 08 11:53:33 2018] [error] [client 66.249.81.152] (1)Operation not permitted: file permissions deny server access: /webtree/redbrick/rb_custom_error/403.html
[Sat Dec 08 11:53:33 2018] [crit] [client 66.249.81.150] (1)Operation not permitted: /home/member/m/.htaccess pcfg_openfile: unable to check htaccess file, ensure it is readable
[Sat Dec 08 11:53:33 2018] [error] [client 66.249.81.150] (1)Operation not permitted: file permissions deny server access: /webtree/redbrick/rb_custom_error/403.html
[Sat Dec 08 11:53:33 2018] [crit] [client 66.249.81.154] (1)Operation not permitted: /home/member/m/.htaccess pcfg_openfile: unable to check htaccess file, ensure it is readable
[Sat Dec 08 11:53:33 2018] [error] [client 66.249.81.154] (1)Operation not permitted: file permissions deny server access: /webtree/redbrick/rb_custom_error/403.html
[Sat Dec 08 11:53:33 2018] [error] [client 66.249.75.33] PHP Warning:  Unknown: failed to open stream: Operation not permitted in Unknown on line 0
[Sat Dec 08 11:53:33 2018] [crit] [client 46.229.168.140] (1)Operation not permitted: /webtree/w/wiki/.htaccess pcfg_openfile: unable to check htaccess file, ensure it is readable
[Sat Dec 08 11:53:33 2018] [error] [client 46.229.168.140] (1)Operation not permitted: file permissions deny server access: /webtree/redbrick/rb_custom_error/403.html
[Sat Dec 08 11:53:38 2018] [crit] [client 157.55.39.210] (1)Operation not permitted: /webtree/p/pubsoc/.htaccess pcfg_openfile: unable to check htaccess file, ensure it is readable
[Sat Dec 08 11:53:38 2018] [error] [client 157.55.39.210] (1)Operation not permitted: file permissions deny server access: /webtree/redbrick/rb_custom_error/403.html

```

    
    *   This lead us to view logs from dmsg
    
    ```
[36821900.601330] NFS: Server 192.168.0.24 reports our clientid is in use
[36821900.605982] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821905.612160] NFS: Server 192.168.0.24 reports our clientid is in use
[36821905.616701] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821910.622881] NFS: Server 192.168.0.24 reports our clientid is in use
[36821910.626795] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821915.633815] NFS: Server 192.168.0.24 reports our clientid is in use
[36821915.637714] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821920.644780] NFS: Server 192.168.0.24 reports our clientid is in use
[36821920.648684] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821925.655444] NFS: Server 192.168.0.24 reports our clientid is in use
[36821925.660511] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821930.666309] NFS: Server 192.168.0.24 reports our clientid is in use
[36821930.670822] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821935.677022] NFS: Server 192.168.0.24 reports our clientid is in use
[36821935.680605] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821940.687986] NFS: Server 192.168.0.24 reports our clientid is in use
[36821940.691938] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821945.698937] NFS: Server 192.168.0.24 reports our clientid is in use
[36821945.702396] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821950.709790] NFS: Server 192.168.0.24 reports our clientid is in use
[36821950.713700] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821955.720501] NFS: Server 192.168.0.24 reports our clientid is in use
[36821955.724923] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821960.731372] NFS: Server 192.168.0.24 reports our clientid is in use
[36821960.735952] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821965.742345] NFS: Server 192.168.0.24 reports our clientid is in use
[36821965.746246] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821970.753027] NFS: Server 192.168.0.24 reports our clientid is in use
[36821970.756539] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821975.763974] NFS: Server 192.168.0.24 reports our clientid is in use
[36821975.767870] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821980.774846] NFS: Server 192.168.0.24 reports our clientid is in use
[36821980.779018] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821985.785629] NFS: Server 192.168.0.24 reports our clientid is in use
[36821985.790880] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821990.796508] NFS: Server 192.168.0.24 reports our clientid is in use
[36821990.800403] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36821995.807262] NFS: Server 192.168.0.24 reports our clientid is in use
[36821995.811159] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36822000.818190] NFS: Server 192.168.0.24 reports our clientid is in use
[36822000.822107] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36822005.828955] NFS: Server 192.168.0.24 reports our clientid is in use
[36822005.833709] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36822010.839894] NFS: Server 192.168.0.24 reports our clientid is in use
[36822010.845658] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36822015.850727] NFS: Server 192.168.0.24 reports our clientid is in use
[36822015.854476] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36822020.861622] NFS: Server 192.168.0.24 reports our clientid is in use
[36822020.865539] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1
[36822025.872404] NFS: Server 192.168.0.24 reports our clientid is in use
[36822025.876325] NFS: state manager: lease expired failed on NFSv4 server 192.168.0.24 with error 1

```

    
    *   From this an admin identified the error “clientid is in use” can mean that NFS (Netword File Storage) and server (Web Server) were out of sync
    *   This means that there were error messages to do with permissions
*   The next step was to try unmount the NFS and remount it
    
*   Apache was using the NFS and would not let the NFS unmount
    
*   Apache was stopped.
    
*   There was still something stopping the NFS from unmounting
    
*   It was decided that safest option over forcing an unmount was the reboot the machine
    
*   The machine was rebooted and the NFS mounted successfully
    
*   The website and files were restored to their working state
    

On behalf of the admin team we appologise for the outage

Regards, greenday && The admin Team