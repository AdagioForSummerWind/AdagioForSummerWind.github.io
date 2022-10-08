# SEK_base_07

>2022春JOANNA老师的软件体系结构

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [topics](#topics)
- [1.Introduction to Software Architecture](#1introduction-to-software-architecture)
  - [Why is Software Architecture Important?](#why-is-software-architecture-important)
  - [The Many Contexts of Software Architecture](#the-many-contexts-of-software-architecture)
- [2.Architecture modelling and representation: Architectural structures and views](#2architecture-modelling-and-representation-architectural-structures-and-views)
- [3.Quality attributes	:Understanding quality attributes and availability](#3quality-attributesunderstanding-quality-attributes-and-availability)
  - [Availability](#availability)
- [Quality attributes: interoperability and modifiability](#quality-attributes-interoperability-and-modifiability)
  - [Interoperability](#interoperability)
  - [Quality attributes: Modifiability](#quality-attributes-modifiability)
- [Quality attributes: Performance, Security](#quality-attributes-performance-security)
  - [Quality attributes: Security](#quality-attributes-security)
- [Patterns and Tactics](#patterns-and-tactics)
- [Quality Attribute Modelling and Analysis](#quality-attribute-modelling-and-analysis)
- [Designing for architecturally significant requirements](#designing-for-architecturally-significant-requirements)
- [Designing and evaluating an architecture](#designing-and-evaluating-an-architecture)
- [Exercise 1 Capturing ASR in practice .](#exercise-1-capturing-asr-in-practice)
- [exercise2](#exercise2)
- [exercise3](#exercise3)

<!-- /code_chunk_output -->



10%+30%+60%

## topics
1. Introduction to the subject: objectives, plan, assessment.Introduction to software architecture: concept and importance of software architecture 
2. Architecture modelling and representation: architectural structures and views, UML
3. Quality attributes: Understanding quality attributes and availability
4. Quality attributes: interoperability and modifiability
Quality attributes: performance and security
Quality attributes: testability, usability and other quality attributes

Achieving quality attribute through tactics and patterns: architectural tactics and patterns
Achieving quality attribute through tactics and patterns: architectural tactics and patterns

Achieving quality attribute through tactics and patterns: quality attribute modelling and analysis
Achieving quality attribute through tactics and patterns: designing for architecturally significant requirements

Designing and evaluating an architecture: TOGAF, ADD and ATAM
Architecture reuse: software product  lines, frameworks and middleware

Capturing ASR in practice (practical exercises)
Designing an architecture in practice (practical exercises)
Documenting software architecture in practice (practical exercises)
Architecture evaluation in practice (practical exercises)
## 1.Introduction to Software Architecture
The software architecture of a system is **the set of structures needed to reason about the system, which comprise software elements, relations among them, and properties of both.**

We can say that architecture is an abstraction.

**What does it mean that architecture is an abstraction?**
- **If the information about element is no useful for reasoning about the system, architecture will omit it.**
- **Architecture shows the system in an abstract way - in terms of its elements and their relationships.**

The architects design structures – make decisions on what architectural elements should be used in a system to solve the problems that the system will face. 
**Some compositions of architectural elements that solve particular problems may be used again in different systems facing similar problems.**

**If a certain composition of architectural elements that solves a  particular problem was found useful over time and over many domains, has been documented and disseminated, it may become a/an….**
- **Architectural pattern**


Architecture Is a Set of Software Structures :
- A structure is a set of elements held together by a relation. 
- Software systems are composed of many structures, and no single structure holds claim to being the  architecture.
- There are three important categories of architectural structures.
    - Module
    - Component and Connector
    - Allocation

Architecture vs Design
- The architecture - the selection of architectural elements, their interaction and the restrictions on those elements and their interactions.
- The design - modularization and the detailed interfaces of the elements of the system, their algorithms and procedures, and the kinds of data needed to support the architecture and satisfy the requirements.

Architecture is an Abstraction
- An architecture specifically omits certain information about elements that is not useful for reasoning about the system.
- The architectural abstraction lets us look at the system in terms of its elements, how they are arranged, how they interact, how they are composed, and so forth. 
- This abstraction is essential to taming the complexity of an architecture.

Every System has a Software Architecture 
- But the architecture may not be known to anyone. 
    - Perhaps all of the people who designed the system are long gone
    - Perhaps the documentation has vanished (or was never produced)
    - Perhaps the source code has been lost (or was never delivered)
- An architecture can exist independently of its description or specification

Structures and Views
- A view is a representation of a coherent set of architectural elements, as written by and read by system stakeholders. 
- A structure is the set of elements itself, as they exist in software or hardware.
- In short, a view is a representation of a structure. 
    - For example, a module structure is the set of the system’s modules and their organization. 
    - A module view is the representation of that structure, documented according to a template in a chosen notation, and used by some system stakeholders.
- Architects design structures. They document views of those structures.
- **Architects design structures.**
- **Architects document views of those structures.**

**What is a view?**
- **a representation of a coherent set of architectural elements**
- **is written by the architects**
- **may be read by the testers or the project clients**




Architectural Patterns
- Architectural elements can be composed in ways that solve particular problems. 
    - The compositions have been found useful over time, and over many different domains
    - They have been documented and disseminated. 
    - These compositions of architectural elements, called architectural patterns.
    - Patterns provide packaged strategies for solving some of the problems facing a system.
- An architectural pattern delineates the element types and their forms of interaction used in solving the problem.

What Makes a “Good” Architecture?
- There is no such thing as an inherently good or bad architecture. 
- Architectures can be evaluated but only **in the context of specific stated goals.**
- Architectures are either more or less **fit for some purpose**
- There are, however, good rules of thumb.

Process “Rules of Thumb”
- The architecture should be the product of a single architect or a small group of architects with an identified technical leader. 
- The architect (or architecture team) should base the architecture on a prioritized list of well-specified quality attribute requirements. 
- The architecture should be documented using views. 
- The architecture should be evaluated for its ability to deliver the system’s important quality attributes. 
- The architecture should lend itself to incremental implementation.

Structural “Rules of Thumb”
- The architecture should feature well-defined modules 
-The architecture should never depend on a particular version of a commercial product or tool
- Modules that produce data should be separate from modules that consume data. 
    - This tends to increase modifiability 
- Don’t expect a one-to-one correspondence between modules and components. 
- Every process should be written so that its assignment to a specific processor can be easily changed, perhaps even at runtime.
- The architecture should feature a small number of ways for components to interact. 
    - The system should do the same things in the same way throughout. 

### Why is Software Architecture Important?

Inhibiting or Enabling System’s Quality Attributes
- Whether a system will be able to exhibit its desired (or required) quality attributes is substantially determined by its architecture.
    - Performance
    - Modifiability
    - Security
    - Scalability
    - Reusability

Reasoning About and Managing Change
- About 80 percent of a typical software system’s total cost occurs after initial deployment
    - accommodate new features
    - adapt to new environments,
    - fix bugs, and so forth. 
- Every architecture partitions possible changes into three categories
    - A local change can be accomplished by modifying a single element. 
    - A nonlocal change requires multiple element modifications but leaves the underlying architectural approach intact. 
    - An architectural change affects the fundamental ways in which the elements interact with each other and will require changes all over the system. 
- Obviously, local changes are the most desirable
- A good architecture is one in which the most common changes are local, and hence easy to make.


**Which kind of a change in the system is most desirable?**(1 answer)
- A local change that can be accomplished by modifying a single element. 
- A nonlocal change that requires multiple element modifications but leaves the underlying architectural approach intact. 
- An architectural change that affects the fundamental ways in which the elements interact with each other and will require changes all over the system. 

Predicting System Qualities
- When we examine an architecture we can confidently predict that the architecture will exhibit the associated qualities.
- The earlier you can find a problem in your design, the cheaper, easier, and less disruptive it will be to fix.

Enhancing Communication Among Stakeholders
- The architecture—or at least parts of it—is sufficiently abstract that most nontechnical people can understand it.
- Most of the system’s stakeholders can use as a basis for creating mutual understanding, negotiating, forming consensus, and communicating with each other.
- Each stakeholder of a software system is concerned with different characteristics of the system
    - Users, client, manager, architect

Earliest Design Decisions
- Software architecture is a manifestation of the earliest design decisions about a system.
- These early decisions affect the system’s remaining development, its deployment, and its maintenance life. 

Earliest Design Decisions
- What are these early design decisions?
    - Will the system run on one processor or be distributed across multiple processors?
    - Will the software be layered? If so, how many layers will there be? What will each one do?
    - Will components communicate synchronously or asynchronously? 
    What communication protocol will we choose?
    - Will the system depend on specific features of the operating system or hardware?
    - Will the information that flows through the system be encrypted or not?

Defining Constraints on an Implementation
- An implementation exhibits an architecture if it conforms to the design decisions prescribed by the architecture. 
    - The implementation must be implemented as the set of prescribed elements
    - These elements must interact with each other in the prescribed fashion
- Each of these prescriptions is a constraint on the implementer.

Influencing the Organizational Structure
- Architecture prescribes the structure of the system being developed.
- The architecture is typically used as the basis for the work-breakdown structure. 
- The work-breakdown structure in turn dictates 
    - units of planning, scheduling, and budget
    - interteam communication channels
    - configuration and file-system organization
    - integration and test plans and procedures; 
    - the maintenance activity

Enabling Evolutionary Prototyping
- Once an architecture has been defined, it can be prototyped as a skeletal system. 
   - A skeletal system is one in which at least some of the infrastructure is built before much of the system’s functionality has been created. 
- The fidelity of the system increases as prototype parts are replaced with complete versions of these parts. 
- This approach aids the development process because the system is executable early in the product’s life cycle. 
- This approach allows potential performance problems to be identified early in the product’s life cycle.
- These benefits reduce the potential risk in the project. 

Improving Cost and Schedule Estimates
- Architecture is used to help the project manager create cost and schedule estimates early in the project life cycle. 
Top-down estimates are useful for setting goals and apportioning budgets.
- Bottom-up understanding of the system’s pieces are typically more accurate than those that are based purely on top-down system knowledge.
- The best cost and schedule estimates will typically emerge from a consensus between the top-down estimates (created by the architect and project manager) and the bottom-up estimates (created by the developers). 

Transferable, Reusable Model
- Reuse of architectures provides tremendous benefits for systems with similar requirements. 
    - Not only can code be reused, but so can the requirements that led to the architecture in the first place
    - When architectural decisions can be reused across multiple systems, all of the early-decision consequences are also transferred

Using Independently Developed Components
- Architecture-based development often focuses on components that are likely to have been developed separately, even independently
- Commercial off-the-shelf components, open source software, publicly available apps, and networked services are example of interchangeable software components.
- The payoff can be
    - Decreased time to market
    - Increased reliability
    - Lower cost 
    - Flexibility (if the component you want to buy is not terribly special purpose, it’s likely to be available from several sources)

**Do you think that from the architect point of view it is better to have less design choices (choices of elements and their interactions) or more?**(1 answer)
- Less is always better
- I want more choices

Restricting Design Vocabulary
- As useful architectural patterns are collected, we see the benefit in restricting ourselves to a relatively small number of choices of elements and their interactions. 
    - We minimize the design complexity of the system we are building.
    - Enhanced reuse
    - More regular and simpler designs that are more easily understood and communicated
    - More capable analysis
    - Shorter selection time
    - Greater interoperability.

Basis for Training
- The architecture can serve as the first introduction to the system for new project members. 
- Module views show someone the structure of a project
    - Who does what, which teams are assigned to which parts of the system, and so forth. 
- Component-and-connector explains how the system is expected to work and accomplish its job.

### The Many Contexts of Software Architecture

Contexts of Software Architecture
- We put software architecture in its place relative to four contexts:
    - Technical.  What technical role does the software architecture play in the system?
    - Project life cycle.  How does a software architecture relate to the other phases of a software development life cycle?
    - Business.  How does the presence of a software architecture affect an organization’s business environment?
    - Professional.  What is the role of a software architect in an organization or a development project?

Technical Context
- The most important technical context factor is the set of quality attributes that the architecture can help to achieve.
- The architecture’s current technical environment is also an important factor.
    - Standard industry practices 
    - Software engineering techniques prevalent in the architect’s professional community.

Project Life-cycle Context
- Software development processes are standard approaches for developing software systems. 
- They tell the members of the team what to do next.
- There are four dominant software development processes:
    - Waterfall 
    - Iterative
    - Agile 
    - Model-driven development 

Architecture Activities
- Architecture is a special kind of design, so architecture finds a home in each process of software development.
- There are activities involved in creating a software architecture, using it to realize a complete design, and then implementing 
    - Understanding the architecturally significant requirements
    - Creating or selecting the architecture
    - Documenting and communicating the architecture
    - Analyzing or evaluating the architecture
    - Implementing and testing the system based on the architecture
    - Ensuring that the implementation conforms to the architecture

Business Context
- Systems are created to satisfy the business goals of one or more organizations.
    - Development organizations: e.g., make a profit, or capture market, or help their customers do their jobs better, or keep their staff employed, or make their stockholders happy
    - Customers have their own goals: e.g. ,make their lives easier or more productive. 
    - Other organizations, such as subcontractors or government regulatory agencies, have their own goals
- Architects need to understand the goals. 
- Many of these goals will influence the architecture.

Architecture and business goals:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507102821.png)

Professional Context
- Architects need more than just technical skills. 
    - Architects need diplomatic, negotiation, and communication skills.
    - Architects need the ability to communicate ideas clearly 
- Architects need up-to-date knowledge. 
    - Know about (for example) patterns, or database platforms, or web services standards.
    - Know about business considerations. 

Stakeholders
- A stakeholder is anyone who has a stake in the success of the system
- Stakeholders typically have different specific concerns on the system
- Early engagement of stakeholders to understand the constraints of the task, manage expectations, negotiate priorities, and make tradeoffs. 

How is Architecture Influenced?
- Technical requirements
- Architects
- Business, social, and technical environment
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507103025.png)

What Do Architectures Influence?
- Technical context
    - The architecture can affect stakeholder’s requirements for the next system 
    - A customer may relax some of their requirements to gain these economies. 
    - Shrinkwrapped software has affected people’s requirements, as it is inexpensive and of high quality.
- Project context
    - The architecture affects the structure of the developing organization. 
    - An architecture prescribes the units of software to be implemented and integrated to the system. 
    - These units are the basis for the development project’s structure. 
    - the development, test, and integration activities all revolve around the units. 
- Business context
    - The architecture can affect the business goals of the developing organization. 
    - The architecture can provide opportunities for the efficient production and deployment of similar systems, and the organization may adjust its goals to take advantage of its newfound expertise. 
- Professional context
    - The process of system building will affect the architect’s experience
    - A system that was successfully built will make the architect more inclined to build systems using the same approach in the future. 
    - Architectures that fail are less likely to be chosen for future projects.

Summary:
- The software architecture of a system is the set of structures needed to reason about the system, which comprise software elements, relations among them, and properties of both.
- A structure is a set of elements and the relations among them.
- A view is a representation of a coherent set of architectural elements. A view is a representation of one or more structures.
- An architecture has an impact on the architect, the organization, and, potentially, the industry

## 2.Architecture modelling and representation: Architectural structures and views		

Intended Learning Outcomes
- By the end of this lesson you will be able to:
    - differentiate between structures and views 	
    - list useful structures and views and understand how they relate to each other 	
    - choose the relevant structure and views 	
    - combine views 	
    - use UML to express the views	

Architecture Is a Set of Software Structures 
- **A structure is a set of elements held together by a relation.**
- Software systems are composed of many structures, and no single structure holds claim to being the  architecture.
- There are **three important categories** of architectural structures.
    - Module
    - Component and Connector
    - Allocation

Module Structures
- Some structures partition systems into **implementation units**, which we call modules. 
- Modules are assigned specific computational responsibilities, and are the basis of **work assignments for programming teams.** 
- In large projects, these elements (modules) are subdivided for assignment to sub-teams.

Component-and-connector Structures
- Other structures focus on the way the elements interact with each other at runtime to carry out the system’s functions.
- We call runtime structures component-and-connector (C&C) structures.
- In our use, a component is always a runtime entity.
    - In SOA,  the system is to be built as a set of services. 
    - These services are made up of (compiled from) the programs in the various implementation units – modules.

Allocation Structures
- Allocation structures describe the mapping from software structures to the system’s environments
- For example
    - Modules are assigned to teams to develop, and assigned to places in a file structure for implementation, integration, and testing. 
    - Components are deployed onto hardware in order to execute. 

Which Structures are Architectural?
- A structure is architectural if it supports reasoning about the system and the system’s properties. 
- The reasoning should be about an attribute of the system that is important to some stakeholder. 
- These include 
    - functionality achieved by the system
    - the availability of the system in the face of faults
    - the difficulty of making specific changes to the system
    - the responsiveness of the system to user requests, 
    - many others.

**Programming team developing our cafeteria system wants to know what units of implementation will be assigned with functional responsibilities. Which structures can help reason about that?**
- **Module structures**

Structures and Views
- A structure is the set of elements itself, as they exist in software or hardware.
- In short, a view is a representation of a structure. 
- Architects design structures. They document views of those structures.

Physiological Structures
- The neurologist, the orthopedist, the hematologist, and the dermatologist all have different views of the structure of a human body. 
- Ophthalmologists, cardiologists, and podiatrists concentrate on specific subsystems. 
- The kinesiologist and psychiatrist are concerned with different aspects of the entire arrangement’s behavior. 
- Although these views are pictured differently and have different properties, all are inherently related, interconnected.
- Together they describe the architecture of the human body.
- So it is with software! 

Module Structures
- Module structures embody decisions as to how the system is to be structured as a set of code or data units
- In any module structure, the elements are modules of some kind (perhaps classes, or layers, or merely divisions of functionality, all of which are units of implementation). 
- Modules are assigned areas of functional responsibility. 

Component-and-connector Structures
- Component-and-connector structures embody decisions as to how the system is to be structured as a set of elements that have runtime behavior (components) and interactions (connectors). 
- Elements are runtime components such as services, peers, clients, servers, or many other types of runtime element) 
- Connectors are the communication vehicles among components, such as call-return, process synchronization operators, pipes, or others. 
- Component-and-connector views help us answer questions such as these:
    - What are the major executing components and how do they interact at runtime?
    - What are the major shared data stores?
    - Which parts of the system are replicated?
    - How does data progress through the system?
    - What parts of the system can run in parallel?

Allocation structures
- Allocation structures show the relationship between the software elements and elements in one or more external environments in which the software is created and executed. 
- Allocation views help us answer questions such as these:
    - What processor does each software element execute on?
    - In what directories or files is each element stored during development, testing, and system building?
    - What is the assignment of each software element to development teams?

Structures Provide Insight
- Each structure provides a perspective for reasoning about some of the relevant quality attributes.
- For example:
    - The module structure, which embodies what modules use what other modules, is strongly tied to the ease with which a system can be extended.
    - The concurrency structure, which embodies parallelism within the system, is strongly tied to the ease with which a system can be made free of deadlock and performance bottlenecks.
    - The deployment structure is strongly tied to the achievement of performance, availability, and security goals.

Some Useful Module Structures
- Decomposition structure
    - The units are modules that are related to each other by the is-a-submodule-of relation.
    - It shows how modules are decomposed into smaller modules recursively until the modules are small enough to be easily understood. 
    - Modules often have products (such as interface specifications, code, test plans, etc.) associated with them. 
    - The decomposition structure determines, to a large degree, the system’s modifiability, by assuring that likely changes are localized. 
- Uses structure. 
    - The units here are also modules, perhaps classes. 
    - The units are related by the uses relation, a specialized form of dependency. 
    - A unit of software uses another if the correctness of the first requires the presence of a correctly functioning version (as opposed to a stub) of the second.
    - The ability to easily create a subset of a system allows for incremental development.
- Layer structure
    - The modules in this structure are called layers. 
    - A layer is an abstract “virtual machine” that provides a cohesive set of services through a managed interface. 
    - Layers are allowed to use other layers in a strictly managed fashion.
        - In strictly layered systems, a layer is only allowed to use a single other layer. 
    - This structure is imbues a system with portability, the ability to change the underlying computing platform.
- Class (or generalization) structure
    - The module units in this structure are called classes.
    - The relation is inherits from or is an instance of.
    - Inheritance is a mechanism for code reuse and to allow independent extensions of the original software  
    - The class structure allows one to reason about reuse and the incremental addition of functionality. 
- Data model structure
    - The data model describes the static information structure in terms of data entities and their relationships 
        - For example, in a banking system, entities will typically include Account, Customer, and Loan.
        - Account has several attributes, such as account number, type (savings or checking), status, and current balance. 



Decomposition structure: an example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507104737.png)

Uses structure: an example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507104844.png)

Layer structure: an example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507105004.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507112920.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507112943.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507113004.png)

Some Useful C&C Structures
- Service structure
    - The units are services that interoperate with each other by service coordination mechanisms such as SOAP
    - The service structure helps to engineer a system composed of components that may have been developed anonymously and independently of each other.
- Concurrency structure
    - This structure helps determine opportunities for parallelism and the locations where resource contention may occur. 
    - The units are components
    - The connectors are their communication mechanisms. 
    - The components are arranged into logical threads.

Some Useful Allocation Structures
- Deployment structure
    - The deployment structure shows how software is assigned to hardware processing and communication elements. 
    - The elements are software elements (usually a process from a C&C view), hardware entities (processors), and communication pathways. 
    - Relations are allocated-to, showing on which physical units the software elements reside, and migrates-to if the allocation is dynamic. 
    - This structure can be used to reason about performance, data integrity, security, and availability. 
    - It is of particular interest in distributed and parallel systems.
- Implementation structure 
    - This structure shows how software elements (usually modules) are mapped to the file structure(s) in the system’s development, integration, or configuration control environments. 
    ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507105444.png)
- Work assignment structure
    - This structure assigns responsibility for implementing and integrating the modules to the teams who will carry it out. 

Relating Structures to Each Other
- Elements of one structure will be related to elements of other structures, and we need to reason about these relations. 
    - A module in a decomposition structure may be manifested as one, part of one, or several components in one of the component-and-connector structures. 
- In general, mappings between structures are many to many.

Structural “Rules of Thumb”
- **The architecture should feature well-defined modules**
- The architecture should never depend on a particular version of a commercial product or tool
- Modules that produce data should be separate from modules that consume data. 
    - This tends to increase modifiability 

Well-defined modules
- Functional responsibilities are assigned on the principle of information hiding and separation of concerns
    - Information-hiding modules should encapsulate things likely to change, thus insulating the software from the effects of those changes
    - Each module should have a well-defined interface that encapsulates or “hides” the changeable aspects from other aspects that uses their facilities
        - Interfaces should allow their respective development teams to work largely independently of each other
- Well-defined specifications for modules and their interfaces that then allow the modules to be developed in parallel and relatively independently

Architecture Documentation
- Even the best architecture will be useless if the people who need it 
    - do not know what it is; 
    - cannot understand it well enough to use, build, or modify it; 
    - misunderstand it and apply it incorrectly. 
- All of the effort, analysis, hard work, and insightful design on the part of the architecture team will have been wasted. 

Three Uses for Architecture Documentation
- Education
    - Introducing people to the system 
        - New members of the team
        - External analysts or evaluators
        - New architect
- Primary vehicle for communication among stakeholders
    - Especially architect to developers
    - Especially architect to future architect!
- Basis for system analysis and construction 
    - Architecture tells implementers what to implement. 
    - Each module has interfaces that must be provided and uses interfaces from other modules. 
    - Documentation can serve as a receptacle for registering and - communicating unresolved issues.
    - Architecture documentation serves as the basis for architecture evaluation. 

Notations
- Informal notations
    - Views are depicted (often graphically) using general-purpose diagramming and editing tools
    - The semantics of the description are characterized in natural language
    - They cannot be formally analyzed
- Semiformal notations
    - Standardized notation that prescribes graphical elements and rules of construction
    - Lacks a complete semantic treatment of the meaning of those elements 
    - Rudimentary analysis can be applied 
    - UML is a semiformal notation in this sense.
- Formal notations 
    - Views are described in a notation that has a precise (usually mathematically based) semantics. 
    - Formal analysis of both syntax and semantics is possible. 
    - Architecture description languages (ADLs)
    - Support automation through associated tools.

Choosing a Notation
- Tradeoffs
    - Typically, more formal notations take more time and effort to create and understand, but offer reduced ambiguity and more opportunities for analysis. 
    - Conversely, more informal notations are easier to create, but they provide fewer guarantees. 
- Different notations are better (or worse) for expressing different kinds of information. 
    - UML class diagram will not help you reason about schedulability, nor will a sequence chart tell you very much about the system’s likelihood of being delivered on time.
    - Choose your notations and representation languages knowing the important issues you need to capture and reason about.

Views
- Principle of architecture documentation:
    - Documenting an architecture is a matter of documenting the relevant views and then adding documentation that applies to more than one view.

Overview of Module Views
- Elements 
    - Modules, which are implementation units of software that provide a coherent set of responsibilities. 
- Relations
    - Is part of, which defines a part/whole relationship between the submodule—the part—and the aggregate module—the whole.
    - Depends on, which defines a dependency relationship between two modules. Specific module views elaborate what dependency is meant.
    - Is a, which defines a generalization/specialization relationship between a more specific module—the child—and a more general module—the parent. 
- Constraints 
    - Different module views may impose specific topological constraints, such as limitations on the visibility between modules.
- Usage
    - Blueprint for construction of the code
    - Change-impact analysis
    - Planning incremental development
    - Requirements traceability analysis
    - Communicating the functionality of a system and the structure of its code base
    - Supporting the definition of work assignments, implementation schedules, and budget information
    - Showing the structure of information that the system needs to manage 

Module Views	
- It is unlikely that the documentation of any software architecture can be complete without at least one module view.

Overview of C&C Views
- Elements
    - Components. Principal processing units and data stores. A component has a set of ports through which it interacts with other components (via connectors).
    - Connectors. Pathways of interaction between components. Connectors have a set of roles (interfaces) that indicate how components may use a connector in interactions. 
- Relations
    - Attachments. Component ports are associated with connector roles to yield a graph of components and connectors.
    - Interface delegation. In some situations component ports are associated with one or more ports in an “internal” subarchitecture. The case is similar for the roles of a connector 
- Constraints
    - Components can only be attached to connectors, not directly to other components.
    - Connectors can only be attached to components, not directly to other connectors.
    - Attachments can only be made between compatible ports and roles.
    - Interface delegation can only be defined between two compatible ports (or two compatible roles).
    - Connectors cannot appear in isolation; a connector must be attached to a component.
- Usage
    - Show how the system works.
    - Guide development by specifying structure and behavior of runtime elements.
    - Help reason about runtime system quality attributes, such as performance and availability. 

Notations for C&C Views
- UML components are good match for C&C components.
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507110842.png)
- UML connectors are not rich enough to represent many C&C connectors.  
    - UML connectors cannot have substructure, attributes, or behavioral descriptions. 
- Represent a “simple” C&C connector using a UML connector—a line. 
    - Many commonly used C&C connectors have well-known, application-independent semantics and implementations, such as function calls or data read operations. 
    - You can use a stereotype to denote the type of connector. 
- Connector roles cannot be explicitly represented with a UML connector.
    - The UML connector element does not allow the inclusion of interfaces.
    - Label the connector ends and use these labels to identify role descriptions that must be documented elsewhere.
- Represent a “rich” C&C connector using a UML component, or by annotating a line UML connector with a tag that explains the meaning of the complex connector. 

Overview of Allocation Views
- Elements
    - Software element and environmental element. 
    - A software element has properties that are required of the environment. 
    - An environmental element has properties that are provided to the software.
- Relations
    - Allocated to. A software element is mapped (allocated to) an environmental element. Properties are dependent on the particular view.
- Constraints
    - Varies by view
- Usage
    - Reasoning about performance, availability, security, and safety. 
    - Reasoning about distributed development and allocation of work to teams. 
    - Reasoning about concurrent access to software versions. 
    - Reasoning about the form and mechanisms of system installation. 

Quality Views
- A quality view can be tailored for specific stakeholders or to address specific concerns. 
- A quality views is formed by extracting the relevant pieces of structural views and packaging them together. 


Quality Views:  Examples
- Security view 
    - Show the components that have some security role or responsibility, how those components communicate, any data repositories for security information, and repositories that are of security interest. 
    - The view’s context information would show other security measures (such as physical security) in the system’s environment. 
    - The behavior part of a security view
        - Show how the operation of security protocols and where and how humans interact with the security elements.
        - Capture how the system would respond to specific threats and vulnerabilities. 
- Communications view 
    - Especially helpful for systems that are globally dispersed and heterogeneous. 
    - Show all of the component-to-component channels, the various network channels, quality-of-service parameter values, and areas of concurrency. 
    - Used to analyze certain kinds of performance and reliability (such as deadlock or race condition detection). 
    - The behavior part of this view could show (for example) how network bandwidth is dynamically allocated.
- Exception or error-handling view 
    - Could help illuminate and draw attention to error reporting and resolution mechanisms. 
    - Show how components detect, report, and resolve faults or errors. 
    - It would help identify the sources of errors and appropriate corrective actions for each. 
- Reliability view 
    - Models mechanisms such as replication and switchover. 
    - Depicts timing issues and transaction integrity. 
- Performance view 
    - Shows those aspects of the architecture useful for inferring the system’s performance. 
    - Show network traffic models, maximum latencies for operations, and so forth.

Which Views?  The Ones You Need!
- Different views support different goals and uses.
- The views you should document depend on the uses you expect to make of the documentation. 
- Each view has a cost and a benefit; you should ensure that the benefits of maintaining a view outweigh its costs.

Choosing the Views
- You can determine which views are required, when to create them, and how much detail to include if you know the following:
    - What people, and with what skills, are available
    - Which standards you have to comply with
    - What budget is on hand
    - What the schedule is
    - What the information needs of the important stakeholders are
    - What the driving quality attribute requirements are
    - What the approximate size of the system is
- At a minimum, expect to have at least one module view, at least one C&C view, and for larger systems, at least one allocation view in your architecture document. 

Building the Documentation Package
- Documentation package consists of
    - Views
    - Documentation beyond views

View Template:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507111604.png)

Documenting Behavior
- Behavior documentation complements each views by describing how architecture elements in that view interact with each other. 
- Behavior documentation enables reasoning about 
    - a system’s potential to deadlock
    - a system’s ability to complete a task in the desired amount of time
    - maximum memory consumption 
    - and more
- Behavior has its own section in our view template’s element catalog.

Notations for Documenting Behavior
- Trace-oriented languages
    - Traces are sequences of activities or interactions that describe the system’s response to a specific stimulus when the system is in a specific state. 
    - A trace describes a particular sequence of activities or interactions between structural elements of the system. 
    - Examples
        - use cases
        - sequence diagrams
        - communication diagrams
        - activity diagrams
        - message sequence charts
        - timing diagrams
        - Business Process Execution Language

Use Case Diagram:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507111752.png)
Use Case Description:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507111817.png)

Sequence Diagram:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507111838.png)

Communication Diagram:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507111859.png)

Activity Diagram:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507111922.png)

Summary:
- The software architecture of a system is the set of structures needed to reason about the system, which comprise software elements, relations among them, and properties of both.
- A structure is a set of elements and the relations among them.
- A view is a representation of a coherent set of architectural elements. A view is a representation of one or more structures.
- You must choose the views to document, must choose the notation to document these views, and must choose a set of views that is both minimal and adequate.
- You must document not only the structure of the architecture but also the behavior.


4+1 View Model
-	Philippe Kruchten. 1995. The 4+1 View Model of Architecture. IEEE Softw. 12, 6 (November 1995), 42–50.

Overview about the article's author:
-	Philippe Kruchten has extensive of experience as a leader of the development team in Rational corporation. 
-	He had a good experiences in industry (Telecom, Air traffic control system) which he used to justify his model.
-	“4+1 views” methodology successfully used in the industry
    -	Air Traffic Control
    -	Telecom

Problems:
-	Architecture documents do not address the concerns of all stakeholders .
-	 Deferent Stakeholders : end-user, system engineers, developers and project managers.
-	 Architecture documents  contained complex diagrams some times they are hard to be represented on the documentation.
Solution
-	Using different notations for several Views each one addressing one specific set for concerns.
-	Use“4+1” view model.

4+1 View Model of Architecture：
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507142705.png)


## 3.Quality attributes	:Understanding quality attributes and availability

Intended Learning Outcomes
-	By the end of this lesson you will be able to:
-	specify a quality attribute requirement 	
-	understand quality design decisions 	
-	apply the design decision categories to different quality attributes	


Architecture and Requirements
-	System requirements can be categorized as:
    -	Functional requirements state what the system must do, how it must behave or react to run-time stimuli.  
    -	Quality attribute requirements qualify functional requirements, e.g., how fast the function must be performed, how resilient it must be to erroneous input, etc. 
    -	Constraints. A constraint is a design decision with zero degrees of freedom.  

**Which of the above system requirements will have an impact on the architecture of the system?**（1 answer）
- Functional requirements
- Quality attributes requirements 
- Constraints 



Functionality 
-	Functionality is the ability of the system to do the work for which it was intended.  
-	Functionality has a strange relationship to architecture:
    -	functionality does not determine architecture; 

Quality Attribute Considerations
-	If a functional requirement is "when the user presses the green button the Options dialog appears”:
    -	a performance qualification might describe how quickly the dialog will appear; 
    -	an availability qualification might describe how often this function will fail, and how quickly it will be repaired; 
    -	a usability qualification might describe how easy it is to learn this function.

Two Categories of Quality Attributes
-	The ones that describe some properties of the system at runtime
-	Availability, performance, usability, security
-	The ones that describe some properties of the development of system
    -	Modifiability
    -	Testability

Quality Attribute Considerations
-	There are problems with previous discussions of quality attributes: 
-	Untestable definitions. The definitions provided for an attribute are not testable. It is meaningless to say that a system will be “modifiable”
-	Overlapping concerns. Is a system failure due to a denial of service attack an aspect of availability, performance, security, or usability? 
-	A solution to the problems (untestable definitions and overlapping concerns) is to use quality attribute scenarios as a means of characterizing quality attributes.

Specifying Quality Attribute Requirements
-	We use a common form to specify all quality attribute requirements as scenarios.
-	Our representation of quality attribute scenarios has these parts:
    -	Stimulus
    -	Stimulus source
    -	Response
    -	Response measure
    -	Environment
    -	Artifact


Specifying Quality Attribute Requirements
-	Stimulus. he stimulus is a condition that requires a response when it arrives at a system.
-	Source of stimulus. his is some entity (a human, a computer system, or any other actuator) that generated the stimulus.
-	Response. he response is the activity undertaken as the result of the arrival of the stimulus. 
-	Response measure. hen the response occurs, it should be measurable in some fashion so that the requirement can be tested. 
-	Environment. he stimulus occurs under certain conditions. The system may be in an overload condition or in normal operation, or some other relevant state.  
-	Artifact. his may be a collection of systems, the whole system, or some piece or pieces of it. Some artifact is stimulated. 


Specifying Quality Attribute Requirements
-	General quality attribute scenarios are system independent and can, potentially, pertain to any system
-	Concrete quality attribute scenarios are specific to the particular system under consideration. 


Specifying Quality Attribute Requirements
-	Example general scenario for availability:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507143141.png)

Achieving Quality Attributes Through Tactics
-	There are a collection of primitive design techniques that an architect can use to achieve a quality attribute response. 
-	We call these architectural design primitives tactics.
-	Tactics, like design patterns, are techniques that architects have been using for years. 
-	We do not invent tactics, we simply capture what architects do in practice. 



Tactics: examples
-	Resource scheduling is a tactic for performance
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507143302.png)


Tactic v.s. Architectural pattern
-	A tactic is a design decision for a single quality attribute
-	A tactic does not consider tradeoffs among quality attributes
-	Architectural patterns can be seen as “packages” of tactics, in which tradeoffs are considered
-	Do we need to study both architectural patterns and tactics?

Achieving Quality Attributes Through Tactics
-	We need to isolate, catalog, and describe the tactics. Why do we do this?  
    -	Design patterns are complex, and often difficult to apply as is; architects need to modify and adapt them. 
    -	If no pattern exists to realize the architect’s design goal, tactics allow the architect to construct a design fragment from “first principles”. 
    -	By cataloguing tactics, we will have a choice of multiple tactics to improve a particular quality attribute. The choice of which tactics to use depends on factors such as the cost to implement. 

Guiding Quality Design Decisions
-	Architecture design is a systematic approach to making design decisions.
-	We categorize the design decisions as follows:
    -	Allocation of responsibilities
    -	Coordination model
    -	Data model
    -	Management of resources
    -	Mapping among architectural elements
    -	Binding time decisions
    -	Choice of technology

Allocation of Responsibilities
-	Decisions involving allocation of responsibilities include:
    -	identifying the responsibilities including basic system functions, architectural infrastructure, and satisfaction of quality attributes. 
    -	determining how these responsibilities are allocated to non-runtime and runtime elements (namely, modules, components, and connectors). 

Coordination Model
-	Decisions about the coordination model include:
    -	identify the elements of the system that must coordinate, or are prohibited from coordinating
    -	determining the properties of the coordination, such as timeliness, currency, correctness, and consistency
    -	choosing the communication mechanisms that realize those properties.  
        -	stateful vs. stateless, 
        -	synchronous vs. asynchronous, 
        -	guaranteed vs. non-guaranteed delivery, and 

Data Model
-	Decisions about the data model include:
    -	choosing the major data abstractions, their operations, and their properties. 
    -	metadata needed for consistent interpretation of the data
    -	organization of the data, i.e., to decide whether the data is going to be kept in a relational data base, a collection of objects or both 
    

Management of Resources
-	Decisions for management of resources include:
    -	identifying the resources that must be managed and determining the limits for each
    -	determining which system element(s) manage each resource 
    -	determining how resources are shared and the strategies employed when there is contention
    -	determining the impact of saturation on different resources. 

Mapping Among Architectural Elements
-	Useful mappings include:
    -	the mapping of modules and runtime elements to each other
    -	the assignment of runtime elements to processors
    -	the assignment of items in the data model to data stores
    -	the mapping of modules and runtime elements to units of delivery 

Binding Time
-	The decisions in the other categories have an associated binding time decision. Examples of such binding time decisions include:
    -	For allocation of responsibilities you can have build-time selection of modules via a parameterized build script. 
    -	For choice of coordination model you can design run-time negotiation of protocols.
    -	For resource management you can design a system to accept new peripheral devices plugged in at run-time.

Summary
-	Requirements for a system come in three categories.
    -	Functional. These requirements are satisfied by including an appropriate set of responsibilities within the design.
    -	Quality attribute. These requirements are satisfied by the structures and behaviors of the architecture.
    -	Constraints. A constraint is a design decision that’s already been made.
-	To express a quality attribute requirement we use a quality attribute scenario.  The parts of the scenario are:
    -	Source of stimulus.  Stimulus. Environment.  Artifact.  Response Response measure. 
-	An architectural tactic is a design decision that affects a quality attribute response. The focus of a tactic is on a single quality attribute response.  
-	Architectural patterns can be seen as “packages” of tactics.
-	The seven categories of architectural design decisions are:
    -	Allocation of responsibilities
    -	Coordination model
    -	Data model
    -	Management of resources
    -	Mapping among architectural elements
    -	Binding time decisions

### Availability
What is Availability?
-	Availability refers to a property of software that it is there and ready to carry out its task when you need it to be. 
-	Availability refers to the ability of a system to mask or repair faults such that the cumulative service outage period does not exceed a required value over a specified time interval. 
-	Availability is about minimizing service outage time by mitigating faults

Availability 
-	Availability v.s. reliability or dependability
    -	Availability encompasses what is normally called reliability.
    -	Availability encompasses other consideration such as service outage due to period maintenance   
-	Availability is closely related to
    -	security, e..g, denial-of-service 
    -	performance
    -	…

Availability General Scenario
-	Stimulus … … … … … …  ?
-	Stimulus source … … … ?
-	Response … … … … … …?
-	Response measure … …?
-	Environment … … … … .?
-	Artifact … … … … … … ..?

Specifying Quality Attribute Requirements
-	Stimulus. he stimulus is a condition that requires a response when it arrives at a system.
-	Source of stimulus. his is some entity (a human, a computer system, or any other actuator) that generated the stimulus.
-	Response. he response is the activity undertaken as the result of the arrival of the stimulus. 
-	Response measure. hen the response occurs, it should be measurable in some fashion so that the requirement can be tested. 
-	Environment. he stimulus occurs under certain conditions. The system may be in an overload condition or in normal operation, or some other relevant state.  
-	Artifact. his may be a collection of systems, the whole system, or some piece or pieces of it. Some artifact is stimulated. 


Availability General Scenario:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507144005.png)

Sample Concrete Availability Scenario
-	The heartbeat monitor determines that the server is nonresponsive during normal operations. The system informs the operator and continues to operate with no downtime. 
-	The heartbeat monitor determines that the server is nonresponsive during normal operations. The system informs the operator and continues to operate with no downtime. 
-	Let us analyze if this scenario is complete 

Specifying Quality Attribute Requirements
-	Stimulus. he stimulus is a condition that requires a response when it arrives at a system.
-	Source of stimulus. his is some entity (a human, a computer system, or any other actuator) that generated the stimulus.
-	Response. he response is the activity undertaken as the result of the arrival of the stimulus. 
-	Response measure. hen the response occurs, it should be measurable in some fashion so that the requirement can be tested. 
-	Environment. he stimulus occurs under certain conditions. The system may be in an overload condition or in normal operation, or some other relevant state.  
-	Artifact. his may be a collection of systems, the whole system, or some piece or pieces of it. Some artifact is stimulated. 


Sample Concrete Availability Scenario
-	The heartbeat monitor determines that the server is nonresponsive during normal operations. The system informs the operator and continues to operate with no downtime. 
    -	Stimulus:  ???
    -	The stimulus is a condition that requires a response when it arrives at a system.




**Sample Concrete Availability Scenario**
-	The heartbeat monitor determines that the server is nonresponsive during normal operations. The system informs the operator and continues to operate with no downtime. 
-	Stimulus: non-responsiveness 
-	Response: inform the operator
-	Response measure: no downtime, or 100 availability percentages
-	Environment: normal operation
-	Artifact: heartbeat monitor
-	Stimulus source: server 

-	We will continue this topic on Monday

**Which statement is NOT true?（The last one）**
- A stakeholder is anyone who has a stake in the success of the system
- Stakeholders typically have different specific concerns on the system
- Stakeholders participate in some parts of the design process.
- Architect is the only stakeholder of the system

Recall our earlier example
- University Town, has directed its software development subsidiary, Campus Software, to develop a cafeteria system that supports a network of cafeteria Kiosks and POSs (points of sale). Kiosks are distributed in diverse locations of University Town near cafeterias and POSs are located near meal serving stations inside of cafeterias. The users are students, faculty and other employees of University Town. They use Kiosks to make queries, and funds transfers from their bank accounts to their cafeteria accounts. They use POSs to pay for their meals. 
- One of the quality attribute requirements for the system in our earlier example is availability. The client stated one of the scenarios for this quality requirement as follows:
“when the POS crashes it should recover withing 20 minutes”
If you were an architect, what would you do?

Goal of Availability Tactics
- Fault v.s. failure ?
- A failure occurs when the system no longer delivers a service consistent with its specification
    - this failure is observable by the system’s actors. 
- A fault (or combination of faults) has the potential to cause a failure. 
- Availability tactics enable a system to endure faults so that services remain compliant with their specifications. 
- The tactics keep faults from becoming failures or at least bound the effects of the fault and make repair possible. 

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507155310.png)

