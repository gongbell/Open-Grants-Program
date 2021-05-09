# W3F Open Grant Proposal


* **Project Name:** WANA4Parachain：An symbolic execution engine for detecting bugs in parachains
* **Team Name:** BHUer
* **Payment Address:** 1NbKV2UVispBLNVktYVYcUFFZujRwrFVRy

## Project Overview :page_facing_up:


### Overview

WANA4Parachain aims at building an verification tool for detecting bugs and exceptions in parachains within polkadot ecosystem based on the symbolic execution of Wasm bytecode. 

The Substrate framework provides powerful building blocks to build a customized parachain. With the rapid growth the Polkadot ecosystem, the number of active parachains connected to the relay chain platform will also increase dramatically. As a result, the correctness and the security of the parachains are crucial to the correctness and security of the whole polkadot ecosystem. Due to the large number of parachains, it would be desirable to have practical tools to automatically verify the correctness and the security of them. 

In substrate, all parachain code will be compiled into Wasm bytecode for execution. Therefore, the Wasm bytecode provides an uniform interface for verifying the correctness and security of parachains. In our previous work, we have built the WANA (https://github.com/gongbell/WANA), a Wasm bytecode symbolic execution engine to verify EOSIO and Ethereum smart contracts. WANA can perform symbolic execution on any Wasm module to traverse each reachable path to detect the existence of bugs or vulnerabilities. We have successfully detected the bugs or security vulnerabilities in EOSIO and Ethereum smart contracts. In this project, we aims at extending WANA to support the verification of parachain code. In general, we want to extend WANA to verify Wasm modules of both smart contracts and blockchain code. Moreover, we would be happy if WANA can be extended to detection parachain bugs or vulnerabilities. 


### Project Details

We expect the teams to already have a solid idea about your project's expected final state. Therefore, we ask the teams to submit (where relevant):

#### Architecture
![](https://github.com/gongbell/MarkDownPics/blob/921ef6c480ad67fd84f5d6c940c276cb3bf681d7/WANA-Architecture.png)

#### Technologies
1. [Apache OpenWhisk](https://openwhisk.apache.org) is an open source, distributed Serverless platform that executes functions (fx) in response to events at any scale. OpenWhisk manages the infrastructure, servers and scaling using Docker containers. The OpenWhisk platform supports a programming model in which developers write functional logic (called Actions), in any supported programming language, that can be dynamically scheduled and run in response to associated events (via Triggers) from external sources (Feeds) or from HTTP requests
2. [Apache Kafka](https://kafka.apache.org) is an open-source distributed event streaming platform. Kafka works well as a replacement for a more traditional message broker. Message brokers are used for a variety of reasons (to decouple processing from data producers, to buffer unprocessed messages, etc.). In comparison to most messaging systems, Kafka has better throughput, built-in partitioning, replication, and fault-tolerance which makes it a good solution for large scale message processing applications.
3. Docker 
4. Substrate
5. Rust  
6. Go

#### Components
1. **Event Sources (Substrate Event Feed)**  
This is the source for events from the blockchain and the events will be sent to the OpenWhisk system. The MVP event source will be implemented using [polkadot-js/api](https://github.com/polkadot-js/api). For the actual implementation, we will be using a custom version of the substrate archive. Substrate archive will be indexing the blocks and an event actor will be decoding the events and data from the block and posting them to the OpenWhisk system. The indexer will use a NoSQL database to store the last indexed block and metadata. Using the substrate indexer as our event source will help us in solving many challenges ranging from fault tolerance to scalability to uniquely identifying multiple events with the same payload within a single block. The indexer will be connected to a substrate-based chain and RocksDB where the chain data is stored. When a new block is produced by the chain, the indexer collects the raw blocks from the RocksDB and sends it to the event actor. The event actor processes the block. The block data is decoded using the parity scale codec and mapped with the data types provided to the runtime. Once the event is decoded, the event actor posts a trigger to the event trigger manager.  

2. **Event Trigger Manager**  
Event trigger manager is composed of multiple actions including using a database to store trigger URLs and their respective auths, and Kafka provider, consumer and producer. Once the event manager receives an event from the event feed, this data is produced to a topic. The feed action in the trigger manager lets the user hook into the system. That is,  once an event is indexed to a particular topic, it can invoke a particular action. While creating the workflow, users can choose the event trigger as feed and provide necessary parameters from which chain it should be listening to and from which block it should start listening. Under the hood, a feed action is invoked with create lifecycle, which accepts the mandatory parameters the lifecycle, auth, trigger name, and other optional parameters of the event source. The feed action invokes the related actions of creating the entry in the database, adding to the Kafka consumer group, etc. The next component in the event trigger manager is a persistent connection to Kafka where it is used to produce and consume the stream of data. Once data is received in Kafka, the event trigger manager invokes the action to check the consumer groups for that particular topic and if found any, the trigger for the users under that particular group is invoked, which in turn invokes the workflow action.  

3. **Workflow Composer**  
Workflow composer consists of an async Rust library to compose multiple triggers, deployment configuration generator, and whisk deployment tool. For creating the workflow, the only input is the configuration file which is a YAML file. The workflow composition is laid out in the YAML which in turn takes care of the deployment and composing the triggers. Once a workflow is deployed to a namespace it creates a specific topic unique workflow id in Kafka. Workflow configuration comprises the input URL of workflow tasks, primarily GitHub repo, the sequence of processing tasks, and argument structure. Arguments must match the task input parameters.  

4. **Web API Gateway and Backend Service**   
This is the end user facing component to utilize the workflow. This component comprises of a backend application which is responsible for user registration, selecting the workflow, managing / creating workflow using friendly APIs, providing input parameters. API gateway / Machine gateway where the external world can connect to the Aurras system.  


* Mockups/designs of any UI components
* Data models / API specifications of the core functionality
* An overview of the technology stack to be used
* Documentation of core components, protocols, architecture, etc. to be deployed
* PoC/MVP or other relevant prior work or research on the topic
* What your project is _not_ or will _not_ provide or implement
  * This is a place for you to manage expectations and to clarify any limitations that might not be obvious

### Ecosystem Fit

* Where and how does your project fit into the ecosystem?
  * The project can help automatically verify the parachains in the ecosystem. It can detect bugs and vulnerabilities within the parachains.
* Who is your target audience (parachain/dapp/wallet/UI developers, designers, your own user base, some dapp's userbase, yourself)?
  * The target audience of the project can be either parachain developers or parachain auditors. With our tool, the parachain developers can verify the parachain implementations for potential bugs or vulnerabilities. And the parachain auditors can use the tool to help audit the parachains for bugs and vulnerabilities before launching them on Polkadot.
* What need(s) does your project meet?
  * Our projects address the parachain bug and vulnerability detection problem.
* Are there any other projects similar to yours in the Substrate / Polkadot / Kusama ecosystem?
  * I am not aware of any projects working on parachain security verification. Because Polkadot is first ecosystem that realize a uniform parachain development model. There are indeed projects on smart contract verification, such as Oyente (https://github.com/enzymefinance/oyente) and MAIAN (https://github.com/ivicanikolicsg/MAIAN). 

## Team :busts_in_silhouette:

### Team members

* Bo Jiang (team leader)
* Yifei Chen (team member)
* Bingqing Xue (team member) 

### Contact

* **Contact Name:** Bo Jiang
* **Contact Email:** Contact email (e.g. gongbell@gmail.com)
* **Website:** http://jiangbo.buaa.edu.cn

### Legal Structure

* **Registered Address:** No. 37 Xueyuan Road, Haidian District, Beijing, Chian
* **Registered Legal Entity:** Beihang University

### Team's experience

Please describe the team's relevant experience. If your project involves development work, we would appreciate it if you singled out a few interesting projects or contributions made by team members in the past. For research-related grants, references to past publications and projects in a related domain are helpful.

If anyone on your team has applied for a grant at the Web3 Foundation previously, please list the name of the project and legal entity here.

### Team Code Repos

* https://github.com/gongbell/ContractFuzzer
* https://github.com/gongbell/EOSFuzzer
* https://github.com/gongbell/WANA
* https://github.com/gongbell/Artemis

### Team LinkedIn Profiles

* https://www.linkedin.com/in/bo-jiang-b0951614

## Development Status :open_book:

If you've already started implementing your project or it is part of a larger repository, please provide a link and a description of the code here. In any case, please provide some documentation on the research and other work you have conducted before applying. This could be:

* links to improvement proposals or [RFPs](https://github.com/w3f/General-Grants-Program/tree/master/rfp-proposal) (requests for proposal),
* academic publications relevant to the problem,
* links to your research diary, blog posts, articles, forum discussions or open GitHub issues,
* references to conversations you might have had related to this project with anyone from the Web3 Foundation,
* previous interface iterations, such as mock-ups and wireframes.

## Development Roadmap :nut_and_bolt:

This section should break the development roadmap down into milestones and deliverables. Since these will be part of the agreement, it helps to describe _the functionality we should expect in as much detail as possible_, plus how we can verify and test that functionality. Whenever milestones are delivered, we refer to this document to ensure that everything has been delivered as expected.

Below we provide an **example roadmap**. In the descriptions, it should be clear how your project is related to Substrate, Kusama or Polkadot. We _recommend_ that the scope of the work can fit within a three-month period and that teams structure their roadmap as 1 milestone ≈ 1 month.

For each milestone,

* make sure to include a specification of your software. _Treat it as a contract_; the level of detail must be enough to later verify that the software meets the specification.
To assist you in defining it, we have created a document with examples for some grant categories [here](../src/grant_guidelines_per_category.md).
* include the amount of funding requested _per milestone_.
* include documentation (tutorials, API specifications, architecture diagrams, whatever is appropriate) in each milestone. This ensures that the code can be widely used by the community.
* provide a test suite, comprising unit and integration tests, along with a guide on how to set up and run them.
* commit to providing Dockerfiles for the delivery of your project.
* indicate milestone duration as well as number of full-time employees working on each milestone, and include the approximate number of days along with the cost per day.
* _Deliverables 0a-0d are mandatory_ and shall not be removed, unless you explicitly specify a reason within the PR's `Additional Notes` section (e.g. Milestone X is research oriented and as such there is no code to test).

> :zap: If any of your deliverables is based on somebody else's work, make sure you work and publish _under the terms of the license_ of the respective project and that you **highlight this fact in your milestone documentation** and in the source code if applicable! **Teams that submit others' work without attributing it will be immediately terminated.**

### Overview

* **Total Estimated Duration:** Duration of the whole project (e.g. 2 months)
* **Full-Time Equivalent (FTE):**  Required workload of a full-time employee for the whole project (see [Wikipedia](https://en.wikipedia.org/wiki/Full-time_equivalent)) (e.g. 2 FTE)
* **Total Costs:** Amount of payment in USD for the whole project. The total amount of funding _needs to be below $30k for initial grants_ and $100k for follow-up grants. (e.g. 12,000 USD)

### Milestone 1 Example — Implement Substrate Modules

* **Estimated Duration:** 1 month
* **FTE:**  2
* **Costs:** 8,000 USD

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| 0a. | License | Apache 2.0 / MIT / Unlicense |
| 0b. | Documentation | We will provide both inline documentation of the code and a basic tutorial that explains how a user can (for example) spin up one of our Substrate nodes. Once the node is up, it will be possible to send test transactions that will show how the new functionality works. |
| 0c. | Testing Guide | Core functions will be fully covered by unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| 0d. | Article/Tutorial | We will publish an article/tutorial/workshop that explains [...] (what was done/achieved as part of the grant). (Content, language and medium should reflect your target audience described above.)
| 1. | Substrate module: X | We will create a Substrate module that will... (Please list the functionality that will be coded for the first milestone) |  
| 2. | Substrate module: Y | We will create a Substrate module that will... |  
| 3. | Substrate module: Z | We will create a Substrate module that will... |  
| 4. | Substrate chain | Modules X, Y & Z of our custom chain will interact in such a way... (Please describe the deliverable here as detailed as possible) |  
| 5. | Docker | We will provide a dockerfile to demonstrate the full functionality of our chain |


### Milestone 2 Example — Additional features

* **Estimated Duration:** 1 month
* **FTE:**  1
* **Costs:** 4,000 USD

...


## Future Plans

Please include here

* how you intend to use, enhance, promote and support your project in the short term, and
* the team's long-term plans and intentions in relation to it.


## Additional Information :heavy_plus_sign:

**How did you hear about the Grants Program?** Web3 Foundation Website / Medium / Twitter / Element / Announcement by another team / personal recommendation / etc.

Here you can also add any additional information that you think is relevant to this application but isn't part of it already, such as:

* Work you have already done.
* Wheter there are any other teams who have already contributed (financially) to the project.
* Previous grants you may have applied for.
