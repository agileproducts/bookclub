# Architecture for Flow

Susanne Kaiser

## Business Strategy with Wardley Mapping

A Wardley Map visualises the landscape in which an organisation operates.

### The Strategy Cycle

1. **Purpose**  - Why are we doing what we are doing?
2. **Landscape** - A description of the environment the organisation operates in
3. **Climate** - The external forces which the organisation cannot control
4. **Doctrine** - Universal principles which can be applied regardless of context
5. **Leadership** - Making context-specific decisions based on the above

By following this cycle an organisation can optimise mean time to respond (MTTR).

An organisation's vision comes from its purpose and core values.

### The Wardley Map

Decide the scope you want to map. You can start small, with just one user or user need. 

Start by describing the **value chain**. At the top are the users, and below that their needs. This is the problem space. Below that come the components which fufil the user needs, and then their dependencies. These are the solution space. 

The users and their needs are the 'anchor' of the map.

Components can be activities, practices, data, knowledge. Their position on the y-axis depends on how visible they are to the users. The components the user interfaces with most directly are at the top, these provide the most value to the user.

The components are then plotted on the x-axis according to it's **stage of evolution**. The stages of evolution are:
* Genesis - uncertain market, novel practices, focus on exploration
* Custom built - A forming market, emerging practices, focus on learning
* Product (including rental) - A growing market, good practices in place, focus on refining
* Commodity (including utilities) - A mature market, best practices in place, focus on efficiency

Components on the left change more than those on the right.

A map where the deeper a dependency is, the further to the right it is describes a system which is balanced and stable. Building mature components on top of volatile ones is risky.

Custom built components further down the chain which are not core to the organisation or exist as commodities may represent an effeiciency gap. But this is not necessarily true - see domain-driven design.

### Understanding Climactic Patterns

* Market forces like supply and demand tend to make components evolve towards the left and become commoditised.
* As components evolve their characteristics change, from volatile and uncertain towards stable and ordered.
* As components become commoditised they enable innovation because new emergent and custom components can be built on them.
* These higher-order systems create new sources of value.
* Competitors' actions will change the environment.
* Past success may breed inertia which impedes the evolution of components and undermines the organisation.

Assessing climactic patterns helps to reveal points of potential evolution.            

### Applying doctrinal principles

* **Know your users** - it's essential to understand this
* **Focus on user needs** - fulfilling these needs is why your business exists
* **Know the details** - understand what components are required to fill user needs
* **Challenge assumptions** - share the map as a group and question it
* **Use a common language** - using a ubiquittous language enables people with different roles to communicate effectively
* **Focus on high situational awareness** - Understand the landscape, this is where a map can help

* **Use appropriate methods per evolution stage** - agile/xp at genesis and custom, lean at product/rental, six sigma at commodity/utility.
* **Think small** - try to break a large landscape into small components that don't cross evolution stages (apply BDD)
* **Think small teams** - the two-pizza rule
* **Provide purpose, mastery and autonomy** - this provides motivation
* **Consider aptitude and attitude** - when building teams think not just about the skillset of individuals but also the mindset
* **There is no one culture** - this will change with evolutionary stage
* **Optimise flow** - the performance of a system is determined by its constraints (see, The Goal)
* **Design for constant evolution** - Adapting to change should not require constant reorganisation

## Exploring the problem space with Strategic Domain-Driven Design and Wardley Mapping

The core concept of DDD is that to build better software its design must align with the business domain. DDD helps us understand the problem domain before trying to develop the technical solution.

### Subdomains in DDD

#### The core domain
The essential, business-critical part of an organisation's problem domain. This usually provides most value to users and differentiates the organisation. In Wardley terms solutions for this subdomain will normally be at the genesis or custom-built stage of evolution. But as the domain matures it can evolve towards product or commodity, and the organisation will increasingly find advantage in cost rather than in differentiation.

#### The supporting subdomain
The supporting subdomain does not provide a competitive advantage and often exists in most similar products and services. It is simpler, non-differentiating, and simpler. If highly customisable or open source solutions are available it may reside at the product stage, but if more specialisation is needed then it becomes custom built, but should not demand high levels of investment.

#### The generic subdomain
The generic subdomain is uniquitous across many business systems, provide no competitive or differentiating advantage and are not specialised. Solutions for this domain should generally be product or commodity.

### Build or buy decisions with subdomain types and evolution stages

Wardley mapping suggests building custom software for the genesis and custom-built stages, buying for the product stage and outsourcing commodity to utility suppliers.

DDD points toward building for the core domain, buying for the generic domain and the decision for the supporting domain depending on the level of specialisation required.

The difference is that making the decision for a component based purely on the evolutionary stage risks overlooking the degree of business criticality. Over-customising off-the-shelf software brings more risk than may be worth it, even effecrive loss of vendor support. One alternative may be to use productised software only as designed and develop custom software for unsupported use cases - i.e. build and buy.

Evolution stages are about market maturity and user perception rather than who makes them. You could indicate buy versus built on a Wardley map using colours. This helps you assess your choices:

* Are we custom-building components that do not belong to the core domain?
* Does the level of specialisation justify a custom solution for supporting domains?

## Designing the solution space with strategic DDD

* The domain model expresses the domain logic and business rules relevant to that area of the system.
* Domain models are expressed in ubiquitous language which reflected a shared understanding within a bounded context.
* A domain model cannot exist without a boundary - the bounded context

### The different kinds of boundaries

* A linguistic and semantic boundary - the space in which things mean the same
* An ownership boundary - something only one team maintains
* A physical boundary  - something which can be implemented as separate solutions with its own artifacts

Architectural styles and business logic can vary from bounded context to context.

### Designing domain models and bounded contexts

An essential part of DDD is the collaboration between domain experts and software developers. By discussing user needs and intended outcomes we can start to derive the domain models and bounded contexts. A variety of techniques are available to help with this.

### EventStorming

EventStorming is a technique for exploring a domain using colour coded sticky notes. There are three formats.

#### Big Picture EventStorming

Identify **events** that have happened in the business domain along a timeline using orange sticky notes - e.g. 'order placed' - 'order shipped'. The most significant events are highlighted as being *pivotal*.

Humam **actors** are placed on yellow notes next to the relevant events. They could be an individual, a group, a persona, a role. External **systems** like another subdomain are placed on pink sticky notes.

Where participants encunter issues, questions, conflicts or risks they can be added on reddish notes as **hotspots**.

Sequences assigned to given actors are stacked using swimlanes.

#### Process modelling EventStorming

Process modelling zooms in on a particular part of the business domain and focuses on designing a process.

**Commands** are added as blue notes. These are instructions issued by actors or policies which are intended to cause domain events to happen, but they could be rejected. 

**Policies** are added as purple notes. They describe how a system is supposed to react when an event happens. 

**Read models** are added as green notes. They represent the state of the system at that moment in time and can be the result of a domain event.

#### Software design EventStorming

Software design EventStorming enables collaborative design of an event-driven software system. Here **aggregates** are introduced which represent consistent business rules. They receive commands and emit events. 

A policy or an actor retreiving information from a read model can issue a command. Commands can be invoked on aggregates or external systems. Aggregates and external systems generate domain events which in turn can translate into read models and policies.

![EventStorming sticky note types](./eventstorming-sticky-types.svg)

![EventStorming example](./corporate-travel-eventstorming-flow.svg)