Availability Tactics:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507155406.png)

Detect Faults:
- Ping/echo: used to determine reachability and the round-trip delay through the associated network path. 
- Monitor: a component used to monitor the state of health of other parts of the system
- Timestamp: used to detect incorrect sequences of events, primarily in distributed message-passing systems. 
- Condition Monitoring: checking conditions in a process or device, or validating assumptions made during the design.	
    - For example, checksum in data storage and transmission
- Voting: the common realization of this tactic is Triple Modular Redundancy (TMR)
- Exception Detection: detection of a system condition that alters the normal flow of execution, e.g. system exception, parameter typing, timeout.
- Self-test: procedure for a component to test itself for correct operation.



Detect Faults: Heartbeat
- Heartbeat: a periodic message exchange between a system monitor and a process being monitored.
    - The process periodically resets the watchdog timer in its monitor,
    - Piggybacking the heartbeat messages on to other control messages reduces the overhead
- Difference between ping and heartbeat?
    - Who initiates the health check?

Checksum:
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507155623.png)

Recover from Faults (Preparation & Repair)
- Active Redundancy (hot spare): all nodes in a protection group process identical inputs in parallel, allowing redundant spares to maintain synchronous state with the active nodes. 	
- Spare (cold spare): redundant spares of a protection group remain out of service until a fail-over occurs, at which point a power-on-reset procedure is initiated on the redundant spare prior to its being placed in service.
- Passive Redundancy (warm spare): only the active members of the protection group process input traffic; 
    - one of their duties is to provide the redundant spare(s) with periodic state updates.  
