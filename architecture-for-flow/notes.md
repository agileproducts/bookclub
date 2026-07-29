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