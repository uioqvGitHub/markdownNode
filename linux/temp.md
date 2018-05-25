```bash
iptables -A INPUT -i vboxnet0 -s 192.168.56.0/24 -p tcp --dport 22 -j ACCEPT
iptables -t nat -A POSTROUTING -s 10.42.0.0/24 ! -d 10.42.0.0/24 -j MASQUERADE
iptables -t nat -I POSTROUTING -s 192.168.56.0/24 ! -d 192.168.56.0/24 -o wlp2s0 -j MASQUERADE
iptables -t nat -I POSTROUTING -s 0.0.0.0/0  -o enp1s0 -j MASQUERADE
```

