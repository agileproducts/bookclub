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

