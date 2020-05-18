Add new token to create job with curl call 

  https://rundeckURL/rundeck/user/profile


Call Exemple : 

  curl --insecure -H "X-Rundeck-Auth-Token: HERE-TOKEN" \
      --data-urlencode "argString=branch:master" \
      -X POST "https://ecash-admin.preprod2019.alienor.net/rundeck/api/12/job/TASK-ID/run"