- Rollback: revert to a previous known good state, referred to as the “rollback line”.
    - This tactic is combined with redundancy  tactics
    - After a rollback has occurred, a standby version of the failed component becomes active
    - Rollback depends on a copy of a previous state (a checkpoint) 
    - Checkpoint can be stored in a fixed location and needs to be updated regularly
- Exception Handling: dealing with the exception by reporting it or handling it, potentially masking the fault by correcting the cause of the exception and retrying.
- Retry: where a failure is transient and retrying the operation may lead to success.
    - For example, network re-transmission
- Ignore Faulty Behavior: ignoring messages sent from a source when it is determined that those messages are spurious.
    - E.g., ignore the messages from a denial of service attacker
- Degradation: maintains the most critical system functions in the presence of component failures, dropping less critical functions.
- Reconfiguration: reassigning responsibilities to the resources left functioning, while maintaining as much functionality as possible.

Prevent Faults:
- Removal From Service: temporarily placing a system component in an out-of-service state for the purpose of mitigating potential system failures 
- Transactions: bundling state updates so that asynchronous messages exchanged between distributed components are atomic, consistent, isolated, and durable. 
- Predictive Model: take corrective action when conditions are detected that are predictive of likely future faults. 
- Exception Prevention: preventing system exceptions from occurring by preventing it via smart pointers, abstract data types, wrappers.
- Increase Competence Set: designing a component to handle more cases—faults—as part of its normal operation


**“Ping/Echo” is an architecture tactic for (  A  ).**
- Availability
- Security
- Performance
- Usability

Summary
- Availability refers to the ability of the system to be available for use when a fault occurs. 
- The fault must be recognized (or prevented) and then the system must respond. 
- The response will depend on the criticality of the application and the type of fault
    - can range from “ignore it” to “keep on going as if it didn’t occur.” 
- Tactics for availability are categorized into detect faults, recover from faults and prevent faults. 
- Detection tactics depend on detecting signs of life from various components. 
- Recovery tactics are retrying an operation or maintaining redundant data or computations. 
- Prevention tactics depend on removing elements from service or limiting the scope of faults. 


## Quality attributes: interoperability and modifiability
### Interoperability 
Intended Learning Outcomes
- By the end of this lesson you will be able to:
    - apply the design decision categories to interoperability 		

What is Interoperability?
- Interoperability is about the degree to which two or more systems can usefully exchange meaningful information via interfaces in a particular context.
- Any discussion of a system’s interoperability needs to identify with whom, and under what circumstance.
- Syntactic interoperability is the ability to exchange data. 
- Semantic interoperability is the ability to interpret the data being exchanged.
- Two perspectives for achieving interoperability
    - With the knowledge about the interfaces of external systems, design that knowledge into the system 
    - Without the knowledge about other systems, design the system to interoperate in a more general fashion 

Motivation
-	The system provides a service to be used by a collection of unknown systems, eg., GoogleMaps
-	The system is constructed from existing systems, for example
    -	Producing a representation of what was sensed
    -	Interpreting the data
    -	Processing the raw data
    -	Sensing the environment

Two Important Aspects of Interoperability
-	Discovery. The consumer of a service must discover the location, identity, and interface of service
-	Handling the response. Three possibilities:
    -	The service reports back to the requester
    -	The service sends its response on to another system
    -	The service broadcasts its response to any interested parties

Example
-	Will this system need to provide services to other system(s)? What do we know about these systems?
-	Will this system be constructed by combining capabilities from other systems? 
    -	Example - a traffic sensing system where the input comes from individual vehicles, the raw data is processed into common units of measurement, is interpreted and fused, and traffic congestion information is broadcast
        -	one of the existing systems is responsible for sensing its environment
        -	another one is responsible for processing the raw data
        -	a third is responsible for interpreting the data
        -	a final one is responsible for producing and distributing a representation of what was sensed

Intelligent transportation system
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507161712.png)


Interoperability General Scenario
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507161758.png)

Sample Concrete Interoperability Scenario
-	Our vehicle information system sends our current location to the traffic monitoring system.
-	The traffic monitoring system combines our location with other information, overlays this information on a Google Map, and broadcasts it.
-	Our location information is correctly included with a probability of 99.9%. 

Sample Concrete Interoperability Scenario
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507161852.png)


Goal of Interoperability Tactics
-	For two or more systems to usefully exchange information they must
    -	Know about each other. That is the purpose behind the locate tactics.
    -	Exchange information in a semantically meaningful fashion. That is the purpose behind the manage interfaces tactics. Two aspects of the exchange are
        -	Provide services in the correct sequence
        -	Modify information produced by one actor to a form acceptable to the second actor.

Goal of Interoperability Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507161946.png)

Interoperability Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507162019.png)

Locate
-	Service Discovery : Locate a service through searching 
-	There are many service discovery mechanisms:
    -	UDDI for Webservices
    -	Jini for Jave objects
    -	Simple Service Discovery Protocol (SSDP) as used in Universal plug-and-play (UPnP)
    -	DNS Service Discovery (DNS-SD)
    -	Bluetooth Service Discovery Protocol (SDP)

Service Discovery – Necessary conditions
-	The searcher wants to find the searched entity and the searched entity wants to be found
-	The searched entity must have identifiers
-	The searcher must acquire sufficient identifiers to identify the searched entity

Searching Method – Searcher’s initiative
-	Flood/Broadcast request
    -	Ask every entity and wait for answer
-	Examples
    -	Paging in the location area to find the mobile terminal
    -	DHCP discover: the client broadcasts on the local subnet to find available servers to ask for IP address 
-	Efficient and less resource consuming for the searcher
-	Low resource consuming for the searched
-	But disturbing and resource consuming for the environment

Searching Method – Searcher’s initiative
-	Successive request:
    -	Ask one entity at the time and perform matching
    -	If no match, continue with next until finding a  match
-	Less efficient and high resource consuming for the searcher 
-	But less disturbing and less resource consuming for the environment

Searching Method – Searched’s initiative
-	Continuous/periodical advertisement:
    -	Continuously or periodically publish advertisement such that every searcher can notice and respond
-	Efficient but high resource consuming for the searched
-	Low resource demanding for the searcher
-	Disturbing and resource consuming for the environment


Searching Method – Searched’s initiative
-	Advertisement upon arrival of new entity
    -	E.g., present himself when a new person enters the lobby
-	Require detection mechanism upon new entity arrival
-	Less resource consuming for the searched
-	Low resource demanding for the searcher
-	Less disturbing and resource consuming for the environment

Searching Method – Registration
-	Introduction of the “middlemen”, registry
    -	The searched entity registers to a registry
    -	The searcher can address to the registry to get information and find the searched entity
-	Example
    -	Service providers register their web services at UDDI registry which can be searched and found by Service Requestors

Searching Method – Registration
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507162241.png)

Example
- Service discovery in web services architecture

Web Service
-	Describes any computational functionality that can be found and invoked over any network (e.g. the Internet)
-	Represents a self-describing, self-contained application
-	Designed to be used by other programs or applications rather than humans
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507162851.png)

Web Service Architecture
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507162907.png)

Web Service Architecture
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507162930.png)

UDDI Registries
-	A network of UDDI registries resembling the Domain Name System (DNS)
-	All UDDI registries exchange information
-	Accessing one registry provides all information contained in all registries
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507163012.png)

Interoperability Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507163026.png)

Manage Interfaces
-	Orchestrate: uses a control mechanism to coordinate, manage and sequence the invocation of services.  
-	Orchestration is used when systems must interact in a complex fashion to accomplish a complex task.
-	Tailor Interface: add or remove capabilities to an interface such as translation, buffering, or data-smoothing.

Checklist for Interoperability
-	Allocation of Responsibility
-	Coordination Model
-	Data Model
-	Mapping among Architectural Elements
-	Resource Management
-	Binding Time

Allocation of Responsibility
-	Ensure that responsibility has been allocated 
    -	to detect a request to interoperate with external systems
    -	to accept/reject the request 
    -	to exchange the information
    -	to notify appropriate entities
    -	log the request, which is essential for interoperability in an untrusted environment

Coordination Model
-	Volume of traffic on the network both created by the systems under your control and generated by system not under your control
-	Timeliness of the messages being sent by your systems
-	Jitter of the messages’ arrival times
-	The system under your control makes assumptions about protocols and underlying networks that are consistent with systems not under your control

Data Model
-	???

**(multiple answers are correct) To design the architecture for a system with high interoperability  requirement, we need to make design decisions regarding the data model**
- Decide on the syntax of the major data abstractions to be exchanged with other systems and make sure the chosen data abstractions are consistent with the data from the interoperating systems.
- **Make sure that components that communicate externally are hosted on processors that can reach the network**
- Decide on semantics of the major data abstractions to be exchanged with other systems and make sure the chosen data abstractions are consistent with the data from the interoperating systems.
- **Make sure that the interoperation with another system will never exhaust critical system resources**


Data Model
-	Determine the syntax and semantics of the major data abstractions to be exchanged
-	Ensure that these data abstractions are consistent with data from the interoperating systems
-	If a system’s data model is confidential, it is needed to transform to and from the data abstraction with which it interoperates

Mapping among Architectural Elements
-	For interoperability, the critical mapping is that of components to processors 
-	Make sure that components that communicate externally are hosted on processors that can reach the network

Resource Management
-	Interoperation with another system can never exhaust critical system resources
    -	E.g., can a flood of requests cause service to be denied
-	Resource load imposed by communications is acceptable
-	If interoperation requires that resources be shared among the participating systems, an adequate policy is in place
    -	E.g., bandwidth scheduling for video sharing

Binding Time
-	Determine when the systems become known to each other
-	It has a policy for dealing with binding to both known and unknown external systems
-	It has mechanisms to reject unacceptable bindings and to log such requests

Summary
-	Interoperability refers to the ability of systems to usefully exchange information. 
-	Achieving interoperability involves the relevant systems locating each other and then managing the interfaces so that they can exchange information.

**Your task**
-	While listening to this part of our topic, try to think about this question “which tactics was used in this research to achieve interoperability between the discovery mechanisms?”

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507163406.png)


Background
-	Heterogeneity - different environments supported by different service management systems, which may use standard protocols as well as tailored made mechanisms
    -	software, 
    -	hardware devices, 
    -	network infrastructures
-	Diverse service discovery mechanisms

Interoperability Requirements
-	No change should be imposed on the existing service discovery mechanisms

-	No change should be imposed on the services registered in domains

-	No functionality of the environment should be compromised

-	The system should be lightweight, scalable, and extendable

-	Support both standard and tailor-made service discovery mechanisms

System Model and Architecture:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507163518.png)
- Environment 
    - Service Discovery Domain, where a native service discovery system is able to find a specified resources if they are available in the domain. 
- Services 
    - provided by hardware devices, software, and other entities
    - Examples: weather forecast, stock quotes, and language translation. - provided directly or via another device


Universal Adaptor Primitives (UAP) 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507163620.png)

Universal Adaptor Mapping (UAM)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507163719.png)

Simulation  Results  
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507163820.png)
- For the too sparse network, neither approach with UA not without UA can achieve good results. 
- The more neighbors the node has the bigger success rate of finding the preferred collaboration partner, quality improves as well. 
- When the network is very dense, the improvement is not visible any more -  additional neighbors available for the node to choose from are treated as duplication.
- Approach with UA always results with providing bigger choice to the node, which in turn gives better success rate and higher quality.
- With UA and without UA results are not very symmetric. The network for the approach with UA becomes “too dense” earlier than for the approach without UA.


Experiment  Results 
- Two service discovery environments:
    - Jini - Jini2_1
    - SLP - jSLP 
- Example: a weather information service
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507163933.png)

Interoperability Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507164000.png)

**Which tactics was used in this research to achieve interoperability between the discovery mechanisms?**(1 answer)
- Discover service
- Orchestrate
- **Tailor interface**



Universal Adaptor Primitives (UAP) 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507164234.png)

