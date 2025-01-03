# Supply Chain Optimization Using Linear Programming
![Masthead Image](/images/image.png)
This project demonstrates how Linear Programming (LP) can be applied to effectively solve complex supply chain problems. Specifically, it focuses on optimizing the assignment of shipping routes to customer orders while minimizing overall transportation and warehousing costs. The solution leverages LP to ensure decisions respect multiple real-world constraints, such as warehouse capacities, product compatibility, customer-warehouse relationships, and port shipping restrictions.

## Motivation
By modeling the supply chain problem as a mathematical optimization task, we can determine cost-effective and feasible solutions that align with business goals, ensuring efficient resource utilization and timely deliveries. This approach highlights the power of LP in handling large-scale decision-making problems in logistics and supply chain management.

## Technology Used
**1- Python:** For data collection, preprocessing, Exploratory Data Analysics and Linear Programming.

**2- Libraries:**

- `pandas` and `numpy` for data manipulation.

- `pulp` for fomrulating and solving the LP.

- `matplotlib` and `seaborn` for visualizations.

## Dataset Overview
This dataset represents the outbound logistics network of a global microchip producer. It includes demand data for 9,216 orders routed through a network of 15 warehouses, 11 origin ports, and 1 destination port. Key details include:

- Warehouses: Limited by stock type, supported customers, and daily order capacity.
- Service Levels: Options include Door-to-Door (DTD), Door-to-Port (DTP), and Customer Referred Freight (CRF).
- Transportation: Orders shipped via 9 couriers using air or ground transport, with rates based on weight bands and service levels.
This dataset provides real-world constraints and routing complexities for supply chain optimization.



