#!/bin/bash

TOKEN=57c58eb8af9a423ec22968b8d16f48a7
KEY=2a570ae74ab8cb7f0b2a0ae0f4e9c8c2
IP_ADD=`ifconfig|grep -A 1 ens33|grep inet|awk '{print $2}'`


#check docker#
ps xuaww|grep -v grep|grep /usr/bin/dockerd-current
if [[ $? == 0 ]];then
        BP_API_TOKEN=${TOKEN} BP_APP_KEY=${KEY} bigpanda-alert ok ${IP_ADD} 'docker check status'
else
        BP_API_TOKEN=${TOKEN} BP_APP_KEY=${KEY} bigpanda-alert crit ${IP_ADD} 'docker check status'
fi


#check zabbix
ps xuaww|grep -v grep|grep "/usr/sbin/zabbix_agentd -c /etc/zabbix/zabbix_agentd.conf"
if [[ $? == 0 ]];then
        BP_API_TOKEN=${TOKEN} BP_APP_KEY=${KEY} bigpanda-alert ok ${IP_ADD} 'zabbix check status'
else
        BP_API_TOKEN=${TOKEN} BP_APP_KEY=${KEY} bigpanda-alert crit ${IP_ADD} 'zabbix check status'
fi

#check user#
user=`uptime |awk -F[,] '{print $3}'|awk '{print $1}'`
if [[ $user > 5 ]];then
        BP_API_TOKEN=${TOKEN} BP_APP_KEY=${KEY} bigpanda-alert crit ${IP_ADD} "user check status" -d  "user is ${user}\n `w`"
elif [[ $user > 2 ]];then
        BP_API_TOKEN=${TOKEN} BP_APP_KEY=${KEY} bigpanda-alert warn ${IP_ADD} "user check status" -d  "user is ${user}\n `w`"
else
        BP_API_TOKEN=${TOKEN} BP_APP_KEY=${KEY} bigpanda-alert ok ${IP_ADD} "user check status" -d  "user is ${user}\n `w`"
fi
