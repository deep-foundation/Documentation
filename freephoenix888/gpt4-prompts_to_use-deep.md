[источник](https://github.com/deep-foundation/documentation/wiki/GPT-4-prompts-to-use-Deep)
# промпты gpt-4 для использования с deep
* ДАННАЯ СТРАНИЦА НАХОДИТСЯ НА ДОРАБОТКЕ


---
## User prompt:

**Context description**

Deep is a system that uses Hasura on top of PostgreSQL. 
`links` table have columns: `id`, `type_id` (mandatory) , `from_id` , `to_id` (all with `bigint` type, `0` by default).
`numbers`, `strings`, `objects` tables have columns `id`, `link_id` and `value`. 
`value` column has: `text` type in `strings` table, `numeric` type in `numbers` table and `jsonb` type in `objects` table.

We have `DeepClient` class in JavaScript, that usually placed in `deep` variable. It has `select`, `insert`, `update`, `delete` methods. These methods return object with `data` field, it is an array. `objects` array is first argument of `insert` method, we pass array or single object directly as the first argument here. `where` object is the first argument of `select`, `update`, `delete` methods, this object does not contain `where` field, this object also does not include `returning` field. `table` and `returning` are set using options object, that is always a last argument of these methods. Only `returning` field is used to define which fields are returned by GraphQL queries/mutations. By default `table` field is `'links'`, and `returning` by default is set to `'id type_id from_id to_id value'`.  If the default values of options are enough to get the code working we should not update them. 

`links` table has also `to`, `in`, `from`, `out`, `number`, `string`, `object` relationships in GQL. All these relationships can be used for nested inserts. To use nested inserts wrap inserted object in `{ data: ... }`. `out` relationship is used for outgoing links, all `out` links have the same `from`/`from_id`. `in` relationship is used for incoming links, all `in` links have the same `to`/`to_id`. `number`, `string`, `object` relationships should use only `value` field on nested insert.  Usage of nested inserts makes code shorter and reduces latency.

Each `Contain` link goes from a container link to a contained link. `Contain` links has `string` relationship that allows giving contained links names in the context of container.
`Type` link instances do not have values attached directly. The name of the type instance is set using incoming `Contain` link. Such `Contain` always inserted with `string` relationship defined.
`Value` link that goes from a type link to `Number`/`String`/`Object` type link itself (not its instance) is created for each type link which instances may have a value attached.
Node in the context of Deep is a link record that have `from_id` and `to_id` columns set to `0`. For these node links we never set `from_id`, `from`, `to_id` and `to` fields on insert.
`value` field is never set on insert of a link, `number`, `string` and `object` fields are used instead, if we know that type of that link supports attaching of a value to an instance.

**Issue description**

`Task` is a user defined node type, its instances have string value. It should be created by `createTaskType` function. 
Each instance of `Task` link has incoming `Contain` link from a container link.
All other mentioned link types are available in `@deep-foundation/core` package.

```js
const typeTypeId = await deep.id('@deep-foundation/core', 'Type');
const containTypeId = await deep.id('@deep-foundation/core', 'Contain');
const valueTypeId = await deep.id('@deep-foundation/core', 'Value');
...

const createTaskType = async (containerId) => {
  ...
}

const createTask = async (containerId, taskTypeId, description) => {
  ...
}
```

@chatgpt fill blanks in the code.

## GPT-4 response:

Considering the provided context, I'll fill in the blanks for the `createTaskType` and `createTask` functions described in the issue:

```js
const typeTypeId = await deep.id('@deep-foundation/core', 'Type');
const containTypeId = await deep.id('@deep-foundation/core', 'Contain');
const valueTypeId = await deep.id('@deep-foundation/core', 'Value');
const stringTypeId = await deep.id('@deep-foundation/core', 'String');

const createTaskType = async (containerId) => {
  const newTaskType = await deep.insert({
    type_id: typeTypeId,
    in: {
      data: {
        type_id: containTypeId,
        from_id: containerId,
        string: { data: { value: 'Task' } },
      },
    },
  });

  const newValueLink = await deep.insert({
    from_id: newTaskType.data[0].id,
    to_id: stringTypeId,
    type_id: valueTypeId,
  });

  return newTaskType.data[0];
};

const createTask = async (containerId, taskTypeId, description) => {
  const newTaskInstance = await deep.insert({
    type_id: taskTypeId,
    string: { data: { value: description } },
    in: {
      data: {
        type_id: containTypeId,
        from_id: containerId,
      },
    },
  });

  return newTaskInstance.data[0];
};
```

In the `createTaskType` function, we first create a new `Task` type link by inserting a node with `type_id` set to `typeTypeId`. This new `Task` type has an incoming `Contain` link from the specified `containerId` and uses nested inserts with `string` relationship to set its name as 'Task'. We then create a `Value` link to establish the relationship between the new `Task` type link and the `String` type link.

In the `createTask` function, we create a new `Task` instance by inserting a link with `type_id` set to `taskTypeId`. The description of the task is added using a nested insert with the `string` relationship. Finally, the task instance has an incoming `Contain` link from the specified `containerId`.

---


## User prompt:

Deep is a system that uses Hasura on top of PostgreSQL. links table have columns: `id`, `type_id` (mandatory) , `from_id` , `to_id` (all with `bigint` type, `0` by default). We have `DeepClient` class in JavaScript, that usually placed in `deep` variable. It has `select`, `insert`, `update`, `delete` methods. Links array is returned as `data` field from these methods. `where` object is the first argument of select, update, delete methods, this object does not contain `where` field, this object also does not include `returning` field. `where` bool_exp has also `to`, `in`  and `from`, `out` relationships. `out` relationship is used for outgoing links, all `out` links have the same `from`/`from_id`. `in` relationship is used for incoming links, all `in` links have the same `to`/`to_id`. `table` and `returning` are set using options object, that is always a last argument of these methods. Only `returning` field is used to define which fields are returned by GraphQL queries/mutations. By default `table` field is `'links'`, and `returning` by default is set to `'id type_id from_id to_id value'`. If the default values of options are enough to get the code working we should not update them.

Each instance of `Package` link has incoming `PackageVersion` link from `PackageNamespace` link.

```js
const deletePackage = async(packageId) => {
  const packageTypeId = await deep.id('@deep-foundation/core' 'Package');
  const packageVersionTypeId = await deep.id('@deep-foundation/core' 'PackageVersion');
  // ...
  return await deep.delete({
    // ...
  });
}
```

@chatgpt fill blanks in the code. Use `_or`  in a single `deep.delete` call.

## GPT-4 response:

With the updated context and the requirement to use `_or` in a single `deep.delete` call, here is the revised implementation of the `deletePackage` function that deletes the `Package` link and its incoming `PackageVersion` link from the `PackageNamespace` link:

```js
const deletePackage = async (packageId) => {
  const packageTypeId = await deep.id('@deep-foundation/core', 'Package');
  const packageVersionTypeId = await deep.id('@deep-foundation/core', 'PackageVersion');

  // Delete the Package link and the PackageVersion link in a single deep.delete call
  const deletedLinks = await deep.delete({
    _or: [
      { to: { id: packageId }, type_id: packageVersionTypeId }, // Delete PackageVersion links
      { id: packageId, type_id: packageTypeId }, // Delete the Package link
    ],
  });

  return deletedLinks;
};
```

In this implementation, we use the `_or` property in the `where` object within a single `deep.delete` call to delete both the `Package` link and the `PackageVersion` links connected to the `PackageNamespace`. The result will be the deleted links, including both the `Package` and its incoming `PackageVersion` links.