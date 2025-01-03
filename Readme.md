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

- Warehouses/Plants: Limited by type of products they can stock, customers they can support, and daily order capacity. Throughout the dataset and the problem, the terms Warehouse and Plants are used interchangably and essentially refer to the same thing.
- Service Levels: Options include Door-to-Door (DTD), Door-to-Port (DTP), and Customer Referred Freight (CRF).
- Transportation: Orders shipped via 9 couriers using air or ground transport, with rates based on weight bands and service levels.
This dataset provides real-world constraints and routing complexities for the supply chain optimization.
Fig. 1 shows a simplified example case for the supply chain model

![Supply Chain Image](/images/supply_chain.png)
*Figure 1: Graphical representation of the outbound supply chain. Each warehouse i is connected to one or many origin ports p. The shipping lane between origin port p
and destination port j is a combination of courier c, service level s, delivery time t and transportation mode m*

## Dataset Description
The dataset consists of 7 interconnected tables, each contributing to defining the constraints and parameters for the optimization model:
**1- OrderList**: Contains all customer orders that need to be assigned to a route.


| Order ID    | Order Date | Origin Port | Carrier | TPT | Service Level | Ship Ahead Day Count | Ship Late Day Count | Customer  | Product ID | Plant Code | Destination Port | Unit Quantity | Weight |
|-------------|------------|-------------|---------|-----|---------------|-----------------------|---------------------|-----------|------------|------------|------------------|---------------|--------|
| 1447296447  | 5/26/13    | PORT09      | V44_3   | 1   | CRF           | 3                     | 0                   | V55555_53 | 1700106    | PLANT16    | PORT09           | 808           | 14.3   |
| 1447158015  | 5/26/13    | PORT09      | V44_3   | 1   | CRF           | 3                     | 0                   | V55555_53 | 1700106    | PLANT16    | PORT09           | 3188          | 87.94  |
| 1447138899  | 5/26/13    | PORT09      | V44_3   | 1   | CRF           | 3                     | 0                   | V55555_53 | 1700106    | PLANT16    | PORT09           | 2331          | 61.2   |

The OrderList contains historical records of how the orders were routed and demand satisfied. Thus, we can calculate the costs of historical network and also optimize for the new constraints.

**2- FreightRates**: The FreightRates table describes all available carriers and the weight gaps for each individual lane and rates associated. There are a total of 9 unique carriers and from each of the carriers stem separate shipping lanes depending on the unique combinations of `[Origin Port, Destination Port, Service, Time, Mode of Transport]` for that carrier corresponding to columns `['orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc']`.

| Carrier  | Orig Port CD | Dest Port CD | Min Weight Qty | Max Weight Qty | Service Code | Minimum Cost | Rate  | Mode Description | Transport Day Count | Carrier Type  |
|----------|--------------|--------------|----------------|----------------|---------------|--------------|-------|------------------|---------------------|---------------|
| V444_6   | PORT08       | PORT09       | 250            | 499.99         | DTD           | $43.23       | $0.71 | AIR              | 2                   | V88888888_0   |
| V444_6   | PORT08       | PORT09       | 65             | 69.99          | DTD           | $43.23       | $0.75 | AIR              | 2                   | V88888888_0   |
| V444_6   | PORT08       | PORT09       | 60             | 64.99          | DTD           | $43.23       | $0.79 | AIR              | 2                   | V88888888_0   |


**3- WhCosts**: This table specifies the cost associated in storing the products in given warehouse measured in dollars per unit. there a total of 19 unique Warehouses and the associated cost of stroing product/unit in each of these Warehouses is shown below;


| WH       | Cost/Unit |
|----------|-----------|
| PLANT15  | 1.42      |
| PLANT17  | 0.43      |
| PLANT18  | 2.04      |
| PLANT05  | 0.49      |
| PLANT02  | 0.48      |
| PLANT01  | 0.57      |
| PLANT06  | 0.55      |
| PLANT10  | 0.49      |
| PLANT07  | 0.37      |
| PLANT14  | 0.63      |
| PLANT16  | 1.92      |
| PLANT12  | 0.77      |
| PLANT11  | 0.56      |
| PLANT09  | 0.47      |
| PLANT03  | 0.52      |
| PLANT13  | 0.47      |
| PLANT19  | 0.64      |
| PLANT08  | 0.52      |
| PLANT04  | 0.43      |


**4- WHCapacities**: Defines the maximum number of orders each Warehouse can process daily.


| Plant ID  | Daily Capacity |
|-----------|----------------|
| PLANT15   | 11             |
| PLANT17   | 8              |
| PLANT18   | 111            |
| PLANT05   | 385            |
| PLANT02   | 138            |
| PLANT01   | 1070           |
| PLANT06   | 49             |
| PLANT10   | 118            |
| PLANT07   | 265            |
| PLANT14   | 549            |
| PLANT16   | 457            |
| PLANT12   | 209            |
| PLANT11   | 332            |
| PLANT09   | 11             |
| PLANT03   | 1013           |
| PLANT13   | 490            |
| PLANT19   | 7              |
| PLANT08   | 14             |
| PLANT04   | 554            |


