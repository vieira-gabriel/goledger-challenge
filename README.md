# GoLedger Challenge

Now you will create your own permissioned blockchain. For that, you will bring up a network, create a channel, join a peer to that channel, install and instantiate a smart contract (aka chaincode in Hyperledger Fabric). After that, you will change the source code of the chaincode and upgrade the network with the new smart contract.
	
To accomplish that we recommend you use a UNIX-like machine (Linux/macOS). Besides that, we will need to install cURL and Docker.

## Install the prerequisites

- Install cURL (https://curl.haxx.se/download.html) 
- Install Docker and Docker Compose (https://www.docker.com/)
- Install Go (https://golang.org/dl/)
- Fork the repository https://github.com/goledgerdev/goledger-challenge 
    - Fork it, do NOT clone it, since you will need to send us your forked repository
    - Make sure you the repository is in your $GOPATH
- Download the Docker images of Hyperledger

        curl -sSL http://bit.ly/2ysbOFE | bash -s

## Generate certificates and Create channel artifacts

Go inside the folder /goledger-network. Now, we need to generate the certificates that will be used by the peer and the orderer so that they can sign and validate transactions.

	./bgldgr.sh generate

This command will create the needed certificates and the channel artifacts.


## Bring up the network containers

Assuming that you have installed Docker and Docker Compose, the following command will bring up the network elements.

	docker-compose -f docker-compose-cli.yaml up -d

You should see the following output:

	Creating orderer.example.com    ... done
	Creating peer0.org1.example.com ... done
	Creating cli                    ... done



Create and Join Channel

When deploying a local network, we can use the `cli` container to do operations in the network. For that, we need to be inside the container. Use the following command to get inside it.

	docker exec -it cli bash

Create the channel.

    peer channel create -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/channel.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
	
Join the channel.

	peer channel join -b mychannel.block
	

## Install and Instantiate Chaincode

Still inside the `cli` container, install the chaincode.
	
    peer chaincode install -n mycc -v 1.0 -p github.com/chaincode/fabcar/go/

Instantiate the chaincode.

    peer chaincode instantiate -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n mycc -v 1.0 -c '{"Args":["init"]}'



