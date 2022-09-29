DinoQL is a new way to think about web APIs. DinoQL treats a web API like you would a library running on the same machine as your client code. Resources and actions related to that resource are grouped together, providing consumers a more intuitive way to understand the API and its capabilities.

While DinoQL was designed with HTTP APIs in mind, any transport layer can be used. DinoQL is designed to be extensible, allowing you to add new data types and metadata without needing to modify the core code.

## Why are you making a new standard?

DinoQL was born of the minor annoyances I've had in creating and consuming web APIs. For more information, see [Why](Why.md).

## How do I use DinoQL?

For creating a DinoQL schema, see [Schema](Schema.md). For using a DinoQL API, see [Usage](Usage.md).

## Are there any examples?

Short answer: yes. Long answer: yes, but they're not very good yet. I'm working on it.

I've created a fairly simple implementation of a generic DinoQL server in TypeScript that you can find [here](https://github.com/Rodentman87/dinoql-server-ts). And a small example server that uses it [here](https://github.com/Rodentman87/dinoql-server-test).

## What's the status of DinoQL?

DinoQL is still in the early stages of development. I'm not super familiar with writing grammars for languages right now, so I'm working on paring it down to a simpler format. In terms of planned things to add to the standard, the next two major things are:

- Adding support for unions
- Decorators for providing metadata about resources, queries, actions, and fields

For the decorators, the goal is to make them flexible enough to provide metadata for various use-cases, such as authentication, permissions, rate-limits, etc.

## How can I contribute?

If you want to discuss the standard, hop on in to the discussions tab here on GitHub. For contributions to the standard, feel free to open a pull request. Contributions of implementations, tooling, and code generationg in all languages are welcomed and encouraged.
