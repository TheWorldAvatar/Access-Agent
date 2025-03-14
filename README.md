# Access Agent

The Access Agent allows for SPARQL federation across a endpoints within a [TWA Stack](https://github.com/TheWorldAvatar/stack) as well as external endpoints.

## Endpoints

### Dataset endpoint

A dataset endpoint is an endpoint that federates across each endpoint related to a dataset in a stack.
When a new dataset is added to this stack a new federated dataset endpoint will be added.
This endpoint will persist for multiple queries and it will therefore be referred to as "static".

### Incoming stack endpoint

An incoming stack endpoint federates across each [dataset endpoint](#dataset-endpoint) in a stack.
This endpoint will be used by external clients querying data in this stack.
When a new dataset is added to this stack its dataset endpoint will be added to this federation.
This endpoint will persist for multiple queries and it will therefore be referred to as "static".

### Outgoing stack endpoint

An outgoing stack endpoint is a federation between internal and external endpoints.
This endpoint will be used by agents internal to this stack, accessing internal and external data.
It is not used by external clients in case loops are caused with other Access Agents.

The challenge of querying from external endpoints is that there are potentially very many of these endpoints and they could have performance difficulties.
There are two potential implementations to address this question in different ways.

#### Dynamic implementation

In this implementation a federation is created at query time ("dynamic").
The endpoints that make up this federation are chosen based on the query and an indexed set of potential endpoints.

A first implementation could just federated across the [incoming stack endpoint](#incoming-stack-endpoint) and a specified list of external endpoints.
In future, new external endpoints could be register and indexing would be used to select the federation.

#### Static implementation

In this implementation an endpoint is created when the Access Agent starts up ("static").
The [service description](https://www.w3.org/TR/sparql11-service-description/) for each external service is cached locally.
If the service description is not available for that endpoint a construct query would be used to make it.
All `ASK` queries from the federation would be answered using some middleware and the service description.
Then, full advantage could be taken of native indexing of the federated endpoint.

## Design

The diagram below illustrates how each of the federated endpoints are related to each other.

```mermaid
graph TB
    ex_client{"External
    Client"}
    ex_client --> incoming("Incoming")
    subgraph Stack
        subgraph "Access Agent"
            outgoing("Outgoing") --> incoming
            incoming --> DA(Dataset A)
            incoming --> DB(Dataset B)
        end
        DA --> DAB([Dataset A Blazegraph])
        DB --> DBB([Dataset B Blazegraph])
        DB --> DBO([Dataset B Ontop])
        in_client{"Internal
        Client"}
        in_client --> outgoing
    end
    outgoing --> ex_fed("External")
    outgoing --> ex_non_fed(["External"])

    style ex_client fill:#a6a6a6, stroke:#ffffff
    style in_client fill:#a6a6a6, stroke:#ffffff

    style outgoing fill:#0e7e44, stroke:#ffffff
    style incoming fill:#0e7e44, stroke:#ffffff

    style DA fill:#0e7e44, stroke:#ffffff
    style DB fill:#0e7e44, stroke:#ffffff


    style DAB fill:#0d6c7e, stroke:#ffffff
    style DBB fill:#0d6c7e, stroke:#ffffff
    style DBO fill:#0d6c7e, stroke:#ffffff
    
    style ex_fed fill:#0e7e44, stroke:#ffffff
    style ex_non_fed fill:#0d6c7e, stroke:#ffffff
```

With the following key

```mermaid
graph TB
    fed("Federated Sparql Endpoint")
    non_fed(["Non-federated Sparql Endpoint"])

    style fed fill:#0e7e44, stroke:#ffffff
    style non_fed fill:#0d6c7e, stroke:#ffffff
```
