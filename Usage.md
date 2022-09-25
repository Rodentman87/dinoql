Like GraphQL, DinoQL operates on a single endpoint. Simply send a POST request to the endpoint with a JSON body containing the query or action you want to execute.

You can also execute queries or actions in batches. You can send a POST request to the endpoint with a JSON body containing an array of queries or actions.

Queries and actions both follow the same format:

| Property     | Type             | Description                                                                                                                                                                                                                                                               |
| ------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `resource`   | string           | The resource you are acting on                                                                                                                                                                                                                                            |
| `id`         | string or object | An object containing the identifiers of the specific instance of this object you're calling this method on. Pass "static" instead if you want to call a static method. This property will always be "static" on static resources.                                         |
| `method`     | string           | The name of the query or action you are calling.                                                                                                                                                                                                                          |
| `params`     | object           | The parameters you are passing to this method. Each key corresponds to a parameter name.                                                                                                                                                                                  |
| `relations?` | object           | An object describing which relations the server should resolve during this request.                                                                                                                                                                                       |
| `batchId`?   | string           | An arbitrary id used to identify this specific request inside a batch request. This is specified by the client and the server will use this to identify the corresponding result in the batch results. This only needs to be specified if you're running a batch request. |

Let's take a look at an example of a simple get query:

```json
{
	"resource": "User",
	"id": "static",
	"method": "get",
	"params": {
		"id": "1"
	}
}
```

and the server response for that query:

```json
{
	"id": "1",
	"name": "maisy",
	"role": "ADMIN",
	"meta": {
		"thing1": "foo",
		"thing2": 42,
		"nullableThing": null,
		"things": ["foo", "bar"]
	}
}
```

And now a more complex action:

```json
{
	"resource": "User",
	"id": {
		"id": "1"
	},
	"method": "update",
	"params": {
		"nickname": "Maisy 🦕"
	}
}
```

and the server response for that action:

```json
{
	"id": "1",
	"name": "maisy",
	"nickname": "Maisy 🦕",
	"role": "ADMIN",
	"meta": {
		"thing1": "foo",
		"thing2": 42,
		"nullableThing": null,
		"things": ["foo", "bar"]
	}
}
```

A note on the `relations` property:

By default, the server will only return the identifiers for any resources nested inside the returned object. To tell the server to fetch the full information for an object when completing a request, put the key for the object into the resources field with the value `true`. For example:

```
resource Profile {
	#id: string;
	bio: string;
}

resource User {
	#id: string;
	name: string;
	profile: Profile;
}
```

If we call `get` query on the `User` resource without specifying any relations, the server will return the following response:

```
{
	"id": "1",
	"name": "maisy",
	"profile": {
		"id": "1"
	}
}
```

Here, only the `id` property of the `Profile` resource is returned. This allows us to later fetch the profile if needed. However, if we know that we need the full data right away, we can pass `{ "profile": true }` to the `relations` property and get:

```
{
	"id": "1",
	"name": "maisy",
	"profile": {
		"id": "1",
		"bio": "I like dinosaurs 🦕"
	}
}
```

This works for arbitrarily nested resources. For example, you could specify `{ "profile": { "posts": true } }` to get the full data for the profile and its posts.

If a method takes a resource as a parameter (or nested inside another parameter like an interface), you specify the resource's ids instead of the full data. For example:

```
resource User {
	#id: string;
	name: string;
	profile: Profile;

	mutualFriends(user: User): [User];
}
```

```json
{
	"resource": "User",
	"id": {
		"id": "1"
	},
	"method": "mutualFriends",
	"params": {
		"user": {
			"id": "2"
		}
	}
}
```

## Batch Requests

When making batch requests, send the server an array of request objects with unique `batchIds`, the server will then return the results as an object with the `batchId` as the key and the result as the value.

For example, if we send the following batch request:

```json
[
	{
		"resource": "User",
		"id": "static",
		"method": "get",
		"params": {
			"id": "1"
		},
		"batchId": "1"
	},
	{
		"resource": "User",
		"id": "static",
		"method": "get",
		"params": {
			"id": "2"
		},
		"batchId": "2"
	}
]
```

We might get a response like:

```json
{
	"1": {
		"id": "1",
		"name": "maisy",
		"role": "ADMIN",
		"meta": {
			"thing1": "foo",
			"thing2": 42,
			"nullableThing": null,
			"things": ["foo", "bar"]
		}
	},
	"2": {
		"id": "2",
		"name": "joe",
		"role": "USER",
		"meta": {
			"thing1": "bar",
			"thing2": 24,
			"nullableThing": null,
			"things": ["foo", "bar"]
		}
	}
}
```