Universal Adaptor Mapping (UAM)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507164309.png)

Manage Interfaces
-	Orchestrate: uses a control mechanism to coordinate, manage and sequence the invocation of services.  
-	Orchestration is used when systems must interact in a complex fashion to accomplish a complex task.
-	Tailor Interface: add or remove capabilities to an interface such as translation, buffering, or data-smoothing.

Interoperability Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507164356.png)


### Quality attributes: Modifiability
Intended Learning Outcomes
-	By the end of this lesson you will be able to:
-	apply the design decision categories to modifiability 	

What is Modifiability?
-	Modifiability is about change and our interest in it is in the cost and risk of making changes.  
-	To plan for modifiability, an architect has to consider four questions: 
    -	What can change? 
    -	What is the likelihood of the change? 
    -	When is the change made and who makes it?
    -	What is the cost of the change?  

Recall our earlier example
-	University Town, has directed its software development subsidiary, Campus Software, to develop a cafeteria system that supports a network of cafeteria Kiosks and POSs (points of sale). Kiosks are distributed in diverse locations of University Town near cafeterias and POSs are located near meal serving stations inside of cafeterias. The users are students, faculty and other employees of University Town. They use Kiosks to make queries, and funds transfers from their bank accounts to their cafeteria accounts. They use POSs to pay for their meals. 


What can change?
-	The functions of the system computers
-	The platforms, i.e., the hardware, operating system, middleware
-	The environment in which the system operates
    -	The systems with which it must interoperate 
    -	The protocols it uses to communicate
-	The capacity
    -	Number of users supported
    -	Number of simultaneous operations

When is the change made and who makes it?
-	Changes can be made during
    -	implementation by modifying the source code
    -	build by choice of libraries
    -	execution by parameter setting, plugins, etc
-	Changes can also be made by 
    -	a developer
    -	an end user
    -	a system administrator
    

What is the cost of the change?
-	Involving two types of cost
    -	The cost of introducing the mechanisms to make the system more modifiable
    -	The cost of making the modification using the mechanisms
-	Example
    -	User interface builder

Example
-	You are an architect on the Campus Software team. There is a possibility that in the future, the client will request changes to be made to the user interface. Your team is considering two ways to handle this problem
    -	1. Do nothing for now. Wait for a change request to come in, then change the source code to accommodate request. 
    -	2. Add additional component to the system now – user interface builder. When there is a change request, the designer will use a drag-and-drop editor to design a new interface, and the user interface builder will produce the new source code directly
-	Let us consider what is the cost of each option
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170012.png)


-	Considering the differences in cost between these options, which one will be a better choice?

**Which option will be a better choice if the chance of the need to make a change is not high?**（1 answer）
- **Do nothing for now.** 
- Add user interface builder to the system. 

**Which option will be a better choice if there is a chance that the changes in the interface will be requested many times??**(1 answer)
- Do nothing for now. 
- **Add user interface builder to the system.**

Modifiability General Scenario
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170227.png)

Sample Concrete Modifiability Scenario
-	The developer wishes to change the user interface by modifying the code at design time. The modifications are made with no side effects within three hours.
    -	Stimulus – Wishes to change UI
    -	Artifact – Code
    -	Environment: Design time
    -	Response – Change made
    -	Response measure – No side effects in three hours
    -	Source - Developer
    

Goal of Modifiability Tactics
-	Goal of modifiability
    -	controlling the complexity of making changes, 
    -	controlling the time and cost to make changes.
    ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170315.png)

Modifiability Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170332.png)

Design 1 – the module includes a great deal of capability
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170402.png)

Design B – the module is refined into several smaller modules (each capability is represented in a separate module)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170414.png)

**If there is a chance needed in this module, which design will have smaller modification cost?**(Second one is right)
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170402.png)
- ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170414.png)

Reduce Size of a Module
-	Split Module: If the module being modified includes a great deal of capability, the modification costs will likely be high. 
-	Refining the module into several smaller modules should reduce the average cost of future changes. 

Increase Cohesion
-	Increase Semantic Coherence: If the responsibilities A and B in a module do not serve the same purpose, they should be placed in different modules. 
-	This may involve creating a new module or it may involve moving a responsibility to an existing module. 

Reducing Coupling
-	What is coupling?
-	If two modules’ responsibilities overlap, a single change may affect them both
-	Coupling is measured by this overlap, i.e., by the probability that a modification to one module will propagate to the other 
-	High coupling is an enemy of modifiability
-	Encapsulate: Encapsulation introduces an explicit interface to a module. This interface includes an API and its associated responsibilities
-	Use an Intermediary: Given a dependency between responsibility A and responsibility B (for example, carrying out A first requires carrying out B), the dependency can be broken by using an intermediary. 

Introduction: Motivations for Pub/Sub model
-	Traditional Client/Server communication model
(Employs RPC, message queue etc..)
    -	Synchronous, tightly-coupled request invocations. 
    -	Very restrictive for distributed applications, especially for WAN and mobile environments. 
    -	When nodes/links fail, system is affected. Fault Tolerance must be built in to support this.

-	Require a more flexible and de-coupled communication style that offers asynchronous mechanisms. 

What is a Publish/Subscribe System?
-	Pub/Sub System is a communication paradigm that allows freedom in the (distributed) system by the decoupling of communication entities in terms of time, space and synchronization. 
-	An event service system that is asynchronous, anonymous and loosely-coupled. 
-	Ability to quickly adapt in a dynamic environment. 

Key components of Pub/Sub System
-	Publishers : Publishers generate event data and publishes them.
-	Subscribers : Subscribers submit their subscriptions and process the events received
-	P/S service: It’s the mediator/broker that filters and routes events from publishers to interested subscribers. 

Publish/Subscribe System 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170646.png)


Decoupling in time, space and synchronization
-	Provides decoupling in time, space and synchronization. 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170736.png)

Classification of Pub/Sub Architectures 
-	Centralized Broker model
    -	Consists of multiple publishers and multiple subscribers and centralized broker/brokers (an overlay network of brokers interacting with each other).
    -	Subscribers/Publishers will contact 1 broker, and does not need to have knowledge about others. 
    -	E.g. CORBA event services, JMS, JEDI etc…


Classification of Pub/Sub Architectures
-	Peer-to-Peer model
    -	Each node can be publisher, subscriber or broker.
    -	Subscribers subscribe to publishers directly and publishers notify subscribers directly. Therefore they must maintain knowledge of each other. 
    -	Complex in nature, mechanisms such as DHT and CHORD are employed to locate nodes in the network.
    -	E.g. Java distributed event service

Key Functions Implemented by P/S Middleware Service
-	Event filtering (event selection)
    -	The process of selecting the set of subscribers that have shown interest in a given event.
    -	 Subscriptions are stored in memory and searched when a publisher publishes a new event. 
-	Event routing (event delivery) 
    -	The process of routing the published events to all interested subscribers   

Event Filtering (Subscription Model) Topic based VS Content based 
-	Topic based
    -	Generally also  known as topic based, group based or channel based event filtering.
    -	Each event is published to one of these channels  by its publisher. 
    -	Subscribers subscribes to a particular channel and will receive ALL events published to the subscribed channel. 

Topic-based subscription 
-	Simple  process  for  matching an event to subscriptions. However, limited expressiveness. 
-	Event filtering is easy, event routing is difficult (Heavy load on the network). The challenge is to multicast event effectively to subscribers. 

Event Filtering- Subscription Model Topic based VS Content based
-	Content based
    -	More flexibility and power to subscribers, by allowing more expression in arbitrary/customized query over the contents of the event.
    -	Event publication by a key/value attribute pair, and subscriptions specify filters using a explicit subscription language. 
    -	E.g. Notify me of all stock quotes of IBM from New York stock exchange if the price is greater than 150

Content-based Subscription 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507170920.png)
-	Added complexity in matching an event to subscriptions. 
-	However, more precision is provided and event routing is easier

Advantages of Pub/Sub
-	Highly suited for mobile applications, ubiquitous computing and distributed embedded systems
-	Robust – Failure of publishers or subscribers does not bring down the entire system
-	Scalability- Suited to build distributed applications consisting a large number  of entities
-	Adaptability- can be varied to suit different environments (mobile, internet game, embedded systems etc…)

Disadvantages of Pub/Sub 
-	Reliability – no strong guarantee on broker to deliver content to subscriber. After a publisher publishes the event, it assumes that all corresponding subscribers would receive it.
-	Potential bottleneck in brokers when subscribers and publishers overload them. (Solve by load balancing techniques)

Modifiability Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507171128.png)

Reduce Coupling
-	Restrict Dependencies: restricts the modules which a given module interacts with or depends on. 
-	By restricting a module’s visibility and by authorization
-	For example, 
    -	a layer is allowed to see the modules in its bottom layer
-	Abstract Common Services: where two modules provide not-quite-the-same but similar services, it may be cost-effective to implement the services just once in a more general (abstract) form. 

Summary
-	Modifiability deals with change and the cost in time or money of making a change, including the extent to which this modification affects other functions or quality attributes. 
-	Tactics to reduce the cost of making a change include making modules smaller, increasing cohesion, and reducing coupling.



## Quality attributes: Performance, Security
Intended Learning Outcomes
-	By the end of this lesson you will be able to:
-	apply the design decision categories to diverse quality attributes	

What is Performance?
-	It is about time
-	Performance is about time and the software system’s ability to meet timing requirements 
-	When events occur, the system must respond to them in time
    -	Events include interrupts, messages, requests from users or other systems, or clock events marking the passage of time 


Performance General Scenario
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507171603.png)


Performance concrete scenario - example
-	You are an architect on the Campus Software team and you are designing the system for performance
-	In the requirements document you have found this requirement:
    -	The system should process the transactions with an average latency of two seconds
-	Is this description complete, is it clear enough for the architect to make good design decisions?
-	Which parts are missing?  

**Which parts are missing in the performance concrete scenario example we discussed?**
- **Source**
- Stimulus
- Artifact
- **Environment**
- Response
- Response measure

Sample Concrete Performance Scenario – not complete
-	The system processes the transactions with an average latency of two seconds.
-	Stimulus: transaction arrivals
-	Source: ???
-	Artifact: the system
-	Response: process the transactions
-	Response measure: average latency of two seconds
-	Environment: ???
-	This scenario is not complete, source and environment parts are missing

How to add the remaining parts?
-	We can use the general scenario to help us look for ideas
-	Then, clarify with the stakeholders about the details of the requirement 

Performance General Scenario
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507171928.png)

Sample Concrete Performance Scenario - complete
-	Users initiate transactions under normal operations. The system processes the transactions with an average latency of two seconds.
    -	Stimulus: transaction arrivals
    -	Source: users
    -	Artifact: the system
    -	Response: process the transactions
    -	Response measure: average latency of two seconds
    -	Environment: under normal operations

Sample Concrete Performance Scenario
-	Users initiate transactions under normal operations. The system processes the transactions with an average latency of two seconds.
    -	Stimulus: transaction arrivals
    -	Source: users
    -	Artifact: the system
    -	Response: process the transactions
    -	Response measure: average latency of two seconds
    -	Environment: under normal operations

Performance Modeling 
-	Two basic contributors to the response time
-	Processing time is the time that the system is working to respond
-	Blocked time is the time that the system is unable to respond
-	Blocked time is caused by
    -	Contention for resources
    -	Availability of resources
    -	Dependency on other computations

Goal of Performance Tactics
-	To generate a response to an event arriving the system within some time-based constraint
-	The event can be single or a stream, and is the trigger to perform computation
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507172037.png)

First, lets consider an example not related to the computing
-	You are an owner of the noodle shop. 
    -	You hired one cook who is able to cook 100 bowls during lunch time
    -	Your restaurant has 20 seats
    -	On the first day, there were over 200 people lining up for your noodles
-	You want to improve the performance of your noodle shop to make sure you serve as many customers as possible during the day.
-	Give examples of the tactics that you could use

Two Tactic Categories
-	Control resource demand
    -	To produce smaller demand on the resources
    -	Operate on the demand side
-	Manage resources
    -	To make the resources at hand work more effectively in handling the demands
    -	Operate on the response side

Resources
-	Hardware resources, e.g., CPU, data stores, network bandwidth, and memory
-	Software resources, e.g., buffers, or critical sections

Performance Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507172144.png)

Control Resource Demand
-	Manage Sampling Rate: to reduce the sampling frequency at which a stream of data is captured
-	Prioritize Events: to impose a priority scheme that ranks events according to the importance
    -	Ignore low-priority events when resources are not enough
-	Reduce Overhead: The use of intermediaries increases the resources consumed in processing an event stream; removing them improves latency. 


**Discussion**
-	We just said that the use of intermediaries increases the resources consumed in processing an event stream; removing them improves latency, therefore if we have high performance requirements on the system, we should not introduce intermediaries in the system architecture
-	In what cases you may want to make a different decision – introduce intermediaries even though the system has high performance requirements?

Control Resource Demand
-	Reduce Overhead: The use of intermediaries increases the resources consumed in processing an event stream; removing them improves latency. 
    -	Tradeoff between the modifiability and performance
-	Bound Execution Times: Place a limit on how much execution time is used to respond to an event.
    -	In algorithm design, limiting the number of iterations is a method for bounding exec. time
    -	Trade-off between the performance and accuracy
-	Increase Resource Efficiency: Improving the algorithms used in critical areas will decrease latency. 
-	To reduce the complexity of the algorithm

Manage Resources
-	Increase Resources: Faster processors, additional processors, additional memory, and faster networks all have the potential for reducing latency. 
-	Increase Concurrency: If requests can be processed in parallel, the blocked time can be reduced.
-	Concurrency can be introduced by processing different streams of events on different threads 

Maintain Multiple Copies of Computations
-	The purpose of replicas is to reduce the resource contention on a single server
-	Load balancer assigns new work to one of the duplicate server
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507172332.png)


Maintain Multiple Copies of Data
-	Data caching is to keep copies of data on storage with different access speeds.  
    -	E.g., memory access v.s. disk access
    -	Local access v.s. remote access via networks
-	Data replication is to keep separate copies of data to reduce the contention from multiple simultaneous accesses
-	How to choose the data to be cached/replicated
-	How to guarantee the consistency of multiple copies 

Scheduling
-	When there is contention for a resource, the resource must be scheduled. 
    -	Processors needs to be scheduled
    -	Buffers needs to be scheduled
    -	Networks are scheduled

Summary
-	Performance is about the management of system resources in the face of particular types of demand to achieve acceptable timing behavior. 
-	Performance can be measured in terms of throughput and latency for both interactive and embedded real time systems.
-	Performance can be improved by reducing demand or by managing resources more appropriately. 

### Quality attributes: Security

What is Security?
-	Security is a measure of the system’s ability to protect data and information from unauthorized access while still providing access to people and systems that are authorized  
-	An action taken against a computer system with the intention of doing harm is called an attack 
-	Attack can be in different forms
    -	unauthorized attempt to access/modify data or service
    -	intended to deny services to legitimate users


What is Security?
-	Security has three main characteristics, called CIA:
-	Confidentiality is the property that data or services are protected from unauthorized access. 
    -	For example, a hacker cannot access your income tax returns on a government computer.
-	Integrity is the property that data or services are not subject to unauthorized manipulation. 
    -	For example, your grade has not been changed since your instructor assigned it.
-	Availability is the property that the system will be available for legitimate use. 
    -	For example, a denial-of-service attack prevent you from ordering a book from an online bookstore.

What is Security?
-	Other characteristics that support CIA are
-	Authentication verifies the identities of the parties to a transaction and checks if they are truly who they claim to be. 
-	Authorization grants a user the privileges to perform a task. 
-	Nonrepudiation guarantees that the sender/recipient of a message cannot later deny having sent/received the message


Sample Concrete Security Scenario
-	A disgruntled employee from a remote location attempts to modify the pay rate table during normal operations. The system maintains an audit trail and the correct data is restored within a day.
    -	Stimulus: unauthorized attempts to modify the pay rate table
    -	Stimulus source: a disgruntled employee
    -	Artifact: the system with pay rate table 
    -	Environment: during normal operation
    -	Response: maintains an audit trail
    -	Response measure: correct data is restored within a day

Security Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507173937.png)

Detect Attacks
-	Detect Intrusion: compare network traffic or service request patterns within a system to a set of signatures or known patterns of malicious behavior stored in a database. 
-	Detect Service Denial: comparison of the pattern or signature of network traffic coming into a system to historic profiles of known Denial of Service (DoS) attacks.

Denial of Service Attack
-	Ping of Death
-	UDP Flood
-	TCP SYN  
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507174029.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507174039.png)

Detect Attacks
-	Verify Message Integrity: use techniques such as checksums or hash values to verify the integrity of messages, resource files, deployment files, and configuration files. 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507174137.png)

Detect Attacks
-	Detect Message Delay: checking the time that it takes to deliver a message, it is possible to detect suspicious timing behavior, i.e., man-in-the-middle attack
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507174149.png)

Resist Attacks
-	Identify Actors: identify the source of any external input to the system. 
-	Authenticate Actors: ensure that a user or remote computer is actually who or what it purports to be.
-	Authorize Actors: ensuring that an authenticated actor has the rights to access and modify either data or services. 
-	Limit Access: limiting access to resources such as memory, network connections, or access points.

Resist Attacks
-	Limit Exposure: minimize the attack surface of a system by having the fewest possible number of access points.
-	E.g., firewall is a single point of access to the intranet
-	E.g., closing a port
-	Passive defense – no human analysis or interaction - limiting security gaps and exposure to threats through firewalls, antimalware systems, intrusion prevention systems, antivirus protection, intrusion detection systems…

Resist Attacks
-	Encrypt Data: apply some form of encryption to data and to communication.
- Symmetric encryption：
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507174219.png)
- asymmetric encryption：
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507174310.png)

Resist Attacks
-	Separate Entities: can be done through physical separation on different servers, the use of virtual machines
-	Change Default Settings: Force the user to change settings assigned by default.

React to Attacks
-	Revoke Access: limit access to sensitive resources, even for normally legitimate users and uses, if an attack is suspected. 
-	Lock Computer: limit access to a resource if there are repeated failed attempts to access it.
-	Inform Actors: notify operators, other personnel, or cooperating systems when an attack is suspected or detected. 

Example
-	We will now look at the example of a specific system. 
-	When listening to this example, try to identify which security tactics it illustrates

Security Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507174341.png)

Example – MarkLogic database
-	MarkLogic is a multi-model NoSQL database 
-	MarkLogic's Enterprise NoSQL database platform is used in publishing, government, finance and other sectors, 
-	It uses a distributed architecture that can handle hundreds of billions of documents and hundreds of terabytes of data
-	You can use MarkLogic to create systems that store and retrieve data.
-	MarkLogic Server includes a powerful and flexible role-based security model to protect your data according to your application security requirements.


Protecting the Execution of XQuery Modules in MarkLogic
-	One simple way to restrict access to your MarkLogic Server application is to limit the users that have permission to run the application. 
-	If you load your Xquery code into a modules database, you can use an execute permission on the XQuery document itself to control who can run it. 
-	Then, a user must possess execute permissions to run the module. 

