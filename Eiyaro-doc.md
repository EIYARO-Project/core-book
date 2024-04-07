 
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
![EIYARO-Node](https://github.com/EIYARO/ey-book/tree/main/image/node_01.png)



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


# Chapter 02 Interactive Tools

## 2.1 Introduction 

​	Eiyarocli and dashboard are two Eiyaro tools to interact with eiyarod. Eiyarocli is a command line tool and dashboard is a more user friendly Web Application. Both of them use RPC protocol to interact with eiyarod process.

​	Any operation related to tokens, accounts, transactions, wallets, mining, etc. can be managed by these interactive tools. 

​	In this chapter, we will analyze the ideas and code behind eiyarocli and eiyarod and briefly introduce dashboard.



## 2.2 Eiyarocli 

### 2.2.1 Commandline arguments for Eiyarocli  

​	There are two ways to use eiyarocli. 
​	1. Using the `flag` (eiyarocli [flags]). Use `-h` to open help. 
​	2. Using a sub-command (eiyarocli [command]). All sub-commands can be seen by `-h` or `-help`. The help section is pretty detailed, useful and easy to understand.


```
$ ./eiyarocli -h
Eiyarocli is a commond line client for eiyaro core (a.k.a. eiyarod)

Usage:
  eiyarocli [flags]
  eiyarocli [command]

Available Commands:
  build-transaction             
  check-access-token            
  create-access-token           
  create-account                
  create-account-receiver       
  create-asset                  
  create-key                  
  create-transaction-feed     
  decode-program                
  decode-raw-transaction        
  delete-access-token       
  delete-account               
  delete-key                 
  delete-transaction-feed      
  estimate-transaction-gas      
  gas-rate                      
  get-asset                  
  get-block                     
  get-block-count               
  get-block-hash              
  get-block-header              
  get-difficulty                
  get-hash-rate                
  get-transaction             
  get-transaction-feed        
  get-unconfirmed-transaction   
  help                          
  is-mining                     
  list-access-tokens            
  list-accounts                 
  list-addresses               
  list-assets                
  list-balances                
  list-keys                   
  list-pubkeys                  
  list-transaction-feeds    
  list-transactions             
  list-unconfirmed-transactions 
  list-unspent-outputs         
  net-info                      
  rescan-wallet                 
  reset-key-password            
  set-mining                    
  sign-message                
  sign-transaction             
  submit-transaction           
  update-asset-alias            
  update-transaction-feed      
  validate-address             
  verify-message             
  version                     
  wallet-info                 
```

​	The following sections will cover how to use sub-commands



### 2.2.2 Use Eiyarocli to View Nodes 

​	Use `net-info` to get the information about running Eiyaro node.
​	Run `eiyarocli net-info -h` to get help information.


```
$ ./eiyarocli net-info -h
Print the summary of network
Usage:
  eiyarocli net-info [flags]
Flags:
  -h, --help   help for net-info
```

​	You can see that `net info` is very simple and has only one flag `-h`.


```
$ ./eiyarocli net-info
{
  "current_block": 36714,
  "highest_block": 36714,
  "listening": true,
  "mining": false,
  "network_id": "mainnet",
  "peer_count": 10,
  "syncing": false,
  "version": "1.0.5+2bc2396a"
}
```

​	Description of the response :

* current_block：The height of the current block is 36714 on this node.
* highest_block：The highest block of the network is 36714, which means the node has all generated blocks.
* listening：Whether the node is listening to the network.
* mining：Whether the node is mining.
* network_id：Identify the type of network (mainnet, wisdom, solonet) this node is listening to.
* peer_count：Show the number of other nodes it connect.
* syncing：Whether the node is syncsynchronizing or not.
* version：The eiyaro version this node is running.



### 2.2.3 Eiyarocli Sub-command example 

​	In this section, we will analyze `net-info` part of eiyarocli code. Other sub-commands also follow the same pattern.

​	Here is the structure of eiyarocli code.


```
$ tree cmd/eiyarocli/
cmd/eiyarocli/
├── eiyarocli
├── commands			
│   ├── accesstoken.go		
│   ├── account.go		
│   ├── asset.go			
│   ├── block.go			
│   ├── eiyarocli.go	
│   ├── key.go			
│   ├── mining.go		
│   ├── net.go			
│   ├── program.go	
│   ├── template.go		
│   ├── transaction.go		
│   ├── txfeed.go	
│   ├── util.go			
│   ├── version.go	
│   └── wallet.go		
└── main.go			
```

***1. Introduction to Cobra ***

​		Eiyarocli is based on Cobra. Cobra is both a library for creating powerful modern CLI (command-line interface) applications as well as a program to generate scaffolding for commandline applications and related command files. Many of the most widely used Go projects such as Kubernetes, docker, etcd, etc also use Cobra.


​	Cobra is built on a structure of commands, arguments & flags.Commands is the central point of the application. Each interaction that the application supports will be contained in a Command. A command can have children commands and optionally run an action.Args are things and Flags are modifiers for those actions. A flag is a way to modify the behavior of a command. Cobra supports fully POSIX-compliant flags as well as the Go flag package. A Cobra command can define flags that persist through to children commands and flags that are only available to that command.

​	Here is an example to show how to create a CLI using Cobra:


***（1）Installing Cobra***

​	Using Cobra is easy. First, use `go get` to install the latest version of the library. This command will install the `cobra` generator executable along with the library and it's dependencies. After installing, you can find cobra in `GOPATH/bin`.


```
$ go get -v github.com/spf13/cobra/cobra
```

***（2）Using Cobra to build an application***

​	We are now going to create a command line program named `demo`. Open the terminal, navigate to `GOPATH/src`, and run the following command:



```
$ cobra init demo 
```

​	There will be a `demo` under `GOPATH/src` as follows:


```
$ tree demo/
demo/
├── LICENSE
├── cmd
│   └── root.go
└── main.go
```

​	This program does not have any sub commands. We can add sub commands later if our applicaiton needs it.

​	​To add a bit more functionality, Open `main.go`:


```
package main

import "demo/cmd"

func main() {
    cmd.Execute()
}
```

​	The `main()` function runs `Execute()`, which is defined in `demo/cmd/root.go`. `root.go` file has some initial imports and the function definition for `Execute`. Cobra creates many imports by default but all of them are not necessary. For eg. `viper`, it is a library for reading configuration files. We can comment it out since we don't intend to use it here. If we don't comment it out, this will be a 10MB application.

​	Create a new file `imp.go` in `demo` and copy these contents:


```
package imp

import(
    "fmt"
)

func Show(name string, age int) {
    fmt.Printf("My Name is %s, My age is %d\n", name, age)
}
```

​	In `imp.go`, `show()` has two arguments: `name` and `age`, which are printed by `fmt`. The `demo` directory now looks as follows:


```
$ tree demo/
demo/
├── LICENSE
├── cmd
│   └── root.go
├── imp
│   ├── imp.go
└── main.go
```

​	All Cobra commands need to be defined by GO struct `cobra.Command`. To implement `demo`, we shall make some changes to RootCmd in `demo/cmd/root.go`.


```
var RootCmd = &cobra.Command{
    Use:   "demo",
    Short: "A test demo",
    Long:  `Demo is a test appcation for print things`,
    // Uncomment the following line if your bare application
    // has an action associated with it:
    Run: func(cmd *cobra.Command, args []string) {
        if len(name) == 0 {
            cmd.Help()
            return
        }
        imp.Show(name, age)
    },
}
```

​	Here, we defined `RootCmd` as a `Command` struct with an impementation for `Run` function, and this function will be executed by `Execute` method. We still need to parse command line arguments before actually running the command's `Run` method and that is done in `init()` function in cmd package. `init()` has higher priority than `main()` and will be executed by default after each package initializes. `init()` is usually used to initialize variables. Here we use `init()` to parse command line arguments before `main()`.

​	Make the following changes in `demo/cmd/root.go`:


```
var (
    name string
    age  int
)

func init() {
    RootCmd.Flags().StringVarP(&name, "name", "n", "", "person's name")
    RootCmd.Flags().IntVarP(&age, "age", "a", 0, "person's age")
}
```

​	We can run `demo` from command line now:


```
$ go run main.go
Usage:
  demo [flags]

Flags:
  -a, --age int         person's age
      --config string   config file (default is $HOME/.demo.yaml)
  -h, --help            help for demo
  -n, --name string     person's name
  -t, --toggle          Help message for toggle
```

​	If we want to create a CLI application with sub commands, just need to run `cobra add` to add sub commands to it. Here we add a sub command `server` as follows:


```
$ cd demo
$ cobra add server
```

​	This will add a file `server.go` in `demo/cmd/` :


```
$ tree demo/
demo/
├── LICENSE
├── cmd
│   ├── root.go
│   └── server.go
├── imp
│   └── imp.go
└── main.go
```

​	We now configure the `server` just like we did in `root.go`. Here it is : 


```
$ go run main.go
Usage:
  demo [flags]
  demo [command]

Available Commands:
  help        Help about any command
  server      A brief description of your command

Flags:
  -a, --age int         person's age
      --config string   config file (default is $HOME/.demo.yaml)
  -h, --help            help for demo
  -n, --name string     person's name
  -t, --toggle          Help message for toggle

Use "demo [command] --help" for more information about a command.
```



***2.eiyarocli design***


​	In the last section, we looked at how to use Cobra to create a CLI application. With a good understanding of how Cobra works, it is easy to read the rest of eiyarocli code. Just like in the `demo` command application, eiyarocli also runs `cmd.Execute()` in the main function at the start of the eiyarocli application. `cmd.Execute()` calls the `Execute()` method from the package named `commands. cmd` is just another name given to package `commands` when it is imported into `main.go`.


```
cmd/eiyarocli/main.go
func main() {
    runtime.GOMAXPROCS(runtime.NumCPU())
    cmd.Execute()
}
```

​	When the program runs `cmd.Execute()`, it first runs `init()` function in `commands` to parse command line arguments. After that, `Execute()` will be run. In `Execute()`, `AddCommands()` s run first and this adds sub commands to Eiyarocli Cmd, while `AddTemplateFunc()` is for adding templates to sub commands. After these (`init()`,`AddCommands()` ,`AddTemplateFunc()`) are run, it will start to execute the commands. When running Eiyarocli `cmd.Execute()`, Cobra will jump into the sub commands given by the user at command input and run its `Run`.


```
cmd/eiyarocli/commands/eiyarocli.go
func Execute() {

    AddCommands()
    AddTemplateFunc()

    if _, err := EiyarocliCmd.ExecuteC(); err != nil {
        os.Exit(util.ErrLocalExe)
    }
}
```

​	Here we take `create-access-token` as the example to analyze what its `Run` does. It takes `arg[0]` as tokenID, then calls `ClientCall` in `util` to get the path of `/create-access-token` and sends tokenID to create a new token.


```
eiyaro/cmd/eiyarocli/commands/accesstoken.go
var createAccessTokenCmd = &cobra.Command{
    Use:   "create-access-token <tokenID>",
    Short: "Create a new access token",
    Args:  cobra.ExactArgs(1),
    Run: func(cmd *cobra.Command, args []string) {
        var token accessToken
        token.ID = args[0]

        data, exitCode := util.ClientCall("/create-access-token", &token)
        if exitCode != util.Success {
            os.Exit(exitCode)
        }
        printJSON(data)
    },
}
```

​	`ClientCall` encapsulates a RPC client, which can compose requests based on path and arguments given by the caller and parse the response from RPC server.


```
eiyaro/util/util.go
func ClientCall(path string, req ...interface{}) (interface{}, int) {

    var response = &api.Response{}
    var request interface{}

    if req != nil {
        request = req[0]
    }

    client := MustRPCClient()
    client.Call(context.Background(), path, request, response)

    switch response.Status {
    case api.FAIL:
        jww.ERROR.Println(response.Msg)
        return nil, ErrRemote
    case "":
        jww.ERROR.Println("Unable to connect to the eiyarod")
        return nil, ErrConnect
    }

    return response.Data, Success
}
```

​	The `create-access-token` completes its execution path after the response is displayed.



## **2.3  Interactive Dashboard**

​	​Eiyaro Dashboard is a seperate project available here.(https://github.com/EIYARO/ey-dashboard). Compiled and compressed version of Dashboard is hard-coded in eiyarod and you can find it's code in `dashboard/dashboard.go`. The `hanlder` information is in `api/api.go`.


```
mux := http.NewServeMux()
mux.Handle("/dashboard/", http.StripPrefix("/dashboard/", static.Handler{
	Assets:  dashboard.Files,
	Default: "index.html",
}))
```
# Chapter 03 Initialization, Starting and Stopping eiyarod

## 3.1 

​	Before you run eiyaro node for the first time, we will need to initialize eiyaro node by defining configuration parameters for network, database location etc.



* eiyarod Commandline flags
* eiyarod Initialization along with initialization of different modules
* eiyarod Daemon.





## 3.2 eiyarod Commandline flags

​	Commandline programs accept input arguments. Programming lanaguages offer functions and libraries to parse these input arguments. In GoLang, 'flag' package handles parsing the input arguments. Sub-commands and other parameters supported by eiyarod are as follows: 


```
$ ./eiyarod -h
Multiple asset management.

Usage:
  eiyarod [command]

Available Commands:
  help        
  init        
  node       
  version     

coral[coralmac] eiyarod $ ./eiyarod node -h
Run the eiyarod

Usage:
  eiyarod node [flags]

Flags:
      --auth.disable                Disable rpc access authenticate
      --chain_id string             Select network type
  -h, --help                        Help for node
      --log_file string             Select file to log
      --log_level string            Select level of log
      --mining                      Enable mining
      --p2p.dial_timeout int        Set dial timeout (default 3)
      --p2p.handshake_timeout int   Set handshake timeout (default 30)
      --p2p.laddr string            Node listen address.
      --p2p.max_num_peers int       Set max num peers (default 50)
      --p2p.pex                     Enable Peer-Exchange  (default true)
      --p2p.seeds string            Comma delimited host:port seed nodes
      --p2p.skip_upnp               Skip UPNP configuration
      --prof_laddr string           Use http to profile eiyarod programs
      --simd.enable                 Enable simd, which is used to optimize Tensority
      --vault_mode                  Run in the offline enviroment
      --wallet.disable              Disable wallet
      --wallet.rescan               Rescan wallet
      --web.closed                  Lanch web browser or not

Global Flags:
      --home string   Root directory for config and data
      --trace         Enable trace and show information bout stack when something going wrong
```

## 3.3 Eiyaro Daemon

​	Daemon is a sepecial process, which keeps on running in the background after it starts and it does not stop until it receives a `kill term` signal.

​	eiyarod and eiyarocli both use Cobra library to handle command line input so we will skip that part here and look into other details of eiyaro daemon.


```
$ tree cmd/eiyarod/
cmd/eiyarod/
├── eiyarod
├── commands
│   ├── init.go	     Initialize the network of the node
│   ├── root.go		   The directory of root
│   ├── run_node.go  node deamon
│   └── version.go	 node version
├── main.go		

```

## 3.4 eiyarod Initialization

​	Eiyaro daemon initializes different modules based on input flags passed to it. This line of `code node.NewNode(config)` starts the functionality of a eiyaro node.



### 3.4.1 Node

```
node/node.go
type Node struct {
	cmn.BaseService
 
	// config
	config *cfg.Config
 
	syncManager *netsync.SyncManager
 
	wallet       *w.Wallet
	accessTokens *accesstoken.CredentialStore
	api          *api.API
	chain        *protocol.Chain
	txfeed       *txfeed.Tracker
	cpuMiner     *cpuminer.CPUMiner
	miningPool   *miningpool.MiningPool
	miningEnable bool
}
```

​	Description of `Node`:

- cmn.BaseService：Service management
- config：Global configuration of the node
- syncManager：Synchronization of transactions and blocks
- wallet：Local wallet management
- accessTokens：Token management, user's access credentials
- api：Api Server
- chain：Local chain
- txfeed：Not be used
- cpuMiner：cpu mining
- miningPool：mining pool
- miningEnable：Enable mining


​	`node.NewNode(config)` creates a new `Node`, which contains all the functionality of eiyarod.

​	`cmn.BaseService` is a service management framwork based on tendermint.  `Node` object calls these methods `OnStart/OnStop/IsRunning` on BaseService in order to manage node lifecycle. Tendermint ensures these operations are not run repeatedly.


### 3.4.2 Configuration 
​	Config variable is initialized with default configuration before running `node.NewNode(config)`. Here we will describe the default configuration in detail:


 cmd/eiyarod/commands/root.go

```
config = cfg.DefaultConfig()
```

​	Eiyaro daemon defines a global variable named `config`, which represents eiyaro daemon configuration and is set by default when daemon starts.


config/config.go

```
func DefaultConfig() *Config {
	return &Config{
		BaseConfig: DefaultBaseConfig(),
		P2P:        DefaultP2PConfig(),
		Wallet:     DefaultWalletConfig(),
		Auth:       DefaultRPCAuthConfig(),
		Web:        DefaultWebConfig(),
		Simd:       DefaultSimdConfig(),
	}
}
```

​	There are six default arguments for different modules. We can divide them into three groups: BaseConfig, P2PConfig and other configuration.


 

***（1）BaseConfig  ***

​	BaseConfig includes data directory, log level, listening address and other relevant configuration.


config/config.go

```
type BaseConfig struct {
	RootDir string `mapstructure:"home"` 		// The root directory for all data.
	ChainID string `mapstructure:"chain_id"` 	// The ID of the network to json. There are three options: mainnet,testnet and solonet
	LogLevel string `mapstructure:"log_level"` 	// log level to set
	PrivateKey string `mapstructure:"private_key"` 	// A JSON file containing the private key to use as a validator in the consensus protoco
	Moniker string `mapstructure:"moniker"` 	// A custom human readable name for this node(default anonymous) 
	ProfListenAddress string `mapstructure:"prof_laddr"` 	// TCP or UNIX socket address for the profiling server to listen on.(default disabled)
	FastSync bool `mapstructure:"fast_sync"` 	// Fast synchronization(default enabled)
	Mining bool `mapstructure:"mining"`		 // Mining.(default disabled)
	FilterPeers bool `mapstructure:"filter_peers"` // not be used(default false)
	TxIndex string `mapstructure:"tx_index"` 	// not be used
	DBBackend string `mapstructure:"db_backend"` // Database backend: leveldb | memdb
	DBPath string `mapstructure:"db_dir"` 		// Database directory
	KeysPath string `mapstructure:"keys_dir"` 	// Keystore directory
	HsmUrl string `mapstructure:"hsm_url"` 		// not be used
	ApiAddress string `mapstructure:"api_addr"` 	// Address of Api Server(default 0.0.0.0:9888)
	VaultMode bool `mapstructure:"vault_mode"`    // No network
	Time time.Time 			// not be used
	LogFile string `mapstructure:"log_file"` // log file name
```

​	`config/toml.go` file provides some default arguments for configuration. e.g, APIAddress is read form `api_addr` value in config file.


config/toml.go

```
var defaultConfigTmpl = `# This is a TOML config file.
fast_sync = true
db_backend = "leveldb"
api_addr = "0.0.0.0:9888"
`
var mainNetConfigTmpl = `chain_id = "mainnet"
[p2p]
laddr = "tcp://0.0.0.0:46657"
seeds = "xxx.xxx.xxx.xxx:46657"
`
```

***（2）P2PConfig ***

​	P2PConfig is used in P2P protocol. It includes local listening ports, dial timeout and address book, etc.



config/config.go

```
type P2PConfig struct {
	RootDir          string `mapstructure:"home"` 	
	ListenAddress    string `mapstructure:"laddr"` 	// tcp://0.0.0.0:46656
	Seeds            string `mapstructure:"seeds"` 	//
	SkipUPNP         bool   `mapstructure:"skip_upnp"` 	// false
	AddrBook         string `mapstructure:"addr_book_file"` 	//peer
	AddrBookStrict   bool   `mapstructure:"addr_book_strict"` 	
	PexReactor       bool   `mapstructure:"pex"` 		
	MaxNumPeers      int    `mapstructure:"max_num_peers"`	 // 50
	HandshakeTimeout int    `mapstructure:"handshake_timeout"` 	// 30s
	DialTimeout      int    `mapstructure:"dial_timeout"` 	// 3s
}
```

***Note***: In Bitcoin, nodes use DNS to find seed node and query it to get the IP addresses of other nodes. In Eiyaro, seed node IP addresses are hard coded into the code. We will explain this in more detail in ***Chapter10 P2P Network***.




***（3）Other Configuration***

​	WalletConfig is used to configure the local wallet. It includes a flag to enable or disable the wallet functionality and another flag to rescan the entire wallet.



config/config.go

```
type WalletConfig struct {
	Disable bool `mapstructure:"disable"`	// Enable the local wallet(default false) 
	Rescan  bool `mapstructure:"rescan"`	// Rescan the wallet
}

type RPCAuthConfig struct {
	Disable bool `mapstructure:"disable"` // Enable the authorization of Api Server(default false)
}

type WebConfig struct {
	Closed bool `mapstructure:"closed"` // Start eiyaro-dashboard(default false)
}

type SimdConfig struct {
	Enable bool `mapstructure:"enable"` // Enable the optimization of Tenaority CPU
}
```

​	After declaring `config = DefaultConfig()`, `init()` method will assign the config attributes. Here is the code:



cmd/eiyarod/commands/run_node.go

```
func init() {
	runNodeCmd.Flags().String("prof_laddr", config.ProfListenAddress, "Use http to profile eiyarod programs")
	runNodeCmd.Flags().Bool("mining", config.Mining, "Enable mining")

	runNodeCmd.Flags().Bool("simd.enable", config.Simd.Enable, "Enable SIMD mechan for tensority")

	runNodeCmd.Flags().Bool("auth.disable", config.Auth.Disable, "Disable rpc access authenticate")

	runNodeCmd.Flags().Bool("wallet.disable", config.Wallet.Disable, "Disable wallet")
	runNodeCmd.Flags().Bool("wallet.rescan", config.Wallet.Rescan, "Rescan wallet")
	runNodeCmd.Flags().Bool("vault_mode", config.VaultMode, "Run in the offline enviroment")
	runNodeCmd.Flags().Bool("web.closed", config.Web.Closed, "Lanch web browser or not")
	runNodeCmd.Flags().String("chain_id", config.ChainID, "Select network type")

	// log level
	runNodeCmd.Flags().String("log_level", config.LogLevel, "Select log level(debug, info, warn, error or fatal")

	// p2p flags
	runNodeCmd.Flags().String("p2p.laddr", config.P2P.ListenAddress, "Node listen address. (0.0.0.0:0 means any interface, any port)")
	runNodeCmd.Flags().String("p2p.seeds", config.P2P.Seeds, "Comma delimited host:port seed nodes")
	runNodeCmd.Flags().Bool("p2p.skip_upnp", config.P2P.SkipUPNP, "Skip UPNP configuration")
	runNodeCmd.Flags().Bool("p2p.pex", config.P2P.PexReactor, "Enable Peer-Exchange ")
	runNodeCmd.Flags().Int("p2p.max_num_peers", config.P2P.MaxNumPeers, "Set max num peers")
	runNodeCmd.Flags().Int("p2p.handshake_timeout", config.P2P.HandshakeTimeout, "Set handshake timeout")
	runNodeCmd.Flags().Int("p2p.dial_timeout", config.P2P.DialTimeout, "Set dial timeout")

	// log flags
	runNodeCmd.Flags().String("log_file", config.LogFile, "Log output file")

	RootCmd.AddCommand(runNodeCmd)
}
```

​	In `init()` method, there are many different types of flag which are bound to `config`. Here is an example: 



```
runNodeCmd.Flags().Bool("mining", config.Mining, "Enable mining")
```

​	Description of the above code:

* Define a flag argument, which is Bool
* The name of this flag is mining
* This flag value will be assigned to `config.Mining`
* The description of this flag is `Enable mining`


​	This concludes the initialization of eiyaro daemon configuration.




### 3.4.3 Create File Lock

​	In this section,  we will look into what happens after initializaiton is complete and the the program starts running.

​	In Eiyaro, a data directory(specified by `--root`) can only be read and written by one eiyaro daemon at a time. That is because the LevelDB Key-Value store runs in a single process. If multiple processes write to the same file, the consistency of data cannot be ensured. To ensure only one process reads or writes the data file at a time, eiyaro uses a file lock.



node/node.go

```
if err := lockDataDirectory(config); err != nil {
	cmn.Exit("Error: " + err.Error())
}

func lockDataDirectory(config *cfg.Config) error {
	_, _, err := flock.New(filepath.Join(config.RootDir, "LOCK"))
	if err != nil {
		return errors.New("datadir already used by another process")
	}
	return nil
}
```

​	When eiyaro starts, `lockDataDirectory()` function use `flock` to create a `LOCK` file in the `RootDir` directory. If one eiyarod process locks the `inode` of a file during the start, then any other process which tries to start at the same time, will get an error message as defined in `errors.New` and this new process will exit failing to aquire the `LOCK`. flock is used for detecting if a eiyarod process is already running.


​	There are three types of flock:

* LOCK_SH：Shared lock. More than one process may hold a shared lock for a given file at a given time.
* LOCK_EX：Exclusive lock. Only one process may hold an exclusive lock for a given file at a given time.
* LOCK_UN：Remove an existing lock held by this process.


​	The `flock` package uses an exclusive lock `LOCK_EX`, which means only one process may hold this lock for a given file at a given time. The code for flock is here:


vendor/github.com/prometheus/prometheus/util/flock/flock_unix.go

```
func (l *unixLock) set(lock bool) error {
	how := syscall.LOCK_UN
	if lock {
		how = syscall.LOCK_EX
	}
	return syscall.Flock(int(l.f.Fd()), how|syscall.LOCK_NB)
}
```



### 3.4.4 Initialize Network type

​	We have mentioned before that Eiyaro has three types of network, mainnet, testnet, solonet.


node/node.go

```
initActiveNetParams(config)

func initActiveNetParams(config *cfg.Config) {
	var exist bool
	consensus.ActiveNetParams, exist = consensus.NetParams[config.ChainID]
	if !exist {
		cmn.Exit(cmn.Fmt("chain_id[%v] don't exist", config.ChainID))
	}
}
```

​	Here `initActiveNetParams()` initializes the network according to the `chain_id` set in config. `consensus.ActiveNetParams()` object refers to the type of current network mode used in Eiyaro. The `consensus.ActiveNetParams` object is used all over the eiyaro code to identify what network this node is connected to.


consensus/general.go

```
var ActiveNetParams = MainNetParams

var NetParams = map[string]Params{
	"mainnet": MainNetParams, 
	"wisdom":  TestNetParams, 
	"solonet": SoloNetParams,
}

var MainNetParams = Params{
	Name:            "main",
	Bech32HRPSegwit: "bm",
	Checkpoints: []Checkpoint{
		{10000, bc.NewHash([32]byte{0x93, 0xe1, 0xeb, 0x78, 0x21, 0xd2, 0xb4, 0xad, 0x0f, 0x5b, 0x1c, 0xea, 0x82, 0xe8, 0x43, 0xad, 0x8c, 0x09, 0x9a, 0xb6, 0x5d, 0x8f, 0x70, 0xc5, 0x84, 0xca, 0xa2, 0xdd, 0xf1, 0x74, 0x65, 0x2c})},
		{20000, bc.NewHash([32]byte{0x7d, 0x38, 0x61, 0xf3, 0x2c, 0xc0, 0x03, 0x81, 0xbb, 0xcd, 0x9a, 0x37, 0x6f, 0x10, 0x5d, 0xfe, 0x6f, 0xfe, 0x2d, 0xa5, 0xea, 0x88, 0xa5, 0xe3, 0x42, 0xed, 0xa1, 0x17, 0x9b, 0xa8, 0x0b, 0x7c})},
		{30000, bc.NewHash([32]byte{0x32, 0x36, 0x06, 0xd4, 0x27, 0x2e, 0x35, 0x24, 0x46, 0x26, 0x7b, 0xe0, 0xfa, 0x48, 0x10, 0xa4, 0x3b, 0xb2, 0x40, 0xf1, 0x09, 0x51, 0x5b, 0x22, 0x9f, 0xf3, 0xc3, 0x83, 0x28, 0xaa, 0x4a, 0x00})},
		{40000, bc.NewHash([32]byte{0x7f, 0xe2, 0xde, 0x11, 0x21, 0xf3, 0xa9, 0xa0, 0xee, 0x60, 0x8d, 0x7d, 0x4b, 0xea, 0xcc, 0x33, 0xfe, 0x41, 0x25, 0xdc, 0x2f, 0x26, 0xc2, 0xf2, 0x9c, 0x07, 0x17, 0xf9, 0xe4, 0x4f, 0x9d, 0x46})},
		{50000, bc.NewHash([32]byte{0x5e, 0xfb, 0xdf, 0xf5, 0x35, 0x38, 0xa6, 0x0b, 0x75, 0x32, 0x02, 0x61, 0x83, 0x54, 0x34, 0xff, 0x3e, 0x82, 0x2e, 0xf8, 0x64, 0xae, 0x2d, 0xc7, 0x6c, 0x9d, 0x5e, 0xbd, 0xa3, 0xd4, 0x50, 0xcf})},
		{62000, bc.NewHash([32]byte{0xd7, 0x39, 0x8f, 0x23, 0x57, 0xf9, 0x4c, 0xa0, 0x28, 0xa7, 0x00, 0x2b, 0x53, 0x9e, 0x51, 0x2d, 0x3e, 0xca, 0xc9, 0x22, 0x59, 0xfc, 0xd0, 0x3f, 0x67, 0x1a, 0x0a, 0xb1, 0x02, 0xbf, 0x2b, 0x03})},
	},
}
```

​	`ActiveNetParams` uses mainnet parameters by default. The parameters of `MainNetParam` are as follows：

* Bech32HRPSegw：Segregated Witness, an upgrade of protocol. See more details in Chapter05.
* Checkpoints：Checkpoint has the height and hash of a block. This information is used to validate blocks when node runs in a fast synchronization mode. In general, when a main network is upgraded, the hardcoded information of checkpoints is also updated in the code.


​	Checkpoints have two uses, one is to prevent forking, which means that the fork that was created before checkpoints will not be accepted by other nodes. This can also protect the network from 51% attack since attackers can't change transactions that happened before checkpoints. The other use is for fast synchronization between nodes, see more details in Chapter-10.



### 3.4.5 Database Initialization 

​	All data (blocks, transactions, etc.) on the blockchain needs to be stored in local K/V database once a pubilc blockchain is created. Eiyaro uses LevelDB as its database .


node/node.go

```
coreDB := dbm.NewDB("core", config.DBBackend, config.DBDir())
store := leveldb.NewStore(coreDB)
```

database/leveldb/store.go

```
type Store struct {
	db    dbm.DB
	cache blockCache
}
```

​	`dbm` uses the db library from tendermint framework. `dbm.NewDB` returns a `DB` object, which provides the LevelDB implementation in GO along with many other functions related to memory mapping, the directory structure of file system etc.


​	`dbm.NewDB` returns a `DB` object and needs three parameters: name of the db, key value store used by db (default is LevelDB) and the path to the db datastore. `leveldb.NewStore()` function returns a `Store` object, which is an encapsulation of LevelDB and implements functions related to blocks cache, blocks validation, blocks status, blocks query, etc. based on LevelDB.


### 3.4.6 Transaction Pool Initialization

​	When transactions are broadcast to the network, miners receive them and add them to local TxPool (transaction pool). Eiyaro TxPool is a limited buffer that can store upto 10,000 transactions by default. If the number of transactions is over 10,000, an error message "transaction pool reach the max number" will be returned.


node/node.go

```
txPool := protocol.NewTxPool()

protocol/txpool.go
func NewTxPool() *TxPool {
	return &TxPool{
		lastUpdated: time.Now().Unix(),
		pool:        make(map[bc.Hash]*TxDesc),
		utxo:        make(map[bc.Hash]bc.Hash),
		errCache:    lru.New(maxCachedErrTxs),
		msgCh:       make(chan *TxPoolMsg, maxMsgChSize),
	}
}
```

​	`protocol.NewTxPool()` method returns  `TxPool` object. Here we only introduce the initialization of TxPool. The following chapters will cover the transaction pool in depth.


### 3.4.7 Create a Local Blockchain 

​	When the node starts for the first time, it will check the status of local persistent storage. If the status is not yet initialized, the node initializes the storage by adding the genesis block to the blockchain at height-0.


node/node.go

```
chain, err := protocol.NewChain(store, txPool)
if err != nil {
	cmn.Exit(cmn.Fmt("Failed to create chain structure: %v", err))
}
```

​	Here, `protocol.NewChain()`method returns a `Chain` object and needs two parameters store and txPool. The Chain object manages the entire Eiyaro blockchain.


protocol/protocol.go

```
func NewChain(store Store, txPool *TxPool) (*Chain, error) {
	c := &Chain{
		orphanManage:   NewOrphanManage(),
		txPool:         txPool,
		store:          store,
		processBlockCh: make(chan *processBlockMsg, maxProcessBlockChSize),
	}
	c.cond.L = new(sync.Mutex)

	storeStatus := store.GetStoreStatus()
	if storeStatus == nil {
		if err := c.initChainStatus(); err != nil {
			return nil, err
		}
		storeStatus = store.GetStoreStatus()
	}

	var err error
	if c.index, err = store.LoadBlockIndex(); err != nil {
		return nil, err
	}

	c.bestNode = c.index.GetNode(storeStatus.Hash)
	c.index.SetMainChain(c.bestNode)
	go c.blockProcesser()
	return c, nil
}
```

​	The `NewChain()` method runs these steps:

（1） Instantiate  `Chain` object

（2）`store.GetStoreStatus()` can get the storage stautus of local blockchain. If it is nil, the blockchain hasn't been initialized and so it runs `initChainStatus` to add the genesis block to initialize local blockchain.

（3）`store.LoadBlockIndex()`  loads the block index and reads all `Block Header` information from database and caches the header information into memory to speed up block header lookups.

（4）`c.index.SetMainChain` refers to the latest block the node has synchronized to local storage.

（5）`go c.blockProcesser()` will start a go routine to update blocks information of local blockchain in storage. 


### 3.4.8 Local Wallet Initialization

​	The local wallet feature is enabled by default. Here is the code:


node/node.go

```
hsm, err := pseudohsm.New(config.KeysDir())
if err != nil {
	cmn.Exit(cmn.Fmt("initialize HSM failed: %v", err))
}

if !config.Wallet.Disable {
	walletDB := dbm.NewDB("wallet", config.DBBackend, config.DBDir())
	accounts = account.NewManager(walletDB, chain)
	assets = asset.NewRegistry(walletDB, chain)
	wallet, err = w.NewWallet(walletDB, accounts, assets, hsm, chain)
	if err != nil {
		log.WithField("error", err).Error("init NewWallet")
	}

	// trigger rescan wallet
	if config.Wallet.Rescan {
		wallet.RescanBlocks()
	}
}
```

​	The code above is run when eiyaro node is start and runs the following steps:

（1）Create `hsm` object, which manages keystore file that stores private key in JSON format. Keystore file is just a string produced by encrypting private key and need to be used along with wallet password.

（2）Create wallet database

（3）Create account management object `walletDB`

（4）Create asset management object `accounts`

（5）Instantiate `Wallet`

（6）`RescanBlocks()` rescans all local blocks and updates local wallet information.



### 3.4.9 Network Synchronization 

​	P2P communication module is managed by SyncManager, which manages the synchronization of information(transactions and blocks) between nodes in the business layer. Here is the code that initializes network synchronization:


node/node.go

```
const (
	maxNewBlockChSize = 1024
)

newBlockCh := make(chan *bc.Hash, maxNewBlockChSize)
syncManager, _ := netsync.NewSyncManager(config, chain, txPool, newBlockCh)
go newPoolTxListener(txPool, syncManager, wallet)
```

​	The description of main parameters :

* newBlockCh：Channel is used to broadcast information about newly mined blocks quickly to other nodes. The size of chanel is 1024.
* netsync.NewSyncManager：Instantiate `syncManager` object,  which synchronizes information of blocks and transactions among nodes.
* newPoolTxListenner：Run a go routine to listen to transactions in TxPool and send transactions to `syncManager` object or local wallet.


​	See more details in `Chapter 10 P2P Network`.


### 3.4.10 pprof performance analysis tool

​	`pprof` is a tool for visualization and analysis of profiling data in GO library. `ppoof` is used to analyze memory and CPU, and observe call stacks, etc. It can generate both text and graphical reports (through the use of the dot visualization package). (See more details on https://golang.org/pkg/net/http/pprof/). In eiyaro, `ppof` is disabled by default, users can enable it by `--prof_laddr`. Here is the code that initializes pprof over http:



node/node.go

```
profileHost := config.ProfListenAddress
if profileHost != "" {
	go func() {
		http.ListenAndServe(profileHost, nil)
	}()
}
```

### 3.4.11 CPU Mining Initialization 

​	Here is the code for initializing CPU mining:

node/node.go

```
node.cpuMiner = cpuminer.NewCPUMiner(chain, accounts, txPool, newBlockCh)
node.miningPool = miningpool.NewMiningPool(chain, accounts, txPool, newBlockCh)

if config.Simd.Enable {
	tensority.UseSIMD = true
}
```

​	`sim` is used to optimize Tensority.



## 3.5 Starting Eiyaro in Daemon mode 

​	Daemon is a long running process that performs system specific tasks and it will keep on running in background until it receives a specific signal and then exits. Most processes in Linux run as daemons.


​	The way we implement the daemon process in GO language is to listen for the standard `SIGTERM` signal. Until such a signal is received ,the process will be in a blocked state and will not exit. The process will change to non-blocking state only when a `SIGTERM` signal is received from outside. For more on linux signals refer here http://man7.org/linux/man-pages/man7/signal.7.html.

​	Here is the code that starts the node as a daemon:


cmd/eiyarod/commands/run_node.go

```
func runNode(cmd *cobra.Command, args []string) error {
	// ...
	n.RunForever()
	// ...
}
```

node/node.go

```
func (n *Node) RunForever() {
	// Sleep forever and then...
	cmn.TrapSignal(func() {
		n.Stop()
	})
}
```

vendor/github.com/tendermint/tmlibs/common/os.go

```
func TrapSignal(cb func()) {
	c := make(chan os.Signal, 1)
	signal.Notify(c, os.Interrupt, syscall.SIGTERM)
	go func() {
		for sig := range c {
			fmt.Printf("captured %v, exiting...\n", sig)
			if cb != nil {
				cb()
			}
			os.Exit(1)
		}
	}()
	select {}
}
```

​	`signal.Notify()` listens for `Interrupt` and `Term` signals and relays them to channel `c`. The goroutine will wait for a signal message from channel `c`. `select {}` in `TrapSignal()` method keeps the current running process in a blocked state. After receiving Term, a callback function is called if it was defined otherwise `os.Exit(1)` will be run and then the daemon exits.


​	There are two ways to send `Term`: one is by running `kill -15 pid` on process Id, the other is using `ctrl+c` on keyboard.



## 3.6 Stopping Eiyaro Daemon 

​	When a eiyaro daemon receives the `Term` signal to terminate itself, the daemon needs to finish a few final house keeping tasks before exiting to make sure that node is not in an inconsistent state. The final tasks are to safely stop the mining process and safely stop the p2p synchronization.eds to finish the final work to exit, such as disabling mining, P2P synchronization, etc. Here is the code: 

​	Here is the code that does these final tasks:


node/node.go

```
func (n *Node) OnStop() {
	n.BaseService.OnStop()
	if n.miningEnable {
		n.cpuMiner.Stop()
	}
	if !n.config.VaultMode {
		n.syncManager.Stop()
	}
}
```

