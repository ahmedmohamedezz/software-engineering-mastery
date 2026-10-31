# Unified Modeling Language

- a diagram captures a statement of system's behavior (for analysis). or vision and details of an architecture (for design)

- why do we need notations ?
  - easier communication among team members (we all know what an arrow between 2 classes mean in a class diagram)
  - notations express some of the work briefly, so we can focus more on other things
  - notations could be used by automated tools for basic checks of consistency and correctness

- there is no one single big diagram that can represent everything in a system. instead, we have a set of diagrams each captures a different view of the system

- even in a single type (class-diagram), we can use multiple ones to express the system instead of having 1 giant diagram

- the use of diagrams is necessary as they serve as a
  - system documentation for other roles (newcomers, designers, analysts) while the system is being built and overtime
  - it let's you think before you code
  - it let's understand the business rules behind the system

---

## Diagram Categories

### Structure Diagrams

- used to show the static structure of elements in the system (arch., classes/elements, domain, configuration)

#### Package Diagram

- used to organize the artifacts of the dev. process (problem analysis & design)

- divide the system into (layers, packages/modules, ...)

#### Component Diagram

- a component is a cluster of classes that are themselves cohesive but are loosely coupled relative to other clusters

- components may be used to hierarchically decompose
a system and represent its logical architecture

#### Deployment Diagram

- used to show the deployment of artifacts to nodes that host them and the interaction between deployment nodes

#### Class Diagram

- used to show the existence of classes and their relationships in the logical view of a system

---

### Behavior Diagrams

#### Use Case Diagram

- used to model the functionality of the system from the actors point-of-view (what they expect the system to do)

#### Activity Diagram

- used to provide visual depictions of the flow of activities, whether in a system, business, workflow, or other process

- These diagrams focus on the activities that are performed and who (or what) is responsible for the performance of
those activities

#### State Machine Diagram

- used to design and understand time-critical systems, in
which the consequences of improper timing are severe

- used to describe the behavior of individual objects

---

### Interaction Diagrams (Sub-category of Behavior Diagrams)

#### Sequence Diagram

- used to trace the execution of a scenario