Steps to allow only selected users to run the application
-	Using the Admin Interface, specify a modules database in the configuration for the App Server (HTTP or WebDAV) that controls the execution of your XQuery module.
-	Load the XQuery module into the modules database, using a URI with an .xqy extension, for example my_module.xqy.
-	Set execute permissions on the XQuery document for a given role. For example, if you want users with the run_application role to be able to execute an XQuery module with the URI http://modules/my_module.xqy, run a query similar to the following:
-	xdmp:document-set-permissions("http://modules/my_module.xqy",
-	xdmp:permission("run_application", "execute") )
-	Create the run_application role.
-	Assign the run_application role to the users who can run this application.
**26**
Security Tactics
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507185236.png)

**In the example we just discussed, which security tactics was described?**
- Lock computer
- Detect intrusion
- **Authorize actors**
- Encrypt data


Summary
-	Attacks against a system can be characterized as attacks against the confidentiality, integrity, or availability of a system. 
-	Identifying, authenticating, and authorizing actors are tactics intended to determine which users or systems are entitled to what kind of access to a system.
-	No security tactic is foolproof. 
-	Tactics exist to detect an attack, limit the spread of any attack, and to react and recover from an attack.

Other quality attributes
-	Availability
-	Interoperability
-	Modifiability
-	Security

-	Testability
-	Usability
-	Portability
-	Development Distributability
-	Scalability
-	Deployability
-	Mobility
-	Monitorability
-	Safety



Testability
-	Software testability refers to the ease with which software can be made to demonstrate its faults through (typically execution-based) testing.  
-	Testability refers to the probability that it will fail on its next test execution. 
-	If a fault is present in a system, then we want it to fail during testing as quickly as possible.


Testability v.s. Fault-tolerance
-	Testability is to make the fault easier to show up
-	Fault-tolerance attempts to hide the faults, and make it difficult to reveal the faults.
-	Are the two design goals incompatible?

**Testability is to make the fault easier to show up.**
**Fault-tolerance attempts to hide the faults, and make it difficult to reveal the faults.****Are the two design goals incompatible?**
- No, they are compatible. Improving the fault-tolerance of the system will also improve its testability. 
- **Yes, they are incompatible. There is a trade-off between testability and fault tolerance.**

What is Usability?
-	Usability is concerned with how easy it is for the user to accomplish a desired task and the user support the system provides
-	Usability is one of the cheapest and easiest ways to improve a system’s quality
-	Usability comprises the following areas:
    - Using a system efficiently.  Learning system features.  Minimizing the impact of errors.  Adapting the system to user needs.  Increasing confidence and satisfaction.  



Portability
-	Portability is also a special form of modifiability.  
-	Portability refers to the ease with which software that built to run on one platform can be changed to run on a different platform. 


Development Distributability
-	Development Distributability is the quality of designing the software to support distributed software development. 
-	The system are developed using globally distributed teams.
-	The purpose is to minimize the coordination among the teams.


Scalability
-	Horizontal scalability (scaling out) refers to adding more resources to logical units such as adding another server to a cluster. 
-	Vertical scalability (scaling up) refers to adding more resources to a physical unit such as adding more memory to a computer.
-	Results in a measurable improvement without disruptive operations 
-	Centralized v.s. decentralized?


Deployability
-	Deployability is concerned with how an executable arrives at a host platform and how it is invoked.
-	Examples:
    -	Javascripts
    -	Virus
    -	Agent

Mobility
-	Mobility deals with the problems of movement and affordances of a platform
-	Issues in mobility include
    -	Battery management
    -	Disconnection and reconnection
    -	Hand-off in cellular network
    -	…


Monitorability
-	Monitorability deals with the ability of the operations staff to monitor the system while it is executing.
-	For example
    -	the health of various component
    -	Average transaction processing time
    -	Queue length

Safety
-	Software safety is about 
    -	the software’s ability to avoid entering states that cause or lead to damage, injury, or loss of life, 
    -	ability to recover and limit the damage when it does enter into bad states. 
-	The architectural concerns with safety are almost identical with those for availability


Standard Lists of Quality Attributes
-	Advantages:
    -	Can be helpful checklists to assist requirements gatherers in making sure that no important needs were overlooked.  
    -	Can serve as the basis for creating your own checklist that contains the quality attributes of concern in your domain, your industry, your organization, your products, …

Standard Lists of Quality Attributes
-	Disadvantages:
    -	No list will ever be complete.  
    -	Lists often generate more controversy than understanding.
    -	Lists often purport to be taxonomies. But what is a denial-of-service attack?

Summary
-	There are many other quality attributes than those that we cover in detail.
-	Taxonomies of attributes may offer some help, but their disadvantages often outweigh their advantages.
-	You may need to design or analyze a system for a “new” quality attribute. While this may be challenging, it is doable.



## Patterns and Tactics
Intended Learning Outcomes
-	By the end of this lesson you will be able to:
    -	Understand how patterns achieve quality attributes
    -	Understand relation between tactics and patterns
    -	Know how to use tactics together


What is a Pattern?
-	An architecture pattern is a package of design decisions that is found repeatedly in practice
-	has known properties that permits reuse, and describes a class of architectures

Architectural Patterns
-	Module patterns
    -	Layered pattern
-	Component-and-Connector patterns
    -	Broker pattern
    -	Model-View-Controller pattern
    -	Pipe-and-Filter pattern
    -	Client-Server pattern
    -	Peer-to-Peer pattern
    -	Service-Oriented Architecture (SOA) pattern
    -	Publish-Subscribe pattern
    -	Shared-Data pattern
-	Allocation patterns
    -	Map-Reduce pattern
    -	Multi-tier Pattern


Layer Pattern
-	Context: Modules of the system may be independently developed and maintained.
-	Problem: To minimize the interaction among the different development organizations, and support portability, modifiability, and reuse.

Layer Pattern
-	Solution: the layered pattern divides the software into units called layers. 
-	Each layer is a grouping of modules that offers a cohesive set of services. 
-	Each layer is exposed through a public interface. 
-	The usage must be unidirectional. 

Layer Pattern Example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507190101.png)

Layer Pattern Solution
-	The layered pattern defines layers and a unidirectional allowed-to-use relation among the layers. 
-	Elements: Layer, a kind of module. 
-	Relations: Allowed to use. 
-	Constraints: 
    -	Every piece of software is allocated to exactly one layer.
    -	There are at least two layers 
    -	The allowed-to-use relations should not be circular
-	Weaknesses: 
    -	The addition of layers adds up cost and complexity to a system.
    -	Layers contribute a performance penalty.

Three layered applications
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507190255.png)


Architectural Patterns
-	Module patterns
    -	Layered pattern
-	Component-and-Connector patterns
    -	Broker pattern
    -	Model-View-Controller pattern
    -	Pipe-and-Filter pattern
    -	Client-Server pattern
    -	Peer-to-Peer pattern
    -	Service-Oriented Architecture (SOA) pattern
    -	Publish-Subscribe pattern
    -	Shared-Data pattern
-	Allocation patterns
    -	Map-Reduce pattern
    -	Multi-tier Pattern

Architectural Patterns
-	Component-and-Connector patterns
-	Broker pattern
-	Model-View-Controller pattern
-	Pipe-and-Filter pattern
-	Client-Server pattern
-	Peer-to-Peer pattern
-	Service-Oriented Architecture (SOA) pattern
-	Publish-Subscribe pattern
-	Shared-Data pattern


Broker Pattern
-	Context: Many systems are constructed from a collection of services distributed across multiple servers
-	Problem: How do we structure distributed software so that service users do not need to know the nature and location of service providers?
-	Solution: The broker pattern separates clients from providers servers by inserting an intermediary, called a broker. 
-	When a client needs a service, it queries a broker via a service interface. The broker then forwards the client’s service request to a server, which processes the request. 

Broker Solution 
-	Overview: The broker pattern defines a runtime component, called a broker, that mediates the communication between a number of clients and servers.
-	Elements: 
    -	Client, a requester of services
    -	Server, a provider of services
    -	Broker, an intermediary that locates an appropriate server to fulfill a client’s request, forwards the request to the server, and returns the results to the client

Broker Solution
-	Constraints: The client can only attach to a broker. The server can only attach to a broker.
-	Weaknesses: 
    -	Brokers add latency between clients and servers, and it may be a communication bottleneck.
    -	….

**Consider there is one broker that mediates the communication between a large number of clients and servers. What happens when the broker fails?**
- Nothing happens, the clients can start communicating directly with the servers
- All the clients and server are not able to communicate in this situation

Broker Solution
-	Constraints: The client can only attach to a broker. The server can only attach to a broker.
-	Weaknesses: 
    -	Brokers add latency between clients and servers, and it may be a communication bottleneck.
    -	The broker can be a single point of failure (this means if just this single component fails, the whole system fails)
    -	A broker may be a target for security attacks.

Architectural Patterns
-	Component-and-Connector patterns
-	Broker pattern
-	Model-View-Controller pattern
-	Pipe-and-Filter pattern
-	Client-Server pattern
-	Peer-to-Peer pattern
-	Service-Oriented Architecture (SOA) pattern
-	Publish-Subscribe pattern
-	Shared-Data pattern

Model-View-Controller Pattern
-	Context: User interface software is the most frequently modified portion of an interactive application. 
-	Problem: How can user interface functionality be kept separate from application functionality and yet still be responsive to user input, or to changes in the underlying application’s data? 
-	And how can multiple views of the user interface be created, maintained, and coordinated when the underlying application data changes?

Model-View-Controller Pattern
-	Solution: The model-view-controller (MVC) pattern separates application functionality into three kinds of components:
    -	A model, which contains the application’s data
    -	A view, which displays some portion of the underlying data and interacts with the user
    -	A controller, which mediates between the model and the view and manages the notifications of state changes

MVC Example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507192334.png)

MVC Solution
-	Elements: the model , view, and controller
-	Relations: The notifies relation connects instances of model, view, and controller, notifying elements of relevant state changes.
-	Constraints: 
    -	There must be at least one instance for each of model, view, and controller.
    -	The model component should not interact directly with the controller.
-	Weaknesses:
    -	The complexity may not be worth it for simple user interfaces.

Architectural Patterns
-	Component-and-Connector patterns
    -	Broker pattern
    -	Model-View-Controller pattern
    -	Pipe-and-Filter pattern
    -	Client-Server pattern
    -	Peer-to-Peer pattern
    -	Service-Oriented Architecture (SOA) pattern
    -	Publish-Subscribe pattern
    -	Shared-Data pattern

Pipe and Filter Pattern
-	Context: Streaming data processing
-	Problem: How to speed up the data processing? 
-	Solution: Data arrives at a filter’s input port, is transformed, and then is passed via its output port through a pipe to the next filter.
-	A single filter can consume data from, or produce data to, one or more ports.
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507192511.png)

Pipe and Filter Solution
-	Elements: 
    -	Filter, which is a component that transforms data read on its input port to data written on its output port. 
    -	Pipe, which is a connector that conveys data from a filter’s output port to another filter’s input port. 
-	Relations: The attachment relation associates the output of filters with the input of pipes and vice versa.
-	Constraints:
    -	Connected filters must agree on the type of data being passed along the connecting pipe.

Architectural Patterns
-	Component-and-Connector patterns
-	Broker pattern
-	Model-View-Controller pattern
-	Pipe-and-Filter pattern
-	Client-Server pattern
-	Peer-to-Peer pattern
-	Service-Oriented Architecture (SOA) pattern
-	Publish-Subscribe pattern
-	Shared-Data pattern


Client-Server Pattern
-	Context: There are shared resources and services that large numbers of distributed clients wish to access, and for which we wish to control access or quality of service.
-	Problem: To improve scalability and availability by centralizing the control of these resources and services.
-	Solution: Clients interact by requesting services of servers. 
-	Some components may act as both clients and servers. There may be one central server or multiple distributed ones.

Client-Server Example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507192750.png)

Client-Server Solution
-	Elements: 
    -	Client, a component that invokes services of a server component.
    -	Server: a component that provides services to clients. Servers have ports that describe the services they provide. 
    -	Request/reply connector: a data connector employing a request/reply protocol. Important characteristics include whether the calls are local or remote, and whether data is encrypted.

Client-Server Solution
-	Relations: The attachment relation associates clients with servers.
-	Constraints: 
    -	Clients are connected to servers through request/reply connectors.
    -	Server components can be clients to other servers.
-	Weaknesses: 
    -	Server can be a performance bottleneck.
    -	Server can be a single point of failure.
    -	Decisions about where to locate functionality (in the client or in the server) are often complex and costly to change after a system has been built.

Architectural Patterns
-	Component-and-Connector patterns
-	Broker pattern
-	Model-View-Controller pattern
-	Pipe-and-Filter pattern
-	Client-Server pattern
-	Peer-to-Peer pattern
-	Service-Oriented Architecture (SOA) pattern
-	Publish-Subscribe pattern
-	Shared-Data pattern


Peer-to-Peer Pattern
-	Context: Distributed computational entities need to cooperate and collaborate to provide a service to a distributed community of users.
-	Problem: How can a set of “equal” distributed computational entities be connected to each other via a common protocol, such that they can organize and share their services with high availability and scalability?
-	Solution: In the peer-to-peer (P2P) pattern, components directly interact as peers. All peers are “equal”. 
-	Peer-to-peer communication is typically a request/reply interaction without the asymmetry found in the client-server pattern.

Peer-to-Peer Solution
-	Elements: 
    -	Peer, which is an independent component running on a network node.
    -	Request/reply connector, which is used to connect to the peer network.
-	Relations: The relation associates peers with their connectors. Attachments may change at runtime.


Peer-to-Peer Solution
-	Constraints:
    -	The number of allowable attachments to any given peer
    -	The number of hops used for searching for a peer
    -	Which peers know about which other peers
    -	Some P2P networks are organized with star topologies, in which peers only connect to supernodes.
-	Weaknesses: 
    -	Managing data consistency, data/service availability, backup, and recovery are all more complex.
    -	Small peer-to-peer systems may not be able to achieve quality goals such as performance and availability.

Architectural Patterns
-	Component-and-Connector patterns
    -	Broker pattern
    -	Model-View-Controller pattern
    -	Pipe-and-Filter pattern
    -	Client-Server pattern
    -	Peer-to-Peer pattern
    -	Service-Oriented Architecture (SOA) pattern
    -	Publish-Subscribe pattern
    -	Shared-Data pattern

Service Oriented Architecture Pattern
-	Context: Service consumers need to be able to understand and use services without any knowledge of their implementation.
-	Problem: How can we support interoperability of distributed components running on different platforms and written in different implementation languages, provided by different organizations, and distributed across the Internet? 
-	Solution: The service-oriented architecture (SOA) pattern describes a collection of distributed components that provide and/or consume services.

Service Oriented Architecture Solution - 1
-	Elements: 
    -	Components:
        -	Service providers, which provide one or more services through published interfaces. 
        -	Service consumers, which invoke services directly or through an intermediary.
    -	Enterprise Service Bus (ESB), which is an intermediary element that can route and transform messages between service providers and consumers.
    -	Registry of services, which may be used by providers to register their services and by consumers to discover services at runtime.

Service Oriented Architecture Solution - 2
-	Connectors:
    -	SOAP connector, which uses the SOAP protocol for synchronous communication between web services, typically over HTTP.
    -	REST connector, which relies on the basic request/reply operations of the HTTP protocol.
    -	Asynchronous messaging connector, which uses a messaging system to offer point-to-point or publish-subscribe asynchronous message exchanges.

Service Oriented Architecture Solution - 3
-	Constraints: Service consumers are connected to service providers, but intermediary components (e.g., ESB, registry) may be used.
-	Weaknesses: 
    -	You don’t control the evolution of independent services.
    -	There is a performance overhead associated with the middleware, which may be performance bottlenecks, and typically do not provide performance guarantees.

Architectural Patterns
-	Component-and-Connector patterns
    -	Broker pattern
    -	Model-View-Controller pattern
    -	Pipe-and-Filter pattern
    -	Client-Server pattern
    -	Peer-to-Peer pattern
    -	Service-Oriented Architecture (SOA) pattern
    -	Publish-Subscribe pattern
    -	Shared-Data pattern

Publish-Subscribe Pattern
-	Context: The precise number and nature of the data producers and consumers are not predetermined or fixed.
-	Problem: To transmit messages among the producers and consumers so they are unaware of each other’s identity, or even their existence?

Publish-Subscribe Example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507193606.png)

Architectural Patterns
-	Component-and-Connector patterns
    -	Broker pattern
    -	Model-View-Controller pattern
    -	Pipe-and-Filter pattern
    -	Client-Server pattern
    -	Peer-to-Peer pattern
    -	Service-Oriented Architecture (SOA) pattern
    -	Publish-Subscribe pattern
    -	Shared-Data pattern

Shared-Data Pattern
-	Context: Various computational components need to share and manipulate large amounts of data. This data does not belong solely to any one of those components.
-	Problem: How can systems store and manipulate persistent data that is accessed by multiple independent components?
-	Solution: In the shared-data pattern, interaction is dominated by the exchange of persistent data between multiple data accessors and at least one shared-data store. 
-	Exchange may be initiated by the accessors or the data store. The connector type is data reading and writing. 

Shared Data Solution - 1
-	Communication between data accessors is mediated by a shared data store. 
-	Data is made persistent by the data store.
-	Elements:
    -	Shared-data store. Concerns include types of data stored, data distribution, and number of accessors permitted.
    -	Data accessor
    -	Data reading and writing connector. 

Shared Data Solution - 2
-	Constraints: Data accessors interact only with the data store(s).
-	Weaknesses: 
    -	The shared-data store may be a performance bottleneck.
    -	The shared-data store may be a single point of failure.
    -	Producers and consumers of data may be tightly coupled. E.g., concurrency control is needed in database

Google File System (GFS)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507193729.png)

Architectural Patterns
-	Module patterns
    -	Layered pattern
-	Component-and-Connector patterns
    -	Broker pattern
    -	Model-View-Controller pattern
    -	Pipe-and-Filter pattern
    -	Client-Server pattern
    -	Peer-to-Peer pattern
    -	Service-Oriented Architecture (SOA) pattern
    -	Publish-Subscribe pattern
    -	Shared-Data pattern
-	Allocation patterns
    -	Map-Reduce pattern
    -	Multi-tier Pattern

MapReduce
-	Origin from Google, [OSDI’04]
-	A simple programming model 
-	Functional model
-	For large-scale data processing
    -	Exploits large set of commodity computers
    -	Executes process in distributed manner
    -	Offers high availability

Architectural Patterns
-	Module patterns
    -	Layered pattern
-	Component-and-Connector patterns
    -	Broker pattern
    -	Model-View-Controller pattern
    -	Pipe-and-Filter pattern
    -	Client-Server pattern
    -	Peer-to-Peer pattern
    -	Service-Oriented Architecture (SOA) pattern
    -	Publish-Subscribe pattern
    -	Shared-Data pattern
-	Allocation patterns
    -	Map-Reduce pattern
    -	Multi-tier Pattern

Multi-Tier Pattern
-	The execution structures of many systems are organized as a set of logical groupings of software and hardware. 
-	Each grouping is termed a tier. 
-	Layer v.s. tier ?

