DinoQL is an API paradigm for building and consuming data in an object-oriented way. It is a declarative language that allows you to define your data models and their relationships in a simple, human-readable way, while also being machine-readable for automated code generation.

DinoQL takes inspiration from both REST and GraphQL, getting the best of both worlds. Taking advantage of the ease of development for both client and server that comes with a structured schema like GraphQL, and the mental model of a REST API, DinoQL is a great way to provide and consume data.

DinoQL is designed to be extensible, allowing you to add new data types and metadata without needing to modify the core code.

# Schema

A DinoQL schema is made up of a set of scalars, enums, interfaces, and resources.

Identifiers for scalars, interfaces, resources, properties, queries, and actions in DinoQL must match the following regular expression: `[a-zA-Z_][a-zA-Z0-9_-]*`.

## Scalars

DinoQL incudes a few default scalar types, but you can add your own with fallback types in case a consumer doesn't have a generator for a specific type.

The built in types are:

- string
- integer
- float
- boolean

These can be extended to include more types, with any new types beign able to specify a fallback type. For example, if you wanted to add a new type called `DateTime`, you could add it to the schema like so:

```
scalar DateTime string;
```

This tells an API consumer that the type `DateTime` will be serialized as a string, and if the consumer has no generator for `DateTime`, it can fall back to the `string` type.

The fallback type can be any default scalar, or an interface if your type is more complex and needs to be serialized as an object.

## Enums

Enums are a way to define a set of possible values for a type. Enums will always be serialized as strings, deserialization is left up to the consumer as not all languages have a native enum type.

```
enum UserRole {
	ADMIN
	USER
}
```

## Interfaces

Interfaces serve as the schema definition for an object that will never exist on its own. An interface can have any number of properties, and each property can be of any type. Interfaces cannot have queries or actions.

```
interface UserMeta {
	thing1: string;
	thing2: integer;
}
```

Properties in DinoQL are non-null by default, but you can make them nullable with the `?` suffix.

```
interface UserMeta {
	thing1: string;
	thing2: integer;
	nullableThing: string?;
}
```

In addition, you can add the `?` suffix to a property name to denote that the property may not be present.

```
interface UserMeta {
	thing1: string;
	thing2: integer;
	nullableThing: string?;
	optionalThing?: string;
}
```

You can also turn a field into an array by surrounding the type with `[` and `]`.

```
interface UserMeta {
	thing1: string;
	thing2: integer;
	nullableThing: string?;
	optionalThing?: string;
	things: [string];
}
```

## Resources

A resource represents a specific type of data that can be fetched from the API, while an interface represents an object type that doesn't exist on its own.

Resources fall into two categories:

- Instanced resources
- Static resources

Instanced resources are any resource where multiple objects exist of that type. For example, an app's users would be an instanced resource.

Static resources are any resource that only has one copy of relevance to the client. For example, static resources could be something global to the app, such as status info, or it could be something that is specific to a user, such as the current user's profile or settings.

Resources are made up of properties, queries, and actions. Queries and actions are the methods that are used to fetch and mutate data on the API.

### Properties

An instanced resource _must_ have at least one primary property, identified by the prefix `#`. Primary properties are the properties that uniquely identify a resource like an `id`, `email`, `etc`. Primary properties are required to be unique. An object can have multiple primary properties to create a composite key.

```
resource User {
	#id: int;
}

static resource AppStatus {

}
```

A resource can have any number of properties. Properties can be of any type, including scalars, enums, interfaces, and other resources.

```
resource User {
	#id: int;
	name: string;
	nickname?: string;
	role: UserRole;
	meta: UserMeta;
}

static resource AppStatus {
	status: string;
}
```

### Queries

Queries come in two flavors, static and instance. Static queries are relevant to the resource as a whole, and are often used to fetch one or more instances of that resource. Instance queries are relevant to a specific instance of that resource, and can be used to grab data related to that object, for example mutual friends withs a user.

Every instanced resource has a default static `get` query that takes an object containing the primary fields of the resource and returns the resouce matching those fields. Global resources have a `get` query that takes no paramaters. This query should not be explicitly defined, and you cannot overwrite it.

To mark a query as static, add the `static` modifier to the query definition.

```
resource User {
	// ...

	static query getByUsername(username: string): User?;

	query getMutualFriends(): [User];
}
```

### Actions

Just like queries, actions come in both static and instance flavors.

Static actions can be used for things like creating a new resource. Instance actions can be used for things like updating or deleting an existing resource.

```
interface CreateUserOptions {
	name: string;
	nickname?: string;
	role: UserRole;
	meta: UserMeta;
}

interface UpdateUserOptions {
	nickname?: string;
	role?: string;
	meta?: UserMeta;
}

resource User {
	// ...

	static action create(options: CreateUserOptions): User;

	action update(options: UpdateUserOptions): User;
}
```
