# PP Learning HLF

Install HLF 1.3 on [Ubuntu 16.0.4/18.0.4](https://medium.com/@eSizeDave/https-medium-com-esizedave-how-to-install-hyperledger-fabric-1-2-on-ubuntu-16-04-lts-ecdfa4dcec72)

### Links

* [Fabric Repo](https://github.com/hyperledger/fabric-samples)
* [Bal Transfer](https://github.com/hyperledger/fabric-samples/tree/release-1.3/balance-transfer) 
* [Edx Repo](https://github.com/hyperledger/education/tree/master/LFS171x)
* [yeasy Repo](https://github.com/yeasy/docker-compose-files/tree/master/hyperledger_fabric/v1.2.0)
* [AWS Template](https://docs.aws.amazon.com/blockchain-templates/latest/developerguide/blockchain-templates-hyperledger.html#blockchain-hyperledger-launch)

### This is a sample app on HLF to store property records
You will be provisioning a local network with the following docker container configuration:

* 1 CA
* A SOLO orderer
* 1 Org (1 Peer per Org)

#### Artifacts
* Crypto material has been generated using the **cryptogen** tool from Hyperledger Fabric and mounted to all peers, the orderering node  and CA containers. More details regarding the cryptogen tool are available [here](http://hyperledger-fabric.readthedocs.io/en/latest/build_network.html#crypto-generator).
* An Orderer genesis block (genesis.block) and channel configuration transaction (mychannel.tx) has been pre generated using the **configtxgen** tool from Hyperledger Fabric and placed within the artifacts folder. More details regarding the configtxgen tool are available [here](https://hyperledger-fabric.readthedocs.io/en/latest/build_network.html#configuration-transaction-generator).

###### This example makes use of configtxgen and is for development ONLY. Make use of Fabric-CA for production environments

```
cd /usr/local/hyperledger/fabric-samples/basic-network
cd /usr/local/hyperledger/fabric-samples/demo-app
```

#### Setup:

* Define/Change crypto-config.yaml
  * Make sure you set EnableNodeOUs: true 
* Network Topology
  * OrdererOrgs
    * Name
    * Domain
    * Hostname 
  * PeerOrgs
    * Name 
  * Domain Users
    * Count 1 in addition to Admin
* Define/Change docker-compose.yml 
  * FABRIC_CA_SERVER_CA_KEYFILE
* Define/Change ./generate.sh (Based on Profiles defined in configtx.yaml)
* Define/Change ./start.sh (Change channel name)
* Run startFabric.sh, and replace the new key by inspecting docker logs ca.example.com
  * Everytime you generate new crypto material, make sure you update "FABRIC_CA_SERVER_CA_KEYFILE" in the "docker-compose.yml" file
* Bring up the n/w by running startFabric.sh 
  * launch network; create channel and join peer to channel
  * launch CLI container in order to install, instantiate chaincode
  * bootstrap/ledger with property
* Install node
```
npm install
```
* Enroll Admin and Register the User
  * node registerAdmin.js
  * node registerUser.js
* Start server 
  * node server.js http://localhost:8000

#### To Do/Findings:

* Investigate the certificates generated by cryptogen using `openssl x509 -in ./path/to/cert.pem -text -noout` 
* `msp/config.yaml` should NOT be placed into both the orderer and peers' MSP directory. It must ONLY go into the peers.

#### Clean the network
Clean the containers and artifacts

```
--Docker Housekeeping
docker rm -f $(docker ps -aq)
--docker rmi -f $(docker images -q)

--remove chaincode images from prior runs
docker rmi -f $(docker images | grep peer[0-9]-peer[0-9] | awk '{print $3}')

--Clear any cached networks
docker network prune

--Clear out your previous key value stores that may have cached user enrollment certificates
rm -rf /tmp/hfc-*
rm -rf ~/.hfc-key-store)

--Delete docker log files
find /var/lib/docker/containers/ -type f -name "*.log" -delete
```

#### Discover IP Address
To retrieve the IP Address for one of your network entities, issue the following command:

```
# this will return the IP Address for peer0
docker inspect peer0 | grep IPAddress
```
