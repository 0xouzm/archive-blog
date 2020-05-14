---
layout: post
title: "hyperledger fabric dev chaincode with node"
category: 
tags: 
---

官网只有go版本，记录node 开发模式流程

参考链接

https://blockchain.jlcs.es/2018/12/16/debugging-hyperledger-fabric-s-nodejs-chaincode-with-vs-code.html



```shell
//启动容器

docker-compose up -d

//启动链码

docker exec -it chaincode bash

// fabric-shim

CORE_CHAINCODE_ID_NAME="mycc:0" node chaincode_example02.js --peer.address peer:7052

//fabric-contract-api

./node_modules/.bin/fabric-chaincode-node start --peer.address peer:7052 --chaincode-id-name "mycc:0"

// install && instantiate

peer chaincode install -l node -p chaincode/chaincode_example02/node -n mycc -v 0

peer chaincode instantiate -o orderer:7050 -l node -n mycc -v 0 -c '{"Args":["init","a","100","b","200"]}' -C myc
```

注意chaincode name与version一致