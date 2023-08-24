[источник](https://github.com/deep-foundation/documentation/wiki/Guide)
# руководство
* ДАННАЯ СТРАНИЦА НАХОДИТСЯ НА ДОРАБОТКЕ


---
# Before you begin
Do not just memorize this information. This is not a verse that you must tell to your teacher. This is a guide that must help you to learn deep by doing

# First Steps

## Launch deep
Choose one of the variants. Right now we recommend you to use gitpod
 - [gitpod](https://gitpod.io/#https://github.com/deep-foundation/dev) 
 - Local. [instruction](https://github.com/deep-foundation/dev)

## Open deepcase (Deep Graphical user interface)  
Deepcase is located on 3007 port. 
Note: For local deep it is localhost:3007, in gitpod you can see ports on the ports panel

## Query links to see them.  
By default you see links contained in your space, you can see more by using queries.  
### Insert query
Press right mouse button and choose `insert` in context menu - insertion window will appear. Write `query` in search panel located in insertion window and choose list view by using one of the two buttons on the top-right corner of the insertion window and then choose query link and create it by using mark button near to the bottom-right corner.
### Edit value of `Query` link
Press right mouse button on query link and choose editor from the context menu.
Query all the users. Literally we are going to query all links where type link is contained as `User` in `@deep-foundation/core` space
```
{
  type_id: {
    _id: ["@deep-foundation/core", "User"]
  }
}
```

### Make query active
Press right mouse button and choose `insert` in context menu - insertion window will appear. Write `active` in search panel located in insertion window and choose list view by using one of the two buttons on the top-right corner of the insertion window and then choose active link and create it by using mark button near to the bottom-right corner.
## See all users
## Find user with name "admin"
## Login as admin
Press right mouse button on the user with name `admin` and press on the `login` button in context menu
## See that you are authenticated as admin user and see admin space (space link + links contained in the space)
Congratulations! 🎉🎉🎉  
You have completed `First steps` and now you can
- Insert links
- Edit links
- Query links
- Login

# Your first package
## Insert package link
You have learned how to insert links in the [First steps](#first-steps). Feel free to refresh your memory by re-reading it again, it is not scary, our mind is not ideal!
## Set package name
Edit value of Package link (not contain link) to set name of the package
## Switch to package space
You see links contained in the space. When you insert links they are contained in the space. We are going to see and contain new links in your new package so we have to switch to its space
Press right mouse button on the package link and press `space` in the context menu
## Insert type
Do you remember `query` and `active` links? They are links of type `Type` that is contained in the `@deep-foundation/core` and they by the way contained in the same package. Let us create our own type! 🔨  
Note: when we were inserting `query` and `active` links types of those links were `Query` and `Active` types that are contained in the `@deep-foundation/core` package. Do not be scared! Types are links too and you use can use links as type, from, to for another link.  
Insert `Type` link by using right mouse button and insertion window. If you have forgot how to do that refresh your memory by reading [Insert query step](#insert-query)  
## Choose how your package treats your type
Edit value of the `Contain` link which points from your package to the `Type` link
In short - to give a name to your type - you have edit `Contain` link which goes from your Package link to the Type link
In detail - `Query` link contained in the `@deep-foundation/core` package is just a `Type` link. A "meaning" is in the string value of the `Contain` link that points from the package to the type with specific string value.  
Literally we say our package treats this type as ...(anything you have written in the value of the contain link)
## Insert npm token
- Go to the space of your user by clicking X near to the space button.  
- Insert link Token from the `@deep-foundation/npm-packager` package (package name is written below type name)
- Set your npm token as value of Token link
## Publish your package
- Press left mouse button on the Package link
- Write package name and version in the appeared window
- Click `publish` button
## Look at your package on npm
## Delete your package from your deep
Note that you should delete links by using `Delete down` button in the context menu on `Contain` links which points to the link you want to delete. If you just delete the link - contain link will not be deleted automatically (Maybe it should? 🤔 ). Delete down delete will delete all the links down to the Contain link through the `containTree`
## Install it again
Open packager window by using the button on the up-right corner and install your package

# Handle events
## General Information
### Links
#### SyncTextFile
SyncTextFile contains code of handler. Each type of handler expects different types of code. For example supportsDockerJs handlers expect async arrow function inside your SyncTextFile while plv8SupportsJs expects arrow function
#### Handler
Handler points from `supports` link to SyncTextFile. 
`Supports` links present which environment your code will be executed in
##### List of `support`:
- `dockerSupportsJs`
- `plv8SupportsJs`
- TODO...
#### Handle
Handle points 
from link-type which links-instances will trigger handler  
to Handler  
#### Types of `Handle`'s
- `HandleInsert`
- `HandleUpdate`
- `HandleDelete`
## Your first Insert/Update/Delete handler
### Insert `SyncTextFile`
Set this as content of the SyncTextFile:
```js
() => {
  return "Hello Deep!"
}
```
### Insert `Query`
You have to see `docketSupportsJs` link therefore we need query
Content of the Query:
```js
{
  id: {
    _id: ["@deep-foundation/core", "dockerSupportsJs"]
  }
}
```
This query literally says give me link that is contained as `dockerSupportsJs` in the `@deep-foundation/core` package
### Make the Query active
After making the query active you should see `dockerSupportsJs` link
### Insert `Handler`
Insert Handler. It must poing from `dockerSupportsJs` to `SyncTextFile` as you can see on the deepcase's hint on the right-bottom corner
### Insert `Handle`
You have handler but it is not used yet. To handle  insertion of specific links we must create Handle which points from your link to `Handler`. Now insert any type of `Handle` links
#### Insert link of type `Type`
What is type? Links that you have met - `SyncTextFile`, `Query` are types and you create their instances
#### Give a name to your new link of type `Type` by editing `Contain`
Name for Type is used to find it by using `deep.id(ownerLinkId, containValue)` (for example `deep.id("@deep-foundation/core", "SyncTextFile")`)
#### Insert Handle
Insert HandleInsert/HandleUpdate/HandleDelete to handle specific operation on instances of your type
### Insert/Update/Delete link of type `YourTypeName`
Do an operation to a link of your type. Insert it or update it or delete it whether what operation your handler is handling
### Enable promises by clicking on `promises` word on the top panel
To see result of async handlers (`dockerSupportsJs` handlers are async) you should enable `promises` setting in deepcase to see handler results on specific link
### See result of your handler 
After doing an operation which is handled by your handler you must see result of handler call as structure of links represented as `Then`, `Promise`, `Result`, (`Resolved` or `Rejected`),`PromiseResult`. Then link is linked to the link you have made operation on
### Congratulation! 
In this part of guide you have learned how to:
- Create package
- Create types
- Create Insert/Update/Delete handlers
- Publish package