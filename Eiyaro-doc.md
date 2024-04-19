 
# EIYARO blockchain architecture
 

## 1.1 Introduction 

​	Blockchain technology or a Distributed Ledger Technology was born in Bitcoin in 2008  with the publication of a paper titled "Bitcoin: A Peer-to-Peer Electronic Cash System", written under the alias of Satoshi Nakamoto.

​	Blockchain is a distributed ledger managed on a decentralized peer to peer computer network without any single trusted authority or a central server. The nodes in the network collectively adhere to well defined protocols for communication between peers, block creation, block storage and block validation to maintain ledger data reliably.

​	Types of Blockchains:

* Public Blockchains: In a public blockchain, there is no single trusted authority, manager or a central server. Any node that can follow the system rules can access the network and participate in the consensus process.

* Private Blockchains: Private blockchains are usually created and managed within an enterprise and the system rules are set according to the requirements of the enterprise. Some nodes can have special privileges compared to other nodes e.g only selected nodes can have block write permissions or only invited nodes can participate in the network. Private Blockchains can also be only partially decentralized.

* Consortium Blockchains: Consortium Blockchains are usually created and managed by a group of different agencies. Consortium Blockchain is a hybrid between a public and a private blockchain, and can also be only partially decentralized.

  

Eiyaro is an open source project written in GO language and hosted on Github (https://github.com/EIYARO-Project/core).

Learn about public blockchain technology by analysing and explaining the Eiyaro blockchain source code.

If Bitcoin represents the Blockchain 1.0 era and Ether with Turing-complete smart contracts represents the Blockchain 2.0 era, then Eiyaro with its multi-asset distributed ledger and Turing-complete smart contracts represents the Blockchain 2.5 era.

Eiyaro is based on the UTXO transaction model, which is similar to Bitcoin's, and offers additional features such as multi-asset transactions, Turing-complete smart contracts, and a new POW consensus algorithm called Tensor.



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

​	Smart contract implementation is the core and the most important part of Eiyaro. In the following chapters, we will introduce different smart contract models (UTXO, Account ), operation principle, and working mechanism of EVM. We will walk through the code to explain how smart contracts can complete transactions without a central trusted authority to interpret the rules of the contract.



### 1.3.5 Kernel Layer - EVM(Eiyaro Virtual Machine) 


​	Eiyaro Virtual Machine is designed to execute smart contract programs in a platform-independent environment. EVM is an important part of Eiyaro, which plays a vital role in the process of storing, processing and validating smart contracts.


​	Users can write smart contract programs in a simple high level programming language called Equity. Equity programs can be compiled into standard Eiyaro Virtual machine opcodes. EVM is a platform-independant virtual machine environment that is part of every eiyaro node in the eiyaro blockchain network.

​	In short, EVM is a code running environment build on the Eiyaro blockchain.



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


# 02 Interactive Tools

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


#  03 Initialization, Starting and Stopping eiyarod

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
      --simd.enable                 Enable simd, which is used to optimize Tensor
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
	Bech32HRPSegwit: "ey",
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

​	`sim` is used to optimize Tensor.



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


# Prisoner of war calculation

## The advantages of Tensor are mainly reflected in the following aspects:

1.	Efficient Utilisation of Computing Resources: the Tensor algorithm makes it possible for the miner to provide AI acceleration services while mining by combining blockchain and AI computing. This design greatly improves the utilisation of computing resources, allowing the miner to contribute to the AI field even during idle periods.

2.	Flexibility: the Tensor algorithm adjusts the difficulty of the computation through the difficulty value, making the algorithm more flexible and adjustable. This flexibility helps the algorithm to adapt to different network environments and computing needs, ensuring the stable operation of the blockchain.

3.	Innovative: as a new type of consensus algorithm, Tensor integrates matrix and tensor calculations into the consensus process, which is an innovative attempt. It not only brings new technical ideas to the blockchain field, but also provides a possibility for the combination of AI and blockchain.

4.	Promoting the integration of AI and blockchain: the exploration and practice of Tensor algorithm helps to promote the integration of AI and blockchain technology. This fusion can bring new application scenarios and business models to the two fields, further expanding the boundaries of the technology.

5.	Social value and economic benefits: by using the idle computing power of miners to provide acceleration for AI, Tensor not only improves the efficiency of resource utilisation, but may also create additional economic benefits for miners. At the same time, it also helps reduce the cost of AI computing and promote the popularity and application of AI technology.

6.	Security and decentralisation guarantee: the Tensor algorithm also inherits the security and decentralisation characteristics of blockchain technology. This means that it is able to ensure data security while avoiding the problems of single point of failure and centralised control.




## In EIYARO, the maximum number of connections of a seed node is an undisclosed security parameter, and the exact number of connections is not disclosed to avoid some attacks.

Dynamic topology reconfiguration technique DDVS is used to change the number of connections between the seed node and other nodes in the network through measures such as multi-layer wedging and same-layer node swapping, so as to enhance the robustness of the network.

To ensure network security, a penalty mechanism for exceeding the connection threshold is also used.

# The penalty mechanism for node connection count exceeding the threshold includes the following.

## 1.	Connection threshold exceeding detection mechanism

EIYARO monitors the number of connections of each node in real time and triggers the penalty mechanism once it finds that the number of connections of a node exceeds the preset threshold.

## 2.	Temporary connection blocking

For nodes exceeding the connection limit, EIYARO will temporarily prevent them from establishing new connections with other nodes in the network until the number of connections drops to a safe range.

## 3.	Reduced Connection Weight

The connection weight determines the maximum number of connections a node can maintain simultaneously. EIYARO will gradually reduce the connection weight of nodes that frequently exceed the connection limit, thus limiting their maximum connection capacity.

## 4.	Increasing connection costs

EIYARO will penalise nodes with an abnormal number of connections by increasing the cost (e.g., weight fuel) that nodes must pay when starting a new connection.

## 5.	Temporary isolation or permanent exclusion

If a node's behaviour is unusually severe, EIYARO may temporarily isolate it in an area or remove it from the network altogether.





# 04 Interface Layer

## 4.1 Introduction 

​	ApiServer is an important component of Eiyaro, which is used to listen to, process and response to requests. Its main functions are receiving users' requests, distributing them according to the routing rules and returningneir results to users.

 

## 4.2 Create a Simple HTTP Server

​	In GoLang, the `net/http` library offers HTTP programming related interfaces and encapsulates many functions such as TCP link and message parsing. `http.request` object and `http.ResponseWriter` object are already enough for users interaction. The arguments of requests will be sent and processed by the handler, which also writes the result to the `Response`. Here is the code of a simple HTTP Server:


```
package main

import (
	"net"
	"net/http"
)

func sayHello(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("Hello, World!"))
}

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/", sayHello)

	server := &http.Server{
		Handler: mux,
	}

	listener, err := net.Listen("tcp", ":8080")
	if err != nil {
		panic(err)
	}

	err = server.Serve(listener)
	if err != nil {
		panic(err)
	}
}
```

​	When this HTTP Service runs, it sends a request to local `8080` port and returns `Hello,Word!` .


​	An HTTP Service is created by these three steps:

* Instantiate `http.NewServeMux()` to get the route of `mux`, then add valid routers to `mux.Handle`. Each router has HTTP request method (GET, POST, PUT, DELET), URL and Handler callback function.
* Listen the `8080` port.
* Use the listening address as an argument. The HTTP Service serves external requests by running`Serve(listener) `.

 

## 4.3 Create HTTP Service 

### 4.3.1 Create API Object 

​	`ApiServer` is managed by `API` object. Before running `ApiServer`, `API` object need to be initialized. Here is the code:


node/node.go

```
func (n *Node) initAndstartApiServer() {
	n.api = api.NewAPI(n.syncManager, n.wallet, n.txfeed, n.cpuMiner, n.miningPool, n.chain, n.config, n.accessTokens)

	listenAddr := env.String("LISTEN", n.config.ApiAddress)
	env.Parse()
	n.api.StartServer(*listenAddr)
}
```

api/api.go

```
func NewAPI(sync *netsync.SyncManager, wallet *wallet.Wallet, txfeeds *txfeed.Tracker, cpuMiner *cpuminer.CPUMiner, miningPool *miningpool.MiningPool, chain *protocol.Chain, config *cfg.Config, token *accesstoken.CredentialStore) *API {
	api := &API{
		sync:          sync,
		wallet:        wallet,
		chain:         chain,
		accessTokens:  token,
		txFeedTracker: txfeeds,
		cpuMiner:      cpuMiner,
		miningPool:    miningPool,
	}
	api.buildHandler()
	api.initServer(config)

	return api
}
```

​	First of all, `API` object is initialized by `api.NewAPI`. There are many arguments in `ApiServer`:

* listenAddr：Local port. If the system environment variable `LISTEN` isn't set, `config.ApiAddress` (default 9888) will be used as `listenAddr`.
* n.api.StartServer: Listen local port addresses and start HTTP Service.




 	`NewAPI()` method has three operations:

* Instantiate `API` object.
* Add routers by `api.buildHandle()` method.
* Instantiate `http.Server`, set `auth`, etc. by `api.initServer()` method.

 

### 4.3.2 Create router 

​	HTTP requests are sent to corresponding callback functions through router that has been matched successfully. Only handlers related to account will be introduced here, since Eiyaro has too many handler and they are all similar. Here is the code:


```
func (a *API) buildHandler() {
	walletEnable := false
	m := http.NewServeMux()
	
	// ...
	m.Handle("/net-info", jsonHandler(a.getNetInfo))
	// ...

	handler := latencyHandler(m, walletEnable)
	handler = maxBytesHandler(handler) // TODO(tessr): consider moving this to non-core specific mux
	handler = webAssetsHandler(handler)
	handler = gzip.Handler{Handler: handler}
	// ...
}
```

​	Here it uses `http.NewServeMux()` in Go library to create a router to distribute routes. As we can see, each router is made of a url and a handle callback function. Once the url user requested matches `/net-info`, `ApiServer` will run `a.getNetInfo` function and send arguments.



​	Other handler work:

* latencyHandler：When requests can't find the web path, it will match `/error` with requests.
* maxBytesHandler：It limit the size of requests with no more than 10MB for each.
* webAssetsHandler：It adds routers of dashboard and equity pages, which are hard-coded in Eiyaro. Their paths are `dashboard/dashboard.go` and `equity/equity.go` respectively.
* gzip.Handler：To enable `gzip`, the level of `gzip.BestSpeed` need to be set (default level-1 and the highest is level-9). Higher level, more time cpu uses. The level can be adjusted according to the the amount of data HTTP Service receives .




### 4.3.3 Instantiate http.Server 

​	`http.Server` is an HTTP Service object. It is the basis of HTTP Service work. Here is the code:


```
func (a *API) initServer(config *cfg.Config) {
	// ...

	coreHandler.wg.Add(1)
	mux := http.NewServeMux()
	mux.Handle("/", &coreHandler)

	handler = mux
	if config.Auth.Disable == false {
		handler = AuthHandler(handler, a.accessTokens)
	}
	handler = RedirectHandler(handler)

	secureheader.DefaultConfig.PermitClearLoopback = true
	secureheader.DefaultConfig.HTTPSRedirect = false
	secureheader.DefaultConfig.Next = handler

	a.server = &http.Server{
		Handler:      secureheader.DefaultConfig,
		ReadTimeout:  httpReadTimeout,
		WriteTimeout: httpWriteTimeout,
		TLSNextProto: map[string]func(*http.Server, *tls.Conn, http.Handler){},
	}

	coreHandler.Set(a)
}
```

​	There are three steps to instantiate `http.Server` :

1) AuthHandler: It enables `auth`, which means users need to enter `access token` on `dashboard` to login.  `auth` is enabled by default .

2) RedirectHandler：When users enter  "/", it will jump to "/dashboard/" and set the status "302" by default.

3) http.Server: Set the timeout of reading and writing and instantiate `http.Server`。


​	By now, all handers have been registered in HTTP Server and `http.Server` object also has been instantiated. We will listen ports and enable HTTP Service next.




### 4.3.4 Enable Api Server 

​	Use `net.listen` in GoLang library to listen local ports addresses. Here starts a goroutine to run HTTP service due to HTTP service is a persistent working. If there was no error message when running `a.server.Serve`, the `9888` port is started successfully. By now, `ApiServer ` has been waiting for users' requests and starts a goroutine to process each request once received. Here is the code:


api/api.go

```
func (a *API) StartServer(address string) {
	log.WithField("api address:", address).Info("Rpc listen")
	listener, err := net.Listen("tcp", address)
	if err != nil {
		cmn.Exit(cmn.Fmt("Failed to register tcp port: %v", err))
	}

	go func() {
		if err := a.server.Serve(listener); err != nil {
			log.WithField("error", errors.Wrap(err, "Serve")).Error("Rpc server")
		}
	}()
}
```

​	The process of `server.Serve` working is as follows:

1) `a.server.Serve` starts a `for` loop to keep receiving accept requests.

2) Instantiate a  `Conn` for each request and also start a `goroutine` for this request to operate `handle`.

3) `c.readRequest(ctx)` reads each request.

4) Chose `handler` according to request and go into the HTTP Server of this handler.

5) Call `w.finishRequest` to delete conn buffer data and stop tcp link. Request is over.



### 4.3.5 Receive and Respond to Request

​	When starting an HTTP request, we can use `curl` commdline to make an HTTP request to get the network status of the current node. Here is the information of `ApiServer` response: 


```
$ curl -s http://localhost:9888/net-info|jq .
{
  "status": "success",
  "data": {
    "listening": true,
    "syncing": false,
    "mining": false,
    "peer_count": 0,
    "current_block": 63304,
    "highest_block": 63304,
    "network_id": "mainnet",
    "version": "1.0.5+2bc2396a"
  }
}
```

​	Anyalse the process of  `ApiServer` :


 api/api.go

```
m.Handle("/net-info", jsonHandler(a.getNetInfo))
```

​	`ApiServer` parses the head of HTTP, matches the path with `/net-info` and jumps to `a.getNetInfo` function.


 api/nodeinfo.go

```
func (a *API) getNetInfo() Response {
	return NewSuccessResponse(a.GetNodeInfo())
}
```

​	`getNetInfo()` function gets the object information by `P2P`, `cpuMinner`, etc. `getNetInfo()` serializes `NetInfo` structure to JSON format and then returns it to users by instantiating `Response`.


 api/api.go

```
type Response struct {
	Status      string      `json:"status,omitempty"`
	Code        string      `json:"code,omitempty"`
	Msg         string      `json:"msg,omitempty"`
	ErrorDetail string      `json:"error_detail,omitempty"`
	Data        interface{} `json:"data,omitempty"`
}

func NewS\uccessResponse(data interface{}) Response {
	return Response{Status: SUCCESS, Data: data}
}
```

​	`Response` is instantiated by `NewSuccessResponse`, and the HTTP status  code is 200. Here is the description:

* Status: The status string
* Code: The status code
* Msg: The status description
* ErrorDetail: Error message. It is null when requesting successfully
* Data: Data to response users.



## 4.4 The Life Cycle of an HTTP Request 

​	`ApiServer` just offers HTTP short link. The link will be shut down after an HTTP request/response and needs to be rebuild when the next HTTP request/response happens. Here is a picture to show the life cycle of an HTTP request : 



The complete life cycle of HTTP request:

1) User sends an HTTP request to ApiServer.

2) ApiServer receives the HTTP request.

3) Start a goroutine to process the request.

4) Verify the auth of the request.

5) Parse the request.

6) Call the handle callback function by router.

7) Get handle information.

8) Set request status code.

9) Respond to user's request.


 

## 4.5 Eiyaro API Description 

#### 1. Wallet related API 

| API                      | Description                                                  |
| ------------------------ | ------------------------------------------------------------ |
| /create-account          | Create account                                               |
| /list-accounts           | Returns the list of all available accounts.                  |
| /delete-account          | Delete existed account                                       |
| /create-account-receiver | Create address and control program                           |
| /list-addresses          | Returns the list of all available addresses by account.      |
| /validate-address        | Verify the address is valid, and judge the address is own.   |
| /list-pubkeys            | Returns the list of all available pubkeys by account.        |
| /get-mining-address      | Query the current mining address                             |
| /set-mining-address      | Set the current mining address                               |
| /get-coinbase-arbitrary  | Get coinbase arbitrary                                       |
| /set-coinbase-arbitrary  | Set coinbase arbitrary                                       |
| /create-asset            | Create asset definition                                      |
| /update-asset-alias      | Update asset alias by assetID                                |
| /get-asset               | Query detail asset by asset ID                               |
| /list-assets             | Returns the list of all available assets                     |
| /create-key              | Create private key                                           |
| /list-keys               | Returns the list of all available keys.                      |
| /delete-key              | Delete existed key                                           |
| /reset-key-password      | Reset key password                                           |
| /check-key-password      | Check key password                                           |
| /sign-message            | Sign a message with the key password(decode encrypted private key) of an address |
| /build-transaction       | Build transaction                                            |
| /sign-transaction        | Sign transaction                                             |
| /get-transaction         | Query the account related transaction by transaction ID      |
| /list-transactions       | Returns the sub list of all the account related transactions |
| /list-balances           | Returns the list of all available account balances.          |
| /list-unspent-outputs    | Returns the sub list of all available unspent outputs for all accounts in your wallet. |
| /backup-wallet           | Backup wallet to image file                                  |
| /restore-wallet          | Restore wallet by image file                                 |
| /rescan-wallet           | Trigger to rescan block information into related wallet      |
| /wallet-info             | Return the information of wallet                             |

#### 2.Token related  API 

| API                  | Description                                     |
| -------------------- | ----------------------------------------------- |
| /create-access-token | Create access token                             |
| /list-access-tokens  | Returns the list of all available access tokens |
| /delete-access-token | Delete existed access token                     |
| /check-access-token  | Check access token is valid                     |

#### 3.Transaction related API 

| API                            | Description                                                  |
| ------------------------------ | ------------------------------------------------------------ |
| /submit-transaction            | Submit transaction                                           |
| /estimate-transaction-gas      | Submit transactions used for batch submit transactions       |
| /get-unconfirmed-transaction   | Estimate consumed neu(1EY = 10^8NEU) for the transaction    |
| /list-unconfirmed-transactions | Query mempool transaction by transaction ID                  |
| /decode-raw-transaction        | Decode a serialized transaction hex string into a JSON object describing the transaction |

#### 4. Block related API 
| API               | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| /get-block        | Returns the detail block by block height or block hash       |
| /get-block-hash   | Returns the current block hash for blockchain                |
| /get-block-header | Returns the detail block header by block height or block hash |
| /get-block-count  | Returns the current block height for blockchain              |
| /get-difficulty   | Returns the block difficulty by block height or block has    |
| /get-hash-rate    | Returns the block hash rate by block height or block hash    |
| /is-mining        | Returns the mining status                                    |
| /set-mining       | Start up node mining                                         |

#### 5. Mining Pool related API 

| API               | Description                               |
| ----------------- | ----------------------------------------- |
| /get-work         | Get the proof of work in Varint format    |
| /get-work-json    | Get the proof of work by JSON             |
| /submit-work      | Submit the proof of work in Varint format |
| /submit-work-json | Submit the proof of work by JSON          |
| /is-mining        | Returns the mining status                 |
| /set-mining       | Start up node mining                      |

#### 6. Contract related API

| API             | Description                                                |
| --------------- | ---------------------------------------------------------- |
| /verify-message | Verify a signed message with derived pubkey of the address |
| /decode-program | Decode program                                             |
| /compile        | Compile equity contract                                    |

#### 7. P2P related API

| API              | Description                                     |
| ---------------- | ----------------------------------------------- |
| /net-info        | Returns the information of current network node |
| /list-peers      | Returns the list of connected peers             |
| /disconnect-peer | Disconnect to specified peer                    |
| /connect-peer    | Connect to specified peer                       |

 

## 4.6  API Tools 
​	API needs to be tested when developing a public blockchain. There are many test tools, `curl` commandline and `postman` platform are recommended here. We take the `create-key` as an example in the following part.


### 4.6.1 Call API via `curl` commandline 

`curl` is a command line tool and library for transferring data with URLs. Use `curl` commandline to send request in the network, then get and collect data and finally show it in the `standard output`.


​	Here we use `curl` commandline to call `create-key` to create private key and return it. Here is the code:


```
$ curl -X POST http://localhost:9888/create-key -d '{ "alias" :"user1" , "password":"123456"}'

{"alias":"user1",
"xpub":"e441f31e16276902d305ab5eb6fb686ec3954a59c00712c5ee7565c93f16589b75ea5e11aa09122962ff7bd04c9dfff5027ca10ac9bea64722934850f6954f56",
"file":"C:\\Users\\Mac\\AppData\\Roaming\\Eiyaro\\keystore\\UTC--2023-09-21T11-51-52.401546400Z--e35c76e1-0f6c-4431-bb0f-eca37206d885"}
```

​	Here are the arguments of `curl` commandline:

* -X: Set the method of request, such as GET, POST, DELETE, etc.
* -d: Use `POST` to send ApiServer data.
* Others can be learn on the internet.


​	Fields of the API result: 

* alias: The alias of the account is `user1`.
* xpub: Public key.
* file: The path of keystore.


### 4.6.2 Call API via Postman 

​	Postman is a collaboration platform for API development. Its features simplify each step of building an API and streamline collaboration so you can create better APIs—faster.

​	Dowland Postman on https://www.getpostman.com/apps.


​	Use Postman to call API by `POST` method. Enter request URL ( `http://127.0.0.1:9888/create-key` ) , enter `{"alias": "user0", "password": "123456"` in `raw` under `Body` in `Text` format, and click `send` button. It returns information that is as same `curl` above: 


​	Call `create-access-token` via postman is as same. Moreover, the list of all available keys will be returned by `list-keys`.


***Note: Everytime create an account needs to change the alias, or it will return fail message that the alias has been created.***





## 4.7 Eiyaro Error Code

eiyaro/api/errors.go

* 0xx—— API errors
* 1xx—— network errors
* 2xx—— signature related errors
* 7xx—— transaction related errors
* 72x - 73x—— transaction building errors
* 73x - 75x—— transaction validation errors
* 76x - 78x—— BVM errors
* 8xx—— HSM related errors




## 4.8

## API Endpoint

Default JSON-RPC endpoints:

Client | URL
:----: | :----:
Go     | http://api.eiyaro.com/

http://api.eiyaro.com/net-info
```bash
{
"status": "success",
"data": {
"listening": true,
"syncing": true,
"mining": false,
"peer_count": 5,
"current_block": 1499,
"highest_block": 2373,
"network_id": "mainnet",
"version_info": {
"version": "1.0.1",
"update": 0,
"new_version": "1.0.1"
}
}
}
```

```bash
$ go version
$ go env GOROOT GOPATH
$ git clone https://github.com/EIYARO-Project/core.git $GOPATH/src/eiyaro/core
$ cd $GOPATH/src/eiyaro/core
$ go mod tidy
$ make eiyarod    
$ make eiyarocli  
$ cd ./cmd/eiyarod
$ ./eiyarod init --chain_id mainnet
$ nohup ./eiyarod node --web.closed &
```

A complete request example via `curl`:

### Go

