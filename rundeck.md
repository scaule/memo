
**Add new token to create job with curl call**

    [https://RUNDECK_URL/rundeck/user/profile](https://rundeckurl/rundeck/user/profile)

**Call Exemple :**

    curl --insecure -H "X-Rundeck-Auth-Token: HERE-TOKEN"  
    --data-urlencode "argString=branch:master"  
    -X POST "https://RUNDECK_URL/rundeck/api/12/job/TASK-ID/run"
