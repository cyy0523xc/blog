title: "ElasticSearch问题汇聚"
date: 2014/8/13
tags: 
- ElasticSearch
- 经验

---

### es只允许本地访问

```bash
sudo iptables -I INPUT -p tcp --dport 9200 -j DROP
sudo iptables -I INPUT -s 127.0.0.1 -p tcp --dport 9200 -j ACCEPT
```

### 部署集群时出现：No route to host

iptables的问题，清空里面的规则，关闭。


