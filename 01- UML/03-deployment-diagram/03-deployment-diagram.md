# Deployment Diagram

- describes the physical deployed nodes of the system (servers like system, caching, logging, and dbs, artifacts, clients, devices like screens, consoles, payment processors, readers, scaners, networks, firewalls, warehouses, communication protocols)

- usually internet is expressed as a node, and the diagram can model network topologies

- treat modules as components that interacte with each other (require, or provide)

> It answers: What are the actual software modules (services, apps, databases) that must be **running** to satisfy the logical design?

- component diagram **essential elements** are
  - **components**
  - **interfaces**
  - **realizations**

![component diagram](./component-diagram.png)

- component represeted by rectangle with the `<<component>>` label and component icon (top-right)

- ports could be named `portName:portType`

- ports could be inside the component

- provided interfaces (provided by this component) represented by **ball-and-socket** notation

- required interfaces represented by **socket** notation

- if we need to show more details about a component’s interfaces, we may provide an interface specification

![interface specification](./component-interfaces.png)

---

## Readings

- cohision vs. coupling