Multi-Tier Example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507193914.png)

Which of these patterns you used in your projects or studied in other courses?
-	Module patterns
    -	Layered pattern
-	Component-and-Connector patterns
    -	Broker pattern
    -	Model-View-Controller pattern
    -	Pipe-and-Filter pattern
    -	Client-Server pattern
    -	Peer-to-Peer pattern
    -	Service-Oriented Architecture (SOA) pattern
    -	Publish-Subscribe pattern
    -	Shared-Data pattern
-	Allocation patterns
    -	Map-Reduce pattern
    -	Multi-tier Pattern


-	In this session we have studied some useful architectural patterns
-	Earlier, we have studied tactics for different quality attributes
-	Let us see what are the relationships between them


Relationships Between Tactics and Patterns
-	Patterns are built from tactics; if a pattern is a molecule, a tactic is an atom.
-	For example, MVC pattern utilizes the tactics:
    -	Increase semantic coherence
    -	Encapsulation
    -	Use an intermediary

Relationships Between Tactics and Patterns
-	Tactics Augment Patterns 
    -	Patterns solve a specific problem but may have weaknesses with respect to other qualities.

Relationships Between Tactics and Patterns
-	Tactics Augment Patterns 
    -	Patterns solve a specific problem but may have weaknesses with respect to other qualities.
    -	Consider the broker pattern
        -	May have performance bottlenecks
        -	May have a single point of failure

Example
-	You decided to use the broker pattern in the system you are designing. 
-	The system also has high availability requirements. 
-	You are aware that one of the weaknesses of the broker pattern is that the broker may become a single point of failure, therefore the availability might be compromised.
-	How to solve this problem?

**You decided to use the broker pattern in the system you are designing. The system also has high availability requirements. You are aware that one of the weaknesses of the broker pattern is that the broker may become a single point of failure, therefore the availability might be compromised. How to solve this problem?**
- There is nothing we can do
- **We can augment the broker pattern by using some tactics for availability**
- Use another pattern


Relationships Between Tactics and Patterns
-	Tactics Augment Patterns 
    -	Patterns solve a specific problem but may have weaknesses with respect to other qualities.
    -	Consider the broker pattern
        -	May have performance bottlenecks
        -	May have a single point of failure
    -	We can solve these problems by using tactics such as
        -	Increase resources will help performance
        -	Maintain multiple copies will help availability


-	We have just studied the relationships between patterns and tactics
-	The next question we will study is what are the interactions between the tactics? How do we use tactics together?

Tactics and Interactions
-	Each tactic has pluses (its reason for being) and minuses – side effects.
-	Use of tactics can help alleviate the minuses.
-	But nothing is free…

Tactics and Interactions - example
- Assume we want to design a system with high availability requirements
- We will use a common tactic for detecting faults - Ping/Echo. 

Tactics and Interactions - example
- However, common side-effects of Ping/Echo are:
    -	security: how to prevent a ping flood attack?
    -	performance: how to ensure that the performance overhead of ping/echo is small?
    -	modifiability: how to add ping/echo to the existing architecture?

Tactics and Interactions  - example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507194650.png)

Tactics and Interactions - example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507194707.png)

**What is the next step we can take?**
- There is nothing we can do
- **Use tactics for performance**


Tactics and Interactions - example
- A tactic to address the performance side-effect that we will use is “Increase Available Resources”.

Tactics and Interactions - example
- Common side effects of Increase Available Resources are:
    -	cost: increased resources cost more
    -	performance: how to utilize the increase resources efficiently?

Tactics and Interactions - example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507195159.png)

Tactics and Interactions - example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507195220.png)

Tactics and Interactions - example
- A tactic to address the efficient use of resources side-effect is “Scheduling Policy”.

Tactics and Interactions - example
- However, common side effects of Scheduling Policy are:
    -	modifiability: how to add the scheduling policy to the existing architecture
    -	modifiability: how to change the scheduling policy in the future?

Tactics and Interactions - example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507195315.png)

Tactics and Interactions - example
- A tactic to address the addition of the scheduler to the system is “Use an Intermediary”.
- Common side effects of Use an Intermediary are:
    -	modifiability: how to ensure that all communication passes through the intermediary?

How Does This Process End?
-	Each use of tactic introduces new concerns.
-	Each new concern causes new tactics to be added.
-	Are we in an infinite progression?

How Does This Process End?
-	Each use of tactic introduces new concerns.
-	Each new concern causes new tactics to be added.
-	Are we in an infinite progression?
-	No.  Eventually the side-effects of each tactic become small enough to ignore. 

Summary
-	An architectural pattern
    -	is a package of design decisions that is found repeatedly in practice,
    -	has known properties that permit reuse, and
    -	describes a class of architectures.
-	Tactics are simpler than patterns
-	Patterns are underspecified with respect to real systems so they have to be augmented with tactics.
    -	Augmentation ends when requirements for a specific system are satisfied.

## Quality Attribute Modelling and Analysis
Intended Learning Outcomes
-	By the end of this lesson you will be able to:
    -	Model architectures to enable quality attribute analysis
    -	Use quality attribute checklists 
    -	Generate and carry out a thought experiment to perform early analysis
    -	Analyse architectures using alternative ways: experiments, simulations and prototypes
    -	Analyse at different stages of the life cycle 	


How do we know the quality attributes of a software?
-	Analytic model
-	Experiment and measurement
-	Simulations

Modeling Architectures to Enable Quality Attribute Analysis 

-	Some quality attributes, most notably performance and availability, have well-understood, time-tested analytic models that can be used to assist in an analysis. 
-	By analytic model, we mean one that supports quantitative analysis. 
-	Let us first consider performance.

Performance Models
-	Parameters: arrival rate of events, chosen queuing discipline, chosen scheduling algorithm, service time for events, network topology,  network bandwidth, routing algorithm chosen
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507200049.png)

Allocation Model for MVC
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507200127.png)


Queuing Model for MVC
-	Arrivals
-	View sends requests to Controller
-	Actions returned to View
- Actions returned to model
- Model sends actions to View

Parameters
-	To solve a queuing model for MVC performance, the following parameters must be known or estimated:
    -	The frequency of arrivals from outside the system
    -	The queuing discipline used at the view queue
    -	The time to process a message within the view
    -	The number and size of messages that the view sends to the controller
    -	The bandwidth of the network that connects the view and the controller
    -	The queuing discipline used by the controller
    -	The time to process a message within the controller
    -	The number and size of messages that the controller sends back to the view
    -	The bandwidth of the network from the controller to the view
    -	The number and size of messages that the controller sends to the model
    -	The queuing discipline used by the model
    -	The time to process a message within the model
    -	The number and size of messages the model sends to the view
    -	The bandwidth of the network connecting the model and the view
    Parameters
    -	To solve a queuing model for MVC performance, the following parameters must be known or estimated:
    -	The frequency of arrivals from outside the system
    -	The queuing discipline used at the view queue
    -	The time to process a message within the view
    -	The number and size of messages that the view sends to the controller
    -	The bandwidth of the network that connects the view and the controller
    -	The queuing discipline used by the controller
    -	The time to process a message within the controller
    -	The number and size of messages that the controller sends back to the view
    -	The bandwidth of the network from the controller to the view
    -	The number and size of messages that the controller sends to the model
    -	The queuing discipline used by the model
    -	The time to process a message within the model
    -	The number and size of messages the model sends to the view
    -	The bandwidth of the network connecting the model and the view

Cost/benefit of Performance Modeling
-	Cost: determining the parameters previously mentioned
-	Benefit: estimate of the latency
-	The more accurately the parameters can be estimated, the better the predication of latency.
-	This is worthwhile when latency is important and questionable.
-	This is not worthwhile when it is obvious there is sufficient capacity to satisfy the demand.

Availability Modeling
-	Another quality attribute with a well-understood analytic framework is availability. 
-	Modeling an architecture for availability—or to put it more carefully, modeling an architecture to determine the availability of a system based on that architecture—is a matter of determining the failure rates and the recovery times of the components.

Example
-	Just as for performance, to model an architecture for availability, we need an architecture to analyze.
-	Suppose we want to increase the availability of a system that uses the Broker pattern, by applying redundancy tactics.  

Availability Modeling
-	Three different tactics for increasing the availability of the broker are:
    -	active redundancy (hot spare)
    -	passive redundancy (warm spare)
    -	spare (cold spare).

Making Broker More Available
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507202005.png)

Applying Probabilities to Tactics
-	Using probabilities to model different tactics
    -	When two events A and B are independent, the probability that A or B will occur is the sum of the probability of each event: P(A or B) = P(A)+ P(B).
    -	When two events A and B are independent, the probability of both occurring is P(A and B) = P(A) - P(B).
    -	When two events A and B are dependent, the probability of both occurring is P(A and B) = P(A) - P(B|A), where the last term means “the probability of B occurring, given that A occurs.”

Passive Redundancy
-	Assume 
    -	failure of a component (primary or backup) is independent of the failure of its counterpart 
    -	assume failure probability of both is the same:  P(F)
-	Then probability that both will fail is: $1 - P(F)^2$
-	Can also estimate probability of failure given  other tactics.
-	Then given a cost of implementing appropriate tactic we can do cost/benefit analysis

Calculated Availability for an Availability-Enhanced Broker
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507202212.png)

Maturity of Quality Attribute Models
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507202255.png)

Quality Attribute Checklists
-	A quality attribute checklist provides a means of:
    -	Checking requirements.  Do the requirements capture all of the nuances of a particular quality attribute?
    -	Auditing. Does the design satisfy all of the aspects necessary for a certification process.

Security Checklists
-	Security checklists are common.
    -	Vendors who accept credit cards should conform to the PCI (Personal Credit Information) standard
    -	Electricity producers have security checklists to prevent attacks on critical infrastructure
-	Checklists have both:
    -	Product requirements. E.g. the PCI checklist states that the security code on the back of the credit card should never be stored.
    -	Process requirements. E.g. patches should be applied promptly and there should be someone who has the organizational responsibility to ensure that they are.

Thought Experiments  
-	A thought experiment is mentally or verbally working through a particular scenario.
    -	Commonly done by the architect during design to explore alternatives. 
    -	Also done during evaluation/documentation to convince third parties of the wisdom of particular design choices

Thought Experiment Steps
-	Enumerate the steps of a use case
-	At each step, ask {yourself, the architect}
    -	What mechanism is being implemented to support the achievement of which particular quality requirement?
    -	Does this mechanism hinder the achievement of other quality attribute requirements?
-	Record problems for later deeper analysis or prototype building


Back-of-the-Envelope Analysis
-	Analysis does not need to be precise or detailed.
-	Rough analysis serves for many purposes. E.g. “the volume of traffic generated by this source should be well within the bounds handled by modern infrastructure”
-	Only do deeper analysis for questionable areas or important requirements.

Experiments, Simulations, and Prototypes
-	Many tools can help perform experiments to determine behavior of a design
    -	Request generators can create synthetic loads to test scalability
    -	Monitors can perform non-intrusive resource usage detection.
-	These depend on having a partial or prototype implementation.
    -	Prototype alternatives for the most important decisions
    -	If possible, implement prototype in a fashion so that some of it can be re-used.
    -	Fault injection tools can induce faults to determine response of system under failure conditions.

Simulations
-	Event based simulators exist that can be used to simulate behavior of system under various loads
    -	Must create the simulation.
    -	Must have a variety of different loads and responses to check for. 

Analysis During Requirements and Design 
-	Different types of analysis are done at different stages of the life cycle
-	Requirements:
    -	Analytic models/back of the envelope analysis can help capacity planning
    -	Checklists can help ensure capture correct set of requirements
-	Design:
    -	Prototypes can help explore design options
    -	Analytic models or simulation can help understand potential bottlenecks
    -	Checklists can help determine if used a correct mechanism

Analysis During Implementation or Fielding
-	Experiments and synthetic load tests can be used during the implementation process or after fielding
-	Monitors can be used after fielding to determine actual behavior and find bottlenecks.

Analysis at Different Stages of the Lifecycle
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507202716.png)

Summary
-	Analysis is always a cost/benefit activity
-	Cost is measure of creating and executing the analysis models and tools
-	Benefit depends on
    -	Accuracy of analysis
    -	Importance of what is being analyzed
-	Analysis can be done through
    -	Models for some attributes
    -	Measurement
    -	Simulations
    -	Prototypes
    -	Thought experiments & Back-of-the-envelope analysis


## Designing for architecturally significant requirements
Intended Learning Outcomes
-	By the end of this lesson you will be able to:
    -	understand the concept of ASR 	
    -	identify and capture ASR 	
    -	design for ASR	

Architecturally Significant Requirement
-	Architectures exist to build systems that satisfy requirements. 
-	But, to an architect, not all requirements are created equal. 
-	An Architecturally Significant Requirement (ASR) is a requirement that will have a profound effect on the architecture.
-	How do we find those?

Approaches to Capture ASRs
-	From Requirements Document
-	By Interviewing Stakeholders
-	By Understanding the Business Goals
-	In Utility Tree

ASRs and Requirements Documents
-	An obvious location to look for candidate ASRs is in the requirements documents
-	Requirements should be in requirements documents! 
-	Unfortunately, this is not usually the case.

Don’t Get Your Hopes Up
-	Many projects don’t create or maintain the detailed, high-quality requirements documents.
-	Standard requirements pay more attention to functionality than quality attributes.
-	The architecture is driven by quality attribute requirements rather than functionalities
-	Most requirements specification does not affect the architecture.

Don’t Get Your Hopes Up
-	Quality attributes are often captured poorly, e.g.
    -	“The system shall be modular” 
    -	“The system shall exhibit high usability” 
    -	“The system shall meet users’ performance expectations”
-	Much of what is useful to an archit ect is not in even the best requirements document
    -	ASRs often derive from business goals in the development organization itself


Gathering ASRs from Stakeholders
-	Stakeholders often have no idea what QAs they want in a system
    -	if you insist on quantitative QA requirements, you’re likely to get numbers that are arbitrary. 
    -	at least some of those requirements will be very difficult to satisfy. 
-	Architects often have very good ideas about what QAs are reasonable to provide. 
-	Interviewing the stakeholders is the surest way to learn what they know and need. 

**“The system shall meet users’ performance expectations”- this is an example of a poorly captured quality attribute requirement. Do you know a tool to help us express quality requirements in a better way?**
- No, there is no better way.
- There should be a better way, but I do not know how
- **Yes – use a quality attribute scenario and make sure all 6 parts are included**


Gathering ASRs from Stakeholders
-	The results of stakeholder interviews should include
    -	a list of architectural drivers 
    -	a set of QA scenarios that the stakeholders (as a group) prioritized. 
-	This information can be used to:
    -	refine system and software requirements
    -	understand and clarify the system’s architectural drivers
    -	provide rationale for why the architect subsequently made certain design decisions
    -	guide the development of prototypes and simulations
    -	influence the order in which the architecture is developed.

Quality Attribute Scenario: Example
-	Our vehicle information system sends our current location to the traffic monitoring system.
-	The traffic monitoring system combines our location with other information, overlays this information on a Google Map, and broadcasts it.
-	Our location information is correctly included with a probability of 99.9%. 

Quality Attribute Scenario: Example
-	The developer wishes to change the user interface by modifying the code at design time. The modifications are made with no side effects within three hours.
    -	Stimulus – Wishes to change UI
    -	Artifact – Code
    -	Environment: Design time
    -	Response – Change made
    -	Response measure – No side effects in three hours
    -	Source - Developer

Quality Attribute Scenario: Example
-	Users initiate transactions under normal operations. The system processes the transactions with an average latency of two seconds.
    -	Stimulus: transaction arrivals
    -	Source: users
    -	Artifact: the system
    -	Response: process the transactions
    -	Response measure: average latency of two seconds
    -	Environment: under normal operation

Quality Attribute Scenario: Example
-	A disgruntled employee from a remote location attempts to modify the pay rate table during normal operations. The system maintains an audit trail and the correct data is restored within a day.
    -	Stimulus: unauthorized attempts to modify the pay rate table
    -	Stimulus source: a disgruntled employee
    -	Artifact: the system with pay rate table 
    -	Environment: during normal operation
    -	Response: maintains an audit trail
    -	Response measure: correct data is restored within a day

Quality Attribute Scenario: Example
-	The user downloads a new application and is using it productively after two minutes of experimentation.
    -	Source: user
    -	Stimulus: download a new application
    -	Artifact: system
    -	Environment: runtime
    -	Response: user uses application productively
    -	Response measure: within two minutes of experimentation

Quality Attribute Workshop
-	The QAW is a facilitated, stakeholder-focused method to generate, prioritize, and refine quality attribute scenarios before the software architecture is completed. 

QAW Steps
-	Step 1: QAW Presentation and Introductions. 
    -	QAW facilitators describe the motivation for the QAW and explain each step of the QAW.
-	Step 2: Business/Mission Presentation. 
    -	The stakeholder representing the business concerns presents the system’s business context, broad functional requirements, constraints, and known quality attribute requirements. 
    -	The quality attributes will be derived largely from the business/mission needs
-	Step 3: Architectural Plan Presentation.
    -	The architect will present the system architectural plans
    -	This lets stakeholders know the current architectural thinking
-	Step 4: Identification of Architectural Drivers.
    -	The facilitators will share their list of key architectural drivers assembled during Steps 2 and 3, 
    -	Architectural drivers includes overall requirements, business drivers, constraints, and quality attributes. 
    -	ask the stakeholders for clarifications, additions, deletions, and corrections, and achieve a consensus on the architectural drivers 
-	Step 5: Scenario Brainstorming. 
    -	Each stakeholder expresses a scenario representing his or her concerns with respect to the system. 
    -	Facilitators ensure that each scenario has an explicit stimulus and response. 
    -	Make at least one representative scenario for each architectural driver listed in Step 4.
-	Step 6: Scenario Consolidation. 
    -	Similar scenarios are consolidated where reasonable. 
-	Step 7: Scenario Prioritization. 
    -	Allocating each stakeholder a number of votes equal to 30 percent of the total number of scenarios
    -	Each stakeholder allocate their votes to scenario
-	Step 8: Scenario Refinement. 
    -	The top scenarios are refined and elaborated. 
    -	Facilitators help the stakeholders put the scenarios in the six-part scenario form

Capturing ASRs in a Utility Tree
An ASR must have the following characteristics:
-	A profound impact on the architecture 
    -	Including this requirement will very likely result in a different architecture than if it were not included.
-	A high business or mission value 
    -	If the architecture is going to satisfy this requirement it must be of high value to important stakeholders.

Utility Tree
-	A way to record ASRs all in one place.
-	Establishes priority of each ASR in terms of 
    -	Impact on architecture
    -	Business or mission value
-	ASRs are captured as scenarios.
-	Root of tree is placeholder node called “Utility”.
-	Second level of tree contains broad QA categories.
-	Third level of tree refines those categories.
-	Leaf nodes are the concrete quality attribute scenarios 

Utility Tree: Next Steps
-	ASRs that rate a (H,H) rating are the ones that deserve the most attention
    -	A very large number of these might be a cause for concern:  Is the system achievable?
