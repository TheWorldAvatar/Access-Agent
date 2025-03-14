# Access Agent

The Access Agent allows for SPARQL federation across a endpoints within a [TWA Stack](https://github.com/TheWorldAvatar/stack) as well as external endpoints.

## Dataset endpoint

A dataset endpoint is an endpoint that federates across each endpoint related to a dataset in a stack.
When a new dataset is added to this stack a new federated endpoint will be added.
This endpoint will persist for multiple queries and it will therefore be referred to as "static".

## Incoming stack endpoint

An incoming stack endpoint federates across each [dataset endpoint](#dataset-endpoint) in a stack.
This endpoint will be used by external clients querying data in this stack.
When a new dataset is added to this stack its dataset endpoint will be added to this federation.
This endpoint will persist for multiple queries and it will therefore be referred to as "static".

## Outgoing stack endpoint

A incoming stack endpoint creates a federation at query time across internal and external endpoints.
This endpoint will be used by agents internal to this stack, accessing internal and external data.
It is not used by external clients in case loops are caused with other Access Agents.
In future, the endpoints will be chose based on the query.
In the first implementation the [incoming stack endpoint](#incoming-stack-endpoint) along with a specified list of external endpoints will be used.

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
    outgoing --> externalfed("External")
    outgoing --> externalnonfed(["External"])

    style ex_client fill:#a6a6a6, stroke:#ffffff
    style in_client fill:#a6a6a6, stroke:#ffffff

    style outgoing fill:#0e7e44, stroke:#ffffff
    style incoming fill:#0e7e44, stroke:#ffffff

    style DA fill:#0e7e44, stroke:#ffffff
    style DB fill:#0e7e44, stroke:#ffffff


    style DAB fill:#0d6c7e, stroke:#ffffff
    style DBB fill:#0d6c7e, stroke:#ffffff
    style DBO fill:#0d6c7e, stroke:#ffffff
    
    style externalfed fill:#0e7e44, stroke:#ffffff
    style externalnonfed fill:#0d6c7e, stroke:#ffffff
```

With the following key

```mermaid
graph TB
    fed("Federated Sparql Endpoint")
    nonfed(["Non-federated Sparql Endpoint"])

    style fed fill:#0e7e44, stroke:#ffffff
    style nonfed fill:#0d6c7e, stroke:#ffffff
```
