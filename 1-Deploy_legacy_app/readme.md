Exercise 1

**Objective: Deploy the worloads in this folder in your own namespace.**

Hints:
* Use your team name as the name of your namespace: <team_name>_ns
* Use the follow commands to test httpbin and tcp-echo workloads:
a. tcp-echo: `echo "port 31400" | nc $INGRESS_IP 31400 | grep "hello" && echo 'connection succeeded' || echo 'connection rejected'`
b. httpbin: `curl -Lv http://$INGRESS_IP:80/headers`


