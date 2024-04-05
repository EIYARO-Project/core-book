 
# EIYARO blockchain architecture
 

## 1.1 Introduction 

​	Blockchain technology or a Distributed Ledger Technology was born in Bitcoin in 2008  with the publication of a paper titled "Bitcoin: A Peer-to-Peer Electronic Cash System", written under the alias of Satoshi Nakamoto.

​	Blockchain is a distributed ledger managed on a decentralized peer to peer computer network without any single trusted authority or a central server. The nodes in the network collectively adhere to well defined protocols for communication between peers, block creation, block storage and block validation to maintain ledger data reliably.

​	Types of Blockchains:

* Public Blockchains: In a public blockchain, there is no single trusted authority, manager or a central server. Any node that can follow the system rules can access the network and participate in the consensus process.

* Private Blockchains: Private blockchains are usually created and managed within an enterprise and the system rules are set according to the requirements of the enterprise. Some nodes can have special privileges compared to other nodes e.g only selected nodes can have block write permissions or only invited nodes can participate in the network. Private Blockchains can also be only partially decentralized.

* Consortium Blockchains: Consortium Blockchains are usually created and managed by a group of different agencies. Consortium Blockchain is a hybrid between a public and a private blockchain, and can also be only partially decentralized.

  

Eiyaro is an open source project written in GO language and hosted on Github (https://github.com/EIYARO/ey).

Learn about public blockchain technology by analysing and explaining the Eiyaro blockchain source code.

If Bitcoin represents the Blockchain 1.0 era and Ether with Turing-complete smart contracts represents the Blockchain 2.0 era, then Eiyaro with its multi-asset distributed ledger and Turing-complete smart contracts represents the Blockchain 2.5 era.

Eiyaro is based on the UTXO transaction model, which is similar to Bitcoin's, and offers additional features such as multi-asset transactions, Turing-complete smart contracts, and a new POW consensus algorithm called Tensority.



​	This book is based on Eiyaro's source code. The differences in public blockchain implementations lie mainly in the choice of consensus algorithm (PoW, PoS) or transaction model (UTXO or account).



* Analyse the architecture and implementation details of the Eiyaro blockchain based on its source code. We will present the details in three parts:

  * The overall architecture.
  * The different modules and their functions.
  * Compilation and deployment of Eiyaro nodes.





## 1.2 Architecture  

Eiyaro Blockchain ("Eiyaro") is a multi-asset public blockchain with smart contracts.

Eiyaro's codebase is clearly structured and small enough for beginners and advanced programmers to easily get started and keep up. Readers can learn a lot from reading Eiyaro source code, such as Go language programming and application development, the architecture and design of public blockchain, the operation principle of public blockchain and network protocols.

The architecture of Eiyaro is designed as follows: 
![EIYARO Architecture Diagram](https://github.com/EIYARO/ey-book/tree/main/image/ey_01.png)


### 1.3.1 User Interaction Layer 


***1. Eiyarocli RPC Client ***

​	Eiyarocli is an RPC client executable that can interact with a eiyarod process. You can run eiyarocli on command line and issue RPC commands to a eiyaro node.

​	Eiyarod receives and processes the request sent by eiyarocli.


***2. Eiyaro Dashboard***

​		Eiyaro dashboard is similar to eiyarocli. Both communicate with eiyarod by sending requests. Dashboard provides a friendly web interface for users to interact with eiyarod.


​	By default eiyaro-dashboard is deployed along with eiyaro node. Users can optionally turnoff eiyaro-dashboard by setting --web.closed parameter. Repository for eiyaro-dashboard is here. https://github.com/EIYARO/ey-dashboard



### 1.3.2  Interface Layer -ApiServer


​	Api Server is a very important part of Eiyaro. It receives, handles and responds to requests from eiyarocli, eiyaro-dashboard, other nodes and mining pools. By default API Server starts on port 9888.

​	It's main funcations are:

* Receive, handle and respond to requests from users and mining pools.
* Manage transactions: package, sign, submit transactions and some other interface operations. 
* Manage the local wallet interface.
* Manage the information interface of the local P2P node.
* Manage the operation interface of local mining


​	API Server constantly listens to requests from eiyarocli and eiyaro-dashboard. When the API Server listener receives a new request, it creates a new goroutine to handle and process this request.

​	First, the API server reads and parses the request content, identifies the route for the request and then calls the callback function of the routing handler for this route to process this request. Finally, the handler responds to the request after the processing is done.



### 1.3.3 Kernel Layer - Blocks and Transactions Management 


​	The kernel layer is the most important part of Eiyaro. It's code is about 54% of overall eiyaro code.

​	A blockchain contains thousands of blocks. A block in a blockchain contains one or more transactions and each block is linked to the previous one in a linked-list like structure.

​	One other important task of kernel layer is to validate transactions and blocks.


​	When the node receives a new block from the network, it validates the block and tries to find it's parent block in the locally stored main blockchain. If the parent block exists, then the new block will be added to the main blockchain, otherwise the received block is regarded as an orphaned block and the node waits for the parent block.


​	When a node receives a new transaction from the network, the node will first validate it. Once the tx is verified successfully, the tx will be put in the transaction pool and the node waits for the miners to pack this tx into a block.



​	A transaction life cycle in a blockchain goes through the following steps:

* User A creates a new transaction using his wallet to send an asset to User B.
* The transaction is propagated onto the P2P network. 
* Miners validate the transaction after receiving it.
* Miners package the transaction and put it into a new block. A block can contain one or more transactions.
* The new block is propagated onto the P2P network and the new block is added to the blockchain on all nodes in the network.
* The transaction is complete and becomes a part of the blockchain.



### 1.3.4 Kernel Layer - Smart Contract


​	A Contract is similar to a real life contract agreed upon by people. Smart contract is a set of rules involving public keys and conditional transactions specified in digital form like a computer program that can be executed on a blockchain. Different parties can agree to the set of rules coded as computer programs and can be run on the virtual machine provided by the blockchain. These programs run inside the blockchain with out depending on any third party to interpret these rules and complete transactions.


​	Smart contract has two features:  traceability and irreversibility.

​	Smart contract implementation is the core and the most important part of Eiyaro. In the following chapters, we will introduce different smart contract models (UTXO, Account ), operation principle, and working mechanism of BVM. We will walk through the code to explain how smart contracts can complete transactions without a central trusted authority to interpret the rules of the contract.



### 1.3.5 Kernel Layer - BVM(Eiyaro Virtual Machine) 


​	Eiyaro Virtual Machine is designed to execute smart contract programs in a platform-independent environment. BVM is an important part of Eiyaro, which plays a vital role in the process of storing, processing and validating smart contracts.


​	Users can write smart contract programs in a simple high level programming language called Equity. Equity programs can be compiled into standard Eiyaro Virtual machine opcodes. BVM is a platform-independant virtual machine environment that is part of every eiyaro node in the eiyaro blockchain network.

​	In short, BVM is a code running environment build on the Eiyaro blockchain.



### 1.3.6 Wallet Layer 


​	Wallet is used to manage keys and UTXO. It's like the real life safe that you can open with key(s) and manage the property (UTXO) in it. In Eiyaro, wallet layer is mainly responsible for storing keys, managing addresses, maintaining UTXOs, creating and signing transactions and offers external interfaces to wallet and transaction functionality.


​	Here are three steps to send a transaction :

* Build: Build the transaction with its input and output information .
* Sign: Sign every input of the transaction with the private key.
* Submit: Submit the transaction to the network and propagate it. Wait for the transaction to be put in a block.




### 1.3.7 Consensus Layer 


​	All the nodes in a Blockchain maintain a decentralized ledger. The Consensus layer is used to achieve blockchain data consistency across all the nodes of network. Consensus layer defines the rules for validating blocks and transactions and rules for generating new blocks which are same on all the nodes. In short, consensus provides a mechanism for nodes participating in the process to win a right to create the next valid block. The node that wins the right can add the next block generated by it to the end of the blockchain. Other nodes will validate and accept this block following the same rules, and discard any other blocks that fail the validation.


​	There are two common types of consensus: PoW(Proof-of-Work), PoS(Proof-of-Stake.

​	Pow)

​		PoW is based on the idea that nodes compete to solve a super complex mathematical problem, like compute a hash result less than a specific value. The hash function is designed such a way that it is impossible to compute or guess the input from the hash result. So the only way is to compute the input by trial and error in a loop until the expected hash result is found, which requires a lot of computing power. The complexity of PoW ensures that a node has to spend a lot of computing power to generate a new block and spend much more computing power than the entire network to tamper or alter the block. Here are advantages and disadvantages of PoW:


| Advantages of PoW                             | Disadvantages of PoW                                         |
| --------------------------------------------- | ------------------------------------------------------------ |
| 1. The algorithm is very simple to implement. | 1. It consumes a lot of resources, which is a waste.         |
| 2. It costs a lot to destroy the consensus.   | 2. The process of computing is complex, resulting in the large interval between generating blocks. |
|                                               | 3. As the ASIC keeps getting better at computing hashes, the computing power is concentrated among a very few users. |




​	PoS is another way of achieving consensus. The nodes participate in the consensus process by locking some currency and the algorithm takes into account the amount of currency locked and the age duration the amount was locked in determining who gets the right to create the next block. PoS generally doesn't need a lot of computation, that's why it is faster and more efficicient than PoW. Here are advantages and disadvantages of PoW:


| Advantages of PoS                                            | Disadvantages of PoS                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1. It saves energy with no need for a lot of computing.                   | 1. The wealth may be gathered among a few people, resulting in a great gap between rich and poor. |
| 2. It is decentralized. All nodes can join in the PoS without extra hardware. | 2. CryptoCurrency usually starts with an ICO, which makes it easy for early users to hoard a lot of it. |



​	Though there are a small number of cryptocurrency implementations which chose other consensus mechanisms, PoW and PoS are still the most popular methods of achieving consensus. Eiyaro believes in the idea of "Computing is Power" so anyone who can bring computing power can participate in the consensus process and have some control. Due to higher need for decentralization and consistency, efficiency had to be sacrifised to get multiple nodes across the globe reach consensus reliably and for this reason eiyaro chose PoW as the consensus mechanism for mainnet public chain.




### 1.3.8 Data Storage Layer

​	Eiyaro stores all blockchain data like addresses, assets, transactions, etc. in the data storage layer. The data storage layer can be divided into two parts. Cache and Persistent storage. Cache is used to respond to most queries quickly and reduce the disk I/O. When a query can't find the data in the cache, it will read from the persistent storage directly and add a copy to the cache for future lookup.

​	Eiyaro uses LevelDB as its persistent storage by default, which is a highly efficient key-value database created by Google. Because LevelDB runs in a single-process, multiple processes cannot read and write to the database at the same time. That means only one process or one process with multiple threads can read and write the database at one time.

​	By default, the data storage is in the " --data" under the "--home". On Darwin (MacOS), the database is in location "$HOME/Library/Eiyaro/data".

* accesstoken.db —— token information( the wallet access control)
* core.db —— Core database, which stores related data to the main blockchain, including the information of blocks, transactions, assets , etc.
* discover.db —— The information of nodes in the distributed network. 
* trusthistory.db—— The information of malicious nodes in the distributed network.
* txfeeds.db —— Not yet used in this version.
* txfeeds.db —— Local wallet database, used to store the information of users, assets, transactions, UTXOs, etc.


### 1.3.9 P2P Distributed Network 

​	As a decentralized distributed system, the pattern of communicating between nodes is very important for Eiyaro to maintain the stability of the whole system. The synchronization of transactions and nodes state all relys on the communication between each node in the network. The network communication of Eiyaro is based on the P2P communication protocol and also has some special changes according its own features. The P2P netwo communication Layer of Eiyaro includes four parts: nodes discovery, synchronization of transactions, synchronization of blocks and propagation of blocks and transactions.



 ***1. Nodes discovery***

​	Nodes discovery of P2P network mainly aims to solve how the new node join the network of Eiyaro, which requires the new node can be known by other nodes as soon as possible, and gets information from other nodes as well. The Eiyaro nodes discovery uses Kademli, which is a overlay network with a specific structure of the P2P network. Each node is identified by a number or node ID, which is stored in each node. Kademlia stores nodes in the k-buckets, and every node just connects with the n closest nodes to form the topological structure of network as follows : 
![EIYARO-Node](https://github.com/EIYARO/ey-book/blob/image/node_01.png)



***2.Synchronization of transactions***

​	To ensure the security of transactions, the node needs to synchronize transactions with other nodes in the network, which is synchronization of transactions. After other nodes finish the synchronization, they will also validate transactions. If successful, these nodes will add transactions into thier transactions. Also,the synchronization of transactions enhances the efficiency of packaging transactions.



***3. Synchronization of blocks***

​	Blockchain is a decentralized distributed ledger, which needs full nodes to store information of all the blocks. Once a new node joins the network, it first needs to synchronize blocks since height 1 to the most recent block from the network and build the complete chain of blocks to date locally.


​	Nodes with fast synchronization can synchronize 1–128 blocks once（ from the current height to the next height of checkpoint block). Apart from fast synchronization, there is general synchronization, which is used to synchronize the newer blocks to ensure nodes can catch up the blockchain.


​	The difference between general synchronization and fast synchronization is that the latter uses `get-headers` and `get-blocks` to get a bunch of blocks upto a checkpoint, which can reduce the PoW computing and improve efficiency. General synchronization just can get one block at a time.

​	General synchronization and fast synchronization aim to meet different needs. Fast synchronization is for building the local copy of blockchain quickly so that the node can join the network as soon as possible. General synchronization is only used when requirements for fast synchronization are not satisfied and the node can just get one block at a time.


 

***4. Propagation of blocks and transactions ***

​	To ensure the transaction can be confirmed and submitted to the main blockchain in time, Eiyaro offers fast broadcasting. Nodes will broadcast new blocks and transactions quickly to other known nodes immediately after receiving them.. 


### 1.3.10 Compilation and Deployment of Eiyaro

​	There are many ways to install Eiyaro. Here we show how to compile the code.


***1.Compile and deploy the Eiyaro***

1）Download ：

```
$ git clone https://github.com/Eiyaro/ey.git $GOPATH/src/eiyaro/ey
```


2）Compile：

```
$ cd $GOPATH/src/eiyaro/ey
$ go mod tidy
$ make eiyarocd    
$ make eiyaroccli  
```

3）Initialize ：

```
$ cd /src/eiyaro/ey/cmd/eiyarod
$ go build
$ ./eiyarod init --chain_id mainnet
```

​	Eiyaro currently has three types of networks, identified by chain_id :

* mainnet: Main Network
* testnet: Also known as Wisdom test network
* solonet: Stand-alone mode useful for development


4）Start the Eiyarod：

```
$ nohup ./eiyarod node &

$ cd /src/eiyaro/ey/cmd/eiyarocli
$ go build
$ ./eiyarocli net-info
{
  "current_block": 2000,
  "highest_block": 2000,
  "listening": true,
  "mining": false,
  "network_id": "mainner",
  "peer_count": 10,
  "syncing": false,
  "version": "1.0.1"
}
```


​	By default, mining is turned off when you run eiyarod for the first time. The eiyarod process establishes a communication with neighbour nodes obtained from the seed nodes of the P2P network and will start synchronizing blocks.




***2. The directory of the the source code***

​	Here is the directory listing of the source code:

```
$ tree -L 1
.
├── accesstoken       
├── account           
├── api               
├── asset            
├── blockchain        
├── cmd               
├── common            
├── config            
├── consensus         
├── crypto            
├── dashboard        
├── database          
├── docs              
├── encoding         
├── env             
├── equity           
├── errors            
├── math             
├── metrics          
├── mining            
├── net               
├── netsync         
├── node              
├── p2p             
├── protocol          
├── test             
├── testutil          
├── util              
├── vendor           
├── version           
└── wallet            
```

***3. Start mining***

​	Use the command line to start mining :

```
$ ./eiyarocli set-mining true
```

​	Mining is turned off by default. There are two ways to start mining.

* Use eiyarocli to set the parameter for mining to true. eiyarocli will interact with eiyarod and the node starts mining. Similarly, set the parameter to false to turn off mining.