-	Stakeholders can review the utility tree to make sure their concerns are addressed.

Tying the Methods Together
-	How should you employ requirements documents, stakeholder interviews, Quality Attribute Workshops, and utility trees together?
    -	If important stakeholders have been overlooked in the requirements-gathering process, use interviews or a QAW.
    -	Use a quality attribute utility tree as a repository for the scenarios produced by a QAW. 

Reminder
-	Next week, we will start practical exercises in which we will design parts of the architecture for a real system. 
-	In the first session, we will collect the ASR for this system and build a utility tree. 

Design Strategy
-	Decomposition
-	Designing to Architecturally Significant Requirements
-	Generate and Test
Decomposition
-	Architecture determines quality attributes
-	Important quality attributes are characteristics of the whole system.
-	Design begins with the whole system
    -	The whole system is decomposed into parts
    -	Each part may inherit all or part of the quality attribute requirements

Design Doesn’t Mean Green Field
-	If you are given components to be used in the final design, then the decomposition must accommodate those components.

Designing to Architecturally Significant Requirements
-	Remember architecturally significant requirements (ASRs)?
-	These are the requirements that you must satisfy with the design
    -	There are a small number of these
    -	They are the most important (by definition)
-	Two questions:
    -	What happens to the other requirement?
    -	Do I design for one ASR at a time or all at once?

What About Other Quality Requirements?
-	If your design does not satisfy a particular non ASR quality requirement
    -	Adjust your design so that the ASRs are still satisfied and so is this additional requirement or
    -	Weaken the additional requirement so that it can be satisfied either by the current design or by a modification of the current design or
    -	Change the priorities so that the one not satisfied becomes one of the ASRs or
    -	Declare the additional requirement non-satisfiable in conjunction with the ASRs.

How Many ASRs Simultaneously?
-	If you are inexperienced in design then design for the ASRs one at a time beginning with the most important.
-	As you gain experience, you will be able to design for multiple ASRs simultaneously.

Generate and Test
-	View the current design as a hypothesis.
-	Ask whether the current design satisfies the requirements (test)
-	If not, then generate a new hypothesis

Raises the Following Questions
-	Where does initial hypothesis come from?
-	How do I test a hypothesis?
-	How do I generate the next hypothesis?
-	When am I done?


Where Does the Initial Hypothesis Come From?
-	Existing systems
    -	Very few systems are completely constructed from the scratch
-	Frameworks
    -	A partial design that provides services that are common in particular domains, e.g., web applications, middleware
    -	A design framework may constrain communication to be via a broker, or publish-subscribe system




Where Does the Initial Hypothesis Come From?
-	Less desirable sources
    -	Patterns and tactics
    -	Design checklists
-	Why “less desirable”? 
    -	The less desirable ones do not cover all of the requirements. 
    -	They typically omit many of the quality attribute requirements.

How Do I Test a Hypothesis?
-	The analysis technique described previously 
-	Design checklists from quality attribute discussion.
-	Architecturally significant requirements

-	What is the output of the tests?
    -	List of requirements – either responsibilities or quality – not met by current design.

How Do I Generate the Next Hypothesis?
-	Add missing responsibilities.
-	Use tactics to adjust quality attribute behavior of hypothesis.
    -	The choice of tactics will depend on which quality attribute requirements are not met.
    -	Be mindful of the side effects of a tactic.

When Am I Done?
-	All ASRs are satisfied and/or…
-	You run out of budget for design activity
    -	In this case, use the best hypothesis so far and begin implementation
    -	To relax or eliminate the requirement
    -	To argue for more budget

Summary
-	Architectures are driven by Architecturally Significant Requirements (ASRs): 
    -	requirements that will have profound effects on the architecture. 
-	ASRs may be captured 
    -	from requirements documents, 
    -	by interviewing stakeholders, or 
    -	by conducting a Quality Attribute Workshop.
-	A useful representation of quality attribute requirements is in a utility tree. 
-	The utility tree helps to capture these requirements in a structured form.
-	Scenarios are prioritized.
-	This prioritized set defines your “marching orders” as an architect.
-	Designing Strategies
    -	Decomposition
    -	Designing to Architecturally Significant Requirements
    -	Generate and Test


## Designing and evaluating an architecture
Intended Learning Outcomes
-	By the end of this lesson you will be able to: 	
-	design a software architecture using ADD approach
-	evaluate an architecture using ATAM method

Design Strategy
-	Decomposition
-	Designing to Architecturally Significant Requirements
-	Generate and Test

The Attribute-Driven Design Method 
-	An iterative method. At each iteration you combine the 3 design strategies we studied earlier
    -	Choose a part of the system to design.
    -	Marshal all the architecturally significant requirements for that part.
    -	Generate and test a design for that part.
-	ADD does not result in a complete design
    -	Set of containers with responsibilities
    -	Interactions and information flow among containers
-	Does not produce an API for containers.

ADD Inputs
-	Requirements
    -	Functional, quality, constraints
-	A context description
    -	What are the boundary of the system being designed?
    -	What are the external systems, devices, users and environment conditions with which the system being designed must interact?

ADD Outputs
-	Architectural elements and their relationship
    -	Responsibility of elements
    -	Interactions
    -	Information flow among the elements


The Steps of ADD 
-	Choose an element of the system to design.
-	Identify the ASRs for the chosen element.
-	Generate a design solution for the chosen element.
-	Inventory remaining requirements and select the input for the next iteration.
-	Repeat steps 1–4 until all the ASRs have been satisfied.

Step 1: Choose an Element of the System to Design
-	For green field designs, the element chosen is usually the whole system.
-	For legacy designs, the element is the portion to be added.
-	After the first iteration:

Step 1 example
- We want to design a system for online travel agency
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507205247.png)

Step 1 example
- In iteration #1, we decide to apply SOA pattern (Service Oriented Architecture, where functions of the system will become services) 
- We decompose our system into necessary components based on SOA pattern 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507205355.png)

Step 1 example
- Then, we need to choose which part of the system to design next,
- In iteration #2, we choose to further design SOA infrastructure components element
- We decide to decompose it into 3 components
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507205446.png)

Step 1 example
- Then, we need to choose which part of the system to design next,
- In iteration #3, which element should we choose to design?
    - Refine one of the remaining SOA elements?
    - Refine one of the SOA infrastructure components?
    ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507205535.png)

Which Element Comes Next?
-	Two basic refinement strategies:
    -	Breadth first
    -	Depth first
-	Which one to choose?
-	If using new technology => depth first: explore the implications of using that technology.
-	If a team needs work => depth first: generate requirements for that team.
-	Otherwise => breadth first.

Step 2: Identify the ASRs for the Chosen Element
-	If the chosen element is the whole system, then use a utility tree (as described earlier).
-	If the chosen element is further down the decomposition tree, then generate a utility tree from the requirements for that element.

Step 3: Generate a Design Solution for the Chosen Element
-	Apply generate and test to the chosen element with its ASRs
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507205631.png)

Step 4
-	Inventory remaining requirements and select the input for the next iteration
-	We need to consider 3 kinds of requirements
    -	Functional requirements
    -	Quality attribute requirements
    -	Constraints

Step 4: Select the Input for the Next Iteration
-	For each functional requirement 
    -	Ensure that requirement has been satisfied.
    -	If not, then add responsibilities to satisfy the requirement.
        -	Add them to container with similar requirements
        -	If no such container, may need to create new one or add to container with dissimilar responsibilities (coherence)
        -	If container has too many requirements for a team, split it into two portions. Try to achieve loose coupling when splitting.

Quality Attribute Requirements 
-	If the quality attribute requirement has been satisfied, it does not need to be further considered.
-	If the quality attribute requirement has not been satisfied then either
    -	Delegate it to one of the child elements
    -	Split it among the child elements
-	If the quality attribute cannot be satisfied, see if it can be weakened. If it cannot be satisfied or weakened then it cannot be met.

Constraints
-	Constraints are treated as quality attribute requirements have been treated.
    -	Satisfied
    -	Delegated
    -	Split
    -	Unsatisfiable 

Repeat Steps 1–4 Until All ASRs are Satisfied
-	At end of step 4, each child element will have associated with it a set of: 
    -	functional requirements, 
    -	quality attribute requirements, and 
    -	constraints.
-	This sets up the child element for the next iteration of the method.

The Architecture Tradeoff Analysis Method
-	The Architecture Tradeoff Analysis Method (ATAM) has been used for over a decade to evaluate software architectures in domains ranging from automotive to financial to defense. 
-	The ATAM is designed so that 
    -	evaluators need not be familiar with the architecture or its business goals
    -	the system need not yet be constructed
    -	there may be a large number of stakeholders. 

Participants in the ATAM
-	The evaluation team
    -	3 to 5 people
    -	Competent, unbiased outsiders
-	Project decision makers 
    -	the project manager, the architect and the customer who bill for the development
-	Architecture stakeholders 
    -	developers, testers, integrators, maintainers, performance engineers, users, builders of systems 
    -	to articulate the specific quality attribute goals 
    -	12 to 15 stakeholders for a large enterprise-critical architecture 

ATAM Evaluation Team Roles
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507205856.png)

Primary Outputs of the ATAM
-	A set of risks and nonrisks 
    -	A risk is defined as an architectural decision that may lead to undesirable consequences in light of quality attribute requirements. 
    -	A nonrisk is an architectural decision that is deemed safe 
-	A set of risk themes
    -	examines the full set of risks to look for themes that identify system weaknesses in the architecture. 
    -	These risk themes will threaten the project’s business goals

Other Outputs of the ATAM
-	A concise presentation of the architecture  
-	Articulation of the business goals. 
-	Prioritized quality attribute requirements expressed as quality attribute scenarios. 
-	Mapping of architectural decisions to quality requirements.
-	A set of identified sensitivity and tradeoff points: architectural decisions that have a marked effect on one or more quality attributes. 



Phases of the ATAM
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507205950.png)

Step 1:  Present the ATAM
-	The evaluation leader describes the ATAM steps in brief and the outputs of the evaluation

Step 2: Present Business Drivers
-	The project decision maker (ideally the project manager or the system’s customer) presents a system overview from a business perspective 
-	The presentation should describe 
    -	important functions
    -	relevant technical, managerial, economic, or political constraints 
    -	business goals and context
    -	major stakeholders
    -	architectural drivers (architecturally significant requirements)

Step 3:  Present the Architecture
-	The lead architect makes a presentation describing the architecture
    -	technical constraints such as operating system, hardware, or middleware prescribed for use, and other systems with which the system must interact. 
    -	architectural approaches, i.e., patterns & tactics used to meet the requirements
-	Should convey the essence of the architecture and NOT go too deeply into the details

Step 4: Identify Architectural Approaches 
-	Understand its architectural approaches, especially patterns and tactics.
-	The evaluation team catalogs the patterns and tactics that have been identified. 
-	The list is publicly captured and will serve as the basis for later analysis

Step 5: Generate Utility Tree
-	The evaluation team works with the project decision makers to identify, prioritize, and refine the system’s most important quality attribute goals 
-	The quality attribute goals are articulated in detail via a quality attribute utility tree.
-	The scenarios are assigned a rank of importance (High, Medium, Low)

Step 6: Analyze Architectural Approaches
-	The evaluation team examines the highest-ranked scenarios one at a time; the architect is asked to explain how the architecture supports each one 
-	Evaluation team members probe for the architectural approaches used to carry out the scenario 
-	Evaluation team documents the relevant architectural decisions and identifies and catalogs their risks, nonrisks, and tradeoff points.   Examples:
    -	Risk:  The frequency of heartbeats affects the time in which the system can detect a failed component. 
    -	Tradeoff: The heartbeat frequency determines the time for detecting a fault. Higher frequency leads to better availability but consumes more processing time and communication bandwidth (potentially reducing performance). 

Phases of the ATAM
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507210106.png)

Step 7:  Brainstorm and Prioritize Scenarios
-	The purpose of scenario brainstorming is to understand what system success means for stakeholders 
-	Once the scenarios have been collected, they are prioritized by voting.
-	The list of prioritized scenarios is compared with those from the utility tree exercise. 
    -	If they agree, it indicates good alignment between the architect had in mind and what the stakeholders actually wanted. 
    -	If additional driving scenarios are discovered, this may itself be a risk. some disagreement between the stakeholders and the architect. 

Step 8: Analyze Architectural Approaches
-	In this step the evaluation team performs the same activities as in step 6

Step 9: Present Results
-	The evaluation team group risks into risk themes, based on some systemic deficiency. 
    -	For example, a group of risks about the system’s inability to function in the face of various hardware and/or software failures might lead to a risk theme about insufficient attention to providing high availability. 
-	For each risk theme, the evaluation team identifies which of the business drivers listed in step 2 are affected. 

Step 9: Present Results
-	The collected information from the evaluation is summarized and presented to stakeholders. 
-	The following outputs are presented:
    -	The architectural approaches documented
    -	The set of scenarios and their prioritization from the brainstorming
    -	The utility tree 
    -	The risks discovered
    -	The nonrisks documented
    -	The sensitivity points and tradeoff points found
    -	Risk themes and the business drivers threatened by each one

Lightweight Architectural Evaluation
-	An ATAM is a substantial undertaking 
    -	It requires some 20 to 30 person-days of effort from an evaluation team, plus even more for the architect and stakeholders. 
    -	Investing this amount of time makes sense on a large and costly project 
-	A Lightweight Architecture Evaluation method for smaller, less risky projects. 
    -	May take place in a single day, or even a half-day meeting. 
    -	May be carried out entirely by members internal to the organization. 
    -	Of course this may not probe the architecture as deeply.

Typical Agenda: 4-6 Hours
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507210256.png)

Summary
-	Architectures are driven by Architecturally Significant Requirements (ASRs): 
    -	requirements that will have profound effects on the architecture. 
-	ASRs may be captured 
    -	from requirements documents, 
    -	by interviewing stakeholders, or 
    -	by conducting a Quality Attribute Workshop.
-	Be mindful of the business goals of the organization. 
    -	Business goals can be expressed in a common, structured form and represented as scenarios. 

Summary
-	A useful representation of quality attribute requirements is in a utility tree. 
-	The utility tree helps to capture these requirements in a structured form.
-	Scenarios are prioritized.
-	This prioritized set defines your “marching orders” as an architect.


## Exercise 1 Capturing ASR in practice .
Intended Learning Outcomes
-	By the end of this lesson you will be able to: 	
-	Specify concrete scenarios for a real system
-	Create a utility tree for the real system	

Our system – ATM network
-	Please check our WeChat group – I have sent a document
-	The document in front of you describes the requirements for the system, including functional requirements and known quality attribute requirements

Activity: Scenario Brainstorming (1)
-	Based on the received document, express scenarios representing your concerns with respect to the system. 
-	Describe your scenarios in as many details as you can (do NOT use the concrete scenario form yet)
-	Pick one of the scenarios and submit as an answer to the question.

Activity: Scenario Brainstorming (2)
-	Read scenarios of other participants, while reading, you can write more scenarios 
    -	Your new scenarios can be inspired by what the other participant shared, but not the same
-	Submit a new scenario you have wrote, inspired by scenarios of other participants


Next steps 
-	Scenarios consolidation
    -	Participants discuss and consolidate the scenarios where reasonable. 
    -	Scenario Prioritization - voting 
        -	Each participants receives a number of votes equal to 30 percent of the total number of scenarios
        -	Each participant allocates their votes to scenarios
-	Refine and elaborate the top scenarios 
    -	Put the scenarios in the six-part scenario form (concrete scenario)

Specifying Quality Attribute Requirements
-	Stimulus. he stimulus is a condition that requires a response when it arrives at a system.
-	Source of stimulus. his is some entity (a human, a computer system, or any other actuator) that generated the stimulus.
-	Response. he response is the activity undertaken as the result of the arrival of the stimulus. 
-	Response measure. hen the response occurs, it should be measurable in some fashion so that the requirement can be tested. 
-	Environment. he stimulus occurs under certain conditions. The system may be in an overload condition or in normal operation, or some other relevant state.  
-	Artifact. his may be a collection of systems, the whole system, or some piece or pieces of it. Some artifact is stimulated. 


Quality Attribute Scenario: Example
-	Our vehicle information system sends our current location to the traffic monitoring system.
-	The traffic monitoring system combines our location with other information, overlays this information on a Google Map, and broadcasts it.
-	Our location information is correctly included with a probability of 99.9%. 

Quality Attribute Scenario: Example
-	The developer wishes to change the user interface by modifying the code at design time. The modifications are made with no side effects within three hours.
    -	Stimulus – Wishes to change UI
    -	Artifact – Code
    -	Environment: Design time
    -	Response – Change made
    -	Response measure – No side effects in three hours
    -	Source - Developer

Quality Attribute Scenario: Example
-	Users initiate transactions under normal operations. The system processes the transactions with an average latency of two seconds.
    -	Stimulus: transaction arrivals
    -	Source: users
    -	Artifact: the system
    -	Response: process the transactions
    -	Response measure: average latency of two seconds
    -	Environment: under normal operation

Quality Attribute Scenario: Example
-	A disgruntled employee from a remote location attempts to modify the pay rate table during normal operations. The system maintains an audit trail and the correct data is restored within a day.
    -	Stimulus: unauthorized attempts to modify the pay rate table
    -	Stimulus source: a disgruntled employee
    -	Artifact: the system with pay rate table 
    -	Environment: during normal operation
    -	Response: maintains an audit trail
    -	Response measure: correct data is restored within a day

Quality Attribute Scenario: Example
-	The user downloads a new application and is using it productively after two minutes of experimentation.
    -	Source: user
    -	Stimulus: download a new application
    -	Artifact: system
    -	Environment: runtime
    -	Response: user uses application productively
    -	Response measure: within two minutes of experimentation

Activity: Scenario Refinement
-	Refine and elaborate the top scenarios 
-	Put the scenarios in the six-part scenario form
-	Make sure you describe your requirement in one or 2 sentences, that contain all the 6 parts of the scenario form.
-	Pick one of the scenarios and submit as an answer to the question.

Next step 
-	Build a utility tree

Capturing ASRs in a Utility Tree
An ASR must have the following characteristics:
-	A profound impact on the architecture 
    -	Including this requirement will very likely result in a different architecture than if it were not included.
-	A high business or mission value 
    -	If the architecture is going to satisfy this requirement it must be of high value to important stakeholders.

Utility Tree Example (excerpt)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507212030.png)

Activity: Utility tree 
-	Create a utility tree for our system
    -	Organize your scenarios into quality attributes and attribute refinement
    -	You may need to spend some time to brainstorm some  more requirements, or to improve their description – what we have done in previous steps
-	Establish priority of each ASR in terms of 
    -	Impact on architecture
    -	Business or mission value

Next step
-	Review your Utility Tree 
    -	ASRs that rate a (H,H) rating are the ones that deserve the most attention
        -	Is the system achievable? Do we have too many of (H,H) ASRs?
    -	Review the utility tree to make sure your concerns are addressed.

Homework 1
-	Expand your Utility Tree for the automated teller machine network with at least 6 ASRs:
    -	Based on the document given in class and/or based on additional information (internet articles, interviews with the stakeholders)
        -	Describe what additional materials you used