The complete request and response are as follows:
```js
// curl -X POST url/method -d data
curl -X POST http://localhost:9888/create-key -d '{"alias": "alice", "password": "123456", "language": "en"}'

// response
{
  "status": "success",
  "data": {
    "alias": "alice",
    "xpub": "66008ba54d0fb103e075c0ab403f2ac1527a3b9d23dc16312dede67c85199423f912c4f84c9515e87e14874bd405953abfced87f66926c25ba8c31968094c05b",
    "file": "/home/.eiyaro/keystore/UTC--2024-4-01T06-44-28.250301455Z--6c794d3a-53b3-4c79-92d0-7b0e216667ae",
    "mnemonic": "ocean exotic route sad volume grace knock frame pumpkin fetch december capital orange cake copper exercise elephant sound aware shiver silver donate pool interest"
  }
}
```

## API methods

### available with wallet enable

* [`create-key`](#create-key)
* [`list-keys`](#list-keys)
* [`update-key-alias`](#update-key-alias)
* [`delete-key`](#delete-key)
* [`check-key-password`](#check-key-password)
* [`reset-key-password`](#reset-key-password)
* [`create-account`](#create-account)
* [`list-accounts`](#list-accounts)
* [`update-account-alias`](#update-account-alias)
* [`delete-account`](#delete-account)
* [`create-account-receiver`](#create-account-receiver)
* [`list-addresses`](#list-addresses)
* [`validate-address`](#validate-address)
* [`get-mining-address`](#get-mining-address)
* [`set-mining-address`](#set-mining-address)
* [`get-coinbase-arbitrary`](#get-coinbase-arbitrary)
* [`set-coinbase-arbitrary`](#set-coinbase-arbitrary)
* [`list-pubkeys`](#list-pubkeys)
* [`create-asset`](#create-asset)
* [`get-asset`](#get-asset)
* [`list-assets`](#list-assets)
* [`update-asset-alias`](#update-asset-alias)
* [`list-balances`](#list-balances)
* [`list-unspent-outputs`](#list-unspent-outputs)
* [`backup-wallet`](#backup-wallet)
* [`restore-wallet`](#restore-wallet)
* [`rescan-wallet`](#rescan-wallet)
* [`recovery-wallet`](#recovery-wallet)
* [`wallet-info`](#wallet-info)
* [`sign-message`](#sign-message)
* [`decode-program`](#decode-program)
* [`get-transaction`](#get-transaction)
* [`list-transactions`](#list-transactions)
* [`build-transaction`](#build-transaction)
* [`build-chain-transactions`](#build-chain-transactions)
* [`sign-transaction`](#sign-transaction)
* [`sign-transactions`](#sign-transactions)

### available Whether or not the wallet is open

* [`submit-transaction`](#submit-transaction)
* [`submit-transactions`](#submit-transactions)
* [`estimate-transaction-gas`](#estimate-transaction-gas)
* [`create-access-token`](#create-access-token)
* [`list-access-tokens`](#list-access-tokens)
* [`delete-access-token`](#delete-access-token)
* [`check-access-token`](#check-access-token)
* [`create-transaction-feed`](#create-transaction-feed)
* [`get-transaction-feed`](#get-transaction-feed)
* [`list-transaction-feeds`](#list-transaction-feeds)
* [`delete-transaction-feed`](#delete-transaction-feed)
* [`update-transaction-feed`](#update-transaction-feed)
* [`get-unconfirmed-transaction`](#get-unconfirmed-transaction)
* [`list-unconfirmed-transactions`](#list-unconfirmed-transactions)
* [`decode-raw-transaction`](#decode-raw-transaction)
* [`get-block-count`](#get-block-count)
* [`get-block-hash`](#get-block-hash)
* [`get-block`](#get-block)
* [`get-block-header`](#get-block-header)
* [`get-difficulty`](#get-difficulty)
* [`get-hash-rate`](#get-hash-rate)
* [`net-info`](#net-info)
* [`is-mining`](#is-mining)
* [`set-mining`](#set-mining)
* [`gas-rate`](#gas-rate)
* [`verify-message`](#verify-message)
* [`compile`](#compile)
* [`list-peers`](#list-peers)
* [`disconnect-peer`](#disconnect-peer)
* [`connect-peer`](#connect-peer)
* [`get-work`](#get-work)
* [`submit-work`](#submit-work)
* [`get-work-json`](#get-work-json)
* [`submit-work-json`](#submit-work-json)

----

#### `create-key`

It is to create private key essentially, returns the information of key. The private key is encrypted in the file and not visible to the user.

##### Parameters

`Object`:

- `String` - *alias*, name of the key.
- `String` - *password*, password of the key.
- `String` - *language*, mnemonic language of the key.

Optional:

- `String` - *mnemonic*, mnemonic of the key, create key by specified mnemonic.

##### Returns

`Object`:

- `String` - *alias*, name of the key.
- `String` - *xpub*, root pubkey of the key.
- `String` - *file*, path to the file of key.

Optional:

- `String` - *mnemonic*, mnemonic of the key, exist when the request mnemonic is null.

##### Example

create key by random pattern:

```js
// Request
curl -X POST create-key -d '{"alias": "alice", "password": "123456", "language": "en"}'

// Result
{
  "alias": "alice",
  "xpub": "a85e6eccb22f4c5fdade905f9a969003a17b6f35c237183a4313354b819a92689d52da3bcfe55f15a550877e8d789bd2bb9620f46e5049ea36470ab1b588a986",
  "file": "/home/yang/.eiyaro/keystore/UTC--2024-3-10T07-09-17.509894697Z--341695b9-9223-470c-a26d-bea210f8e1bb",
  "mnemonic": "verb smoke glory dentist annual peanut oval dragon fiction current orbit lab load total language female mushroom coyote regular toy slide welcome employ three"
}
```

create key by specified mnemonic:

```js
// Request
curl -X POST create-key -d '{"alias":"jack", "password":"123456", "mnemonic":"please observe raw beauty blue sea believe then boat float beyond position", "language":"en"}'

// Result
{
  "alias": "jack",
  "xpub": "c7bcb65febd31c6d900bc84c386d95c3d5b047090628d9bf5c51a848945b6986e99ff70388018a7681fa37a240dbd8df39a994c86f9314a61e75feb33563ca72",
  "file": "/home/yang/.eiyaro/keystore/UTC--2024-3-10T07-08-51.815030323Z--46ee932e-88d3-4680-a5c1-dd9e63918fcc"
}
```

----

#### `list-keys`

Returns the list of all available keys.

##### Parameters

none

##### Returns

- `Array of Object`, keys owned by the client.
  - `Object`:
    - `String` - *alias*, name of the key.
    - `String` - *xpub*, pubkey of the key.

##### Example

```js
// Request
curl -X POST list-keys

// Result
[
  {
    "alias": "alice",
    "xpub": "a7dae957c2d35b42efe7e6871cf5a75ebd2a0d0e51caffe767db42d3e6d69dbe211d1ca492ecf05908fe6fa625ad61b3253375ea744c9442dd5551613ba50aea",
    "file": "/Path/To/Library/Eiyaro/keystore/UTC--2024-03-21T02-35-15.035935116Z--4f2b8bd7-0576-4b82-8941-6cc6da05efe3"
  },
  {
    "alias": "bob",
    "xpub": "d30a810e88532f73816b7b5007d413cbd21e526ae9159023e5262511893adc1526b8eacd691b27c080201d7d79336a4f3d2cb4c167d997821cad445765916254",
    "file": "/Path/To/Library/Eiyaro/keystore/UTC--2018-03-22T06-30-27.609315219Z--0e34293c-8856-4f5f-b934-37456a3820fa"
  }
]
```

----

#### `update-key-alias`

Update alias for the existed key.

##### Parameters

`Object`:

- `String` - *xpub*, pubkey of the key.
- `String` - *new_alias*, new alias of the key.

##### Returns

none if the key alias is updated successfully.

##### Example

```js
// Request
curl -X POST update-key-alias -d '{"xpub": "a7dae957c2d35b42efe7e6871cf5a75ebd2a0d0e51caffe767db42d3e6d69dbe211d1ca492ecf05908fe6fa625ad61b3253375ea744c9442dd5551613ba50aea", "new_alias": "new_key"}'

// Result
```

----

#### `delete-key`

Delete existed key, please make sure that there is no balance in the related accounts.

##### Parameters

`Object`:

- `String` - *xpub*, pubkey of the key.
- `String` - *password*, password of the key.

##### Returns

none if the key is deleted successfully.

##### Example

```js
// Request
curl -X POST delete-key -d '{"xpub": "a7dae957c2d35b42efe7e6871cf5a75ebd2a0d0e51caffe767db42d3e6d69dbe211d1ca492ecf05908fe6fa625ad61b3253375ea744c9442dd5551613ba50aea", "password": "123456"}'

// Result
```

----

#### `check-key-password`

Check key password.

##### Parameters

`Object`:

- `String` - *xpub*, pubkey of the key.
- `String` - *password*, password of the key.

##### Returns

`Object`:

- `Boolean` - *check_result*, result of check key password, true is check successfully, otherwise is false.

##### Example

```js
// Request
curl -X POST check-key-password -d '{"xpub": "a7dae957c2d35b42efe7e6871cf5a75ebd2a0d0e51caffe767db42d3e6d69dbe211d1ca492ecf05908fe6fa625ad61b3253375ea744c9442dd5551613ba50aea", "password": "123456"}'

// Result
{
  "check_result": true
}
```

----

#### `reset-key-password`

Reset key password.

##### Parameters

`Object`:

- `String` - *xpub*, pubkey of the key.
- `String` - *old_password*, old password of the key.
- `String` - *new_password*, new password of the key.

##### Returns

`Object`:

- `Boolean` - *changed*, result of reset key password, true is reset successfully, otherwise is false.

##### Example

```js
// Request
curl -X POST reset-key-password -d '{"xpub": "a7dae957c2d35b42efe7e6871cf5a75ebd2a0d0e51caffe767db42d3e6d69dbe211d1ca492ecf05908fe6fa625ad61b3253375ea744c9442dd5551613ba50aea", "old_password": "123456", "new_password": "654321"}'

// Result
{
  "changed": true
}
```

----

#### `create-account`

Create account to manage addresses. single sign account contain only one root_xpubs and quorum; however multi sign account contain many number of root_xpubs and quorum, quorum is the number of verify signature, the range is [1, len(root_xpubs)].

##### Parameters

`Object`:

- `Array of String` - *root_xpubs*, pubkey array.
- `String` - *alias*, name of the account.
- `Integer` - *quorum*, the default value is `1`, threshold of keys that must sign a transaction to spend asset units controlled by the account.

Optional:

- `String` - *access_token*, if optional when creating account locally. However, if you want to create account remotely, it's indispensable.

##### Returns

`Object`:

- `String` - *id*, account id.
- `String` - *alias*, name of account.
- `Integer` - *key_index*, key index of account.
- `Integer` - *quorom*, threshold of keys that must sign a transaction to spend asset units controlled by the account.
- `Array of Object` - *xpubs*, pubkey array.

##### Example

```js
// Request
curl -X POST create-account -d '{"root_xpubs":["2d6c07cb1ff7800b0793e300cd62b6ec5c0943d308799427615be451ef09c0304bee5dd492c6b13aaa854d303dc4f1dcb229f9578786e19c52d860803efa3b9a"],"quorum":1,"alias":"alice"}'

// Result
{
  "alias": "alice",
  "id": "08FO663C00A02",
  "key_index": 1,
  "quorum": 1,
  "xpubs": [
    "2d6c07cb1ff7800b0793e300cd62b6ec5c0943d308799427615be451ef09c0304bee5dd492c6b13aaa854d303dc4f1dcb229f9578786e19c52d860803efa3b9a"
  ]
}
```

----

#### `list-accounts`

Returns the list of all available accounts.

##### Parameters

Optional:

- `String` - *id*, account id.
- `String` - *alias*, name of account.

##### Returns

- `Array of Object`, account array.
  - `Object`:
    - `String` - *id*, account id.
    - `String` - *alias*, name of account.
    - `Integer` - *key_index*, key index of account.
    - `Integer` - *quorom*, threshold of keys that must sign a transaction to spend asset units controlled by the account.
    - `Array of Object` - *xpubs*, pubkey array.

##### Example

list all the available accounts:

```js
// Request
curl -X POST list-accounts -d '{"alias":"alice"}'

// Result
[
  {
    "alias": "alice",
    "id": "086KQD75G0A02",
    "key_index": 1,
    "quorum": 1,
    "xpubs": [
      "180aab8bf247932a7cf68da5cc9a873266279155097612f1e5fdda4add88d5e91e2e7ce5b736f3ac933824cdee9effcf1531b90dfcb388e5cc306d14e9a2c85e"
    ]
  }
]
```

----

#### `update-account-alias`

Update alias for the existed account.

##### Parameters

`Object`: account_alias | account_id
- `String` - *new_alias*, new alias of account.

optional:

- `String` - *account_alias*, alias of account.
- `String` - *account_id*, id of account.


##### Returns

none if the account alias is updated successfully.

##### Example

```js
// Request
curl -X POST update-account-alias -d '{"account_id": "08FO663C00A02", "new_alias": "new_account"}'
or
curl -X POST update-account-alias -d '{"account_alias": "alice", "new_alias": "new_account"}'

// Result
```

----

#### `delete-account`

Delete existed account, please make sure that there is no balance in the related addresses.

##### Parameters

`Object`: account_alias | account_id

optional:

- `String` - *account_alias*, alias of account.
- `String` - *account_id*, id of account.

##### Returns

none if the account is deleted successfully.

##### Example

```js
// Request
curl -X POST delete-account -d '{"account_id": "08FO663C00A02"}'
or
curl -X POST delete-account -d '{"account_alias": "alice"}'

// Result
```

----

#### `create-account-receiver`

create address and control program, the address and control program is are one to one relationship. in build-transaction API, receiver is address when action type is control_address, and receiver is control program when action type is control_program, they are the same result.

##### Parameters

`Object`: account_alias | account_id

optional:

- `String` - *account_alias*, alias of account.
- `String` - *account_id*, id of account.

##### Returns

`Object`:

- `String` - *address*, address of account.
- `String` - *control_program*, control program of account.

##### Example

//Request
```bash
curl -X POST create-account-receiver -d '{"account_alias": "alice", "account_id": "0BDQARM800A02"}'
```
// Result
```json
{
    "address": "ey1q5u8u4eldhjf3lvnkmyl78jj8a75neuryzlknk0",
    "control_program": "0014a70fcae7edbc931fb276d93fe3ca47efa93cf064"
}
```

----

#### `list-addresses`

Returns the sub list of all available addresses by account.

##### Parameters

- `String`  - *account_alias*, alias of account.
- `String`  - *account_id*, id of account.
- `Integer` - *from*, the start position of first address
- `Integer` - *count*, the number of returned

##### Returns

- `Array of Object`, account address array.
  - `Object`:
    - `String` - *account_alias*, alias of account.
    - `String` - *account_id*, id of account.
    - `String` - *address*, address of account.
    - `Boolean` - *change*, whether the account address is change.

##### Example

list three addresses from first position by account_id or account_alias:

```js
// Request
curl -X POST list-addresses -d '{"account_alias": "alice", "account_id": "086KQD75G0A02", "from": 0, "count": 3}'

// Result
[
  {
    "account_alias": "alice",
    "account_id": "086KQD75G0A02",
    "address": "ey1qcn9lf7nxhswratvmg6d78nq7r7yupm36qgsv55",
    "change": false
  },
  {
    "account_alias": "alice",
    "account_id": "086KQD75G0A02",
    "address": "ey1qew4h5uvt5ssrtg2alms0j77r94c30m78ucrcxy",
    "change": false
  },
  {
    "account_alias": "alice",
    "account_id": "086KQD75G0A02",
    "address": "ey1qgnp4lte7wge0rsekevjlrdh39vkzz0c2alheue",
    "change": false
  }
]
```

----

#### `validate-address`

Verify the address is valid, and judge the address is own.

##### Parameters

`Object`:

- `string` - *address*, address of account.

##### Returns

`Object`:

- `Boolean` - *valid*, whether the account address is valid.
- `Boolean` - *is_local*, whether the account address is local.

##### Example

check whether the address is valid or not.

```js
// Request
curl -X POST validate-address -d '{"address": "ey1qcn9lf7nxhswratvmg6d78nq7r7yupm36qgsv55"}'

// Result
{
   "valid": true,
   "is_local": true,
}
```

----

#### `get-mining-address`

Query the current mining address.

##### Parameters

none

##### Returns

`Object`:

- `String` - *mining_address*, the current mining address being used.

##### Example

```js
// Request
curl -X POST get-mining-address

// Result
{
    "mining_address":"ey1qnhr65jq3q9gf8uymza8vp0ew8tfyh642wddxh6"
}
```

----

#### `set-mining-address`

Set the current mining address, no matter whethere the address is a local one. It returns an error message if the address format is incorrect.

##### Parameters

`Object`:

- `String` - *mining_address*, mining address to set.

##### Returns

`Object`:

- `String` - *mining_address*, the new mining address.

##### Example

```js
// Request
curl -X POST set-mining-address -d '{"mining_address":"ey1qnhr65jq3q9gf8uymza8vp0ew8tfyh642wddxh6"}'


// Result
{
    "mining_address":"ey1qnhr65jq3q9gf8uymza8vp0ew8tfyh642wddxh6"
}
```

----

#### `get-coinbase-arbitrary`

Get coinbase arbitrary.

##### Parameters

none

##### Returns

`Object`:

- `String` - *arbitrary*, the abitrary data append to coinbase, in hexdecimal format. (The full coinbase data for a block will be `0x00`&block_height&abitrary.)

##### Example

```js
// Request
curl -X POST get-coinbase-arbitrary

// Result
{
    "arbitrary":"ff"
}
```

----

#### `set-coinbase-arbitrary`

Set coinbase arbitrary.

##### Parameters

`Object`:

- `String` - *arbitrary*, the abitrary data to be appended to coinbase, in hexdecimal format.

##### Returns

`Object`:

- `String` - *arbitrary*, the abitrary data being appended to coinbase, in hexdecimal format. (The full coinbase data for a block will be `0x00`&block_height&abitrary.)

##### Example

```js
// Request
curl -X POST set-coinbase-arbitrary -d '{"arbitrary":"ff"}'

// Result
{
    "arbitrary":"ff"
}
```

----

#### `list-pubkeys`

Returns the list of all available pubkeys by account.

##### Parameters

`Object`: account_alias | account_id

optional:

- `String` - *account_alias*, alias of account.
- `String` - *account_id*, id of account.
- `string` - *public_key*, public key.

##### Returns

`Object`:

- `string` - *root_xpub*, root xpub.
- `Array of Object` -*pubkey_infos*, public key array.
  - `String` - *pubkey*, public key.
  - `Object` - *derivation_path*, derivated path for root xpub.

##### Example

```js
// Request
curl -X POST list-pubkeys -d '{"account_id": "0GO0LLUV00A02"}'

// Result
{
  "pubkey_infos": [
    {
      "derivation_path": [
        "010100000000000000",
        "0100000000000000"
      ],
      "pubkey": "b7730319feac582056379548360da5c08258e248e5c29de08a97a6614df1425d"
    },
    {
      "derivation_path": [
        "010100000000000000",
        "0200000000000000"
      ],
      "pubkey": "5044a0d6113faaf4cb2550f63a820ab579a2af6134e503b76378490d5fe75af4"
    },
    {
      "derivation_path": [
        "010100000000000000",
        "0300000000000000"
      ],
      "pubkey": "ff5c28ce257b25c2a6e172ded490a708a8e654253836d92eb0a68b81ce63bea3"
    }
  ],
  "root_xpub": "94a909319eac179f7694b99b8367b9c02b4414b95961e2e3a5bd887e0616af05a7c5e4448df92cd6cdfd82e57cd7aefc1ee0a7fd0d6a2194b5e5faf82556bedc"
}
```

----

#### `create-asset`

Create asset definition, it prepares for the issuance of asset.

##### Parameters

`Object`:

- `String` - *alias*, name of the asset.
- `Object` - *definition*, definition of asset.

Optional:(please pick one form the following two ways)

- `Array of String` - *root_xpubs*, xpub array.
- `Integer` - *quorum*, the default value is `1`, threshold of keys that must sign a transaction to spend asset units controlled by the account.

or

- `String` - *issuance_program*, user-defined contract program.

##### Returns

`Object`:

- `String` - *id*, asset id.
- `String` - *alias*, name of the asset.
- `String` - *issuance_program*, control program of the issuance of asset.
- `Array of Object` - *keys*, information of asset pubkey.
- `String` - *definition*, definition of asset.
- `Integer` - *quorum*, threshold of keys that must sign a transaction to spend asset units controlled by the account.

##### Example

create asset by xpubs:

```js
// Request
curl -X POST create-asset -d '{"alias": "GOLD", "root_xpubs": ["f6a16704f745a168642712060e6c5a69866147e21ec2447ae628f87d756bb68cc9b91405ad0a95f004090e864fde472f62ba97053ea109837bc89d63a64040d5"], "quorum":1}'

// Result
{
  "id": "3c1cf4c9436e3f942cb2f1d70a584f1c61df3697698dacccdc89e46f46a003d0",
  "alias": "GOLD",
  "issuance_program": "766baa209683b893483c0a5a317bf9868a8e2a09691f8aa8c1f3e2a7bb62b157e76712e05151ad696c00c0",
  "keys": [
    {
      "root_xpub": "f6a16704f745a168642712060e6c5a69866147e21ec2447ae628f87d756bb68cc9b91405ad0a95f004090e864fde472f62ba97053ea109837bc89d63a64040d5",
      "asset_pubkey": "9683b893483c0a5a317bf9868a8e2a09691f8aa8c1f3e2a7bb62b157e76712e012bd443fa7d56a0627df0a29dffcdc52641672a0f5cba54d104ad76ebeb8dfc3",
      "asset_derivation_path": [
        "000200000000000000"
      ]
    }
  ],
  "quorum": 1,
  "definition": {}
}
```

create asset by issuance_program:

```js
// Request
curl -X POST create-asset -d '{"alias": "TESTASSET","issuance_program": "20e9108d3ca8049800727f6a3505b3a2710dc579405dde03c250f16d9a7e1e6e78160014c5a5b563c4623018557fb299259542b8739f6bc20163201e074b22ed7ae8470c7ba5d8a7bc95e83431a753a17465e8673af68a82500c22741a547a6413000000007b7b51547ac1631a000000547a547aae7cac00c0", "definition":{"name":"TESTASSET","symbol":"TESTASSET","decimals":8,"description":{}}}'

// Result
{
  "id": "59621aa82c047bd21f73711d4a7905b7a9fbb49bc1a3fdc309b13807cc8b9094",
  "alias": "TESTASSET",
  "issuance_program": "20e9108d3ca8049800727f6a3505b3a2710dc579405dde03c250f16d9a7e1e6e78160014c5a5b563c4623018557fb299259542b8739f6bc20163201e074b22ed7ae8470c7ba5d8a7bc95e83431a753a17465e8673af68a82500c22741a547a6413000000007b7b51547ac1631a000000547a547aae7cac00c0",
  "keys": null,
  "quorum": 0,
  "definition": {
    "decimals": 8,
    "description": {},
    "name": "TESTASSET",
    "symbol": "TESTASSET"
  }
}
```

----

#### `get-asset`

Query detail asset by asset ID.

##### Parameters

`Object`:

- `String` - *id*, id of asset.

##### Returns

`Object`:

- `String` - *id*, asset id.
- `String` - *alias*, name of the asset.
- `String` - *issuance_program*, control program of the issuance of asset.
- `Integer` - *key_index*, index of key for xpub.
- `Integer` - *quorum*, threshold of keys that must sign a transaction to spend asset units controlled by the account.
- `Array of Object` - *xpubs*, pubkey array.
- `String` - *type*, type of asset.
- `Integer` - *vm_version*, version of VM.
- `String` - *raw_definition_byte*, byte of asset definition.
- `Object` - *definition*, description of asset.

##### Example

get asset by assetID.

```js
// Request
curl -X POST get-asset -d '{"id": "50ec80b6bc48073f6aa8fa045131a71213c33f3681203b15ddc2e4b81f1f4730"}'

// Result
{
  "alias": "SILVER",
  "definition": null,
  "id": "50ec80b6bc48073f6aa8fa045131a71213c33f3681203b15ddc2e4b81f1f4730",
  "issue_program": "ae2029cd61d9ef31d40af7541f9a50831d6317fdb0870249d0564fcfa9a8f843589c5151ad",
  "key_index": 1,
  "quorum": 1,
  "raw_definition_byte": "",
  "type": "asset",
  "vm_version": 1,
  "xpubs": [
    "34b16ee500615cd325f8b84099f83c1ebecaca67977c5dc9b71ae32ceaf18207f996b0a9725b901d3792689b2babcb60febe3b81a684d9b56b65f67f307d453d"
  ]
}
```

----

#### `list-assets`

Returns the list of all available assets.

##### Parameters

none

##### Returns

- `Array of Object`, asset array.
  - `Object`:
    - `String` - *id*, asset id.
    - `String` - *alias*, name of the asset.
    - `String` - *issuance_program*, control program of the issuance of asset.
    - `Integer` - *key_index*, index of key for xpub.
    - `Integer` - *quorum*, threshold of keys that must sign a transaction to spend asset units controlled by the account.
    - `Array of Object` - *xpubs*, pubkey array.
    - `String` - *type*, type of asset.
    - `Integer` - *vm_version*, version of VM.
    - `String` - *raw_definition_byte*, byte of asset definition.
    - `Object` - *definition*, description of asset.

##### Example

list all the available assets:

```js
// Request
curl -X POST list-assets -d {}

// Result
[
  {
    "alias": "EY",
    "definition": {
      "decimals": 8,
      "description": "Eiyaro Official Issue",
      "name": "EY",
      "symbol": "EY"
    },
    "id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
    "issue_program": "",
    "key_index": 0,
    "quorum": 0,
    "raw_definition_byte": "7b0a202022646563696d616c73223a20382c0a2020226465736372697074696f6e223a20224279746f6d204f6666696369616c204973737565222c0a2020226e616d65223a202262746d222c0a20202273796d626f6c223a202262746d220a7d",
    "type": "internal",
    "vm_version": 1,
    "xpubs": null
  },
  {
    "alias": "SILVER",
    "definition": null,
    "id": "50ec80b6bc48073f6aa8fa045131a71213c33f3681203b15ddc2e4b81f1f4730",
    "issue_program": "ae2029cd61d9ef31d40af7541f9a50831d6317fdb0870249d0564fcfa9a8f843589c5151ad",
    "key_index": 1,
    "quorum": 1,
    "raw_definition_byte": "",
    "type": "asset",
    "vm_version": 1,
    "xpubs": [
      "34b16ee500615cd325f8b84099f83c1ebecaca67977c5dc9b71ae32ceaf18207f996b0a9725b901d3792689b2babcb60febe3b81a684d9b56b65f67f307d453d"
    ]
  }
]
```

----

#### `update-asset-alias`

Update asset alias by assetID.

##### Parameters

`Object`:

- `String` - *id*, id of asset.
- `String` - *alias*, new alias of asset.

##### Returns

none if the asset alias is updated success.

##### Example

update asset alias.

```js
// Request
curl -X POST update-asset-alias -d '{"id":"50ec80b6bc48073f6aa8fa045131a71213c33f3681203b15ddc2e4b81f1f4730", "alias":"GOLD"}'

// Result
```

----

#### `list-balances`

Returns the list of all available account balances.

##### Parameters

Optional:

- `String` - *account_id*, account id.
- `String` - *account_alias*, name of account.

##### Returns

- `Array of Object`, balances owned by the account.
  - `Object`:
    - `String` - *account_id*, account id.
    - `String` - *account_alias*, name of account.
    - `String` - *asset_id*, asset id.
    - `String` - *asset_alias*, name of asset.
    - `Integer` - *amount*, specified asset balance of account.

##### Example

list all the available account balances.

```js
// Request
curl -X POST list-balances -d {}

// Result
[
  {
    "account_alias": "default",
    "account_id": "0BDQ9AP100A02",
    "amount": 35508000000000,
    "asset_alias": "EY",
    "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"
  },
  {
    "account_alias": "alice",
    "account_id": "0BDQARM800A04",
    "amount": 60000000000,
    "asset_alias": "EY",
    "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"
  }
]
```

list available account balances by the given account_id:

```js
// Request
curl -X POST list-balances -d {"account_id":"0BDQ9AP100A02"}

// Result
[
  {
    "account_alias": "default",
    "account_id": "0BDQ9AP100A02",
    "amount": 35508000000000,
    "asset_alias": "EY",
    "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"
  }
]
```

----

#### `list-unspent-outputs`

Returns the sub list of all available unspent outputs for all accounts in your wallet.

##### Parameters

`Object`:

optional:

- `String` - *id*, id of unspent output.
- `Boolean` - *unconfirmed*, is include unconfirmed utxo
- `Boolean` - *smart_contract*, is contract utxo
- `Integer` - *from*, the start position of first utxo
- `Integer` - *count*, the number of returned
- `String` - *account_id*, account id.
- `String` - *account_alias*, name of account.

##### Returns

- `Array of Object`, unspent output array.
  - `Object`:
    - `String` - *account_id*, account id.
    - `String` - *account_alias*, name of account.
    - `String` - *asset_id*, asset id.
    - `String` - *asset_alias*, name of asset.
    - `Integer` - *amount*, specified asset balance of account.
    - `String` - *address*, address of account.
    - `Boolean` - *change*, whether the account address is change.
    - `String` - *id*, unspent output id.
    - `String` - *program*, program of account.
    - `String` - *control_program_index*, index of program.
    - `String` - *source_id*, source unspent output id.
    - `String` - *source_pos*, position of source unspent output id in block.
    - `String` - *valid_height*, valid height.

##### Example

list all the available unspent outputs:

```js
// Request
curl -X POST list-unspent-outputs -d {}

// Result
[
  {
    "account_alias": "alice",
    "account_id": "0BKBR6VR00A06",
    "address": "ey1qv3htuvug7qdv46ywcvvzytrwrsyg0swltfa0dm",
    "amount": 2000,
    "asset_alias": "GOLD",
    "asset_id": "1883cce6aab82cf9af8cd085a3115dd4a92cdb8e6a9152acd73d7ae4adb9030a",
    "change": false,
    "control_program_index": 2,
    "id": "58f29f0f85f7bd2a91088bcbe536dee41cd0642dfb1480d3a88589bdbfd642d9",
    "program": "0014646ebe3388f01acae88ec318222c6e1c0887c1df",
    "source_id": "5988c1630c1f325e69bb92cb4b19af14286aa107311bc64b8f1a54629a33e0f4",
    "source_pos": 2,
    "valid_height": 0
  },
  {
    "account_alias": "default",
    "account_id": "0BKBR2D2G0A02",
    "address": "ey1qx7ylnhszg24995d5e0nftu9e87kt9vnxcn633r",
    "amount": 624000000000,
    "asset_alias": "EY",
    "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
    "change": false,
    "control_program_index": 12,
    "id": "5af9d3c9b69470983377c1fc0c9125c4ac3bfd32c8d505f2a6042aade8503bc9",
    "program": "00143789f9de0242aa52d1b4cbe695f0b93facb2b266",
    "source_id": "233d1dd49e591980f98e11f333c6c28a867e78448e272011f045131df5aa260b",
    "source_pos": 0,
    "valid_height": 12
  }
]
```

list the unspent output matching the given id:

```js
// Request
curl -X POST list-unspent-outputs -d '{"id": "58f29f0f85f7bd2a91088bcbe536dee41cd0642dfb1480d3a88589bdbfd642d9"}'

// Result
{
  "account_alias": "alice",
  "account_id": "0BKBR6VR00A06",
  "address": "ey1qv3htuvug7qdv46ywcvvzytrwrsyg0swltfa0dm",
  "amount": 2000,
  "asset_alias": "GOLD",
  "asset_id": "1883cce6aab82cf9af8cd085a3115dd4a92cdb8e6a9152acd73d7ae4adb9030a",
  "change": false,
  "control_program_index": 2,
  "id": "58f29f0f85f7bd2a91088bcbe536dee41cd0642dfb1480d3a88589bdbfd642d9",
  "program": "0014646ebe3388f01acae88ec318222c6e1c0887c1df",
  "source_id": "5988c1630c1f325e69bb92cb4b19af14286aa107311bc64b8f1a54629a33e0f4",
  "source_pos": 2,
  "valid_height": 0
}
```

----

#### `backup-wallet`

Backup wallet to image file, it contain accounts image, assets image and keys image.

##### Parameters

none

##### Returns

`Object`:

- `Object` - *account_image*, account image.
- `Object` - *asset_image*, asset image.
- `Object` - *key_images*, key image.

##### Example

```js
// Request
curl -X backup-wallet -d {}

// Result
{
  "account_image": {
    "slices": [
      {
        "account": {
          "type": "account",
          "xpubs": [
            "395d6e0ac25978c3f52f9c7bdfdf75ce6af02639fd7875b4b1f40778ab1120c6dcf461b7ab6fd310983afb54a9a0fb3e09b6ec0d4364c4808c94383d50fb0681"
          ],
          "quorum": 1,
          "key_index": 1,
          "ID": "0CQTA3EOG0A02",
          "Alias": "def"
        },
        "contract_index": 2
      }
    ]
  },
  "asset_image": {
    "assets": []
  },
  "key_images": {
    "xkeys": [
      {
        "crypto": {
          "cipher": "aes-128-ctr",
          "ciphertext": "bf44766fec149478af9500e25ce0a6bc50bb2fa04e40465781da6ff64e9b3a4c9af3d214cd92c5a41d8498db5f4376526740f960ff429b16e52876aec6860e1d",
          "cipherparams": {
            "iv": "1b0fc61ae4dacb15f0f77d2b4ba67635"
          },
          "kdf": "scrypt",
          "kdfparams": {
            "dklen": 32,
            "n": 4096,
            "p": 6,
            "r": 8,
            "salt": "e133b1e7caae771ff1ab34b14824d6e27ef399f2b7ded4ad3500f080ede4a1dd"
          },
          "mac": "bc6bf411fb63e61a17bc15b94f29cf0d5a0f084c328955da1f7e2b26757cfc23"
        },
        "id": "1f40be59-7400-4fdc-b46b-15009f65363a",
        "type": "eiyaro_kd",
        "version": 1,
        "alias": "default",
        "xpub": "c4ec9bfd5df19d175e17ff7fed89193c37a4a64e1c0928387da01387ca76c3bfd99390e3373ec4d438522cc2d4644214cd2ec3b00965f7a1fa3546809583191c"
      },
      {
        "crypto": {
          "cipher": "aes-128-ctr",
          "ciphertext": "f0887c8603cbbafc0a66d5b45f71488e089708c7dea4342625a67858a49d6d08c79cd3f1800627e3c8b4668e8df34fcf0be9df5d9d4503acff05373976c312a9",
          "cipherparams": {
            "iv": "c111b46f9104f49f2c40aedb827e53b5"
          },
          "kdf": "scrypt",
          "kdfparams": {
            "dklen": 32,
            "n": 4096,
            "p": 6,
            "r": 8,
            "salt": "d9ef588b258b111dea1d99a4e4c5a4f968ab69072176bb95b111922e3bbea9e6"
          },
          "mac": "336f5fee643776e139f05ebe5e4f209d992ff97e16b906105fadac9e86133554"
        },
        "id": "611d407c-9e97-4297-a02a-13cd68e47983",
        "type": "eiyaro_kd",
        "version": 1,
        "alias": "def",
        "xpub": "395d6e0ac25978c3f52f9c7bdfdf75ce6af02639fd7875b4b1f40778ab1120c6dcf461b7ab6fd310983afb54a9a0fb3e09b6ec0d4364c4808c94383d50fb0681"
      }
    ]
  }
}
```

----

#### `restore-wallet`

Restore wallet by image file.

##### Parameters

`Object`:

- `Object` - *account_image*, account image.
- `Object` - *asset_image*, asset image.
- `Object` - *key_images*, key image.

##### Returns

none if restore wallet success.

##### Example

```js
// Request
curl -X POST restore-wallet -d '{"account_image":{"slices":[{"account":{"type":"account","xpubs":["395d6e0ac25978c3f52f9c7bdfdf75ce6af02639fd7875b4b1f40778ab1120c6dcf461b7ab6fd310983afb54a9a0fb3e09b6ec0d4364c4808c94383d50fb0681"],"quorum":1,"key_index":1,"ID":"0CQTA3EOG0A02","Alias":"def"},"contract_index":2}]},"asset_image":{"assets":[]},"key_images":{"xkeys":[{"crypto":{"cipher":"aes-128-ctr","ciphertext":"bf44766fec149478af9500e25ce0a6bc50bb2fa04e40465781da6ff64e9b3a4c9af3d214cd92c5a41d8498db5f4376526740f960ff429b16e52876aec6860e1d","cipherparams":{"iv":"1b0fc61ae4dacb15f0f77d2b4ba67635"},"kdf":"scrypt","kdfparams":{"dklen":32,"n":4096,"p":6,"r":8,"salt":"e133b1e7caae771ff1ab34b14824d6e27ef399f2b7ded4ad3500f080ede4a1dd"},"mac":"bc6bf411fb63e61a17bc15b94f29cf0d5a0f084c328955da1f7e2b26757cfc23"},"id":"1f40be59-7400-4fdc-b46b-15009f65363a","type":"eiyaro_kd","version":1,"alias":"default","xpub":"c4ec9bfd5df19d175e17ff7fed89193c37a4a64e1c0928387da01387ca76c3bfd99390e3373ec4d438522cc2d4644214cd2ec3b00965f7a1fa3546809583191c"},{"crypto":{"cipher":"aes-128-ctr","ciphertext":"f0887c8603cbbafc0a66d5b45f71488e089708c7dea4342625a67858a49d6d08c79cd3f1800627e3c8b4668e8df34fcf0be9df5d9d4503acff05373976c312a9","cipherparams":{"iv":"c111b46f9104f49f2c40aedb827e53b5"},"kdf":"scrypt","kdfparams":{"dklen":32,"n":4096,"p":6,"r":8,"salt":"d9ef588b258b111dea1d99a4e4c5a4f968ab69072176bb95b111922e3bbea9e6"},"mac":"336f5fee643776e139f05ebe5e4f209d992ff97e16b906105fadac9e86133554"},"id":"611d407c-9e97-4297-a02a-13cd68e47983","type":"eiyaro_kd","version":1,"alias":"def","xpub":"395d6e0ac25978c3f52f9c7bdfdf75ce6af02639fd7875b4b1f40778ab1120c6dcf461b7ab6fd310983afb54a9a0fb3e09b6ec0d4364c4808c94383d50fb0681"}]}}'

// Result
```

----

#### `rescan-wallet`

Trigger to rescan block information into related wallet.

##### Parameters

none

##### Returns

none if restore wallet success.

##### Example

```js
// Request
curl -X POST rescan-wallet -d '{}'

// Result
```

----

#### `recovery-wallet`

Recovery wallet and accounts from root XPubs.
All accounts and balances of bip44 multi-account hierarchy for deterministic wallets can be restored via root xpubs.

##### Parameters

`Object`:

- `Object` - *xpubs*, root XPubs.


##### Returns

Status of recovery wallet.

##### Example

```js
// Request
curl -X POST recovery-wallet -d '{ "xpubs":["c536a2c11fafd8278e02e9393dcbf5aa420eb51a1761a7e5da7f2b9b37969b52a8f8e2b692e7dcaf79dfa0d1e28c63eb9fda42942f20feaa8a71b383d9a4668c"]}'

// Result
{
    "status": "success"
}
```
----

#### `wallet-info`

Return the information of wallet.

##### Parameters

none

##### Returns

`Object`:

- `Integer` - *best_block_height*, current block height.
- `Integer` - *wallet_height*, current block height for wallet.

##### Example

```js
// Request
curl -X POST wallet-info -d '{}'

// Result
{
  "best_block_height": 150,
  "wallet_height": 150
}
```

----

#### `sign-message`

Sign a message with the key password(decode encrypted private key) of an address.

##### Parameters

`Object`:

- `String` - *address*, address for account.
- `String` - *message*, message for signature by address xpub.
- `String` - *password*, password of account.

##### Returns

`Object`:

- `String` - *derived_xpub*, derived xpub.
- `String` - *signature*, signature of message.

##### Example

```js
// Request
curl -X POST sign-message -d '{"address":"ey1qx2qgvvjz734ur8x5lpfdtlau74aaa5djs0a5jn", "message":"this is a test message", "password":"123456"}'

// Result
{
  "signature": "74da3d6572233736e3a439166719244dab57dd0047f8751b1efa2da26eeab251d915c1211dcad77e8b013267b86d96e91ae67ff0be520ef4ec326e911410b609",
  "derived_xpub": "6ff8c3d1321ce39a3c3550f57ba70b67dcbcef821e9b85f6150edb7f2f3f91009e67f3075e6e76ed5f657ee4b1a5f4749b7a8c74c8e7e6a1b0e5918ebd5df4d0"
}
```

----

#### `decode-program`

Decode program.

##### Parameters

`Object`:

- `String` - *program*, program for account.

##### Returns

`Object`:

- `String` - *instructions*, instructions and data for program.

##### Example

```js
// Request
curl -X POST decode-program -d '{"program":"0014a86c83ee12e6d790fb388345cc2e2b87056a0773"}'

// Result
{
  "instructions": "DUP \nHASH160 \nDATA_20 a86c83ee12e6d790fb388345cc2e2b87056a0773\nEQUALVERIFY \nTXSIGHASH \nSWAP \nCHECKSIG \n"
}
```

----

#### `get-transaction`

Query the account related transaction by transaction ID.

##### Parameters

`Object`:

- `String` - *tx_id*, transaction id, hash of transaction.

##### Returns

`Object`:

- `String` - *tx_id*, transaction id, hash of the transaction.
- `Integer` - *block_time*, the unix timestamp for when the requst was responsed.
- `String` - *block_hash*, hash of the block where this transaction was in.
- `Integer` - *block_height*, block height where this transaction was in.
- `Integer` - *block_index*, position of the transaction in the block.
- `Integer` - *block_transactions_count*, transactions count where this transaction was in the block.
- `Boolean` - *status_fail*, whether the state of the transaction request has failed.
- `Integer` - *size*, size of transaction.
- `Array of Object` - *inputs*, object of inputs for the transaction.
  - `String` - *type*, the type of input action, available option include: 'spend', 'issue', 'coinbase'.
  - `String` - *asset_id*, asset id.
  - `String` - *asset_alias*, name of asset.
  - `Object` - *asset_definition*, definition of asset(json object).
  - `Integer` - *amount*, amount of asset.
  - `Object` - *issuance_program*, issuance program, it only exist when type is 'issue'.
  - `Object` - *control_program*, control program of account, it only exist when type is 'spend'.
  - `String` - *address*, address of account, it only exist when type is 'spend'.
  - `String` - *spent_output_id*, the front of outputID to be spent in this input, it only exist when type is 'spend'.
  - `String` - *account_id*, account id.
  - `String` - *account_alias*, name of account.
  - `Object` - *arbitrary*, arbitrary infomation can be set by miner, it only exist when type is 'coinbase'.
  - `String` - *input_id*, hash of input action.
  - `Array of String` - *witness_arguments*, witness arguments.
- `Array of Object` - *outputs*, object of outputs for the transaction.
  - `String` - *type*, the type of output action, available option include: 'retire', 'control'.
  - `String` - *id*, outputid related to utxo.
  - `Integer` - *position*, position of outputs.
  - `String` - *asset_id*, asset id.
  - `String` - *asset_alias*, name of asset.
  - `Object` - *asset_definition*, definition of asset(json object).
  - `Integer` - *amount*, amount of asset.
  - `String` - *account_id*, account id.
  - `String` - *account_alias*, name of account.
  - `Object` - *control_program*, control program of account.
  - `String` - *address*, address of account.

##### Example

```js
// Request
curl -X POST get-transaction -d '{"tx_id": "15b8d66e227feff47b3de0f278934ea16d6c828371ec6c13c8f84713dd11703b"}'

// Result
{
  "block_hash": "1fa9bb389cf974a9b37b63ca38c0cf3453c30f394b9e8ae7f04f2d1b52c329b4",
  "block_height": 530,
  "block_index": 1,
  "block_time": 1528772056,
  "block_transactions_count": 2,
  "inputs": [
    {
      "account_alias": "default",
      "account_id": "0ER7MEFGG0A02",
      "address": "sy1q4pkg8msjumtep7ecsdzuct3tsuzk5pmnm3p8nr",
      "amount": 41250000000,
      "asset_alias": "EY",
      "asset_definition": {
        "decimals": 8,
        "description": "Eiyaro Official Issue",
        "name": "EY",
        "symbol": "EY"
      },
      "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
      "control_program": "0014a86c83ee12e6d790fb388345cc2e2b87056a0773",
      "input_id": "02702fe116e052aaf4473b034ed40720bfb3aba77df64625311ca3947d367336",
      "spent_output_id": "002025b727148d04197cc7b9cf7eafd9986041f07641ca82dc0a1d9e227b52f6",
      "type": "spend",
      "witness_arguments": [
        "944a35f256a49712f95319743671152b12360df859deedbfa9f37f9fe6a81b5ff2dce36d9ee6fc19e8be8b1dd5915719d4341f66f5569aad26283859d3c1bc05",
        "bedfd27f48007c59555da672b6207ac997add62241894ff181bb9d8cba3b7e25"
      ]
    }
  ],
  "outputs": [
    {
      "account_alias": "default",
      "account_id": "0ER7MEFGG0A02",
      "address": "sy1qmt6jxrr8etssufr8qp98emyaly3lknxyndh5cj",
      "amount": 29450000000,
      "asset_alias": "EY",
      "asset_definition": {
        "decimals": 8,
        "description": "Eiyaro Official Issue",
        "name": "EY",
        "symbol": "EY"
      },
      "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
      "control_program": "0014daf5230c67cae10e2467004a7cec9df923fb4cc4",
      "id": "35a46dd36eb27b1ffdfdefbe5366175b6325e8f56e5bc3dd2aa1a47197e26e6c",
      "position": 0,
      "type": "control"
    },
    {
      "account_alias": "alice",
      "account_id": "0ER7OAK400A02",
      "address": "sy1qxe4jwhkekgnxkezu7xutu5gqnnpmyc8ppq98me",
      "amount": 11700000000,
      "asset_alias": "EY",
      "asset_definition": {
        "decimals": 8,
        "description": "Eiyaro Official Issue",
        "name": "EY",
        "symbol": "EY"
      },
      "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
      "control_program": "0014366b275ed9b2266b645cf1b8be51009cc3b260e1",
      "id": "ae791bbde0cc5b370e28a505933b85082d67be8db81bdcc56b8202f200b883e7",
      "position": 1,
      "type": "control"
    }
  ],
  "size": 332,
  "status_fail": false,
  "tx_id": "15b8d66e227feff47b3de0f278934ea16d6c828371ec6c13c8f84713dd11703b"
}
```

----

#### `list-transactions`

Returns the sub list of all the account related transactions.

##### Parameters

`Object`:

optional:

- `String` - *id*, transaction id, hash of transaction.
- `String` - *account_id*, id of account.
- `Boolean` - *detail* , flag of detail transactions, default false (only return transaction summary)
- `Boolean` - *unconfirmed*, flag of unconfirmed transactions(query result include all confirmed and unconfirmed transactions), default false.
- `Integer` - *from*, the start position of first transaction
- `Integer` - *count*, the number of returned

##### Returns

`Array of Object`, transaction array.

optional:

  - `Object`:(summary transaction)
    - `String` - *tx_id*, transaction id, hash of the transaction.
    - `Integer` - *block_time*, the unix timestamp for when the requst was responsed.
    - `Array of Object` - *inputs*, object of summary inputs for the transaction.
      - `String` - *type*, the type of input action, available option include: 'spend', 'issue', 'coinbase'.
      - `String` - *asset_id*, asset id.
      - `String` - *asset_alias*, name of asset.
      - `Integer` - *amount*, amount of asset.
      - `String` - *account_id*, account id.
      - `String` - *account_alias*, name of account.
      - `Object` - *arbitrary*, arbitrary infomation can be set by miner, it only exist when type is 'coinbase'.
    - `Array of Object` - *outputs*, object of summary outputs for the transaction.
      - `String` - *type*, the type of output action, available option include: 'retire', 'control'.
      - `String` - *asset_id*, asset id.
      - `String` - *asset_alias*, name of asset.
      - `Integer` - *amount*, amount of asset.
      - `String` - *account_id*, account id.
      - `String` - *account_alias*, name of account.
      - `Object` - *arbitrary*, arbitrary infomation can be set by miner, it only exist when type is input 'coinbase'(this place is empty).

  - `Object`:(detail transaction)
    - `String` - *tx_id*, transaction id, hash of the transaction.
    - `Integer` - *block_time*, the unix timestamp for when the requst was responsed.
    - `String` - *block_hash*, hash of the block where this transaction was in.
    - `Integer` - *block_height*, block height where this transaction was in.
    - `Integer` - *block_index*, position of the transaction in the block.
    - `Integer` - *block_transactions_count*, transactions count where this transaction was in the block.
    - `Boolean` - *status_fail*, whether the state of the transaction request has failed.
    - `Integer` - *size*, size of transaction.
    - `Array of Object` - *inputs*, object of inputs for the transaction.
      - `String` - *type*, the type of input action, available option include: 'spend', 'issue', 'coinbase'.
      - `String` - *asset_id*, asset id.
      - `String` - *asset_alias*, name of asset.
      - `Object` - *asset_definition*, definition of asset(json object).
      - `Integer` - *amount*, amount of asset.
      - `Object` - *issuance_program*, issuance program, it only exist when type is 'issue'.
      - `Object` - *control_program*, control program of account, it only exist when type is 'spend'.
      - `String` - *address*, address of account, it only exist when type is 'spend'.
      - `String` - *spent_output_id*, the front of outputID to be spent in this input, it only exist when type is 'spend'.
      - `String` - *account_id*, account id.
      - `String` - *account_alias*, name of account.
      - `Object` - *arbitrary*, arbitrary infomation can be set by miner, it only exist when type is 'coinbase'.
      - `String` - *input_id*, hash of input action.
      - `Array of String` - *witness_arguments*, witness arguments.
    - `Array of Object` - *outputs*, object of outputs for the transaction.
      - `String` - *type*, the type of output action, available option include: 'retire', 'control'.
      - `String` - *id*, outputid related to utxo.
      - `Integer` - *position*, position of outputs.
      - `String` - *asset_id*, asset id.
      - `String` - *asset_alias*, name of asset.
      - `Object` - *asset_definition*, definition of asset(json object).
      - `Integer` - *amount*, amount of asset.
      - `String` - *account_id*, account id.
      - `String` - *account_alias*, name of account.
      - `Object` - *control_program*, control program of account.
      - `String` - *address*, address of account.

##### Example

list all the available transactions:

```js
// Request
curl -X POST list-transactions -d {}

// Result
[
  {
    "block_time": 1521771059,
    "inputs": [
      {
        "arbitrary": "06",
        "asset_id": "0000000000000000000000000000000000000000000000000000000000000000",
        "type": "coinbase"
      }
    ],
    "outputs": [
      {
        "account_alias": "default",
        "account_id": "0BMHBOBVG0A02",
        "amount": 41250000000,
        "asset_alias": "EY",
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "type": "control"
      }
    ],
    "tx_id": "c631a8de401913a512c338bcf4a61cb2de6cede12a7385d9d11637eaa6578f33"
  },
  {
    "block_time": 1521770515,
    "inputs": [
      {
        "account_alias": "default",
        "account_id": "0BMHBOBVG0A02",
        "amount": 41250000000,
        "asset_alias": "EY",
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "type": "spend"
      }
    ],
    "outputs": [
      {
        "account_alias": "default",
        "account_id": "0BMHBOBVG0A02",
        "amount": 34649500000,
        "asset_alias": "EY",
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "type": "control"
      },
      {
        "account_alias": "alice",
        "account_id": "0BMHDI1P00A04",
        "amount": 6600000000,
        "asset_alias": "EY",
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "type": "control"
      }
    ],
    "tx_id": "1151ce5c7b32b8755b5e48109ec7ed956fb1783eaea9558bf5a2ad957825e4b7"
  }
]
```

list the transaction matching the given tx_id with detail:

```js
// Request
curl -X POST list-transactions -d '{"id": "7e9f9b999381da936e3cae48b5bac2b9bc28bb56c6c862be6c110448f7e2f6b3","detail": true}'

// Result
[
  {
    "block_hash": "1b2d0efa025256603e9330273d37f5a900cd3dfb213e015ac53cf645e2315a6d",
    "block_height": 72,
    "block_index": 1,
    "block_time": 1528528584,
    "block_transactions_count": 2,
    "inputs": [
      {
        "account_alias": "default",
        "account_id": "0ER7MEFGG0A02",
        "address": "sy1q4pkg8msjumtep7ecsdzuct3tsuzk5pmnm3p8nr",
        "amount": 41250000000,
        "asset_alias": "EY",
        "asset_definition": {
          "decimals": 8,
          "description": "Eiyaro Official Issue",
          "name": "EY",
          "symbol": "EY"
        },
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "control_program": "0014a86c83ee12e6d790fb388345cc2e2b87056a0773",
        "input_id": "adcef046c3f61fb6ba0d6a7107122f6e31cd4b49c7a3b05aa3391e5b0529d69a",
        "spent_output_id": "0072a2c1cee30a7c7b7b006ca08a48c2b479bc81c0ec6463fe4865ef37626ab6",
        "type": "spend",
        "witness_arguments": [
          "944a35f256a49712f95319743671152b12360df859deedbfa9f37f9fe6a81b5ff2dce36d9ee6fc19e8be8b1dd5915719d4341f66f5569aad26283859d3c1bc05",
          "bedfd27f48007c59555da672b6207ac997add62241894ff181bb9d8cba3b7e25"
        ]
      }
    ],
    "outputs": [
      {
        "account_alias": "default",
        "account_id": "0ER7MEFGG0A02",
        "address": "sy1qskj096x5w7ejcmk746g3djmv84dpxts62dewvd",
        "amount": 34649500000,
        "asset_alias": "EY",
        "asset_definition": {
          "decimals": 8,
          "description": "Eiyaro Official Issue",
          "name": "EY",
          "symbol": "EY"
        },
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "control_program": "001485a4f2e8d477b32c6edeae9116cb6c3d5a132e1a",
        "id": "b08c9bfc816064ca33da8b569998229774fc9552da7d4f16870b2c5a8f645b3b",
        "position": 0,
        "type": "control"
      },
      {
        "account_alias": "alice",
        "account_id": "0ER7OAK400A02",
        "address": "sy1qxe4jwhkekgnxkezu7xutu5gqnnpmyc8ppq98me",
        "amount": 6600000000,
        "asset_alias": "EY",
        "asset_definition": {
          "decimals": 8,
          "description": "Eiyaro Official Issue",
          "name": "EY",
          "symbol": "EY"
        },
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "control_program": "0014366b275ed9b2266b645cf1b8be51009cc3b260e1",
        "id": "0e8f8dc83a39b2b6d00a77759a797102d047f82f800fe21f5b1d80bb4d5e2e39",
        "position": 1,
        "type": "control"
      }
    ],
    "size": 333,
    "status_fail": false,
    "tx_id": "7e9f9b999381da936e3cae48b5bac2b9bc28bb56c6c862be6c110448f7e2f6b3"
  }
]
```

list the transaction matching the given account_id and unconfirmed flag(unconfirmed transaction's block_hash, block_height and block_index is default for zero):

```js
// Request
curl -X POST list-transactions -d '{"account_id": "0F1MQVI500A02", "unconfirmed": true, "detail": true}'

// Result
[
  {
    "block_hash": "0000000000000000000000000000000000000000000000000000000000000000",
    "block_height": 0,
    "block_index": 0,
    "block_time": 1529032899,
    "inputs": [
      {
        "account_alias": "default",
        "account_id": "0F1L5Q3V00A02",
        "address": "sy1ql67n04pj8mfqzv3wjq8num3yrltdykemgrr45j",
        "amount": 41250000000,
        "asset_alias": "EY",
        "asset_definition": {
          "decimals": 8,
          "description": "Eiyaro Official Issue",
          "name": "EY",
          "symbol": "EY"
        },
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "control_program": "0014febd37d4323ed201322e900f3e6e241fd6d25b3b",
        "input_id": "192ac93bad580cd53626b7f11c17e6eca64f66d1947add13a5620b78f666693e",
        "spent_output_id": "00570443cbac4f68638ff565e8b04db2062800b9e23b7701913ddf6b190dbe65",
        "type": "spend",
        "witness_arguments": [
          "512a2b60324433de96cd4274bd298b4b109a29c4d9d68582952065dfd0d7c00663cbc49e8e42fdef740a7e1b78622ee31abf2e9b0d5609755f275afd6751590b",
          "bedfd27f48007c59555da672b6207ac997add62241894ff181bb9d8cba3b7e25"
        ]
      },
      {
        "account_alias": "default",
        "account_id": "0F1L5Q3V00A02",
        "address": "sy1ql67n04pj8mfqzv3wjq8num3yrltdykemgrr45j",
        "amount": 41250000000,
        "asset_alias": "EY",
        "asset_definition": {
          "decimals": 8,
          "description": "Eiyaro Official Issue",
          "name": "EY",
          "symbol": "EY"
        },
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "control_program": "0014febd37d4323ed201322e900f3e6e241fd6d25b3b",
        "input_id": "83713c02b52eb18f782de67b322c43571d83793e082596b6410e2d3a8a41387d",
        "spent_output_id": "01df9011ca0bed4bb9b95dc84da4c5103fed06ca28c03d92d34ee3d61b945288",
        "type": "spend",
        "witness_arguments": [
          "512a2b60324433de96cd4274bd298b4b109a29c4d9d68582952065dfd0d7c00663cbc49e8e42fdef740a7e1b78622ee31abf2e9b0d5609755f275afd6751590b",
          "bedfd27f48007c59555da672b6207ac997add62241894ff181bb9d8cba3b7e25"
        ]
      }
    ],
    "outputs": [
      {
        "account_alias": "default",
        "account_id": "0F1L5Q3V00A02",
        "address": "sy1qdcfprk7wjy6flavkzhcjh3dxyrwlm935trrs5m",
        "amount": 41249100000,
        "asset_alias": "EY",
        "asset_definition": {
          "decimals": 8,
          "description": "Eiyaro Official Issue",
          "name": "EY",
          "symbol": "EY"
        },
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "control_program": "00146e1211dbce91349ff59615f12bc5a620ddfd9634",
        "id": "09fabb1a2bac44c45054175453e23e81a764557147523d8df70d8a190cf2eb17",
        "position": 0,
        "type": "control"
      },
      {
        "account_alias": "default",
        "account_id": "0F1L5Q3V00A02",
        "address": "sy1qt92xx2f4ys63dyhy58jle87nttcf37zftweklh",
        "amount": 39150000000,
        "asset_alias": "EY",
        "asset_definition": {
          "decimals": 8,
          "description": "Eiyaro Official Issue",
          "name": "EY",
          "symbol": "EY"
        },
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "control_program": "0014595463293524351692e4a1e5fc9fd35af098f849",
        "id": "6efae48663e872193e8a672eb85b8bbf29d8aee98e42816340fa0b2340cc355d",
        "position": 1,
        "type": "control"
      },
      {
        "account_alias": "alice",
        "account_id": "0F1MQVI500A02",
        "address": "sy1qum6ly8aq9u9k7xrkuck9pq64xg67gw40khnnxu",
        "amount": 2100000000,
        "asset_alias": "EY",
        "asset_definition": {
          "decimals": 8,
          "description": "Eiyaro Official Issue",
          "name": "EY",
          "symbol": "EY"
        },
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "control_program": "0014e6f5f21fa02f0b6f1876e62c5083553235e43aaf",
        "id": "aca1ecc59d8bcf548e4f5afb8a97e38f0eb56e1387b17400fd3c693c074a703d",
        "position": 2,
        "type": "control"
      }
    ],
    "size": 1194,
    "status_fail": false,
    "tx_id": "9c28a6a2a039ed5bdebe81eea44cdb22a951c472bc25cb1e8188ae423a98f251"
  },
  {
    "block_hash": "474b9c28b225fba02278ad3b097d561bf8f5c562ff2a548226fc10fc1d75b7ed",
    "block_height": 255,
    "block_index": 1,
    "block_time": 1528963126,
    "block_transactions_count": 2,
    "inputs": [
      {
        "account_alias": "alice",
        "account_id": "0F1MQVI500A02",
        "address": "sy1qum6ly8aq9u9k7xrkuck9pq64xg67gw40khnnxu",
        "amount": 10000000000,
        "asset_alias": "EY",
        "asset_definition": {
          "decimals": 8,
          "description": "Eiyaro Official Issue",
          "name": "EY",
          "symbol": "EY"
        },
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "control_program": "0014e6f5f21fa02f0b6f1876e62c5083553235e43aaf",
        "input_id": "0705bb7f4aea4ef869f22ab5e4a26e051b066e41290c1b74734a82aee8c03dfc",
        "spent_output_id": "767649aafdfe2c22d46d641a5b74d934e2590330f7280b0fc55b978812a99a58",
        "type": "spend",
        "witness_arguments": [
          "a4d4f09a04371516d37e1d27f92c9cb41e4b1e7f62762cf23ed3904a9dfd2d794195862fffd00bf7ac373e5891c8d2eb660dc5ff9c040ec4e01f973bbfd31c23",
          "2ecb3f55bfde18ec95a93c456dd3d44cb55da83148a68cbc059ea04e7b12d3bc"
        ]
      },
      {
        "account_alias": "alice",
        "account_id": "0F1MQVI500A02",
        "address": "sy1qum6ly8aq9u9k7xrkuck9pq64xg67gw40khnnxu",
        "amount": 1000000000000,
        "asset_alias": "GOLD",
        "asset_definition": {
          "decimals": 8,
          "description": {},
          "name": "",
          "symbol": ""
        },
        "asset_id": "71deb74415f16a1f7bffb04c61d427bb1f93adfba257ffba2673f102d602e78f",
        "control_program": "0014e6f5f21fa02f0b6f1876e62c5083553235e43aaf",
        "input_id": "35764d80217d0d2a3c1b000dc2dd47cf0c8bc152c842ce6e3a7783140087d3d6",
        "spent_output_id": "5d7a88851f5696ded279cb9bc380e050024c555258ea7851dfdedc2797b0d820",
        "type": "spend",
        "witness_arguments": [
          "a4d4f09a04371516d37e1d27f92c9cb41e4b1e7f62762cf23ed3904a9dfd2d794195862fffd00bf7ac373e5891c8d2eb660dc5ff9c040ec4e01f973bbfd31c23",
          "2ecb3f55bfde18ec95a93c456dd3d44cb55da83148a68cbc059ea04e7b12d3bc"
        ]
      }
    ],
    "outputs": [
      {
        "account_alias": "alice",
        "account_id": "0F1MQVI500A02",
        "address": "sy1q39sztlh4jq5nknstn2udvvpm6v5ugussx2djc0",
        "amount": 9980000000,
        "asset_alias": "EY",
        "asset_definition": {
          "decimals": 8,
          "description": "Eiyaro Official Issue",
          "name": "EY",
          "symbol": "EY"
        },
        "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
        "control_program": "0014896025fef590293b4e0b9ab8d6303bd329c47210",
        "id": "2b44969d28d79544006e792411d6cd1d245f9af20419f6138494b4b5aac2a72e",
        "position": 0,
        "type": "control"
      },
      {
        "account_alias": "alice",
        "account_id": "0F1MQVI500A02",
        "address": "sy1q258yd0gvatje4pn0qc8z9w8cdv45j9tvhfpjh8",
        "amount": 999999999901,
        "asset_alias": "GOLD",
        "asset_definition": {
          "decimals": 8,
          "description": {},
          "name": "",
          "symbol": ""
        },
        "asset_id": "71deb74415f16a1f7bffb04c61d427bb1f93adfba257ffba2673f102d602e78f",
        "control_program": "0014550e46bd0ceae59a866f060e22b8f86b2b49156c",
        "id": "54be1bc876d1deccb9845acec79eabf62d7eacd5935e337850233657914d0f9d",
        "position": 1,
        "type": "control"
      },
      {
        "amount": 99,
        "asset_alias": "GOLD",
        "asset_definition": {
          "decimals": 8,
          "description": {},
          "name": "",
          "symbol": ""
        },
        "asset_id": "71deb74415f16a1f7bffb04c61d427bb1f93adfba257ffba2673f102d602e78f",
        "control_program": "20e864761d8181103b6476435a805cba97361df9a05c40fae644c27f69ce045d3c16001464d928e181900d382fa33def66534c7323c778c4015820684d6683d014abb4e019878b50fbbb547bcbf9c4739498d8eeef565d37f9a82f741a547a6413000000007b7b51547ac1631a000000547a547aae7cac00c0",
        "id": "347553923bb550c236a703e46600d53f25161e3eb74ee3183884d398e5d894b0",
        "position": 2,
        "type": "control"
      }
    ],
    "size": 691,
    "status_fail": false,
    "tx_id": "383f8636842301b2fe292c5b8b2f540c6ed7867ba5751680b2e77827c300e41e"
  }
]
```

----

#### `build-transaction`

Build transaction.

##### Parameters

`Object`:

- `String` - *base_transaction*, base data for the transaction, default is null.
- `Integer` - *ttl*, integer of the time to live in milliseconds, it means utxo will be reserved(locked) for builded transaction in this time range, if the transaction will not to be submitted into block, it will be auto unlocked for build transaction again after this ttl time. it will be set to 5 minutes(300 seconds) defaultly when ttl is 0.
- `Integer` - *time_range*, the block height at which this transaction will be allowed to be included in a block. If the block height of the main chain exceeds this value, the transaction will expire and no longer be valid.
- `Arrary of Object` - *actions*:
  - `Object`:
    - `String` - *account_id* | *account_alias*, (type is spend_account) alias or ID of account.
    - `String` - *asset_id* | *asset_alias*, (type is spend_account, issue, retire, control_program and control_address) alias or ID of asset.
    - `Integer` - *amount*, (type is spend_account, issue, retire, control_program and control_address) the specified asset of the amount sent with this transaction.
    - `String`- *type*, type of transaction, valid types: 'spend_account', 'issue', 'spend_account_unspent_output', 'control_address', 'control_program', 'retire'.
    - `String` - *address*, (type is control_address) address of receiver, the style of address is P2PKH or P2SH.
    - `String` - *control_program*, (type is control_program) control program of receiver.
    - `String` - *use_unconfirmed*, (type is spend_account and spend_account_unspent_output) flag of use unconfirmed UTXO, default is false.
    - `String` - *arbitrary*, (type is retire) arbitrary additional data by hexadecimal.
    - `Arrary of Object` - *arguments*, (type is issue and spend_account_unspent_output) arguments of contract, null when it's not contract.
      - `String`- *type*, type of argument, valid types: 'raw_tx_signature', 'data'.
      - `Object`- *raw_data*, json object of argument content.
        - `String`- *xpub*, (type is raw_tx_signature) root xpub.
        - `String`- *derivation_path*, (type is raw_tx_signature) derived path.
        - `String`- *value*, (type is data) string of binary value.

##### Returns

- `Object of build-transaction` - *transaction*, builded transaction.

##### Example

- `spend` - transaction type is spend
```js
// Request
curl -X POST build-transaction -d '{"base_transaction":null,"actions":[{"account_id":"0BF63M2U00A04","amount":20000000,"asset_id":"ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff","type":"spend_account"},{"account_id":"0BF63M2U00A04","amount":99,"asset_id":"3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680","type":"spend_account"},{"amount":99,"asset_id":"3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680","address":"bm1q50u3z8empm5ke0g3ngl2t3sqtr6sd7cepd3z68","type":"control_address"}],"ttl":0,"time_range": 43432}'
```

- `issue` - transaction type is issue
```js
// Request
curl -X POST build-transaction -d '{"base_transaction":null,"actions":[{"account_id":"0BF63M2U00A04","amount":20000000,"asset_id":"ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff","type":"spend_account"},{"amount":10000,"asset_id":"3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680","type":"issue"},{"amount":10000,"asset_id":"3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680","address":"ey1q50u3z8empm5ke0g3ngl2t3sqtr6sd7cepd3z68","type":"control_address"}],"ttl":0,"time_range": 43432}'
```

- `address` - transaction type is address
```js
// Request
curl -X POST build-transaction -d '{"base_transaction":null,"actions":[{"account_id":"0BF63M2U00A04","amount":20000000,"asset_id":"ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff","type":"spend_account"},{"account_id":"0BF63M2U00A04","amount":99,"asset_id":"3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680","type":"spend_account"},{"amount":99,"asset_id":"3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680","address":"ey1q50u3z8empm5ke0g3ngl2t3sqtr6sd7cepd3z68","type":"control_address"}],"ttl":0,"time_range": 43432}'
```

- `retire` - transaction type is retire
```js
// Request
curl -X POST build-transaction -d '{"base_transaction":null,"actions":[{"account_id":"0BF63M2U00A04","amount":20000000,"asset_id":"ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff","type":"spend_account"},{"account_id":"0BF63M2U00A04","amount":99,"asset_id":"3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680","type":"spend_account"},{"amount":99,"asset_id":"3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680","arbitrary":"77656c636f6d65efbc8ce6aca2e8bf8ee69da5e588b0e58e9fe5ad90e4b896e7958c","type":"retire"}],"ttl":0,"time_range":43432}'
```

- `spend_account_unspent_output` - transaction type is spend_account_unspent_output(user can get UTXO information by call `list-unspent-outputs` API)

   attention:
   - action field `output_id` correspond to UTXO result `id` field
   - UTXO asset and amount will be spent in this transaction
   - transaction fee is (utxo asset_amount - output asset_amount)

```js
// Request
curl -X POST build-transaction -d '{"base_transaction":null,"actions":[{"type":"spend_account_unspent_output","output_id":"01c6ccc6f522228cd4518bba87e9c43fbf55fdf7eb17f5aa300a037db7dca0cb"},{"amount":41243000000,"asset_id":"ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff","address":"sy1qmw8c5s29zlexknfahrze3ghvlqrtn2huuntvpn","type":"control_address"}],"ttl":0,"time_range":0}'
```

```js
// Result(this type is spend, the other types are similar.)
{
  "allow_additional_actions": false,
  "local": true,
  "raw_transaction": "07010000020161015fb6a63a3361170afca03c9d5ce1f09fe510187d69545e09f95548b939cd7fffa3ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80fc93afdf01000116001426bd1b851cf6eb8a701c20c184352ad8720eeee90100015d015bb6a63a3361170afca03c9d5ce1f09fe510187d69545e09f95548b939cd7fffa33152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680c03e0101160014489a678741ccc844f9e5c502f7fac0a665bedb25010003013effffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80a2cfa5df0101160014948fb4f500e66d20fbacb903fe108ee81f9b6d9500013a3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680dd3d01160014cd5a822b34e3084413506076040d508bb12232c70001393152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc436806301160014a3f9111f3b0ee96cbd119a3ea5c60058f506fb1900",
  "signing_instructions": [
    {
      "position": 0,
      "witness_components": [
        {
          "keys": [
            {
              "derivation_path": [
                "010100000000000000",
                "0500000000000000"
              ],
              "xpub": "ee9dd8affdef7e0cacd0fbbf310217c7f588156c28e414db74c27afaedd8f876cf54547a672b431ff06ee8a146207df9595638a041b55ada1a764a8b5b30bda0"
            }
          ],
          "quorum": 1,
          "signatures": null,
          "type": "raw_tx_signature"
        },
        {
          "type": "data",
          "value": "62a73b6b7ffe52b6ad782b0e0efdc8309bf2f057d88f9a17d125e41bb11dbb88"
        }
      ]
    },
    {
      "position": 1,
      "witness_components": [
        {
          "keys": [
            {
              "derivation_path": [
                "010100000000000000",
                "0600000000000000"
              ],
              "xpub": "ee9dd8affdef7e0cacd0fbbf310217c7f588156c28e414db74c27afaedd8f876cf54547a672b431ff06ee8a146207df9595638a041b55ada1a764a8b5b30bda0"
            }
          ],
          "quorum": 1,
          "signatures": null,
          "type": "raw_tx_signature"
        },
        {
          "type": "data",
          "value": "ba5a63e7416caeb945eefc2ce874f40bc4aaf6005a1fc792557e41046f7e502f"
        }
      ]
    }
  ]
}
```

----
#### `build-chain-transactions`
Build chain transactions. To solve the problem of excessive utxo causing the transaction to fail, the utxo merge will be performed automatically. Currently, only ey transactions are supported.Warning, this feature requires the mine pool eiyarod software to be higher than v1.0.6.


##### Parameters

`Object`:

- `String` - *base_transaction*, base data for the transaction, default is null.
- `Integer` - *ttl*, integer of the time to live in milliseconds, it means utxo will be reserved(locked) for builded transaction in this time range, if the transaction will not to be submitted into block, it will be auto unlocked for build transaction again after this ttl time. it will be set to 5 minutes(300 seconds) defaultly when ttl is 0.
- `Integer` - *time_range*, time stamp(block height)is maximum survival time for the transaction, the transaction will be not submit into block after this time stamp.
- `Arrary of Object` - *actions*:
  - `Object`:
    - `String` - *account_id* | *account_alias*, (type is spend_account) alias or ID of account.
    - `String` - *asset_id* | *asset_alias*, (type is spend_account, issue, retire, control_program and control_address) alias or ID of asset.
    - `Integer` - *amount*, (type is spend_account, issue, retire, control_program and control_address) the specified asset of the amount sent with this transaction.
    - `String`- *type*, type of transaction, valid types: 'spend_account', 'issue', 'spend_account_unspent_output', 'control_address', 'control_program', 'retire'.
    - `String` - *address*, (type is control_address) address of receiver, the style of address is P2PKH or P2SH.
    - `String` - *control_program*, (type is control_program) control program of receiver.
    - `String` - *use_unconfirmed*, (type is spend_account and spend_account_unspent_output) flag of use unconfirmed UTXO, default is false.
    - `Arrary of Object` - *arguments*, (type is issue and spend_account_unspent_output) arguments of contract, null when it's not contract.
      - `String`- *type*, type of argument, valid types: 'raw_tx_signature', 'data'.
      - `Object`- *raw_data*, json object of argument content.
        - `String`- *xpub*, (type is raw_tx_signature) root xpub.
        - `String`- *derivation_path*, (type is raw_tx_signature) derived path.
        - `String`- *value*, (type is data) string of binary value.

##### Returns

- `Object of raw_transaction` - *raw_transaction*, builded transactions.
- `Object of signing_instructions` - *signing_instructions*, Information used to sign a transactions.
##### Example

- `spend` - transaction type is spend
```js
// Request
curl -X POST localhost:9888/build-chain-transactions -d '{"base_transaction": null,"actions":[{"account_id":"0JCH28A600A02","amount":30000500000000,"asset_id":"ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff","type": "spend_account"}, {"amount": 30000490000000,"asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff","address": "sm1qx93sge8jkgzclc7pled7uqr596hjm2xe558lkr","type": "control_address"}],"ttl": 1000000,"time_range": 0}'
```

```js
// Result
{
	"status": "success",
	"data": [{
		"raw_transaction": "0701000201620160a0d36052ca3d1335120ae48e1ffb2fb6b25588628eff90fa88bef3117dfb4301ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80ddb2c490e906010116001431630464f2b2058fe3c1fe5bee00742eaf2da8d901000161015f72de2064ab999acf22c05b5cf9c7d53164f80038b46b1ce426708514a30a3485ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80d4f4f69901000116001431630464f2b2058fe3c1fe5bee00742eaf2da8d9010001013fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8084c5b6aaea060116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900",
		"signing_instructions": [{
			"position": 0,
			"witness_components": [{
				"type": "raw_tx_signature",
				"quorum": 1,
				"keys": [{
					"xpub": "b4d084e77bcda7fd8a37e31135200b2a6af98d19018674125dc6290dd14176f92523f229d9f1f3514b461f6931ac2073f586a35cd628c90270063725e6e1e983",
					"derivation_path": ["010100000000000000", "0100000000000000"]
				}],
				"signatures": null
			}, {
				"type": "data",
				"value": "a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a8973919"
			}]
		}, {
			"position": 1,
			"witness_components": [{
				"type": "raw_tx_signature",
				"quorum": 1,
				"keys": [{
					"xpub": "b4d084e77bcda7fd8a37e31135200b2a6af98d19018674125dc6290dd14176f92523f229d9f1f3514b461f6931ac2073f586a35cd628c90270063725e6e1e983",
					"derivation_path": ["010100000000000000", "0100000000000000"]
				}],
				"signatures": null
			}, {
				"type": "data",
				"value": "a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a8973919"
			}]
		}],
		"allow_additional_actions": false
	}, {
		"raw_transaction": "0701000101620160571cc5d99a2994ff6b192bc9387838a3651245cb66dad4a6bc5f660310cebfa9ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8084c5b6aaea06000116001431630464f2b2058fe3c1fe5bee00742eaf2da8d9010002013effffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80faafed99010116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900013fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80ddb2c490e9060116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900",
		"signing_instructions": [{
			"position": 0,
			"witness_components": [{
				"type": "raw_tx_signature",
				"quorum": 1,
				"keys": [{
					"xpub": "b4d084e77bcda7fd8a37e31135200b2a6af98d19018674125dc6290dd14176f92523f229d9f1f3514b461f6931ac2073f586a35cd628c90270063725e6e1e983",
					"derivation_path": ["010100000000000000", "0100000000000000"]
				}],
				"signatures": null
			}, {
				"type": "data",
				"value": "a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a8973919"
			}]
		}],
		"allow_additional_actions": false
	}]
}
```
----

#### `sign-transaction`

Sign transaction.

##### Parameters

`Object`:

- `String` - *password*, signature of the password.
- `Object` - *transaction*, builded transaction.

##### Returns

`Object`:

- `Boolean` - *sign_complete*, returns true if sign succesfully and false otherwise.
- `Object of sign-transaction` - *transaction*, signed transaction.

##### Example

```js
// Request
curl -X POST sign-transaction -d '{"password":"123456","transaction":{"allow_additional_actions":false,"local":true,"raw_transaction":"07010000020161015fb6a63a3361170afca03c9d5ce1f09fe510187d69545e09f95548b939cd7fffa3ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80fc93afdf01000116001426bd1b851cf6eb8a701c20c184352ad8720eeee90100015d015bb6a63a3361170afca03c9d5ce1f09fe510187d69545e09f95548b939cd7fffa33152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680c03e0101160014489a678741ccc844f9e5c502f7fac0a665bedb25010003013effffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80a2cfa5df0101160014948fb4f500e66d20fbacb903fe108ee81f9b6d9500013a3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680dd3d01160014cd5a822b34e3084413506076040d508bb12232c70001393152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc436806301160014a3f9111f3b0ee96cbd119a3ea5c60058f506fb1900","signing_instructions":[{"position":0,"witness_components":[{"keys":[{"derivation_path":["010100000000000000","0500000000000000"],"xpub":"ee9dd8affdef7e0cacd0fbbf310217c7f588156c28e414db74c27afaedd8f876cf54547a672b431ff06ee8a146207df9595638a041b55ada1a764a8b5b30bda0"}],"quorum":1,"signatures":null,"type":"raw_tx_signature"},{"type":"data","value":"62a73b6b7ffe52b6ad782b0e0efdc8309bf2f057d88f9a17d125e41bb11dbb88"}]},{"position":1,"witness_components":[{"keys":[{"derivation_path":["010100000000000000","0600000000000000"],"xpub":"ee9dd8affdef7e0cacd0fbbf310217c7f588156c28e414db74c27afaedd8f876cf54547a672b431ff06ee8a146207df9595638a041b55ada1a764a8b5b30bda0"}],"quorum":1,"signatures":null,"type":"raw_tx_signature"},{"type":"data","value":"ba5a63e7416caeb945eefc2ce874f40bc4aaf6005a1fc792557e41046f7e502f"}]}]}}'

// Result
{
  "sign_complete": true,
  "transaction": {
    "allow_additional_actions": false,
    "local": true,
    "raw_transaction": "07010000020161015fb6a63a3361170afca03c9d5ce1f09fe510187d69545e09f95548b939cd7fffa3ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80fc93afdf01000116001426bd1b851cf6eb8a701c20c184352ad8720eeee96302400d432e6f0e22da3168d76552273e60d23d432d61b4dac53e6769d39a1097f1cd1bd8e54c7d39eda334803e5c904bc2de2f27ff29748166e0334dcfded20e980b2062a73b6b7ffe52b6ad782b0e0efdc8309bf2f057d88f9a17d125e41bb11dbb88015d015bb6a63a3361170afca03c9d5ce1f09fe510187d69545e09f95548b939cd7fffa33152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680c03e0101160014489a678741ccc844f9e5c502f7fac0a665bedb256302401eadd84ad07c3643f71a35cc5669a2c1def96ae98e790d287217e6a3543fe602dd90afffe853c729bd5237a28f33538df631572847d9870829fb1fd1100ff20820ba5a63e7416caeb945eefc2ce874f40bc4aaf6005a1fc792557e41046f7e502f03013effffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80a2cfa5df0101160014948fb4f500e66d20fbacb903fe108ee81f9b6d9500013a3152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc43680dd3d01160014cd5a822b34e3084413506076040d508bb12232c70001393152a15da72be51b330e1c0f8e1c0db669269809da4f16443ff266e07cc436806301160014a3f9111f3b0ee96cbd119a3ea5c60058f506fb1900",
    "signing_instructions": [
      {
        "position": 0,
        "witness_components": [
          {
            "keys": [
              {
                "derivation_path": [
                  "010100000000000000",
                  "0500000000000000"
                ],
                "xpub": "ee9dd8affdef7e0cacd0fbbf310217c7f588156c28e414db74c27afaedd8f876cf54547a672b431ff06ee8a146207df9595638a041b55ada1a764a8b5b30bda0"
              }
            ],
            "quorum": 1,
            "signatures": [
              "0d432e6f0e22da3168d76552273e60d23d432d61b4dac53e6769d39a1097f1cd1bd8e54c7d39eda334803e5c904bc2de2f27ff29748166e0334dcfded20e980b"
            ],
            "type": "raw_tx_signature"
          },
          {
            "type": "data",
            "value": "62a73b6b7ffe52b6ad782b0e0efdc8309bf2f057d88f9a17d125e41bb11dbb88"
          }
        ]
      },
      {
        "position": 1,
        "witness_components": [
          {
            "keys": [
              {
                "derivation_path": [
                  "010100000000000000",
                  "0600000000000000"
                ],
                "xpub": "ee9dd8affdef7e0cacd0fbbf310217c7f588156c28e414db74c27afaedd8f876cf54547a672b431ff06ee8a146207df9595638a041b55ada1a764a8b5b30bda0"
              }
            ],
            "quorum": 1,
            "signatures": [
              "1eadd84ad07c3643f71a35cc5669a2c1def96ae98e790d287217e6a3543fe602dd90afffe853c729bd5237a28f33538df631572847d9870829fb1fd1100ff208"
            ],
            "type": "raw_tx_signature"
          },
          {
            "type": "data",
            "value": "ba5a63e7416caeb945eefc2ce874f40bc4aaf6005a1fc792557e41046f7e502f"
          }
        ]
      }
    ]
  }
}
```
----

#### `sign-transactions`

Sign transactions used for batch signing transactions.

##### Parameters

`Object`:

- `String` - *password*, signature of the password.
- `Object` - *transaction*, builded transactions.

##### Returns

`Object`:

- `Boolean` - *sign_complete*, returns true if sign succesfully and false otherwise.
- `Object of sign-transactions` - *transaction*, signed transactions.

##### Example

```js
// Request
curl -X POST localhost:9888/sign-transactions -d '{"password":"123456","transactions":[{"raw_transaction":"0701000201620160a0d36052ca3d1335120ae48e1ffb2fb6b25588628eff90fa88bef3117dfb4301ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80ddb2c490e906010116001431630464f2b2058fe3c1fe5bee00742eaf2da8d901000161015f72de2064ab999acf22c05b5cf9c7d53164f80038b46b1ce426708514a30a3485ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80d4f4f69901000116001431630464f2b2058fe3c1fe5bee00742eaf2da8d9010001013fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8084c5b6aaea060116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900","signing_instructions":[{"position":0,"witness_components":[{"type":"raw_tx_signature","quorum":1,"keys":[{"xpub":"b4d084e77bcda7fd8a37e31135200b2a6af98d19018674125dc6290dd14176f92523f229d9f1f3514b461f6931ac2073f586a35cd628c90270063725e6e1e983","derivation_path":["010100000000000000","0100000000000000"]}],"signatures":null},{"type":"data","value":"a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a8973919"}]},{"position":1,"witness_components":[{"type":"raw_tx_signature","quorum":1,"keys":[{"xpub":"b4d084e77bcda7fd8a37e31135200b2a6af98d19018674125dc6290dd14176f92523f229d9f1f3514b461f6931ac2073f586a35cd628c90270063725e6e1e983","derivation_path":["010100000000000000","0100000000000000"]}],"signatures":null},{"type":"data","value":"a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a8973919"}]}],"allow_additional_actions":false},{"raw_transaction":"0701000101620160571cc5d99a2994ff6b192bc9387838a3651245cb66dad4a6bc5f660310cebfa9ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8084c5b6aaea06000116001431630464f2b2058fe3c1fe5bee00742eaf2da8d9010002013effffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80faafed99010116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900013fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80ddb2c490e9060116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900","signing_instructions":[{"position":0,"witness_components":[{"type":"raw_tx_signature","quorum":1,"keys":[{"xpub":"b4d084e77bcda7fd8a37e31135200b2a6af98d19018674125dc6290dd14176f92523f229d9f1f3514b461f6931ac2073f586a35cd628c90270063725e6e1e983","derivation_path":["010100000000000000","0100000000000000"]}],"signatures":null},{"type":"data","value":"a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a8973919"}]}],"allow_additional_actions":false}]}'
```

```
// Result
{
	"status": "success",
	"data": {
		"transaction": [{
				"raw_transaction": "0701000201620160a0d36052ca3d1335120ae48e1ffb2fb6b25588628eff90fa88bef3117dfb4301ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80ddb2c490e906010116001431630464f2b2058fe3c1fe5bee00742eaf2da8d9630240acb57bc06f7e5de99ef3e630ce34fc74c33d4694301202968092ca50ae7842e3331bfeb0cf7b65f383e27670c4d58aeeeb0b77e5355957ca729298d2b4e2470c20a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a89739190161015f72de2064ab999acf22c05b5cf9c7d53164f80038b46b1ce426708514a30a3485ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80d4f4f69901000116001431630464f2b2058fe3c1fe5bee00742eaf2da8d96302404298424e89e5528f1d0cdd9028489b9d9e3f031ec34a74440cacc7900dc1eac9359c408a4342fc6cef935d2978919df8b23f3912ac4419800d375fac06ddb50620a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a897391901013fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8084c5b6aaea060116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900",
				"signing_instructions": [{
						"position": 0,
						"witness_components": [{
								"type": "raw_tx_signature",
								"quorum": 1,
								"keys": [{
									"xpub": "b4d084e77bcda7fd8a37e31135200b2a6af98d19018674125dc6290dd14176f92523f229d9f1f3514b461f6931ac2073f586a35cd628c90270063725e6e1e983",
									"derivation_path": [
										"010100000000000000",
										"0100000000000000"
									]
								}],
								"signatures": [
									"acb57bc06f7e5de99ef3e630ce34fc74c33d4694301202968092ca50ae7842e3331bfeb0cf7b65f383e27670c4d58aeeeb0b77e5355957ca729298d2b4e2470c"
								]
							},
							{
								"type": "data",
								"value": "a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a8973919"
							}
						]
					},
					{
						"position": 1,
						"witness_components": [{
								"type": "raw_tx_signature",
								"quorum": 1,
								"keys": [{
									"xpub": "b4d084e77bcda7fd8a37e31135200b2a6af98d19018674125dc6290dd14176f92523f229d9f1f3514b461f6931ac2073f586a35cd628c90270063725e6e1e983",
									"derivation_path": [
										"010100000000000000",
										"0100000000000000"
									]
								}],
								"signatures": [
									"4298424e89e5528f1d0cdd9028489b9d9e3f031ec34a74440cacc7900dc1eac9359c408a4342fc6cef935d2978919df8b23f3912ac4419800d375fac06ddb506"
								]
							},
							{
								"type": "data",
								"value": "a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a8973919"
							}
						]
					}
				],
				"allow_additional_actions": false
			},
			{
				"raw_transaction": "0701000101620160571cc5d99a2994ff6b192bc9387838a3651245cb66dad4a6bc5f660310cebfa9ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8084c5b6aaea06000116001431630464f2b2058fe3c1fe5bee00742eaf2da8d96302408c742d77eba6c56a8db8c114e60be6c6263df6120aefd7538376129d04ec71b78b718c2085bba85254b44bf4600ba31d4c5a7869d0be0c46d88bd5eb27490e0820a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a897391902013effffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80faafed99010116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900013fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80ddb2c490e9060116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900",
				"signing_instructions": [{
					"position": 0,
					"witness_components": [{
							"type": "raw_tx_signature",
							"quorum": 1,
							"keys": [{
								"xpub": "b4d084e77bcda7fd8a37e31135200b2a6af98d19018674125dc6290dd14176f92523f229d9f1f3514b461f6931ac2073f586a35cd628c90270063725e6e1e983",
								"derivation_path": [
									"010100000000000000",
									"0100000000000000"
								]
							}],
							"signatures": [
								"8c742d77eba6c56a8db8c114e60be6c6263df6120aefd7538376129d04ec71b78b718c2085bba85254b44bf4600ba31d4c5a7869d0be0c46d88bd5eb27490e08"
							]
						},
						{
							"type": "data",
							"value": "a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a8973919"
						}
					]
				}],
				"allow_additional_actions": false
			}
		],
		"sign_complete": true
	}
}
```
----

#### `submit-transaction`

Submit transaction.

##### Parameters

`Object`:

- `Object` - *raw_transaction*, raw_transaction of signed transaction.

##### Returns

`Object`:

- `String` - *tx_id*, transaction id, hash of transaction.

##### Example

```js
// Request
curl -X POST submit-transaction -d '{"raw_transaction":"07010000010161015ffe8bdb49bbd08f711a54f0fbed4141b74c276de44c831999aac43bdd56f98309ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80c0b1ca94120001160014d61f9b3354771283461e6cb269bec98e7ee975f6630240403d7b35ec72a80816b21822d645adc98af2c1f07d5b379dda4527d8585ec12ef213ada312e3807ae0ccf7206575f313ffe2b405a286309d3172feabe07e7e0620d013706a314a57e84c2b262c2a291e8e2b063785aea9338476a66d41be4de4f202013effffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80bc82eb931201160014bdf260424fd2f322dbec9ce4ddbaed8618d8e959000139ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff6301160014a3f9111f3b0ee96cbd119a3ea5c60058f506fb1900"}'

// Result
{
  "tx_id": "2c0624a7d251c29d4d1ad14297c69919214e78d995affd57e73fbf84ece316cb"
}
```
----

#### `submit-transactions`

Submit transactions used for batch submit transactions.

##### Parameters

`Object`:

- `Object` - *raw_transactions*, raw_transactions of signed transactions.

##### Returns

`Object`:

- `String` - *tx_id*, transactions id, hash of transactions.

##### Example

```js
// Request
curl -X POST localhost:9888/submit-transactions -d '{"raw_transactions":["0701000201620160a0d36052ca3d1335120ae48e1ffb2fb6b25588628eff90fa88bef3117dfb4301ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80ddb2c490e906010116001431630464f2b2058fe3c1fe5bee00742eaf2da8d9630240acb57bc06f7e5de99ef3e630ce34fc74c33d4694301202968092ca50ae7842e3331bfeb0cf7b65f383e27670c4d58aeeeb0b77e5355957ca729298d2b4e2470c20a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a89739190161015f72de2064ab999acf22c05b5cf9c7d53164f80038b46b1ce426708514a30a3485ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80d4f4f69901000116001431630464f2b2058fe3c1fe5bee00742eaf2da8d96302404298424e89e5528f1d0cdd9028489b9d9e3f031ec34a74440cacc7900dc1eac9359c408a4342fc6cef935d2978919df8b23f3912ac4419800d375fac06ddb50620a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a897391901013fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8084c5b6aaea060116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900","0701000101620160571cc5d99a2994ff6b192bc9387838a3651245cb66dad4a6bc5f660310cebfa9ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8084c5b6aaea06000116001431630464f2b2058fe3c1fe5bee00742eaf2da8d96302408c742d77eba6c56a8db8c114e60be6c6263df6120aefd7538376129d04ec71b78b718c2085bba85254b44bf4600ba31d4c5a7869d0be0c46d88bd5eb27490e0820a86ab33efa9d71994270898ad99f198d60889ef617d5eaf25e776929a897391902013effffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80faafed99010116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900013fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80ddb2c490e9060116001431630464f2b2058fe3c1fe5bee00742eaf2da8d900"]}'
```

```
// Result
{
	"status": "success",
	"data": {
		"tx_id": ["8524bf38701c17c57e2ad7368c0d6c815eb30e92713ff5dd86c1a931cddf2e95", "a0bbfb75c9a00bb2d4801aa95ed0479993a67acfd1cec5b77a8ff86966f52dac"]
	}
}
```

----

#### `estimate-transaction-gas`

Estimate consumed neu(1EY = 10^8NEU) for the transaction.

##### Parameters

`Object`:

- `Object` - *transaction_template*, builded transaction response.

##### Returns

`Object`:

- `Integer` - *total_neu*, total consumed neu(1EY = 10^8NEU) for execute transaction, total_neu is rounded up storage_neu + vm_neu.
- `Integer` - *storage_neu*, consumed neu for storage transaction .
- `Integer` - *vm_neu*, consumed neu for execute VM.

##### Example

```js
// Request
curl -X POST estimate-transaction-gas -d '{"transaction_template":{"allow_additional_actions":false,"raw_transaction":"070100010161015ffe8a1209937a6a8b22e8c01f056fd5f1730734ba8964d6b79de4a639032cecddffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8099c4d59901000116001485eb6eee8023332da85df60157dc9b16cc553fb2010002013dffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80afa08b4f011600142b4fd033bc76b4ddf5cb00f625362c4bc7b10efa00013dffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8090dfc04a011600146eea1ce6cfa5b718ae8094376be9bc1a87c9c82700","signing_instructions":[{"position":0,"witness_components":[{"keys":[{"derivation_path":["010100000000000000","0100000000000000"],"xpub":"cb4e5932d808ee060df9552963d87f60edac42360b11d4ad89558ef2acea4d4aaf4818f2ebf5a599382b8dfce0a0c798c7e44ec2667b3a1d34c61ba57609de55"}],"quorum":1,"signatures":null,"type":"raw_tx_signature"},{"type":"data","value":"1c9b5c1db7f4afe31fd1b7e0495a8bb042a271d8d7924d4fc1ff7cf1bff15813"}]}]}}'

// Result
{
  "storage_neu": 3840000,
  "total_neu": 5259000,
  "vm_neu": 1419000
}
```

----

#### `create-access-token`

Create access token, it provides basic access authentication for HTTP protocol, returns token contain username and password, they are separated by a colon.

##### Parameters

`Object`:

- `String` - *id*, token ID.

optional:

- `String` - *type*, type of token.

##### Returns

`Object`:

- `String` - *token*, access token, authentication username and password are separated by a colon.
- `String` - *id*, token ID.
- `String` - *type*, type of token.
- `Object` - *created_at*, time to create token.

##### Example

create access token.

```js
// Request
curl -X POST create-access-token -d '{"id":"token1"}'

// Result
{
  "token": "token1:1fee70f537128a201338bd5f25a3adbf33dad02eae4f4c9ac43f336a069df8f3",
  "id": "token1",
  "created_at": "2024-03-20T18:56:01.043919771+08:00"
}
```

----

#### `list-access-tokens`

Returns the list of all available access tokens.

##### Parameters

none

##### Returns

- `Array of Object`, access token array.
  - `Object`:
    - `String` - *token*, access token.
    - `String` - *id*, token ID.
    - `String` - *type*, type of token.
    - `Object` - *created_at*, time to create token.

##### Example

list all the available access tokens.

```js
// Request
curl -X POST list-access-tokens -d {}

// Result
[
  {
    "token": "token1:1fee70f537128a201338bd5f25a3adbf33dad02eae4f4c9ac43f336a069df8f3",
    "id": "token1",
    "created_at": "2024-03-20T18:56:01.043919771+08:00"
  },
  {
    "token": "alice:78598c6d9fb9e3258d01f78005d4e5725ad0d45e20af90a30b577b407d4a2edd",
    "id": "alice",
    "created_at": "2024-03-20T18:56:01.043919771+08:00"
  }
]
```

----

#### `delete-access-token`

Delete existed access token.

##### Parameters

`Object`:

- `String` - *id*, token ID.

##### Returns

none if the access token is deleted successfully.

##### Example

delete access token.

```js
// Request
curl -X POST delete-access-token -d '{"id": "token1"}'

// Result
```

----

#### `check-access-token`

Check access token is valid.

##### Parameters

`Object`:

- `String` - *id*, token ID.
- `String` - *secret*, secret of token, the second part of the colon division for token.

##### Returns

none if the access token is checked valid.

##### Example

check whether the access token is vaild or not.

```js
// Request
curl -X POST check-access-token -d '{"id": "token1", "secret": "1fee70f537128a201338bd5f25a3adbf33dad02eae4f4c9ac43f336a069df8f3"}'

// Result
```

----

#### `create-transaction-feed`

Create transaction feed.

##### Parameters

`Object`:

- `String` - *alias*, name of the transaction feed.
- `String` - *filter*, filter of the transaction feed.

##### Returns

none if the transaction feed is created success.

##### Example

```js
// Request
curl -X POST create-transaction-feed -d '{"alias": "test1", "filter": "asset_id='84778a666fe453cf73b2e8c783dbc9e04bc4fd7cbb4f50caeaee99cf9967ebed' AND amount_lower_limit = 50 AND amount_upper_limit = 100"}'

// Result
```

----

#### `get-transaction-feed`

Query detail transaction feed by name.

##### Parameters

`Object`:

- `String` - *alias*, name of the transaction feed.

##### Returns

`Object`:

- `String` - *id*, id of the transaction feed.
- `String` - *alias*, name of the transaction feed.
- `String` - *filter*, filter of the transaction feed.
- `Object` - *param*, param of the transaction feed.
  - `String` - *assetid*, asset id.
  - `Integer` - *lowerlimit*, the lower limit of asset amount.
  - `Integer` - *upperlimit*, the upper limit of asset amount.
  - `String` - *transtype*, type of transaction.

##### Example

list the available txfeed by alias:

```js
// Request
curl -X POST get-transaction-feed -d '{"alias": "test1"}'

// Result
{
  "alias": "test1",
  "filter": "asset_id='84778a666fe453cf73b2e8c783dbc9e04bc4fd7cbb4f50caeaee99cf9967ebed' AND amount_lower_limit = 50 AND amount_upper_limit = 100",
  "param": {}
}
```

----

#### `list-transaction-feeds`

Returns the list of all available transaction feeds.

##### Parameters

none

##### Returns

- `Array of Object`, the transaction feeds.
  - `Object`:
    - `String` - *id*, id of the transaction feed.
    - `String` - *alias*, name of the transaction feed.
    - `String` - *filter*, filter of the transaction feed.
    - `Object` - *param*, param of the transaction feed.
      - `String` - *assetid*, asset id.
      - `Integer` - *lowerlimit*, the lower limit of asset amount.
      - `Integer` - *upperlimit*, the upper limit of asset amount.
      - `String` - *transtype*, type of transaction.

##### Example

list all the available txfeed:

```js
// Request
curl -X POST list-transaction-feeds -d {}

// Result
[
  {
    "alias": "test1",
    "filter": "asset_id='84778a666fe453cf73b2e8c783dbc9e04bc4fd7cbb4f50caeaee99cf9967ebed' AND amount_lower_limit = 50 AND amount_upper_limit = 100",
    "param": {}
  },
  {
    "alias": "test2",
    "filter": "asset_id='cee6a588cc3fc280749021ef42d5209952a1e6feceada4e69dd8a424ad22b199' AND amount_lower_limit = 30 AND amount_upper_limit = 100",
    "param": {}
  }
]
```

----

#### `delete-transaction-feed`

Delete transaction feed by name.

##### Parameters

`Object`:

- `String` - *alias*, name of the transaction feed.

##### Returns

none if the transaction feed is deleted success.

##### Example

```js
// Request
curl -X POST delete-transaction-feed -d '{"alias": "test1"}'

// Result
```

----

#### `update-transaction-feed`

Update transaction feed.

##### Parameters

`Object`:

- `String` - *alias*, name of the transaction feed.
- `String` - *filter*, filter of the transaction feed.

##### Returns

none if the transaction feed is updated success.

##### Example

deleted when the txfeed exists, and create it with alias and filter:

```js
// Request
curl -X POST update-transaction-feed -d '{"alias": "test1", "filter": "asset_id='84778a666fe453cf73b2e8c783dbc9e04bc4fd7cbb4f50caeaee99cf9967ebed' AND amount_lower_limit = 60 AND amount_upper_limit = 80"}'

// Result
```

----

#### `get-unconfirmed-transaction`

Query mempool transaction by transaction ID.

##### Parameters

`Object`:

- `String` - *tx_id*, transaction id, hash of transaction.

##### Returns

`Object`:

- `String` - *id*, transaction id, hash of the transaction.
- `Integer` - *version*, version of transaction.
- `Integer` - *size*, size of transaction.
- `Integer` - *time_range*, the time range of transaction.
- `Boolean` - *status_fail*, whether the state of the request has failed.
- `String` - *mux_id*, the previous transaction mux id(wallet enable can be acquired, this place is empty).
- `Array of Object` - *inputs*, object of inputs for the transaction(input struct please refer to get-transction API description).
- `Array of Object` - *outputs*, object of outputs for the transaction(output struct please refer to get-transction API description).

##### Example

```js
// Request
curl -X POST get-unconfirmed-transaction -d '{"tx_id": "382090f24fbfc2f737fa7372b9d161a43f00d1c597a7130a56589d1f469d04b5"}'

// Result
{
  "id": "382090f24fbfc2f737fa7372b9d161a43f00d1c597a7130a56589d1f469d04b5",
  "inputs": [
    {
      "address": "ey1qqrm7ruecx7yrg9smtwmnmgj3umg9vcukgy5sdj",
      "amount": 41250000000,
      "asset_definition": {},
      "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
      "control_program": "001400f7e1f338378834161b5bb73da251e6d0566396",
      "input_id": "a0c2fa0719bfe1446681537dcf1f8d0f03add093e29d12481eb807e07778d7b3",
      "spent_output_id": "161b44e547a6cc68d732eb64fa38031da98211a99319e088cfe632223f9ac6d8",
      "type": "spend",
      "witness_arguments": [
        "cf0e1b217ab92ade8e81fab10f9f307bb5cc1ad947b5629e3f7a760aba722f5044f2ab59ec92fa4264ff5811de4361abb6eabd7e75ffd28a813a98ceff434c01",
        "6890a19b21c326059eef211cd8414282a79d3b9203f2592064221fd360e778a7"
      ]
    }
  ],
  "mux_id": "842cd07eed050b547377b5b123f14a5ec0d76933d564f030cf4d5d5c15769645",
  "outputs": [
    {
      "address": "ey1qehxd5cdnepckh5jc72ggn30havd78lsgcqmt7k",
      "amount": 21230000000,
      "asset_definition": {},
      "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
      "control_program": "0014cdccda61b3c8716bd258f29089c5f7eb1be3fe08",
      "id": "a8f21ad24689c290634db85278f56d152efe6fe08bc194e5dee5127ed6d3ebee",
      "position": 0,
      "type": "control"
    },
    {
      "address": "ey1q2me9gwccnm3ehpnrcr99gcnj730js2zfucms3r",
      "amount": 20000000000,
      "asset_definition": {},
      "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
      "control_program": "001456f2543b189ee39b8663c0ca546272f45f282849",
      "id": "78219e422ea3257aeb32f6d952b5ce5560dab1d6440c9f3aebcdaad2a852d2a8",
      "position": 1,
      "type": "control"
    }
  ],
  "size": 664,
  "status_fail": false,
  "time_range": 0,
  "version": 1
}
```

----

#### `list-unconfirmed-transactions`

Returns the total number of mempool transactions and the list of transaction IDs.

##### Parameters

none

##### Returns

`Object`:

- `Integer` - *total*, version of transaction.
- `Array of Object` - *tx_ids*, list of transaction id.

##### Example

```js
// Request
curl -X POST list-unconfirmed-transactions -d {}

// Result
{
  "total": 2,
  "tx_ids": [
    "382090f24fbfc2f737fa7372b9d161a43f00d1c597a7130a56589d1f469d04b5",
    "fc2da5dfa094c2170144f149fa07a312983157aec0ec95063a1319eedcb2d23b"
  ]
}
```

----

#### `decode-raw-transaction`

Decode a serialized transaction hex string into a JSON object describing the transaction.

##### Parameters

`Object`:

- `String` - *raw_transaction*, hexstring of raw transaction.

##### Returns

`Object`:

- `String` - *tx_id*, transaction ID.
- `Integer` - *version*, version of transaction.
- `String` - *size*, size of transaction.
- `String` - *time_range*, time range of transaction.
- `String` - *fee*, fee for sending transaction.
- `Array of Object` - *inputs*, object of inputs for the transaction(input struct please refer to get-transction API description).
- `Array of Object` - *outputs*, object of outputs for the transaction
(output struct please refer to get-transction API description).

##### Example

```js
// Request
curl -X POST decode-raw-transaction -d '{"raw_transaction": "070100010161015fc8215913a270d3d953ef431626b19a89adf38e2486bb235da732f0afed515299ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8099c4d59901000116001456ac170c7965eeac1cc34928c9f464e3f88c17d8630240b1e99a3590d7db80126b273088937a87ba1e8d2f91021a2fd2c36579f7713926e8c7b46c047a43933b008ff16ecc2eb8ee888b4ca1fe3fdf082824e0b3899b02202fb851c6ed665fcd9ebc259da1461a1e284ac3b27f5e86c84164aa518648222602013effffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff80bbd0ec980101160014c3d320e1dc4fe787e9f13c1464e3ea5aae96a58f00013cffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff8084af5f01160014bb93cdb4eca74b068321eeb84ac5d33686281b6500"}'

// Result
{
  "fee": 20000000,
  "inputs": [
    {
      "address": "sy1q26kpwrrevhh2c8xrfy5vnaryu0ugc97csrdy69",
      "amount": 41250000000,
      "asset_definition": {},
      "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
      "control_program": "001456ac170c7965eeac1cc34928c9f464e3f88c17d8",
      "input_id": "9963265eb601df48501cc240e1480780e9ed6e0c8f18fd7dd57954068c5dfd02",
      "spent_output_id": "01bb3309666618a1507cb5be845b17dee5eb8028ee7e71b17d74b4dc97085bc8",
      "type": "spend",
      "witness_arguments": [
        "b1e99a3590d7db80126b273088937a87ba1e8d2f91021a2fd2c36579f7713926e8c7b46c047a43933b008ff16ecc2eb8ee888b4ca1fe3fdf082824e0b3899b02",
        "2fb851c6ed665fcd9ebc259da1461a1e284ac3b27f5e86c84164aa5186482226"
      ]
    }
  ],
  "outputs": [
    {
      "address": "sy1qc0fjpcwuflnc06038s2xfcl2t2hfdfv0lxzg7s",
      "amount": 41030000000,
      "asset_definition": {},
      "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
      "control_program": "0014c3d320e1dc4fe787e9f13c1464e3ea5aae96a58f",
      "id": "567b34857614d16292220beaca16ce34b939c75023a49cc43fa432fff51ca0dd",
      "position": 0,
      "type": "control"
    },
    {
      "address": "sy1qhwfumd8v5a9sdqepa6uy43wnx6rzsxm9essn4l",
      "amount": 200000000,
      "asset_definition": {},
      "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
      "control_program": "0014bb93cdb4eca74b068321eeb84ac5d33686281b65",
      "id": "a8069d412e48c2b2994d2816758078cff46b215421706b4bad41f72a32928d92",
      "position": 1,
      "type": "control"
    }
  ],
  "size": 332,
  "time_range": 0,
  "tx_id": "4c97d7412b04d49acc33762fc748cd0780d8b44086c229c1a6d0f2adfaaac2db",
  "version": 1
}
```

----

#### `get-block-count`

Returns the current block height for blockchain.

##### Parameters

none

##### Returns

`Object`:

- `Integer` - *block_count*, recent block height of the blockchain.

##### Example

```js
// Request
curl -X POST get-block-count

// Result
{
    "block_count": 519
}
```

----

#### `get-block-hash`

Returns the current block hash for blockchain.

##### Parameters

none

##### Returns

`Object`:

- `String` - *block_hash*, recent block hash of the blockchain.

##### Example

```js
// Request
curl -X POST get-block-hash

// Result
{
  "block_hash": "997bf5cecb4df097991a7a121a7fd3cb5a404fa856e3d6032c791ac07bc7c74c"
}
```

----

#### `get-block`

Returns the detail block by block height or block hash.

##### Parameters

`Object`: block_height | block_hash

optional:

- `String` - *block_hash*, hash of block.
- `Integer` - *block_height*, height of block.

##### Returns

`Object`:

- `String` - *hash*, hash of block.
- `Integer` - *size*, size of block.
- `Integer` - *version*, version of block.
- `Integer` - *height*, height of block.
- `String` - *previous_block_hash*, previous block hash.
- `Integer` - *timestamp*, timestamp of block.
- `Integer` - *nonce*, nonce value.
- `Integer` - *bits*, bits of difficulty.
- `String` - *difficulty*, difficulty value(String type).
- `String` - *transaction_merkle_root*, merkle root of transaction.
- `String` - *transaction_status_hash*, merkle root of transaction status.
- `Array of Object` - *transactions*, transaction object:
  - `String` - *id*, transaction id, hash of the transaction.
  - `Integer` - *version*, version of transaction.
  - `Integer` - *size*, size of transaction.
  - `Integer` - *time_range*, the unix timestamp for when the requst was responsed.
  - `Boolean` - *status_fail*, whether the state of the request has failed.
  - `String` - *mux_id*, the previous transaction mux id(source id of utxo).
  - `Array of Object` - *inputs*, object of inputs for the transaction.
    - `String` - *type*, the type of input action, available option include: 'spend', 'issue', 'coinbase'.
    - `String` - *asset_id*, asset id.
    - `String` - *asset_alias*, name of asset.
    - `Object` - *asset_definition*, definition of asset(json object).
    - `Integer` - *amount*, amount of asset.
    - `Object` - *issuance_program*, issuance program, it only exist when type is 'issue'.
    - `Object` - *control_program*, control program of account, it only exist when type is 'spend'.
    - `String` - *address*, address of account, it only exist when type is 'spend'.
    - `String` - *spent_output_id*, the front of outputID to be spent in this input, it only exist when type is 'spend'.
    - `String` - *account_id*, account id.
    - `String` - *account_alias*, name of account.
    - `Object` - *arbitrary*, arbitrary infomation can be set by miner, it only exist when type is 'coinbase'.
    - `String` - *input_id*, hash of input action.
    - `Array of String` - *witness_arguments*, witness arguments.
  - `Array of Object` - *outputs*, object of outputs for the transaction.
    - `String` - *type*, the type of output action, available option include: 'retire', 'control'.
    - `String` - *id*, outputid related to utxo.
    - `Integer` - *position*, position of outputs.
    - `String` - *asset_id*, asset id.
    - `String` - *asset_alias*, name of asset.
    - `Object` - *asset_definition*, definition of asset(json object).
    - `Integer` - *amount*, amount of asset.
    - `String` - *account_id*, account id.
    - `String` - *account_alias*, name of account.
    - `Object` - *control_program*, control program of account.
    - `String` - *address*, address of account.

##### Example

get specified block information by block_hash or block_height, if both exists, the block result is querying by hash.

```js
// Request
curl -X POST get-block -d '{"block_height": 43, "block_hash": "886a8e85b275e7d65b569ba510875c0e63dece1a94569914d7624c0dac8002f9"}'

// Result
{
  "bits": 2305843009222082600,
  "difficulty": "5549086336",
  "hash": "886a8e85b275e7d65b569ba510875c0e63dece1a94569914d7624c0dac8002f9",
  "height": 43,
  "nonce": 3,
  "previous_block_hash": "2c72ccbd53b92a4f9423c5b610b4e106bbe8fbf3ecf2e16a1266b17ee323ff9d",
  "size": 386,
  "timestamp": 1521614189,
  "transaction_merkle_root": "77d40262cfeca3a16d4587132974552ef00951e43ce567a26801ebc3dbdb4d96",
  "transaction_status_hash": "53c0ab896cb7a3778cc1d35a271264d991792b7c44f5c334116bb0786dbc5635",
  "transactions": [
    {
      "id": "4576b1b1ec251da3263dbdd5486bcbf9a1cd1f712172dbe7a7a5fe46ab194629",
      "inputs": [
        {
          "amount": 0,
          "arbitrary": "09",
          "asset_definition": "7b7d",
          "asset_id": "0000000000000000000000000000000000000000000000000000000000000000",
          "input_id": "6cb8491e4b1cbdc052c2fdb5f2849194d59118b954d5ea5244bbd20e3cff3b80",
          "type": "coinbase",
          "witness_arguments": null
        }
      ],
      "mux_id": "2383cefe8a34ea5810cc0706f2cf8cf08a106f90fc3eb3441f723cecdbc61331",
      "outputs": [
        {
          "address": "sy1q4pkg8msjumtep7ecsdzuct3tsuzk5pmnm3p8nr",
          "amount": 624000000000,
          "asset_definition": "7b7d",
          "asset_id": "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff",
          "control_program": "0014f3403bcd8b443d03882a280b50f6f98986e1a255",
          "id": "da87b40854a9b93be72ecdc24fe7bb03986ea3530e344b0f918f0788c3d83717",
          "position": 0,
          "type": "control"
        }
      ],
      "size": 77,
      "status_fail": false,
      "time_range": 0,
      "version": 1
    }
  ],
  "version": 1
}
```

----

#### `get-block-header`

Returns the detail block header by block height or block hash.

##### Parameters

`Object`: block_height | block_hash

optional:

- `String` - *block_hash*, hash of block.
- `Integer` - *block_height*, height of block.

##### Returns

`Object`:

- `Object` - *block_header*, header of block.
- `Integer` - *reward*, reward.

##### Example

```js
// Request
curl -X POST get-block-header -d '{"block_height": 43, "block_hash": "886a8e85b275e7d65b569ba510875c0e63dece1a94569914d7624c0dac8002f9"}'

// Result
{
  "block_header": "01019601e87da37e7d73f31d54304c719c9058ec7bc7de7819deda89a7c8834a99bc05b8fbdbe6d60540eba9e5d5cb79fd87b3c0fad32f6772c1e4483f2a070e093a6176d85226d986a8c9c377e5192668bc0a367e4a4764f11e7c725ecced1d7b6a492974fab1b6d5bc00ad918480808080801e",
  "reward": 41250000000
}
```

----

#### `get-difficulty`

Returns the block difficulty by block height or block hash.

##### Parameters

`Object`:

optional:

- `String` - *block_hash*, hash of block.
- `Integer` - *block_height*, height of block.

##### Returns

`Object`:

- `Integer` - *bits*, bits of block.
- `String` - *difficulty*, difficulty of block.
- `String` - *hash*, block hash.
- `Integer` - *height*, block height.

##### Example

Get difficulty for specified block hash / height.

```js
// Request
curl -X POST get-difficulty -d '{"block_height": 506, "block_hash": "d1fce60caea5466eae2b812e4586b55120c52aca27b6c92bd7c51e9cda82dcdf"}'

// Result
{
  "bits": 2161727821137910500,
  "difficulty": "15154807",
  "hash": "d1fce60caea5466eae2b812e4586b55120c52aca27b6c92bd7c51e9cda82dcdf",
  "height": 506
}
```

----

#### `get-hash-rate`

Returns the block hash rate by block height or block hash, it returns the current block hash rate when request is empty.

##### Parameters

`Object`:

optional:

- `String` - *block_hash*, hash of block.
- `Integer` - *block_height*, height of block.

##### Returns

`Object`:

- `Integer` - *hash_rate*, difficulty of block.
- `String` - *hash*, block hash.
- `Integer` - *height*, block height.

##### Example

Get hash rate for specified block hash / height.

```js
// Request
curl -X POST get-hash-rate -d '{"block_height": 506, "block_hash": "d1fce60caea5466eae2b812e4586b55120c52aca27b6c92bd7c51e9cda82dcdf"}'

// Result
{
  "hash": "d1fce60caea5466eae2b812e4586b55120c52aca27b6c92bd7c51e9cda82dcdf",
  "hash_rate": 7577403,
  "height": 506
}
```

----

#### `net-info`

Returns the information of current network node.

##### Parameters

none

##### Returns

`Object`:

- `Boolean` - *listening*, whether the node is listening.
- `Boolean` - *syncing*, whether the node is syncing.
- `Boolean` - *mining*, whether the node is mining.
- `Integer` - *peer_count*, current count of connected peers.
- `Integer` - *current_block*, current block height in the node's blockchain.
- `Integer` - *highest_block*, current highest block of the connected peers.
- `String` - *network_id*, network id.
- `Object` - *version_info*, eiyarod version information:
    * `String` - *version*, current version of the running `eiyarod`.
    * `uint16` - *update*, whether there exists an update.
        + 0: no update;
        + 1: small update;
        + 2: significant update.
    * `String` - *new_version*, the newest version of `eiyarod` if there is one.

##### Example

```js
// Request
curl -X POST net-info

// Result
{
  "listening": true,
  "syncing": true,
  "mining": true,
  "peer_count": 0,
  "current_block": 627,
  "highest_block": 0,
  "network_id": "mainnet",
  "version": "0.5.0"
}
```

----

#### `is-mining`

Returns the mining status.

##### Parameters

none

##### Returns

`Object`:

- `Boolean` - *is_mining*, whether the node is mining.

##### Example

```js
// Request
curl -X POST is-mining

// Result
{
  "is_mining": true
}
```

----

### `set-mining`

Start up node mining.

##### Parameters

`Object`:

- `Boolean` - *is_mining*, whether the node is mining.

##### Example

```js
// Request
curl -X POST set-mining -d '{"is_mining": true}'

// Result
```

----

#### `gas-rate`

Quary gas rate.

##### Parameters

none

##### Returns

`Object`:

- `Integer` - *gas_rate*, gas rate.

##### Example

```js
// Request
curl -X POST gas-rate -d '{}'

// Result
{
  "gas_rate": 1000
}
```

----

#### `verify-message`

Verify a signed message with derived pubkey of the address.

##### Parameters

`Object`:

- `String` - *address*, address for account.
- `String` - *derived_xpub*, derived xpub.
- `String` - *message*, message for signature by derived_xpub.
- `String` - *signature*, signature for message.

##### Returns

`Object`:

- `Boolean` - *result*, verify result.

##### Example

```js
// Request
curl -X POST verify-message -d '{"address":"ey1qx2qgvvjz734ur8x5lpfdtlau74aaa5djs0a5jn", "derived_xpub":"6ff8c3d1321ce39a3c3550f57ba70b67dcbcef821e9b85f6150edb7f2f3f91009e67f3075e6e76ed5f657ee4b1a5f4749b7a8c74c8e7e6a1b0e5918ebd5df4d0", "message":"this is a test message", "signature":"74da3d6572233736e3a439166719244dab57dd0047f8751b1efa2da26eeab251d915c1211dcad77e8b013267b86d96e91ae67ff0be520ef4ec326e911410b609"}'

// Result
{
  "result": true
}
```

----

#### `compile`

Compile equity contract.

##### Parameters

`Object`:

- `String` - *contract*, content of equity contract.

Optional:

- `Array of Object` - *args*, parameters of contract.
  - `Boolean` - *boolean*, boolean parameter.
  - `Integer` - *integer*, integer parameter.
  - `String` - *string*, string parameter.

##### Returns

`Object`:

- `String` - *name*, contract name.
- `String` - *source*, source content of contract.
- `String` - *program*, generated program by compiling contract.
- `Array of Object` - *params*, parameters of contract.
- `String` - *value*, locked value name of contract.
- `Array of Object` - *clause_info*, clauses of contract.
- `String` - *opcodes*, opcodes of contract.
- `String` - *error*, returned error information for compiling contract.

##### Example

```js
// Request
{
  "contract": "contract LockWithPublicKey(publicKey: PublicKey) locks locked { clause unlockWithSig(sig: Signature) { verify checkTxSig(publicKey, sig) unlock locked }}",
  "args": [
    {
      "string": "e9108d3ca8049800727f6a3505b3a2710dc579405dde03c250f16d9a7e1e6e78"
    }
  ]
}

// Result
{
  "name": "LockWithPublicKey",
  "source": "contract LockWithPublicKey(publicKey: PublicKey) locks locked { clause unlockWithSig(sig: Signature) { verify checkTxSig(publicKey, sig) unlock locked }}",
  "program": "20e9108d3ca8049800727f6a3505b3a2710dc579405dde03c250f16d9a7e1e6e787403ae7cac00c0",
  "params": [
    {
      "name": "publicKey",
      "type": "PublicKey"
    }
  ],
  "value": "locked",
  "clause_info": [
    {
      "name": "unlockWithSig",
      "args": [
        {
          "name": "sig",
          "type": "Signature"
        }
      ],
      "value_info": [
        {
          "name": "locked"
        }
      ],
      "block_heights": [],
      "hash_calls": null
    }
  ],
  "opcodes": "0xe9108d3ca8049800727f6a3505b3a2710dc579405dde03c250f16d9a7e1e6e78 DEPTH 0xae7cac FALSE CHECKPREDICATE",
  "error": ""
}
```

----

#### `list-peers`

Returns the list of connected peers.

##### Parameters

none

##### Returns

- `Array of Object`, connected peers.
  - `Object`:
    - `String` - *peer_id*, peer id.
    - `String` - *remote_addr*, the address(IP and port) of connected peer.
    - `Integer` - *height*, the current height of connected peer.
    - `String` - *ping*, the delay time of connected peer.
    - `String` - *duration*, the connected time.
    - `Integer` - *total_sent*, total data sent in byte.
    - `Integer` - *total_received*, total data received in byte.
    - `Integer` - *average_sent_rate*, average data sent rate in byte.
    - `Integer` - *average_received_rate*, average data received rate in byte.
    - `Integer` - *current_sent_rate*, current data sent rate in byte.
    - `Integer` - *current_received_rate*, current data received rate in byte.

##### Example

```js
// Request
curl -X POST list-peers -d '{}'

// Result
[  
   {  
      "peer_id":"3B58D7139B53066F2031FC1F027D2B3423FA4CE01F1FB1CC2DC4003C78413C24",
      "remote_addr":"52.83.251.197:46656",
      "height":84222,
      "ping":"40ms",
      "duration":"17.26s",
      "total_sent":17565,
      "total_received":3642187,
      "average_sent_rate":1018,
      "average_received_rate":211019,
      "current_sent_rate":2420,
      "current_received_rate":157087
   }
]
```

----

#### `disconnect-peer`

Disconnect to specified peer.

##### Parameters

`Object`:

- `String` - *peer_id*, peer id.

##### Returns

none if disconnect peer successfully.

##### Example

```js
// Request
curl -X POST disconnect-peer -d '{"peer_id":"29661E8BB9A8149F01C6594E49EA80C6B18BF247946A7E2E01D8235BBBFC3594"}'

// Result
```

----

#### `connect-peer`

Connect to specified peer.

##### Parameters

`Object`:

- `String` - *ip*, peer IP address.
- `Integer` - *port*, peer port.

##### Returns

`Object`:

- `String` - *peer_id*, peer id.
- `String` - *remote_addr*, the address(IP and port) of connected peer.
- `Integer` - *height*, the current height of connected peer.
- `Integer` - *delay*, the delay time of connected peer.

##### Example

```js
// Request
curl -X POST connect-peer -d '{"ip":"139.198.177.164", "port":46657}'

// Result
{
  "peer_id": "29661E8BB9A8149F01C6594E49EA80C6B18BF247946A7E2E01D8235BBBFC3594",
  "remote_addr": "139.198.177.164:46657",
  "height": 65941,
  "delay": 0
}
```

----

#### `get-work`

Get the proof of work.

##### Parameters

none

##### Returns

`Object`:

- `Object` - *block_header*, raw block header.
- `String` - *seed*, seed.

```go
type GetWorkResp struct {
	BlockHeader *types.BlockHeader `json:"block_header"`
	Seed        *bc.Hash           `json:"seed"`
}
```

##### Example

```js
// Request
curl -X POST get-work -d '{}'

// Result
{
  "block_header": "0101870103f2c7495164c8f3af43697e81faa21dcb2d60aa5e10ce4f233491e62420742fbeadfcd50540bef2670a5fade2e58ad4955e2375a04ad1e4cb9c104faddab43f4a79e35be253c9c377e5192668bc0a367e4a4764f11e7c725ecced1d7b6a492974fab1b6d5bc00ffffff838080808020",
  "seed": "702bef3f1707577fd0d75b6359a2919fa216487fe306771e27710acbaa9164ce"
}
```

----

#### `submit-work`

Submit the proof of work.

##### Parameters

`Object`:

- `Object` - *block_header*, raw block header.

```go
type SubmitWorkReq struct {
	BlockHeader *types.BlockHeader `json:"block_header"`
}
```

##### Returns

true if success

##### Example

```js
// Request
curl -X POST submit-work -d '{"block_header": "0101870103f2c7495164c8f3af43697e81faa21dcb2d60aa5e10ce4f233491e62420742fbeadfcd50540bef2670a5fade2e58ad4955e2375a04ad1e4cb9c104faddab43f4a79e35be253c9c377e5192668bc0a367e4a4764f11e7c725ecced1d7b6a492974fab1b6d5bc00ffffff838080808020"}'

// Result
true / error
```

----

#### `get-work-json`

Get the proof of work by json.

##### Parameters

none

##### Returns

`Object`:

- `Object` - *block_header*, Object of block header.
  - `Integer` - *version*, version of block.
  - `Integer` - *height*, height of block.
  - `String` - *previous_block_hash*, previous block hash.
  - `Integer` - *timestamp*, timestamp of block.
  - `Integer` - *nonce*, nonce value.
  - `Integer` - *bits*, bits of difficulty.
  - `Object` - *block_commitment*, Object of block commitment.
    - `String` - *transaction_merkle_root*, merkle root of transaction.
    - `String` - *transaction_status_hash*, merkle root of transaction status.
- `String` - *seed*, seed.

##### Example

```js
// Request
curl -X POST get-work-json -d '{}'

// Result
{
  "block_header": {
    "version": 1,
    "height": 62960,
    "previous_block_hash": "dabdb926f8635791ac43f5d5fc62a4597e10e140f00aced3af621a77ead4e9fd",
    "timestamp": 1533006396,
    "nonce": 0,
    "bits": 2017612633069711400,
    "block_commitment": {
      "transaction_merkle_root": "a13fc86af3852ab73e30c3ae30e8cedbe990560a3c0f20dc37c4c14562b94802",
      "transaction_status_hash": "c9c377e5192668bc0a367e4a4764f11e7c725ecced1d7b6a492974fab1b6d5bc"
    }
  },
  "seed": "2e2010e11289d6b273b7b1ea947f98cc5ad60d206df184b1501f8ac903fa01a9"
}
```

----

#### `submit-work-json`

Submit the proof of work by json.

##### Parameters

`Object`:

- `Object` - *block_header*, Object of block header.
  - `Integer` - *version*, version of block.
  - `Integer` - *height*, height of block.
  - `String` - *previous_block_hash*, previous block hash.
  - `Integer` - *timestamp*, timestamp of block.
  - `Integer` - *nonce*, nonce value.
  - `Integer` - *bits*, bits of difficulty.
  - `Object` - *block_commitment*, Object of block commitment.
    - `String` - *transaction_merkle_root*, merkle root of transaction.
    - `String` - *transaction_status_hash*, merkle root of transaction status.

##### Returns

true if success

##### Example

```js
// Request
curl -X POST submit-work-json -d '{"block_header":{"version":1,"height":62960,"previous_block_hash":"dabdb926f8635791ac43f5d5fc62a4597e10e140f00aced3af621a77ead4e9fd","timestamp":1533006396,"nonce":0,"bits":2017612633069711400,"block_commitment":{"transaction_merkle_root":"a13fc86af3852ab73e30c3ae30e8cedbe990560a3c0f20dc37c4c14562b94802","transaction_status_hash":"c9c377e5192668bc0a367e4a4764f11e7c725ecced1d7b6a492974fab1b6d5bc"}}}'

// Result
true / error
```





# 05 Kernel Layer - Block and Chain

## 5.1 Introduction

​	Blockchain is a growing list of blocks that are linked in chronological order containing many transactions. This data is a typical link-list structure, all blocks are linked in one blockchain. Each block includes the cryptography hash of the prior block in the blockchain, linking the two. That cryptography hash comes from hashing(SHA256) the previous blockheader and can uniquely identifies a block. 


​	As blockchain only allows to link a block to the end of the chain, it is also said that it is like a stack, the first block is in the bottom and then add blocks above it in order, of course, the latest block is the top of stack. The data of blocks can be stored in relational database as well as non-relational database like LevelDB. There are some special words in blockchain, such as genesis block ( the first block of chain, also known as the bottom stack block), which is usually hard-coded and is mined by miners. The height of block: the number of blocks from genesis block to current block.


​	The block structure is similar to HTTP request, consisting of `Header` and `Body`, storing some metadata and valid data respectively. `Header` is just the head of block, while `Body` is the main part of block which normally represents a block and stores all transaction information.


​	Although each block only has one parent block, one block may have many children blocks. It is known as forks, happens when many blocks take a same block as their parent block and will be found by miners. Forks are very common, but will not exist for a long time under the blockchain consensus that ensures one chain. Because of forks, the latest few blocks in blockchain might be changed by recomputing. In Eiyaro, the latest 6-8 blocks might be changed, but it is almost impossible over 6 blocks. Therefore, a transaction that is confirmed by six consecutive blocks can be seen a success. 


​	Generally, because of the chain structure, currencies based on blockchain are believed to be very safe compared with traditional currencies. The `previous block hash` field is inside the block header and thereby affects the current block’s hash. The child’s own identity changes if the parent’s identity changes. This cascade effect ensures that once a block has many generations following it, it cannot be changed without forcing a recalculation of all subsequent blocks, while such a recalculation would require enormous computation which can hardly be met.



* Structure of the block and the chain
* The genesis Block
* Block validation, link to the blockchain and the difficulty target
* Orphan block management




## 5.2 Block 

​	Data structure is defined into two layers: types layer (protocol/bc/types) and bc layer (protocol/bc). Such as `Tx` is defined in both of them, and transformed by `MapTx` method between two layers. Layers make the data structure more specific. 

* types layer: It is for raw data and transferred between nodes
* bc layer: It is for visual machine computing, especially for improving transactions validation. 


 

### 5.2.1 Structure of a block 

​	The block is made of a header, containing metadata, and a body, storing all transactions. A block can be seen as a ledger that records transactions and has a title page for brief introduction, namely, block header. Here is the `Block` struct:

 

protocol/bc/block.go

```
type Block struct {
	*BlockHeader
	ID           Hash
	Transactions []*Tx
}
```

The structure of a block:

###### **chart 5-1**   **The structure of a block:**

| Field        | Bytes    | Description                                   |
| ------------ | -------- | --------------------------------------------- |
| BlockHeader  | Variable | Several fields form the block header          |
| ID           | 32       | Block hash which can identify a certain block |
| Transactions | Variable | The transactions recorded in this block       |

### 5.2.2 Structure of Block Header

​	The block header consists of three parts. The first is a reference to a previous block hash, which connects this block to the previous block in the blockchain. The second part relates to mining, including the `Timestamp`, `Noce` and `Bits`. Miner need to compute the `Nonce` until it meets the `Bits`. The last part is the merkle tree root, a data structure used to efficiently summarize all the transactions in the block. Here is the `Block Header` structure: 


 protocol/bc/bc.pb.go

```
type BlockHeader struct {
	Version               uint64
	Height                uint64
	PreviousBlockId       *Hash
	Timestamp             uint64
	TransactionsRoot      *Hash
	TransactionStatusHash *Hash
	Nonce                 uint64
	Bits                  uint64
	TransactionStatus     *TransactionStatus
}
```

 

The structure of a block header:

###### **chart5-2** **The structure of a block header:**

| Field                 | Bytes    | Description                                                  |
| --------------------- | -------- | ------------------------------------------------------------ |
| Version               | 8        | A version number to track software/protocol upgrades         |
| Height                | 8        | The height of the block, counted from the genesis block      |
| PreviousBlockId       | 32       | A reference to the hash of the previous (parent) block in the chain |
| Timestamp             | 8        | The approximate creation time of this block (seconds from Unix Epoch) |
| TransactionsRoot      | 32       | A hash of the root of the merkle tree of this block’s transactions |
| TransactionStatusHash | 32       | A hash of the root of the merkle tree of validation results of this block’s transactions |
| Nonce                 | 8        | A counter used for the Proof-of-Work algorithm               |
| Bits                  | 8        | The Proof-of-Work algorithm difficulty target for this block |
| TransactionStatus     | Variable | The status of transaction after being validated              |

 

### 5.2.3 Block Identifiers 

​	The block identifier can identify a block uniquely. Block identifiers include block header hash and block Height.


The block header hash is made by hashing the block header twice through the SHA256 algorithm. The resulting 32- byte hash is called the *block hash* but is more accurately the *block header hash*. The SHA256 algorithm6 is a digtial signature algorithm defined by digtial signature standard, mainly used to compute the digest of message. No matter many bytes the input is, there will always be a 32- byte hash computed by the SHA256 algorithm. After receiving the message, it can be checked by this 32-byte hash. A tampered message can be catch since its 32-byte hash has been changed. 

​	Features of the SHA256 algorithm:

* No one can get the original message by the 32- byte hash 
* Messages that are different can not produce a same 32- byte hash 


​	Because of the second feature, this 32-byte hash is also called digital fingerprint and identify a block uniquely.


​	Note that the block hash is not actually included inside the block’s data structure, and there will not go through all blocks from the highest and check their `PreviousBlockHash` . Instead, the block hash is computed by each node as the block is received from the network. The block hash is stored in a LevelDB as the `key`, to facilitate indexing and faster retrieval of blocks from disk, and the block is its `value`.


​	A second way to identify a block is by its position in the blockchain, called the *block height*. But unlike the block hash, the block height is not a unique identifier. That because of the blockchain forks, two or more blocks might have the same block height, competing for the same position in the blockchain. In this scenario, their heights are totally same, while the block hash differs from each other. Obviously, the height can not identify a block uniquely. Blockchain soft fork is just a temporary status, ending up being selected by the consensus algorithm. It is generally believed that in the height smaller than `the highest - 6`, there is not the blockchain soft forks 


### 5.2.4 The Genesis Block

​	The first block in the blockchain is called the genesis block.It is the common ancestor of all the blocks in the blockchain, meaning that if you start at any block and follow the chain backward in time, you will eventually arrive at the genesis block. Every node always starts with a blockchain of at least one block because the genesis block is statically encoded within the core of the code, such that it cannot be altered. 


​	The genesis block can be got by `eiyarocli` commandline tool, here it is :


```
$ ./eiyarocli get-block 0

{
  "bits": 2161727821137910500,
  "difficulty": "15154807",
  "hash": "a75483474799ea1aa6bb910a1a5025b4372bf20bef20f246a2c2dc5e12e8a053",
  "height": 0,
  "nonce": 9253507043297,
  "previous_block_hash": "0000000000000000000000000000000000000000000000000000000000000000",
  "size": 546,
  "timestamp": 1524549600,
  "transaction_merkle_root": "58e45ceb675a0b3d7ad3ab9d4288048789de8194e9766b26d8f42fdb624d4390",
  "transaction_status_hash": "c9c377e5192668bc0a367e4a4764f11e7c725ecced1d7b6a492974fab1b6d5bc",
  "transactions": [
  // ...
  ],
  "version": 1
}
```

​	The description of the genesis block:

* Bits: The target. Only if the miner produces a hash that is less than the target, the block can be packaged successfully by him. Although the target of genesis block is *216172782113791050* , it is not produced by miners but statically encoded within the block, so in fact it can be written whatever.
* Difficulty：The difficulty, computed from bits. One can estimate the amount of work it takes to succeed from the difficulty.
* Height: The height of the block. The genesis block is height-0, which means blocks are counted from height-0.
* Nonce: A counter used for the Proof-of-Work algorithm. It is used to vary the output of a cryptographic function to meet the bits.
* previous_block_hash：A reference to the hash of the previous (parent) block in the chain. Since the genesis block is the first block and doesn't have a parent block, here is statically encoded with *000000...000000* by default.
* timestamp：The approximate creation time of this block. The genesis block's is `152454960` (2018-04-24 14:00:00), namely the time of Eiyaro Mainnet implementation. 
* transaction_merkle_root: A hash of the root of the merkle tree of this block’s transactions. It is used to validate the integrity of transactions in this block.
* transaction_status_hash: A hash of the root of the merkle tree of validation results of this block’s transactions.
* transactions: The list of transactions in this block.


 

### 5.2.5 Generate the Genesis Block 

​	When starting a node, it will firstly try to get the blocks information from local Level DB. If the node didn't get blocks, it would regard itself as a new node and initialize the local blockchain according to the statically encoded genesis block. Here is the code to generate the genesis block: 


config/genesis.go

```
func mainNetGenesisBlock() *types.Block {
   tx := genesisTx()
   txStatus := bc.NewTransactionStatus()
   if err := txStatus.SetStatus(0, false); err != nil {
      log.Panicf(err.Error())
   }
   txStatusHash, err := types.TxStatusMerkleRoot(txStatus.VerifyStatus)
   // ...

   merkleRoot, err := types.TxMerkleRoot([]*bc.Tx{tx.Tx})
   // ...
   block := &types.Block{
      BlockHeader: types.BlockHeader{
         Version:   1,
         Height:    0,
         Nonce:     9253507043297,
         Timestamp: 1524549600,
         Bits:      2161727821137910632,
         BlockCommitment: types.BlockCommitment{
            TransactionsMerkleRoot: merkleRoot,
            TransactionStatusHash:  txStatusHash,
         },
      },
      Transactions: []*types.Tx{tx},
   }
   return block
}
```

​	`mainNetGenesisBlock()` function is used to generate the genesis block. Firstly, a coinbase transaction is produced by `genesisTx()` function and here puts additional information, *Information is power. -- Jan/11/2013. Computing is power. -- Apr/24/2018.*, in this coinbase transaction to memorize the computer genius *Aaron Swart* for his innovative spirit of openning network. There are about 1.4 billion EY in the output of this coinbase transaction bound with a lock script *00148c9d063ff74ee6d9ffa88d83aeb038068366c4c4*.  These 1.4 billion EY is kept by the Eiyaro official, partly used to exchange tokens with the exchanges.


 	After building the coinbase transaction, the node will set the coinbase transaction validation status successful. Here usually has a misunderstanding, why it is successful with a `false` argument? That because Eiyaro uses `StatusFail` to record transaction status (succeed or fail), and setting `false` means the transaction is successful. Later, the node will generate a hash of the root of the merkle tree of the genesis block’s transactions to build the block.


 

### 5.2.6 Blocks Validation 

​	Blocks validation is vital to ensure the blockchian doesn't have forks. If there is no validation, many forks will be produced in the process of blocks sychrolization between nodes. Forks are great threats to the safety of assets in the blockchain.


​	Usually, blocks are validated in the following situations:

* Once a mining node successfully mines a block and submits it to the blockchain, this block will be validated firstly.
* When users submit blocks to the blockchain by API, the node validate blocks.
* In the process of syncing blocks, every time the node receives blocks from others, it will validate blocks and only link valid blocks to the local blockchain.
* When mining nodes submit their work to the mining pool, the mining pool will validate the blocks they submit.


​	In order to improve the efficiency of  blocks synchronization, Eiyaro offers a safe, simple also fast methond to validate blocks compared with *Etherum*. The blocks validation strategy of *Etherum* is a little complicated, which always makes machine beep  when the blocks validation starts. Here is the code: 


protocol/block.go

```
func (c *Chain) processBlock(block *types.Block) (bool, error) {
   blockHash := block.Hash()
   if c.BlockExist(&blockHash) {
      log.WithFields(log.Fields{"hash": blockHash.String(), "height": block.Height}).Info("block has been processed")
      return c.orphanManage.BlockExist(&blockHash), nil
   }
   if parent := c.index.GetNode(&block.PreviousBlockHash); parent == nil {
      c.orphanManage.Add(block)
      return true, nil
   }
   if err := c.saveBlock(block); err != nil {
      return false, err
   }
   bestBlock := c.saveSubBlock(block)
   bestBlockHash := bestBlock.Hash()
   bestNode := c.index.GetNode(&bestBlockHash)
   if bestNode.Parent == c.bestNode {
      log.Debug("append block to the end of mainchain")
      return false, c.connectBlock(bestBlock)
   }
   if bestNode.Height > c.bestNode.Height && bestNode.WorkSum.Cmp(c.bestNode.WorkSum) >= 0 {
      log.Debug("start to reorganize chain")
      return false, c.reorganizeChain(bestNode)
   }
   return false, nil
}
```

​	`processBlock()` function works in the following steps:

1) Compute the block hash and check whether the block exists in the local blockchain or orphan blocks pool by its hash. If the block has been in the local blockchain, then it would be thrown away directly.

2) Find the block's parent block in the local blockchain according to its `PreviousBlockHash`. If the parent block doesn't exist, this block will be put into orphan block pool.

3) If its parent block exists in the local blockchain, link this block to the local blockchain after verifing its transactions and metadata.

4) Check if there are blocks in orphan block pool that can be linked to the blockchain. If there are, link the blocks to the blockchain in some order and then get the highest block `bestNode`.

5) If the `bestNode`'s  parent block is the highest block `c.betNode` before this operation, link the `bestNode` after `c.betNode`. If not, that means the `bestNode` is in the sidechain instead of the main blockchain.

6）If the `bestNode` is in the sidechain and its height is over the main blockchain, which means the work of this sidechain is more than the main blockchain, the node will reorganize the blockchain.


​	In step (3), it needs to validate the block header before linking the block to the blockchain. This work covers 5 parts. Here is the code:


 protocol/validation/block.go

```
func ValidateBlockHeader(b *bc.Block, parent *state.BlockNode) error {
   if b.Version < parent.Version {
      // ...
   }
   if b.Height != parent.Height+1 {
      // ...
   }
   if b.Bits != parent.CalcNextBits() {
      // ...
   }
   if parent.Hash != *b.PreviousBlockId {
      // ...
   }
   if err := checkBlockTime(b, parent); err != nil {
      // ...
   }
   if !difficulty.CheckProofOfWork(&b.ID, parent.CalcNextSeed(), b.BlockHeader.Bits) {
      // ...
   }
   return nil
}
```

​	The block header validation includes the following work:

- Check if the version of the block equals to the version of its parent block
- Check if the height of the block is the height of the parent block plus 1
- Check if the block target equals the next block target that is calculated by the parent block height. 
- Check if the block's `Previous Block Hash` is the hash of its parent block
- Check that the timestamp isn't later more than one hours compared with the current time and not earlier than the intermediate time of the last 11 blocks.
- Check if the hash of the block equals to the target.


### 5.2.7 Calculate the Next Target 

​	Here is the code that calculates the next target according the parent block height: 


 protocol/state/blockindex.go

```
func (node *BlockNode) CalcNextBits() uint64 {
   if node.Height%consensus.BlocksPerRetarget != 0 || node.Height == 0 {
      return node.Bits
   }

   compareNode := node.Parent
   for compareNode.Height%consensus.BlocksPerRetarget != 0 {
      compareNode = compareNode.Parent
   }
   return difficulty.CalcNextRequiredDifficulty(node.BlockHeader(), compareNode.BlockHeader())
}
```

**Algorithm**:  If `the parent block height` *mod* `consensus.BlocksPerRetarget（2016)` doesn't equal 0 as well as the parent block height, the next target will be the same as the parent block's. Or the target will be adjusted. See more detials about `retarget` in the consensus part.


### **5.2.8 Orphan Block Management **

​	If a valid block is received and no parent is found in the existing chains, that block is considered an “orphan.” Orphan blocks are saved in the orphan block pool where they will stay until their parent is received. Once the parent is received and linked into the existing chains, the orphan can be pulled out of the orphan pool and linked to the parent, making it part of a chain. 



​	Orphan blocks usually occur when two blocks that were mined within a short time of each other are received in reverse order (child before parent). Here assumes that there are three blocks and their heights are 100, 101, 102 respectively, then they are received by the node in the order of 102, 101, 100. In this situation, the node will put 102, 101 blocks into orphan block pool where they will stay until their parent is received. Once the 100 block is received and linked to the existing chain after validation, those two orphan blocks will be found by a recursive function, and linked to the parent.


​	Here is the orphan block structure:


protocol/orphan_manage.go

```
type orphanBlock struct {
   *types.Block
   expiration time.Time
}
```

​	The description of orphan block:

* types.Block: The orphan block structure.
* Expiration: The expiration of the orphan block, which means if this orphan block doesn't be linked to the blockchain before its expiration, it will be thrown away.


​	Here is the orphan block pool structure :


```
type OrphanManage struct {
   orphan      map[bc.Hash]*orphanBlock
   prevOrphans map[bc.Hash][]*bc.Hash
   mtx         sync.RWMutex
}
```

​	The description of orphan block pool:

* Orphan: Used to store orphan blocks, key is block hash and value is the block.
* prevOrphans: Orphan block's parent block.
* Mtx: Exclusive lock, used to ensure the consistency of `map` structure data in multiple concurrency.


​	The orphan block manager has main functions as follow:

* BlockExist(): Check if a block is orphan
* Add(): Add new orphan blocks into the orphan block pool
* Delete(): Delete an orphan block from the orphan block pool
* Get(): Get a block from the orphan block pool according to its hash
* GetPrevOrphans(): Get all blocks that have a same specific parent block hash from the orphan block pool.
* orphanExpireWorker(): A timer that sets the time to clean the timeout orphan blocks
* orphanExpire(): Used to clean the timeout orphan block




1) Here is the code that adds an orphan block into the orphan block pool:


```
func (o *OrphanManage) Add(block *types.Block) {
   blockHash := block.Hash()
   o.mtx.Lock()
   defer o.mtx.Unlock()
   if _, ok := o.orphan[blockHash]; ok {
      return
   }
   o.orphan[blockHash] = &orphanBlock{block, time.Now().Add(orphanBlockTTL)}
   o.prevOrphans[block.PreviousBlockHash] = append(o.prevOrphans[block.PreviousBlockHash], &blockHash) 
}
```

​	Adding a orphan block means not only putting the orphan block into the orphan block pool, also recording the relationship between the orphan block and its parent block, so that the orphan blocks that have the same parent can be found easily.


2) Here is the code that deletes an orphan block from the orphan block pool: 

```
func (o *OrphanManage) delete(hash *bc.Hash) {
   block, ok := o.orphan[*hash]
   if !ok {
      return
   }
   delete(o.orphan, *hash)
   prevOrphans, ok := o.prevOrphans[block.Block.PreviousBlockHash]
   if !ok || len(prevOrphans) == 1 {
      delete(o.prevOrphans, block.Block.PreviousBlockHash)
      return
   }
   for i, preOrphan := range prevOrphans {
      if preOrphan == hash {
         o.prevOrphans[block.Block.PreviousBlockHash] = append(prevOrphans[:i], prevOrphans[i+1:]...)
         return
      }
   }
}
```

​	Accordingly,deleting an orphan block from the orphan block pool needs to delete the orphan block as well as its recorded relationship.


​	Here is the code that cleans the orphan blocks regularly:


```
orphanExpireScanInterval = 3 * time.Minute

func (o *OrphanManage) orphanExpireWorker() {
   ticker := time.NewTicker(orphanExpireWorkInterval) //3分钟
   for now := range ticker.C {
      o.orphanExpire(now)
   }
   ticker.Stop()
}

func (o *OrphanManage) orphanExpire(now time.Time) {
   o.mtx.Lock()
   defer o.mtx.Unlock()
   for hash, orphan := range o.orphan {
      if orphan.expiration.Before(now) { //孤块超时时间60分钟
         o.delete(&hash)
      }
   }
}
```

​	Regular orphan block cleaning is for preventing a lot of useless orphan blocks keeping using memory and wasting resources. There might be some blocks that are produced by the malicious nodes and have no existed parent block, so if these blocks are not cleaned up, it will keep occupying a lot local memory. Therefore, orphan block manager starts a goroutine to clean them regularly, running a timer every 3 minutes to clean the orphan blocks that have existed over 60 minutes.

 

## 5.3 Blockchain 

​	The blockchain data structure is an ordered, back-linked list of blocks of transactions. Here are more details about blockchain data structure in the following part.

> 区块是区块链上的一个元素，区块链按照时间顺序将区块顺序相连，最终形成一条以时间为序列的链表结构。下面详细讲解区块链相关的核心数据结构。

### 5.3.1 The Blockchain Data Structure 

​	The chain-related functions are mainly done by `Chain` structure. `Chain` offers many functions, such as initializing chain, getting the block according to a specific height or hash, getting the current blockchain height, validating the blocks, link and reorganize blocks, etc. Here is the structure of `Chain`: 


protocol/protocol.go

```
type Chain struct {
	index          *state.BlockIndex
	orphanManage   *OrphanManage
	txPool         *TxPool
	store          Store
	processBlockCh chan *processBlockMsg

	cond     sync.Cond
	bestNode *state.BlockNode
}
```

​	The description of `Chain` structure:

* Index: The index of block that is stored in the memory. It is designed to be a tree structure to seek blocks quickly.
* orphanManage: Orphan block manager.
* txPool: Transaction pool.
* Store: The interface of blocks persistent storage.
* processBlockCh: The cache of blocks that wait for validation.
* cond: It is a structure defined in the `sync` package of GoLang, used as the concurrency control between goroutines.
* bestNode: Record the highest block of the current blockchain.


​	The functions of chain is the most important part in Eiyaro, which realize many operations, such as the transaction validation, the blocks linkage and validation, etc. Here are more details:

* func (c *Chain) BestBlockHeight()：Get the height of the main blockchain
* func (c *Chain) BestBlockHash()：Get the highest block of the current main blockchain
* func (c *Chain) BestBlockHeader()：Get  the highest block's header of the current main blockchain
* func (c *Chain) InMainChain()：Check if the block is in the main blockchain
* func (c *Chain) CalcNextSeed()：Calculate the seed in Tensor algorithm  
* func (c *Chain) CalcNextBits()：Calculate the next bits according to the parent block information
* func (c *Chain) GetTransactionStatus()：Get the status of all transactions in the block
* func (c *Chain) GetTransactionsUtxo()：Get UTXOs referred by all transactions in the block 
* func (c *Chain) ValidateTx()：Validate transactions
* func (c *Chain) ProcessBlock() ：Process blocks. Blocks that are validated successfully will be link to the main blockchain 



 

### 5.3.2 Add Blocks into the Blockchain 

​	It is clear that the block will be linked to the blockchain after a series operations of validation mentioned in the block validation part. But there is no more details about how the blocks are store into the blockchain. 

​	In this section, we will go to the details. This work is mainly done by `func (c *Chain) saveBlock(block *types.Block)` function in `protocol/block.go`, and the `saveBlock()` function works in the following steps.


**Step 1**: Store the block into the local LevelDB.

​	In the `saveBlock()` function of the `Chain`, the block will be stored into the local LevelDB by the `SaveBlock()` interface after being validated. Here is the code of this interface : 


database/leveldb/store.go

```
func (s *Store) SaveBlock(block *types.Block, ts *bc.TransactionStatus) error {
   binaryBlock, err := block.MarshalText()
   // ...
   binaryBlockHeader, err := block.BlockHeader.MarshalText()
   // ...
   binaryTxStatus, err := proto.Marshal(ts)
   // ...
   blockHash := block.Hash()
   batch := s.db.NewBatch()
   batch.Set(calcBlockKey(&blockHash), binaryBlock)
   batch.Set(calcBlockHeaderKey(block.Height, &blockHash), binaryBlockHeader)
   batch.Set(calcTxStatusKey(&blockHash), binaryTxStatus)
   batch.Write()
   
   return nil
}
```

​	In the `SaveBlock()` interface, there are three types of block information needed to be stored into the LevelDB. The first is the serialized information of the whole block, using  `B`  as its prefix of key, follwed by the block hash. The second is the serialized information of the block header, using  `BH`  as its prefix of key, follwed by the block height and hash. The third is the serialized information of all transactions is the block, using  `BTS`  as its prefix of key, follwed by the block hash. 


**Step 2**: Delete a block from the orphan block pool that has been stored into the LevelDB.


```
c.orphanManage.Delete(&bcBlock.ID)
```

**Step 3**: Update the new block into the memory of block index.


```
node, err := state.NewBlockNode(&block.BlockHeader, parent)
if err != nil {
   return err
}

c.index.AddNode(node)
```

​	In the memory, the `BlockIndex` structure is used to conduct and store the block information. In the `BlockIndex` structure, the `index` maps the block, using the hash of the block header as its key. A block can be found quickly by using its hash to match the index. Meanwhile, there is also a `blockNode` array to store the blocks. ???


```
type BlockIndex struct {
	sync.RWMutex

	index     map[bc.Hash]*BlockNode
	mainChain []*BlockNode
}
```

​	For a block, being store into the blockchain doesn't equal to being in the main blockchain, there are chances to be in the side chain. Therefore, when a block is add by the `AddNode()` function, only the block information of the `index` will be updated with no change in the `mainChain` structure.


```
func (bi *BlockIndex) AddNode(node *BlockNode) {
  bi.Lock()
  bi.index[node.Hash] = node
  bi.Unlock()
}
```

### **5.3.3 Linking Blocks in the Blockchain **

​	Blocks need to be connected after being add into the blockchain. This work mainly involves operations on `UTXO`,  including marking all UTXOs in the block  "spent" and removing all transactions out of the transaction pool. Here is the code: 


protocol/block.go

```
func (c *Chain) connectBlock(block *types.Block) (err error) {
   bcBlock := types.MapBlock(block)
   if bcBlock.TransactionStatus, err = c.store.GetTransactionStatus(&bcBlock.ID); err != nil {
      return err
   }
   utxoView := state.NewUtxoViewpoint()
   if err := c.store.GetTransactionsUtxo(utxoView, bcBlock.Transactions); err != nil {
      return err
   }
   if err := utxoView.ApplyBlock(bcBlock, bcBlock.TransactionStatus); err != nil {
      return err
   }
   node := c.index.GetNode(&bcBlock.ID)
   if err := c.setState(node, utxoView); err != nil {
      return err
   }
   for _, tx := range block.Transactions {
      c.txPool.RemoveTransaction(&tx.Tx.ID)
   }
   return nil
}
```

​	First of all, get all UTXOs spent by transactions of this block by the `GetTransactionsUtxo()` function, then put them into the `UtxoViewpoint`, which represents a set of UTXOs. The `ApplyBlock()` function is used to update the status of UTXOs, that is the UTXOs in the input will be marked "spent" if the following two conditions are met:

* The transaction is successful and the asset in the UTXOs is EY
* If it is an UTXO produced by a coinbase transaction, it can only be used in a transaction after 100 blocks, which means the coinbase UTXO will be unlocked after 100 blocks.


​	After the  `ApplyBlock()` function updates the status of the UTXOs , it will store the UTXO that is produced by the coinbase transaction into the UTXOs set of the LevelDB. Apart from that, the status of the UTXOs that has been spent also needs to be updated into the UTXOs set of the LevelDB by the   `setState()` function.


### 5.3.4 Reorganize the Blockchain

​	The node adds the received block into the blockchain. The soft fork or the side chain is a forward-compatible change to the consensus rules that allows unupgraded clients to continue to operate in consensus with the new rules. The side chain also have chances to be the main chain, which all depends on the work of chain. In the `processBlock` function, if there is a block whose parent block is not in the main chain, the work of the main and side chains will be measured. The blockchain will be reorganized if the side chain wins. The side chain becomes the main chain. Her is the flow chart of the blockchain reorganization.




​	The calculation algorithm of the amout of work is here:


```
func CalcWork(bits uint64) *big.Int {
   difficultyNum := CompactToBig(bits)
   if difficultyNum.Sign() <= 0 {
      return big.NewInt(0)
   }
   denominator := new(big.Int).Add(difficultyNum, bigOne)
   return new(big.Int).Div(oneLsh256, denominator)
}
```

​	The formula of the amount of work:


```
SumPoW = 2^256 / (difficultyNum+1)
```

​	If the amount of work on the new side chain is over than the older best chain, this side chain will become the new main chain. To achieve that, it needs to find the detach node then cut the link with the old chain and link the following new blocks to the new main chain. All operations above are mainly made by the `reorganizeChain()` function.


**Step 1**, figure out the blocks that need to be reorganized and deleted.

​	The  `reorganizeChain()` functions finds the detach node and records `detachNodes` and `attachNodes`.  `detachNodes` are nodes that need to be cut when reorganization happens, while  `attachNodes` needs to be appended to the blockchain. Here is the code:


protocol/block.go

```
func (c *Chain) calcReorganizeNodes(node *state.BlockNode) ([]*state.BlockNode, []*state.BlockNode) {
	var attachNodes []*state.BlockNode
	var detachNodes []*state.BlockNode

	attachNode := node
	for c.index.NodeByHeight(attachNode.Height) != attachNode {
		attachNodes = append([]*state.BlockNode{attachNode}, attachNodes...)
		attachNode = attachNode.Parent
	}

	detachNode := c.bestNode
	for detachNode != attachNode {
		detachNodes = append(detachNodes, detachNode)
		detachNode = detachNode.Parent
	}
	return attachNodes, detachNodes
}
```

​	The `calcReorganizeNode` function will start with the last block to go through the whole side chain. 


**Step 2** , process the ` detachNodes`.

​	Find all blocks that need to be deleted in the `detachNode` array and then process the status of transactions in these blocks by the `DetachTransaction` function. The  `DetachTransaction` function set all UTXOs used in transactions "unspent" so that they can still be spent later. Meanwhile, the `OUTPUT` of these transactions will be set referred to avoid being used in the new transactions. The UTXO set is returned to the original status by operations above. Here is the code:


```
func (view *UtxoViewpoint) DetachTransaction(tx *bc.Tx, statusFail bool) error {
	for _, prevout := range tx.SpentOutputIDs {
		spentOutput, err := tx.Output(prevout)
		if err != nil {
			return err
		}
		if statusFail && *spentOutput.Source.Value.AssetId != *consensus.EYAssetID {
			continue
		}

		entry, ok := view.Entries[prevout]
		if ok && !entry.Spent {
			return errors.New("try to revert an unspent utxo")
		}
		if !ok {
			view.Entries[prevout] = storage.NewUtxoEntry(false, 0, false)
			continue
		}
		entry.UnspendOutput()
	}

	for _, id := range tx.TxHeader.ResultIds {
		output, err := tx.Output(*id)
		if err != nil {
			// error due to it's a retirement, utxo doesn't care this output type so skip it
			continue
		}
		if statusFail && *output.Source.Value.AssetId != *consensus.EYAssetID {
			continue
		}

		view.Entries[*id] = storage.NewUtxoEntry(false, 0, true)
	}
	return nil
}
```

**Step 3**, process the `attachNode`

​	This step works just in the opposite of step 2. Here, the node goes through the `attachNodes` array to find the blocks that need to be linked to the main chain. They are linked to the chain by the `ApplyBlock()` function and the `ApplyTransaction()` function  locks all transactions of the block by setting all UTXOs "spent" and instantiates the new UTXO object。


 **Step 4**, Update the UTXO set.

​	Add all UTXOs instantiated in the last step into the UTXO set. Update the chain status and change the best block to the highest block of the side chain.


 

### 5.3.5 the Status of the Main Blockchain 

​	At the first time to start a node, initializing the main blockchain or not depends on the `blockStore` key in the database. When initializing the main blockchain, the height of the blockchain is 0 and the hash is the genesis block hash. Here is the code:


protocol/store.go

```
type BlockStoreState struct {
	Height uint64
	Hash   *bc.Hash
}
```

​	The description of the main blockchain status:

* Height: The biggest height of the current main blockchain.
* Hash: The hash of the highest block in the current main blockchain.


​	The status of the main blockchain needs to be store in persistent storage since it will be used every time the node is restarted. 


database/leveldb/store.go

```
blockStoreKey     = []byte("blockStore")
```

​	The steps of updating the main blockchain status:

1) Update the main blockchain status at the first time the node is started.

2) Update the main blockchain status when new blocks are linked to the main blockchain.

3) Update the main blockchain status when reorganization happens on the main blockchain.



​	This chapter analyzes in the detail on the structure of block and blockchain as well as  their basic operations. Analysis mainly includes the genesis block, block validation, target, orphan block management, link and reorganize blocks, etc., offering a macro view of the blockchain.

