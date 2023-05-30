# GraphQL API

Boost daemon exposes a GraphQL API that is used by the Web UI to query and update information about storage and retrieval deals.

The GraphQL API query endpoint is at [http://localhost:8080/graphql/query](http://localhost:8080/graphql/query)

You can also run your own queries against the GraphQL API using `curl` or a programming language that has a [GraphQL client](https://graphql.org/code/).

Boost has a built-in GraphQL explorer at [http://localhost:8080/graphiql](http://localhost:8080/graphiql)

You can test out queries, or explore the GraphQL API by clicking on the `< Docs` link at the top right of the page:

![](<../../.gitbook/assets/Screen Shot 2022-04-21 at 10.06.16 AM.png>)

To run a GraphQL query with `curl`:

```
curl -X POST
-H "Content-Type: application/json"
-d '{"query":"query { deals(offset: 5, limit: 10) { deals { ID CreatedAt PieceCid } } }"}'
http://localhost:8080/graphql/query | jq
```

This 1m video shows how to use these tools to build an run a GraphQL query against Boost:

{% embed url="https://youtu.be/yN_H-hDrBao" %}
Use the GraphQL explorer to create a query against Boost
{% endembed %}

### Example Queries

1\. Query failed deals

```
curl -X POST \
-H "Content-Type: application/json" \
-d '{"query":"query { deals(limit: 10, query: \"failed to get size of imported\") { deals { ID CreatedAt Message } } }"}' \
http://localhost:8080/graphql/query | jq
```

2\. Cancel a deal where `ab12345c-5678-90de-12f3-45a6b78cd9ef` is the deal ID

```
curl -X POST \
-H "Content-Type: application/json" \
-d '{"query":"mutation { dealCancel(id: \"ab12345c-5678-90de-12f3-45a6b78cd9ef\") }"}' \
http://localhost:8080/graphql/query | jq
```