**5- ProductsPerPlant**: this table lists all supported warehouse-product combinations. There are some `Produt IDs` that can only be supported by specific Warehouses/Plants.

| Plant Code | Product ID |
|------------|------------|
| PLANT15    | 1698815    |
| PLANT17    | 1664419    |

The table hence shows that `Product ID` `1698815` can be supported by PLANT15.

**6- VmiCustomers**: This table lists all special cases, where warehouse is only allowed to support specific customers, while any other non-listed warehouse can supply any customer
| Plant Code | Customers             |
|------------|-----------------------|
| PLANT02    | V5555555555555_16     |
| PLANT02    | V555555555555555_29   |
| PLANT02    | V555555555_3          |
| PLANT02    | V55555555555555_8     |
| PLANT02    | V55555555_9           |
| PLANT02    | V55555_10             |
| PLANT02    | V55555555_5           |
| PLANT06    | V555555555555555_18   |
| PLANT06    | V55555_10             |
| PLANT10    | V555555555555555_29   |
| PLANT10    | V555555_34            |
| PLANT10    | V5555555555555555555_54 |
| PLANT10    | V55555_10             |
| PLANT11    | V5555555555555555555_54 |

Hence, the above Customers can only be serviced by the respective Plants shown in the table. Other customers can be serviced through any of the 19 Plants.

**7- PlantPorts**: This table describes the allowed links between the warehouses and shipping ports in real world.
| Plant Code | Port   |
|------------|--------|
| PLANT01    | PORT01 |
| PLANT01    | PORT02 |
| PLANT02    | PORT03 |
| PLANT03    | PORT04 |

Hence, as can be seen above, `PLANT01` can service orders from `PORT01` and `PORT02` only


## Formulating the Linear Program
The problem involves assigning customer orders to warehouses and shipping routes while minimizing total costs and satisfying the following constraints constraints:

- Orders must be assigned to a single warehouse.
- Warehouses can only process a limited number of orders per day.
- Shipping lanes have weight limits and cost structures.
- Warehouses can only ship through specific ports.
- Product compatibility and special customer restrictions must be respected

A new Minimization LP is initiated using the `Pulp` library in Python
```python
model = pulp.LpProblem("SupplyChainOptimization", pulp.LpMinimize)
```

### Decision Variables
The following decision variables are defined in the linear programming (LP) formulation:
![Decision Variables](/images/LP1.png)


These variables are defined in the Linear Program formulation using the `PulP` library as follows:

```python
# Generate the x_ki variable
x_ki = pulp.LpVariable.dicts(
    "x_ki",
    (
    (k, i)
    for k in data['OrderList'].index # For every order number in the OrderList table
    for i in data['PlantPorts']['Plant Code'].unique() # For every unique Warehouse/Plant in the PlantPorts table
    ),
    cat = "Binary"
)
```

```python
# Generate the y_kcpjstm variable
y_kcpjstm = pulp.LpVariable.dicts(
    "y_kcpjstm",
    (
        (k, c, p, j, s, t, m)  # Exclude 'k' from FreightRates indexing
        for k in data['OrderList'].index  # Orders
        for c, group in data['FreightRates'].groupby('Carrier')  # Iterate over each Carrier
        for p, j, s, t, m in group[['orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc']].drop_duplicates().itertuples(index=False)
    ),
    cat="Binary",
)

```

### Objective Function
The objective is to  minimize the total cost, which is the sum of:

- Warehousing costs: Costs associated with storing products at assigned warehouses.

- Transportation costs: Costs associated with shipping products via assigned routes.

**Warehouse Cost**
![Warehouse Cost](/images/WHCost.png)

This is formulated in the Pulp LP as follows:
```python
warehouse_cost = pulp.lpSum(
    x_ki[k, i] * data['OrderList'].loc[k, 'Unit quantity'] * data['WhCosts'].set_index('WH').loc[i, 'Cost/unit']
    for k in data['OrderList'].index[:500]
    for i in data['WhCosts']['WH']
)
```

