<p style="font-size:14px" align="right">
<a href="https://upasian.org" target="_blank">Join My Website</a>
</p>

<p align="center">
  <img width="550" height="auto" src="https://user-images.githubusercontent.com/108946833/198363166-1fcfd9ad-326e-41d6-afee-5b8796e74876.png">
</p>

# Deinfra node setup for testnet

Official documentation:
>- [Validator setup instructions](https://doc.thepower.io/docs/Community/testnet-flow/#prerequisites-for-a-node)


## Install Docker
```
sudo apt update -y && sudo apt install apt-transport-https ca-certificates curl software-properties-common -y && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable" && sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin && sudo apt install docker-compose
```
```
docker pull thepowerio/tpnode
```
![image](https://user-images.githubusercontent.com/108946833/198363579-ef38a53a-aea6-4ef1-b5ce-91af5ea3c042.png)


```
docker run -d -p 44000:44000 --name tpnode thepowerio/tpnode
```

```
curl http://IPMU:44000/api/node/status | jq
```
![image](https://user-images.githubusercontent.com/108946833/198363683-bbcee99c-d52a-4b3b-a1ad-65c72135d231.png)


### Thanks to: [megumii](https://megumii.eth.limo) 
