Like GraphQL, DinoQL operates on a single endpoint. Simply send a POST request to the endpoint with a JSON body containing the query or action you want to execute.

You can also execute queries or actions in batches. Simply send a POST request to the endpoint with a JSON body containing an array of queries or actions.

Queries and actions both follow the same format:

| Property   | Type             | Description                                                                                                                                                                                                                                                               |
| ---------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `resource` | string           | The resource you are acting on                                                                                                                                                                                                                                            |
| `id`?      | string or object | An object containing the identifiers of the specific instance of this object you're calling this method on. Pass "static" instead if you want to call a static method. This property can be omitted on static resources.                                                  |
| `method`   | string           | The name of the query or action you are calling.                                                                                                                                                                                                                          |
| `params`   | object           | The parameters you are passing to this method. Each key corresponds to a parameter name.                                                                                                                                                                                  |
| `batchId`? | string           | An arbitrary id used to identify this specific request inside a batch request. This is specified by the client and the server will use this to identify the corresponding result in the batch results. This only needs to be specified if you're running a batch request. |

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