**Transportation Cost**
![Warehouse Cost](/images/TPCost.png)
For each shipping lane *cpjstm*, there is a minimum cost of shipping. If the respectve rate x weight is lower than the minimum cost, then the minimum cost is considered and vice versa.
This is formulated in the Pulp LP as follows:
```python
transportation_cost = pulp.lpSum(
    y_kcpjstm[k, c, p, j, s, t, m]
    * max(
        # Minimum cost
        data['FreightRates']
        .set_index(['Carrier', 'orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc'])
        .loc[(c, p, j, s, t, m)]['minimum cost'].iloc[0],

        # Rate multiplied by weight
        (
            data['FreightRates']
            .set_index(['Carrier', 'orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc'])
            .loc[(c, p, j, s, t, m)]
            .query("minm_wgh_qty <= @data['OrderList'].loc[@k]['Weight'] <= max_wgh_qty")['rate'].iloc[0]
            * data['OrderList'].loc[k]['Weight']
        )
        if not data['FreightRates']
        .set_index(['Carrier', 'orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc'])
        .loc[(c, p, j, s, t, m)]
        .query("minm_wgh_qty <= @data['OrderList'].loc[@k]['Weight'] <= max_wgh_qty").empty
        else data['FreightRates']
        .set_index(['Carrier', 'orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc'])
        .loc[(c, p, j, s, t, m)]['rate'].mean()
    )
    for k in data['OrderList'].index[:500]
    for c, group in data['FreightRates'].groupby('Carrier')
    for p, j, s, t, m in group[['orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc']]
    .drop_duplicates().itertuples(index=False)
)
```

The combined Objective Funtion then looks like the following
![Warehouse Cost](/images/ObjectiveFunction.png)
These costs are added to the minimization LP as follows:
```python
model += warehouse_cost + transportation_cost
```

### Constraints
**1- Each order is assigned to exaclty 1 Warehouse**
![Warehouse Cost](/images/Constraint1.png)
This is formulated in python as:
```python
for k in data['OrderList'].index:
    model += pulp.lpSum(x_ki[k, i] for i in data['WhCosts']['WH']) == 1
```

**2- Each order is assigned to one shipping lane**
![Warehouse Cost](/images/Constraint2.png)
This is formulated in python as:
```python
for k in data['OrderList'].index:
    model += pulp.lpSum(
        y_kcpjstm[k, c, p, j, s, t, m]
        for c, group in data['FreightRates'].groupby('Carrier')  # Iterate over each Carrier
        for p, j, s, t, m in group[['orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc']]
        .drop_duplicates().itertuples(index=False)
    ) == 1
```

**3- Warehouse capacity constraints**
![Warehouse Cost](/images/Constraint3.png)
This is formulated in python as:
```python
for i in data['WhCapacities']['Plant ID']:
    model += pulp.lpSum(x_ki[k, i] for k in data['OrderList'].index) <= data['WhCapacities'].set_index('Plant ID').loc[i, 'Daily Capacity ']
```

**4- Shipping lane weight constraints**
![Warehouse Cost](/images/Constraint4.png)
This is formulated in python as:
```python
for c, group in data['FreightRates'].groupby('Carrier'):
    for (p, j, s, t, m) in group[['orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc']].drop_duplicates().itertuples(index=False):
        model += pulp.lpSum(
            y_kcpjstm[k, c, p, j, s, t, m] * data['OrderList'].loc[k, 'Weight'] for k in data['OrderList'].index
            ) <= data['FreightRates'].set_index(['Carrier', 'orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc']).loc[(c, p, j, s, t, m), 'max_wgh_qty'].max()
```

**4- Product Compatibility with Warehouses**
![Warehouse Cost](/images/Constraint5.png)
This is formulated in python as:
```python
for k in data['OrderList'].index:
    for i in data['WhCosts']['WH']:
        products = data['ProductsPerPlant'].set_index('Plant Code').loc[i, 'Product ID']
        products = [products] if not isinstance(products, pd.Series) else products.to_list()
        if data['OrderList'].loc[k, 'Product ID'] not in products:
            model += x_ki[k, i] == 0
```


**5- Customer-warehouse restrictions**
![Warehouse Cost](/images/Constraint6.png)
This is formulated in python as:
```python
for k in data['OrderList'].index:
    for i in data['WhCosts']['WH']:
        # Check if the warehouse-customer combination is in VmiCustomers
        if i in data['VmiCustomers']['Plant Code'].unique():
            allowed_customers = data['VmiCustomers'].set_index('Plant Code').loc[i, 'Customers']
            if data['OrderList'].loc[k, 'Customer'] not in allowed_customers:
                model += x_ki[k, i] == 0
```

**7-Restrict warehouses to ship orders only through allowed ports**
![Warehouse Cost](/images/Constraint7.png)
This is formulated in python as:
```python
for k in data['OrderList'].index[:500]:
    for i in data['WhCosts']['WH']:
        # Get the allowed ports for the current warehouse
            allowed_ports = data['PlantPorts'].set_index('Plant Code').loc[i, 'Port']
            for c, group in data['FreightRates'].groupby('Carrier'):
                for (p, j, s, t, m) in group[['orig_port_cd', 'dest_port_cd', 'svc_cd', 'tpt_day_cnt', 'mode_dsc']].drop_duplicates().itertuples(index=False):
                    if p not in allowed_ports:
                        # Enforce the constraint
                        model += y_kcpjstm[k, c, p, j, s, t, m] <= 1 - x_ki[k, i]
```








