So, why does this exist? What are the advantages and disadvantages of DinoQL over REST or GraphQL?

I created DinoQL to pull what I believe are the best parts of both REST and GraphQL, creating an API standard that I feel is as flexible as GraphQL, and as simple to use as REST.

Let's take a look at each one individually to get a clearer picture of what DinoQL borrows from each.

- [REST](#rest)
  - [Grouping of resources](#grouping-of-resources)
  - [Easy to write queries](#easy-to-write-queries)
- [GraphQL](#graphql)
  - [Built-in schema](#built-in-schema)
  - [Nested queries](#nested-queries)

## REST

### Grouping of resources

REST represents resources and actions as a set of HTTP requests. Resources are typically grouped by how the urls are structured. for instance, `/users` is static actions related to users, while `/users/:id` is actions related to a specific user. DinoQL borrows this concept from REST by placing actions related to a resource inside that resource's definition.

### Easy to write queries

With REST you provide a set of parameters to the server and the server returns a response, nothing more to it. DinoQL provides this same simplicity of requests and responses, but with a few extra features.

## GraphQL

### Built-in schema

Just like GraphQL, DinoQL was built with a well defined schema in mind. This means that all DinoQL servers will have a schema that can be used to generate documentation, validate queries, and generate client code.

### Nested queries

GraphQL allows you to pick and choose exactly what data you want to receive from the server. DinoQL pulls back a bit on the customization, for the sake of simplicity of both client and server code. Instead of specifying individual properties, all properties are returned by default, however, for related resources (like a user's profile, friends, or roles), the server will only return the identifying fields of those resources. This allows the client to query those resources directly if needed later. If a client knows ahead of time that they need the full data for a related resource, they can specify that in the query so that they can save a round trip to the server.