-	Submission: 
    -	Deadline:  May 8th 
    -	Place: submit to Blackboard

## exercise2
Please comply with epidemic prevention and control policies 
-	Choose the seat that you will use during the whole semester and register your information in the system.
-	Make sure to take the same seat during every lecture.

Intended Learning Outcomes
-	By the end of this lesson you will be able to: 	
    -	Create a design for a real system using the ADD method employing and instantiating a pattern	

ASRs
-	We will add/expand the following ASRs to our utility tree
    -	Modifiability
    -	Performance

Modifiability
-	The system must be easily modified to take advantage of new platform capabilities (for example, it must not be tied to a single database or to a single kind of client hardware or software) and that it must be extensible to allow the addition of new functions and new business rules.

Modifiability scenarios
-	A developer wishes to add a new auditing business rule at design time and makes the modification, without affecting other functionality, in 10 person-days.
-	A developer wishes to change the relational schema to add a new view to the database, without affecting other functionality, in 30 person-days.
-	A system administrator wishes to employ a new database and makes the modification, without affecting other functionality, in 18 person-months.
-	A developer wishes to add a new function to a client menu, without side effects, in 15 person-days.
-	A developer needs to add a Web-based client to the system, without affecting the functionality of the existing ATM client, in 90 person-days.

Performance 
-	Latency of an operation (such as an ATM withdrawal)

Performance scenario  
-	“The user can withdraw a limit of $300 from an account that has sufficient funds in less than 10 seconds.” 
    -	There are two functional requirements and one performance requirement in this scenario. 
        -	One function is a withdrawal
        -	Another function is a limit (a constraint of $300 if it is in the account)
        -	Performance constraint/quality attribute - “less than 10 seconds” 

ADD method
-	The ADD method defines a software architecture by basing the design process on the quality attribute requirements of the system.
-	The ADD approach follows a recursive decomposition process where, at each stage in the decomposition, architectural tactics and patterns are selected to satisfy a chosen set of high-priority quality scenarios.

ADD:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507212344.png)

The Steps of ADD 
-	Choose an element of the system to design.
-	Identify the ASRs for the chosen element.
-	Generate a design solution for the chosen element.
-	Inventory remaining requirements and select the input for the next iteration.
-	Repeat steps 1–4 until all the ASRs have been satisfied.

Step 1: Choose an Element of the System to Design
-	For green field designs, the element chosen is usually the whole system.
-	For legacy designs, the element is the portion to be added.
-	After the first iteration:
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507212413.png)

Step 2: Identify the ASRs for the Chosen Element
-	If the chosen element is the whole system, then use a utility tree (as described earlier).
-	Let us first look at our modifiability scenarios

Modifiability scenarios
-	A developer wishes to add a new auditing business rule at design time and makes the modification, without affecting other functionality, in 10 person-days.
-	A developer wishes to change the relational schema to add a new view to the database, without affecting other functionality, in 30 person-days.
-	A system administrator wishes to employ a new database and makes the modification, without affecting other functionality, in 18 person-months.
-	A developer wishes to add a new function to a client menu, without side effects, in 15 person-days.
-	A developer needs to add a Web-based client to the system, without affecting the functionality of the existing ATM client, in 90 person-days.

Step 3: Generate a Design Solution for the Chosen Element
-	Apply generate and test to the chosen element with its ASRs
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507212453.png)

Activity: Use patterns and tactics
-	Based on the modifiability scenarios, generate a candidate design employing and instantiating a pattern. Which tactics are you going to use?


Tips (1)
-	In order to propose a candidate design we need to make these decisions
    -	What components do we need in the system?
    -	What will be the responsibilities of these components?

-	Read the modifiability scenarios carefully while keeping in mind this question: 
    -	How to assign responsibilities to elements so that the majority of changes to the system will affect a small number of those elements (ideally each change will affect just a single element)?

Tips (2)
-	Let’s read one of the requirements together, while keeping this question in mind
-	Scenario: A developer wishes to change the relational schema to add a new view to the database, without affecting other functionality, in 30 person-days.
-	Question: How to assign responsibilities to elements so that the majority of changes to the system will affect a small number of those elements (ideally each change will affect just a single element)?
-	Deciding to decompose our system into what kind of element will result in the change described in this scenario  affecting just a single element? What is the artifact in this scenario? Where does the developer wish to make a change? 
    -	– database
-	If we decide that one of our elements will be a database, then the change described in this scenario will affect just a single element (database) – therefore a database is a good candidate for one of the elements

Modifiability scenarios
-	A developer wishes to add a new auditing business rule at design time and makes the modification, without affecting other functionality, in 10 person-days.
-	A developer wishes to change the relational schema to add a new view to the database, without affecting other functionality, in 30 person-days.
-	A system administrator wishes to employ a new database and makes the modification, without affecting other functionality, in 18 person-months.
-	A developer wishes to add a new function to a client menu, without side effects, in 15 person-days.
-	A developer needs to add a Web-based client to the system, without affecting the functionality of the existing ATM client, in 90 person-days.
-	To achieve these modifiability requirements, one or more architectural tactics will need to be employed. 

Modifiability scenarios
-	A developer wishes to add a new auditing business rule at design time and makes the modification, without affecting other functionality, in 10 person-days.
-	A developer wishes to change the relational schema to add a new view to the database, without affecting other functionality, in 30 person-days.
-	A system administrator wishes to employ a new database and makes the modification, without affecting other functionality, in 18 person-months.
-	A developer wishes to add a new function to a client menu, without side effects, in 15 person-days.
-	A developer needs to add a Web-based client to the system, without affecting the functionality of the existing ATM client, in 90 person-days.
-	To achieve these modifiability requirements, one or more architectural tactics will need to be employed. 

Tactics – Reduce the coupling between modules
-	If modifiability is important, then you need to pay careful attention to assigning responsibilities to elements so that the majority of changes to the system will affect a small number of those elements (ideally each change will affect just a single element) 
-	This suggests that in our system, the business rules, database, and client should be localized into components
-	Based on experience of the architect, he/she will propose a candidate architecture using known patterns

Pattern: Multi-Tier Pattern
-	The execution structures are organized as a set of logical groupings of software and hardware. 
-	Let’s start with 3 tiers in our candidate architecture: 
    -	Business rules
    -	Database
    -	Client
-	Remember: design is an iterative process

Three-tier client-server model
-	Allocate the client, database, business rules to their own tiers
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507212658.png)

Our progress
-	Initial sketch of the system architecture
    -	Initial set of architecturally significant elements to be used as a basis for analysis
    -	Initial set of analysis mechanisms
    -	Initial layering and organization of the system

Step 4: Select the Input for the Next Iteration
-	For each functional requirement 
    -	Ensure that requirement has been satisfied.
    -	If not, then add responsibilities to satisfy the requirement.
        -	Add them to container with similar requirements
        -	If no such container, may need to create new one or add to container with dissimilar responsibilities (coherence)
        -	If container has too many requirements for a team, split it into two portions. Try to achieve loose coupling when splitting.

Step 4: Select the Input for the Next Iteration
-	Quality Attribute Requirements 
    -	If the quality attribute requirement has been satisfied, it does not need to be further considered.
    -	If the quality attribute requirement has not been satisfied then either
        -	Delegate it to one of the child elements
        -	Split it among the child elements
    -	If the quality attribute cannot be satisfied, see if it can be weakened. If it cannot be satisfied or weakened then it cannot be met.

Step 4: Select the Input for the Next Iteration
-	Constraints
    -	Constraints are treated as quality attribute requirements have been treated.
        -	Satisfied
        -	Delegated
        -	Split
        -	Unsatisfiable 

Let us look at our requirements again
-	A developer wishes to add a new auditing business rule at design time and makes the modification, without affecting other functionality, in 10 person-days.
-	A developer wishes to change the relational schema to add a new view to the database, without affecting other functionality, in 30 person-days.
-	A system administrator wishes to employ a new database and makes the modification, without affecting other functionality, in 18 person-months.
-	A developer wishes to add a new function to a client menu, without side effects, in 15 person-days.
-	A developer needs to add a Web-based client to the system, without affecting the functionality of the existing ATM client, in 90 person-days.

Let us look at our requirements again
-	A developer needs to add a Web-based client to the system, without affecting the functionality of the existing ATM client, in 90 person-days.
-	Can our initial design satisfy this requirement?

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507212919.png)

-	Currently we only have one client (ATM client). If we want to add a Web-based client to this system, this will also affect ATM client 

Let us look at our requirements again
-	A developer needs to add a Web-based client to the system, without affecting the functionality of the existing ATM client, in 90 person-days.
-	To avoid affecting ATM client when adding a Web-based client to the system, we need to revise our architecture
-	Our client component needs to be split into 2 clients 
    -	Web-based client with a web server
    -	ATM client

Tactics: Use an intermediary
-	In addition, we will use “use an intermediary” tactics to mediate the communication between the tiers by some abstract interface
    -	A data access layer that uses Open DataBase Connectivity (ODBC) between the business rules and the database
    -	A translation layer between the business rules and the client that understands Extensible Markup Language (XML)
-	With these intermediaries, it will be simple to add new databases or clients. 

Revised design
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213014.png)

-	A developer can now add a Web-based client and server as a simple addition to the architecture, without affecting the ATM client

Repeat Steps 1–4 Until All ASRs are Satisfied
-	Let us consider our performance requirement now
-	“The user can withdraw a limit of $300 from an account that has sufficient funds in less than 10 seconds.” 

Activity: Use patterns and tactics
-	Based on the performance scenario “The user can withdraw a limit of $300 from an account that has sufficient funds in less than 10 seconds” , how would you revise our  candidate design? Why?

Tips 
-	What tactics for performance do you know? Could we use them in the architecture of our system?

Achieving performance requirement 
-	What tactics can we employ?
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213112.png)

Performance Tactics
-	Can we use the first group of tactics? 
-	Can we control resource demand with an ATM?
    -	From technical point of view, yes - controlling resource demand would mean the bank would can ask customers to use ATM less often
    -	But from business point of view, NO-  asking customers to limit their use of ATM is bad for business
    ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213144.png)

Performance Tactics
-	In that case, let us consider the second group of tactics – Manage resources

What we can do
-	Introduce concurrency
    -	Make servers multithreaded so that they  may execute multiple requests in parallel 
-	Increase resources
    -	Deploy additional database servers and business rule servers
-	Arbitrate between multiple resources to ensure that the processing load is distributed among the system’s resources according to a chosen scheduling policy
    -	Introduce a “load balancer” component 
    Elaborated version of our architecture
    ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213239.png)


Activity: Discuss the candidate architecture 
-	With the generated design, can we achieve 10 seconds latency requirements now?
-	What is missing in our efforts?
-	When can we be confident that our architecture achieves the requirement?



Tips 
-	“The user can withdraw a limit of $300 from an account that has sufficient funds in less than 10 seconds”
-	We have improved latency of our system by employing tactics for performance,  but we are not able to say to what extent the latency is improved. We do not know if our system can achieve 10 seconds latency – we did not do anything that would allow us to have this knowledge. 
-	What do we need to do to have a confidence in what is the latency of our system?

Discussion
-	We have not yet specified the precise degree of replication of any of the deployed clients or servers
-	We have not yet specified the size of the thread pool in each of them
-	This more detailed specification is the next step in the design process

Discussion
-	Once these characteristics have been specified, the latency of an architecture can be evaluated via performance queuing model – only then we will be confident if our architecture meets the requirement 

Discussion
-	In addition – performance cost will be imposed by other architectural decisions
    -	Addition of a load balancing component will create additional computation and communication overhead
    -	Each layer will impose performance cost
    -	…

Homework 2
Based on the ASRs from your own Utility Tree, create a design fragment using the ADD method employing and instantiating a pattern. 
-	Submission: 
    -	Deadline:  May 8th 
    -	Place: submit to Blackboard






## exercise3
Please comply with epidemic prevention and control policies 
-	Choose the seat that you will use during the whole semester and register your information in the system.
-	Make sure to take the same seat during every lecture.


Intended Learning Outcomes
-	By the end of this lesson you will be able to:
    -	Create a documentation for a real system

Structures and Views
-	Architecture Is a Set of Software Structures 
-	A structure is the set of elements itself, as they exist in software or hardware.
-	In short, a view is a representation of a structure. 
-	Architects design structures. They document views of those structures.

Architecture Documentation
-	Even the best architecture will be useless if the people who need it 
    -	do not know what it is; 
    -	cannot understand it well enough to use, build, or modify it; 
    -	misunderstand it and apply it incorrectly. 
-	All of the effort, analysis, hard work, and insightful design on the part of the architecture team will have been wasted. 

Architecture Documentation and Stakeholders
- Education
    -	Introducing people to the system 
    -	New members of the team
    -	External analysts or evaluators
    -	New architect
- Primary vehicle for communication among stakeholders
    -	Especially architect to developers
    -	Especially architect to future architect!
- Basis for system analysis and construction 
    -	Architecture tells implementers what to implement. 
    -	Each module has interfaces that must be provided and uses interfaces from other modules. 
    -	Documentation can serve as a receptacle for registering and communicating unresolved issues.
    -	Architecture documentation serves as the basis for architecture evaluation. 

Views
-	Principle of architecture documentation:
    -	Documenting an architecture is a matter of documenting the relevant views and then adding documentation that applies to more than one view.

Which Views?  The Ones You Need!
-	Different views support different goals and uses.
-	The views you should document depend on the uses you expect to make of the documentation. 
-	Each view has a cost and a benefit; you should ensure that the benefits of maintaining a view outweigh its costs.

Building the Documentation Package
-	Documentation package consists of
    -	Views
    -	Documentation beyond views

View Template
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213609.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213625.png)

Implementation View 
-	Describes the static organization of the software in its development environment.
-	Viewer
    -	Programmers and Software Managers
-	Considers
    -	Software module organization - Hierarchy of layers, software management, reuse, constraints of tools
-	Style
    -	layered style


Notation
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213717.png)

Activity
-	For our simple ATM network, with additional database servers deployed, create an implementation view 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213731.png)

Implementation and deployment views
- Implementation view
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213752.png)

Deployment View 
-	Describes the mapping(s) of the software onto the hardware and reflects its distributed aspect.
-	Viewer
    -	System Engineers
-	Considers
    -	Non-functional requirement (reliability, availability and performance) regarding to underlying hardware.

Deployment view example 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213827.png)

Activity
-	For our simple ATM network, with additional database servers deployed, create a deployment view 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213841.png)
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213853.png)

Implementation and deployment views
- Implementation view
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213913.png)

- Deployment view
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213934.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507213951.png)

Use Case View
-	Captures system functionality as seen by users
-	Built in early stages of development
-	Developed by analysts and domain experts
-	System behavior, that is what functionality it must provide, is documented in a use case model.

Use Case Model
-	Illustrates the system’s intended functions (use cases), its surroundings (actors), and relationships between the use cases and actors (use case diagrams).
    -	provides a vehicle used by the customers or end users and the developers to discuss the system’s functionality and behavior.
    -	starts in the Inception phase with the identification of actors and principal use cases for the system, and is then matured in the elaboration phase, by adding more details and additional use cases.

Graphical Constructs
-	Actors
-	represent anyone or anything that must interact with the system: they are not part of the system.
-	may:
    -	only input information to the system
    -	only receive information from the system
    -	input and receive information to and from the system
-	In the UML, an actor is represented as a stickman with a name

Use Cases
-	represent the functionality provided by the system; that is:
    -	what capabilities will be provided to an actor by the system or
    -	what tasks are performed by each actor?
-	sequence of transactions performed by a system that yields a measurable result of values for a particular actor.
-	In the UML, a use case is represented by an oval with a name inside:

Use Case Relationships
-	Association:
    -	a relationship that represents communication between an actor and a use case;
    -	can be navigable in both ways or in only one way.
-	Inheritance:
    -	Generalization or specialization relationships that may exist between actors.
-	Two types of relationships that may exist between use cases: uses and extends:
    -	A functionality shared by multiple use cases can be placed in a separate use case which is related to these uses cases by a uses relationship.
    -	An extends relationship is used to show:
        -	Optional behaviour
        -	Behaviour that is only run under certain conditions, such as triggering an alarm
        -	Different flows which may be run based on actor selection

Use Case Diagrams
-	A graphical view of the actors, use cases, and their interactions identified for a system.
    -	Consists of the system boundary, and the graphical description of the actors, use cases, and their relationships.

Use Case Diagram Example: (Embedded) Cellular Telephone System
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214156.png)

Activity
-	Provide the use case diagram for the architecture of an ATM System

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214230.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214247.png)

Sequence Diagram Example
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214303.png)

Activity
-	Expand your Use Case with a sequence diagram to document a basic sequence of actions during transactions

Activity
-	Expand your Use Case with a sequence diagram to document a basic sequence of actions during transactions
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214334.png)

Textual Description of a Use Case
-	Each use case is documented with a flow of events, which is a description of the events needed to accomplish the required behavior.
-	The flow of events is written in the language of the domain and describe what the system should do and not how the system does it.
-	The flow of events should include:
    -	When and how the use case starts and ends
    -	What interaction the use case has with the actors
    -	What data is needed by the use case
    -	The normal sequence of events for the use case
    -	The description of any alternate or exceptional flows

Textual Description of a Use Case
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214404.png)

Use Case Diagram Example 2
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214425.png)

Use Case Description Example 
![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214441.png)

Activity
-	Provide the textual description for Check PIN Use Case of our ATM 
    -	ATM System Requirements:
        -	The Interbank Consortium, a hypothetical financial institution, has directed its software development subsidiary, Interbank Software, to develop new services that support a network of automated teller machines (ATMs).
        -	Customers use ATMs to make queries, withdrawals, deposits and funds transfers involving their accounts. Thieves or crooks must be prevented from interfering with these actions.
        -	Interactions with the ATM would work like this:
        -	The customer inserts his/her bank card into the ATM.
        -	The ATM prompts the customer for a "password" which the user enters at the ATM.
        -	The customer then selects an action to be performed; the selected action is then performed by the branch (perhaps causing dispersal of cash at the ATM).
        -	Additionally, the bank would like to be able to use the system to maintain statistics about customers’ behaviour in order to adapt its services to their needs, and also to send them some advertisements when they are using the system.
        ![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214532.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214550.png)

![](https://raw.githubusercontent.com/JF-011101/Image_hosting_rep/main/20220507214609.png)

Discussion
-	Relating Structures to Each Other
    -	Elements of one structure will be related to elements of other structures, and we need to reason about these relations. 
        -	A module in a decomposition structure may be manifested as one, part of one, or several components in one of the component-and-connector structures. 
    -	In general, mappings between structures are many to many.
-	Not all architectures need all views.
-	Architecture documents need to address the concerns of all stakeholders 
    -	end-user
    -	system engineers
    -	developers
    -	project managers
-	Use different notations for several views each one addressing one specific set for concerns.


Homework 3
-	Develop 2 more views for our ATM system. 
-	For each view describe who and when will need to use this view.
-	Submission: 
    -	Deadline:  May 8th 
    -	Place: submit to Blackboard

