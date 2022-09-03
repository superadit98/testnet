
<p style="font-size:14px" align="left">
<a href="https://upasian.org/" target="_blank">Visit our website </a>
<a href="https://nodes.guru/aleo/setup-guide/en" target="_blank">Guide From Node Guru</a>
</p>





# Masa node setup

Official documentation:
- https://github.com/masa-finance/masa-node-v1.0

## Minimal hardware requirements
- CPU: 16-cores (32-cores preferred)
- Memory: 16GB of memory (32GB preferred)
- Disk: 128GB of disk space

## Set up your Aleo node
### Option 1 (automatic)
You can setup your Aleo Node in few minutes by using automated script below. It will prompt you to input your node name!
```
wget -q -O aleo_snarkos3.sh https://api.nodes.guru/aleo_snarkos3.sh && chmod +x aleo_snarkos3.sh && sudo /bin/bash aleo_snarkos3.sh
```

## Usefull commands

### Check  node logs
You have to see blocks comming
```
journalctl -u aleod -f -o cat 
```

### Restart Node
```
systemctl restart aleod
```

### Stop Node
```
systemctl stop aleod
```

### Delte Node
```
wget -q -O aleo_remove_snarkos2.sh https://api.nodes.guru/aleo_remove_snarkos2.sh && chmod +x aleo_remove_snarkos2.sh && sudo /bin/bash aleo_remove_snarkos2.sh

```
