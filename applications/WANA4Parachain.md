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


#### Architecture

The architecture of WANA4Parachain is shown in the figure below. First, the Polkadot parachain (or smart contracts) source code can be compiled into Wasm bytecode, which is then loaded and initialized within the Wasm symbolic execution engine. Note that the deployed parachain Wasm bytecode can be directly loaded into WANA4Parachain for analysis as long as the entrance function is specified. Second, the symbolic execution engine starts to traverse the paths of the Wasm code with symbolic inputs. During symbolic execution, the symbolic execution engine will invoke the Z3 constraint solver to check and prune unsatisfiable paths along the way. Meanwhile, the symbolic execution engine will also consult the library simulator when the Wasm bytecode is interacting with external functions. Then, the execution information useful for vulnerability analysis are collected during symbolic execution. Third, WANA4Parachain will perform bug or vulnerability analysis based on the execution information collected during the symbolic execution process. When a bug is detected, the corresponding report will be generated. Note that the step 2 and step 3 are iterative process, where the bug/vulnerability analysis and symbolic execution will interleave with each other. The whole workflow ends when all the code has been traversed.

![](https://github.com/gongbell/MarkDownPics/blob/921ef6c480ad67fd84f5d6c940c276cb3bf681d7/WANA-Architecture.png)


#### Components
1. **The Wasm Symbolic Execution Engine** 

    In general, the Symbolic Execution engine of WANA4Parachain is reused from our previous research work WANA (https://github.com/gongbell/WANA). The Symbolic Execution engine is implemented as a new virtual machine that can consume both concrete and symbolic inputs according to WASM specification. Wasm supports 4 data types, including two integer data types and two floating-point data types under IEEE 754 standard. The  symbolic execution engine supports all of them for analysis. 
    
    WebAssembly programs are organized into modules, which are the units of deployment, loading, and compilation. The parachain source code are also compiled into Wasm modules for deployment. During the loading and initialization phase, the symbolic execution engine will prepare the memories, tables, global variables and a stack as the execution environment for executing the Wasm instructions within a module. For a module, the exports component defines a set of exported functions and data structures that become accessible to the host environment once the module has been instantiated. In other words, the exported functions serve as the public interfaces for a Wasm module. Therefore, the WANA4Parachain framework will start the symbolic execution by iterating each exported function for different parachains. 
    
    For each Wasm function invocation within the instructions in a module, WANA4Parachain will first prepare a frame as its execution context, which includes arguments, local variables, return values, and references to its module. Then WANA4Parachain will start symbolically executing the instructions within the code section of the function sequentially. The Wasm instructions mainly include the numeric instructions, memory instructions, control instructions, and function call instructions. WANA4Parachain has realized 171 out of the 185 instructions in WebAssembly specification version 1.0. 


2. **Handling Library Functions**  

    In Wasm, there are two types of function calls: direct function calls and indirect function calls. For indirect function calls, Wasm VM has to first get an index from the top of the stack. Then the VM will use the index to get the real function address from a function table. For the functions in the current Wasm module, WANA4Parachain will directly step into the functions to continue symbolic execution. For the library functions of parachain platform, WANA4Parachain will emulate their behaviors in terms of their impact on symbolic execution. 

    This is where WANA4Parachain will extend WANA for parachain libraries. For output related functions, they have no impact on the control flow or data flow of symbolic execution. Therefore, WANA4Parachain will just pop-up their arguments from the stack and continue execution. For input related functions, returned the values from these input-related functions may impact execution path. WANA4Parachain will return new symbolic values from them to continue symbolic execution. For memory or variable manipulation functions, WANA4Parachain will perform symbolic execution on them to generate function summaries and reuse the summaries for compositional symbolic execution.

3. **Bug Detection Algorithm**  

    The current projects mainly aims at detecting crash bugs and exceptions in parachain code. This is also where WANA4Parachain will extend WANA to detect bugs related to parachain. WANA4Parachain will incorporate the exception/crash reporting mechaism of parachain code into the symbolic execution engine. When exception or crash happens during symbolic execution, WANA4Parachain will report it accordingly. 

4. **CLI interface**   

    This is the end user interface to use the tool. We will package the WANA4Parachain tool as a CLI interface for use with parachain source code projects or parachain bytecode. 

* PoC/MVP or other relevant prior work or research on the topic
  * The WANA project is our proof of work project on smart contract vulnerability analysis based on the same Wasm symbolic execution engine.
* What your project is _not_ or will _not_ provide or implement
  * At the current stage of polkadot/substrate ecosystem, the security vulnerabilities of the parachain code or polkadot smart contracts are still at their infancy. It is hard to analyze their features for security analysis right now. We expect the security vulnerabilities with parachain code or polkadot smart contracts will increase with the rapid growth of polkadot ecosystem. We may support the security vulnerabilitiy detection of parachain code or polkadot smart contracts in follow-up grants applications. 

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
