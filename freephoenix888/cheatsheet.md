[источник](https://github.com/deep-foundation/documentation/wiki/CheatSheet)
# шпаргалка
* ДАННАЯ СТРАНИЦА НАХОДИТСЯ НА ДОРАБОТКЕ


---
# Found a bug?
Feel free to open issues

# Dev

## Set NEXT_PUBLIC_GQL_PATH env in Gitpod
```
NEXT_PUBLIC_GQL_PATH_WITH_PROTOCOL=$(gp url 3006)
export NEXT_PUBLIC_GQL_PATH="${NEXT_PUBLIC_GQL_PATH_WITH_PROTOCOL#*://}/gql"
```

## Update
```ts
git pull --recurse-submodules &&
npm install &&
$(
for directory in */ ; do
  cd ${directory}
  npm install
done
)
```

## Hard Cleaning (?????)
```
npm run gitpod-unmigrate; npm run docker-clear; npm run rm-migrates; npm run gitpod-engine; npm run gitpod-migrate;
```

## Remigrate
Note: do it when deep is running
```
npm run gitpod-remigrate
```

# Deeplinks

## Create a snapshot
```
cd packages/deeplinks && npm run snapshot:create && cd ../..;
```

## Load a snapshot
```
cd packages/deeplinks && npm run snapshot:last && cd ../..;
```

## Delete contains which point to PackageQuery/Install links
```ts
await deep.delete({
  type_id: {
      _id: ["@deep-foundation/core", "Contain"]
  },
  to: {
      _or: [
        {
          type_id: {
            _id: ["@deep-foundation/core", "PackageQuery"]
          }
        },
        {
          type_id: {
            _id: ["@deep-foundation/npm-packager", "Install"]
          }
        }
      ]
  }
})```

## Delete down to Contain links by containTree in containerLinkid
```ts
const containerLinkId = ;

await deep.delete({
  up: {
    tree_id: {
      _id: ['@deep-foundation/core', 'containTree'],
    },
    parent: {
      type_id: {
        _id: ['@deep-foundation/core', 'Contain'],
      },
      from_id: containerLinkId,
    },
  },
});
```


## Delete PackageQuery and Install links
DO NOT USE THIS! I have found out that it also deletes the packages that was installed by using install links
```ts
await deep.delete({
   up: {
      tree_id: {
         _id: ['@deep-foundation/core', 'containTree']
      },
      parent: {
         type_id: {
            _id: ['@deep-foundation/core', 'Contain']
         },
         to: {
            _or: [
               {
                  type_id: {
                     _id: ['@deep-foundation/npm-packager', 'Install']
                  }
               },
               {
                  type_id: {
                     _id: ['@deep-foundation/core', 'PackageQuery']
                  }
               }
            ]
         }
      }
   }
})
```

## Delete links created after MigrationEnd && contained in container && not packages
```ts
const containerLinkId = ;

const { data: [migrationsEndLink] } = await deep.select({
   type_id: {
      _id: ["@deep-foundation/core", "MigrationsEnd"]
   }
})

await deep.delete({
   up: {
      tree_id: {
         _eq: await deep.id("@deep-foundation/core", "containTree")
      },
      parent: {
         id: {
            _gt: migrationsEndLink.id,
         },
         type_id: await deep.id("@deep-foundation/core", "Contain"),
         from_id: containerLinkId,
         _not: {
            to: {
               type_id: {
                  _id: ["@deep-foundation/core", "Package"]
               }
            }
         }
      }
   }
})
```

## Delete links created after MigrationEnd contained in container
```ts
const containerLinkId = ;

const { data: [migrationsEndLink] } = await deep.select({
   type_id: {
      _id: ["@deep-foundation/core", "MigrationsEnd"]
   }
})

await deep.delete({
   up: {
      tree_id: {
         _eq: await deep.id("@deep-foundation/core", "containTree")
      },
      parent: {
         id: {
            _gt: migrationsEndLink.id,
         },
         type_id: await deep.id("@deep-foundation/core", "Contain"),
         from_id: containerLinkId
      }
   }
})
```

## Delete links created after MigrationEnd
```ts
const {data: [migrationsEndLink]} = await deep.select({
   type_id: {
      _id: ["@deep-foundation/core", "MigrationsEnd"]
   }
})

await deep.delete({
   id: {
      _gt: migrationsEndLink.id
   }
})
```


## Insert link and contain it
```ts
await deep.serial({
  operations: [
    {
      table: 'links',
      type: 'insert',
      objects: [
        {
          from_id: deep.linkId,
          type_id: await deep.id("@freephoenix888/object-to-types-async-converter", "Convert"),
          to_id: 1140,
          in: {
            data: {
              type_id: await deep.id("@deep-foundation/core", "Contain"),
              from_id: deep.linkId
            }
          }
        }
      ]
    }
  ]
})
```

## Delete all Focus links in container
```ts
const containerLinkId = 1040;

await deep.serial({
   operations: [
      {
         type: 'delete',
         table: 'links',
         exp: {
            up: {
               tree_id: {
                  _id: ['@deep-foundation/core', 'containTree']
               },
               parent: {
                  from_id: containerLinkId,
                  type_id: {
                     _id: ['@deep-foundation/core', 'Contain']
                  },
                  to: {
                     type_id: {
                        _id: ['@deep-foundation/core', 'Focus']
                     }
                  }
               }
            }
         }
      }
   ]
})
```

## Create type
```ts
const containTypeLinkId = await deep.id("@deep-foundation/core", "Contain");
const valueTypeLinkId = await deep.id("@deep-foundation/core", "Value");
const typeTypeLinkId = await deep.id("@deep-foundation/core", "Type");

const typeName = ;
const containerLinkId = ;
const typeOfValue = /* 'string' | 'number' | 'object' */;

const typeOfValueInsertData = {
  type_id: valueTypeLinkId,
  to_id: await deep.id("@deep-foundation/core", typeOfValue.slice(0, 1).toUpperCase() + typeOfValue.slice(1)),
  in: {
    data: [
      {
        type_id: containTypeLinkId,
        from_id: containerLinkId,
        string: {
          data: {
            value: `TypeOfValueOf${typeName}`
          }
        }
      }
    ]
  }
}

await deep.serial({
  operations: [
    {
      table: 'links',
      type: 'insert',
      objects:
        [
          {
            type_id: typeTypeLinkId,
            from_id: await deep.id("@deep-foundation/core", "Any"),
            to_id: await deep.id("@deep-foundation/core", "Any"),
            in: {
              data: [
                {
                  type_id: containTypeLinkId,
                  from_id: containerLinkId,
                  string: {
                    data: {
                      value: typeName
                    }
                  }
                }
              ]
            },
            out: {
              data: [
                typeOfValueInsertData
              ]
            }
          }
        ]
    }
  ]
})
```

## Give admin permissions to all packages
```ts
await deep.insert([
  {
    type_id: await deep.id("@deep-foundation/core", "Join"),
    from_id: await deep.id('deep', 'users', 'packages'),
    to_id: await deep.id('deep', 'admin'),
  },
])
```

## Give (admin permissions) to all packages
```ts
const joinTypeLinkId = await deep.id("@deep-foundation/core", "Join");
await deep.insert([
  {
    type_id: joinTypeLinkId,
    from_id: await deep.id('deep', 'users', 'packages'),
    to_id: await deep.id('deep', 'admin'),
  },
])
```

## Give (admin and other packages permissions) to package
```ts
const joinTypeLinkId = await deep.id("@deep-foundation/core", "Join");
const packageLinkId = ;
await deep.insert([
  {
    type_id: joinTypeLinkId,
    from_id: packageLinkId,
    to_id: await deep.id('deep', 'users', 'packages'),
  },
  {
    type_id: joinTypeLinkId,
    from_id: packageLinkId,
    to_id: await deep.id('deep', 'admin'),
  },
])
```

## Publish package
```ts
const packageNamespaceTypeLinkId = await deep.id("@deep-foundation/npm-packager", "PackageNamespace");
const packageQueryTypeLinkId = await deep.id("@deep-foundation/npm-packager", "PackageQuery");
const packageVersionTypeLinkId = await deep.id("@deep-foundation/npm-packager", "PackageVersion");
const publishTypeLinkId = await deep.id("@deep-foundation/npm-packager", "Publish");
const packageNamespace = ;
const packageVersion = ;
const packageLinkId = ;

const reservedIds = await deep.reserve(2);
const packageNamespaceInsertData = {
  id: reservedIds.pop(),
  type_id: packageNamespaceTypeLinkId,
  string: {
    data: {
      value: packageNamespace
    }
  }
};
const packageQueryInsertData = {
  id: reservedIds.pop(),
  type_id: packageQueryTypeLinkId,
  string: {
    data: {
      value: packageNamespace
    }
  }
};
const packageVersionInsertData = {
  type_id: packageVersionTypeLinkId,
  string: {
    data: {
      value: packageVersion
    }
  }
}
const publishInsertData = {
  type_id: publishTypeLinkId,
  from_id: packageLinkId,
  to_id: packageQueryInsertData.id
}
await deep.insert([
  packageNamespaceInsertData, packageQueryInsertData, packageVersionInsertData, publishInsertData
])
```


## Get contain link which points to link
```ts
const linkId = ;
await deep.select(
  {
    to_id: linkId,
    type_id: {
      _id: [
        '@deep-foundation/core', 'Contain'
      ]
    }
  }
);
```

## Update a link-value
```ts
const linkId = PLACEHOLDER;
const value = PLACEHOLDER;

await deep.update(
  {
    id: linkId
  },
  {
    value: value
  },
  {
    table: (typeof value) + 's'
  }
)
```

## Update a value of a link
```ts
const linkId = PLACEHOLDER;
const value = PLACEHOLDER;

await deep.update(
  {
    link_id: linkId
  },
  {
    value: value
  },
  {
    table: (typeof value) + 's'
  }
)
```

## Get a link whose value contains an object
```ts
const value = PLACEHOLDER;
await deep.select({
  [typeof value]: {
    value: {
      _contains: value,
    },
  },
});
```

## Login as admin
```ts
const adminLinkId = await deep.id('deep', 'admin');
await deep.login({
  linkId: adminLinkId,
});
```

## Initialize JS deep client
```ts
import { DeepClient } from "@deep-foundation/deeplinks/imports/client";
import { generateApolloClient } from "@deep-foundation/hasura/client";

const graphQlPath: string = /* process.env.GQL_URL_WITHOUT_PROTOCOL*/;
const ssl: boolean = /* !!~process.env.GQL_URL_WITHOUT_PROTOCOL.indexOf('localhost') ? false : true */;
const apolloClient = generateApolloClient({
  path: graphQlPath,
  ssl: ssl,
});
const unloginedDeep = new DeepClient({ apolloClient });
const guest = await unloginedDeep.guest();
const guestDeep = new DeepClient({ deep: unloginedDeep, ...guest });
const admin = await guestDeep.login({
  linkId: await guestDeep.id('deep', 'admin'),
});
const deep = new DeepClient({ deep: guestDeep, ...admin });
```

## Insert value-link
```ts
const value = /* "Value" / 123 / {property: value}  */;
const linkId = ;
await deep.insert(
  {
    link_id: linkId, value: value 
  },
  {
    table: `${typeof value}s`
  }
);
```

## Query Publish links and result
```ts
{
  up: {
    tree_id: {
      _id: ["@deep-foundation/core", "containTree"]
    },
    parent: {
      type_id: {
        _id: ["@deep-foundation/npm-packager", "Publish"]
      }
    }
  }
}
```

## Query package link
```ts
{
  "id": {
    "_id": [
      "@deep-foundation/core"
    ]
  }
}
```

## Query package links
```
{
  up: {
    parent: {
      id: {
        _id: ["@freephoenix888/boolean"]
      }
    },
    tree_id: {
      _id: ["@deep-foundation/core", "containTree"]
    }
  }
}
```

## Query links up to a tree and link
```ts
{
  down: {
    tree_id: treeId,
    link_id: parentId 
  }
}
```

## Query links down to a tree and link
```ts
{
  up: {
    tree_id: treeId,
    link_id: parentId 
  }
}
```

## Query links up to a tree and parent link
```ts
{
  down: {
    tree_id: treeId,
    parent_id: parentId 
  }
}
```

## Query links down to a tree and parent link
```ts
{
  up: {
    tree_id: treeId,
    parent_id: parentId 
  }
}
```

## Delete package and instances of package links
```ts
const packageName = "@deep-foundation/action-sheet";

await deep.delete({
    up: {
        "tree_id": {
          "_id": [
            "@deep-foundation/core",
            "containTree"
          ]
        },
      parent: {
        type_id: {
          _id: ["@deep-foundation/core", "Contain"]
        },
        to: {
          type: {
            in: {
              type_id: {
                _id: ["@deep-foundation/core", "Contain"]
              },
              from: { string: { value: { _eq: packageName  } } }
            }
          }
        }
      }
    }
  })

await deep.delete({
      up: {
        "tree_id": {
          "_id": [
            "@deep-foundation/core",
            "containTree"
          ]
        },
        parent: {
          type_id: {
            _id: ["@deep-foundation/core", "Contain"]
          },
          to: {
            type_id: {
              _id: ["@deep-foundation/core", "Package"]
            },
            string: {
              value: packageName 
            }
          }
        }
      }
    })
```

## Delete contain links which `to` does not exist
```ts
const {data: containLinks} = await deep.select({
    type_id: {
        _id: ["@deep-foundation/core", "Contain"]
    }
}, {returning: `${deep.selectReturning} to {${deep.selectReturning}}`})


const containLinkIdsToDelete = [];
for(const containLink of containLinks) {
    if(!containLink.to){
        containLinkIdsToDelete.push(containLink.id)
    }
}
await deep.delete({
    id: {
        _in: containLinkIdsToDelete
    }
})
```


## Delete link-instances from specific package contained in deep.linkId
```ts
const PACKAGE_NAME = "@deep-foundation/device";

await deep.delete({
    up: {
      parent: {
        type_id: {
          _id: ["@deep-foundation/core", "Contain"]
        },
        from_id: deep.linkId,
        to: {
          type: {
            in: {
              type_id: {
                _id: ["@deep-foundation/core", "Contain"]
              },
              from: { string: { value: { _eq: PACKAGE_NAME  } } }
            }
          }
        }
      }
    }
  })
```

## Delete link and contain link pointing to it
```ts
await deep.serial({
  operations: [
    {
      table: 'links',
      type: 'delete',
      exp: {
        up: {
          tree_id: {_eq: await deep.id("@deep-foundation/core", "containTree")},
          parent: {
            type_id: { _id: ["@deep-foundation/core", "Contain"] },
            to: ,
            from_id: ,
          }
        }
      }
    }
  ]
})
```

## Insert handler
### By Using Serial
```ts
const syncTextFileTypeLinkId = await deep.id("@deep-foundation/core", "SyncTextFile")
const containTypeLinkId = await deep.id("@deep-foundation/core", "Contain")
const supportsJsLinkId = await deep.id("@deep-foundation/core", "dockerSupportsJs" /* | "plv8SupportsJs" */ )
const handlerTypeLinkId = await deep.id("@deep-foundation/core", "Handler")
const handleOperationLinkId = await deep.id("@deep-foundation/core", "HandleInsert" /* | HandleUpdate | HandleDelete */);
const triggerTypeLinkId = ;
const packageLinkId = ;
const reservedIds = await deep.reserve(2);
const syncTextFileLinkId = reservedIds.pop();
const handlerLinkId = reservedIds.pop();

await deep.serial({
  operations: [
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: 
        {
          id: syncTextFileLinkId,
          type_id: syncTextFileTypeLinkId,
          in: {
            data: {
              type_id: containTypeLinkId,
              from_id: packageLinkId,
              string: { data: { value: "MyInsertHandlerCode" } },
            }
          }
        }
    }),
    createSerialOperation({
      table: 'strings',
      type: 'insert',
      objects: {
        link_id: syncTextFileLinkId,
        value: fs.readFileSync(path.join(__dirname, 'handler.js'), { encoding: 'utf-8' }), // Handler file must contain async function like this: async ({deep, data: {oldLink, newLink, triggeredByLinkId}}) => {},
      }
    }),
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: {
        id: handlerLinkId,
        type_id: handlerTypeLinkId,
        from_id: supportsJsLinkId,
        to_id: syncTextFileLinkId,
        in: {
          data: {
            type_id: containTypeLinkId,
            from_id: packageLinkId,
            string: { data: { value: "MyInsertHandler" } },
          }
        }
      }
    }),
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: {
        type_id: handleOperationLinkId,
        from_id: triggerTypeLinkId,
        to_id: handlerLinkId,
        in: {
          data: {
            type_id: containTypeLinkId,
            from_id: packageLinkId,
            string: { data: { value: "HandleOperation" } },
          }
        }
      }
    }),
  ]
})
```

### By Using Reserved ids + insert
```ts
const syncTextFileTypeLinkId = await deep.id("@deep-foundation/core", "SyncTextFile")
const containTypeLinkId = await deep.id("@deep-foundation/core", "Contain")
const supportsJsLinkId = await deep.id("@deep-foundation/core", "dockerSupportsJs" /* | "plv8SupportsJs" */ )
const handlerTypeLinkId = await deep.id("@deep-foundation/core", "Handler")
const handleOperationLinkId = await deep.id("@deep-foundation/core", "HandleInsert" /* | HandleUpdate | HandleDelete */);
const reservedIds = await deep.reserve(2);

const syncTextFileInsertData: MutationInputLink = {
  id: reservedIds.pop(),
  type_id: syncTextFileTypeLinkId,
  string: {
    data: {
      value: fs.readFileSync(path.join(__dirname, 'notifyInsertHandler.js'), { encoding: 'utf-8' }); // Handler file must contain async function like this: async ({deep, data: {oldLink, newLink, triggeredByLinkId}}) => {}
    },
  },
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: packageLinkId,
      string: { data: { value: "MyInsertHandlerCode" } },
    }
  }
}

const handlerInsertData: MutationInputLink = {
	id: reservedIds.pop(),
  type_id: handlerTypeLinkId,
  from_id: supportsJsLinkId,
  to_id: syncTextFileInsertData.id,
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: packageLinkId,
      string: { data: { value: "MyInsertHandler" } },
    }
  }
};

const handleOperationData: MutationInputLink = {
  type_id: handleOperationLinkId,
  from_id: triggerTypeLinkId,
  to_id: handlerInsertData.id,
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: packageLinkId,
      string: { data: { value: "HandleOperation" } },
    }
  }
}

await deep.insert([
  syncTextFileInsertData,
  handlerInsertData,
  handleOperationData
])
```

![image](https://user-images.githubusercontent.com/66206278/226910797-c21a577e-6ec4-4af8-96c9-d0936cc75594.png)


### Questions
#### Why do we use fs.readFile instead of importing?
If you import your handler function it will be bundled and handler code inside deep will not be human readable
If you put your handler code inside \` \` you will not be able to use ` inside handler code or you will have to escape that.

## Insert route handler
### By using serial
```ts
const portTypeLinkId = await deep.id('@deep-foundation/core', 'Port');
const containTypeLinkId = await deep.id('@deep-foundation/core', 'Contain');
const routerListeningLinkId = await deep.id('@deep-foundation/core', 'RouterListening');
const routerTypeLinkId = await deep.id('@deep-foundation/core', 'Router');
const routerStringUseLinkId = await deep.id('@deep-foundation/core', 'RouterStringUse');
const routeTypeLinkId = await deep.id('@deep-foundation/core', 'Route');
const handleRouteLinkId = await deep.id('@deep-foundation/core', 'HandleRoute');
const handlerTypeLinkId = await deep.id('@deep-foundation/core', 'Handler');
const supportsJsLinkId = await deep.id('@deep-foundation/core', 'dockerSupportsJs');
const syncTextFileTypeLinkId = await deep.id('@deep-foundation/core', 'SyncTextFile');

const code = ; 
const route = ;
const port = ;
const ownerLinkId = ;

const reservedIds = await deep.reserve(6);

const syncTextFileLinkId = reservedIds.pop();
const handlerLinkId = reservedIds.pop();
const routeLinkId = reservedIds.pop();
const routerStringUseLinkId = reservedIds.pop();
const routerLinkId = reservedIds.pop();
const portLinkId = reservedIds.pop();

await deep.serial({
  operations: [
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: {
        id: syncTextFileLinkId,
        type_id: syncTextFileTypeLinkId,
        in: {
          data: {
            type_id: containTypeLinkId,
            from_id: ownerLinkId
          }
        }
      }
    }),
    createSerialOperation({
      table: 'strings',
      type: 'insert',
      objects: {
        link_id: syncTextFileLinkId,
        value: code
      }
    }),
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: {
        id: handlerLinkId,
        type_id: handlerTypeLinkId,
        from_id: supportsJsLinkId,
        to_id: syncTextFileLinkId,
        in: {
          data: {
            type_id: containTypeLinkId,
            from_id: ownerLinkId
          }
        }
      }
    }),
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: {
        id: routeLinkId,
        type_id: routeTypeLinkId,
        in: {
          data: {
            type_id: containTypeLinkId,
            from_id: ownerLinkId
          }
        },
      }
    }),
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: {
        type_id: handleRouteLinkId,
        from_id: routeLinkId,
        to_id: handlerLinkId,
        in: {
          data: {
            type_id: containTypeLinkId,
            from_id: ownerLinkId
          }
        },
      }
    }),
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: {
        id: routerLinkId,
        type_id: routerTypeLinkId,
        in: {
          data: {
            type_id: containTypeLinkId,
            from_id: ownerLinkId
          }
        },
      }
    }),
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: {
        id: routerStringUseLinkId,
        type_id: routerStringUseTypeLinkId,
        from: routeLinkId,
        to_id: routerLinkId,
        in: {
          data: {
            type_id: containTypeLinkId,
            from_id: ownerLinkId
          }
        },
      }
    }),
    createSerialOperation({
      table: 'strings',
      type: 'insert',
      objects: {
        link_id: routerStringUseLinkId,
        value: route
      }
    }),
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: {
        id: portLinkId,
        type_id: portTypeLinkId,
        in: {
          data: {
            type_id: containTypeLinkId,
            from_id: ownerLinkId
          }
        },
      }
    }),
    createSerialOperation({
      table: 'numbers',
      type: 'insert',
      objects: {
        link_id: portLinkId,
        value: port
      }
    }),
    createSerialOperation({
      table: 'links',
      type: 'insert',
      objects: {
        type_id: routerListeningLinkId,
        from_id: routerLinkId,
        to_id: portLinkId,
        in: {
          data: {
            type_id: containTypeLinkId,
            from_id: ownerLinkId
          }
        },
      }
    }),
  ]
});
```
### By using insert
```ts
const port: number = ;
const route: string = ;
const handlerCode: string = fs.readFileSync('./routeHandler.js', {encoding: 'utf-8'}); // // Handler file must contain async function like this: async ({req, res, next, deep}) => {}
const ownerLinkId = deep.linkId;

// Type IDs
const portTypeLinkId = await deep.id('@deep-foundation/core', 'Port');
const containTypeLinkId = await deep.id('@deep-foundation/core', 'Contain');
const routerListeningLinkId = await deep.id('@deep-foundation/core', 'RouterListening');
const routerTypeLinkId = await deep.id('@deep-foundation/core', 'Router');
const routerStringUseLinkId = await deep.id('@deep-foundation/core', 'RouterStringUse');
const routeTypeLinkId = await deep.id('@deep-foundation/core', 'Route');
const handleRouteLinkId = await deep.id('@deep-foundation/core', 'HandleRoute');
const handlerTypeLinkId = await deep.id('@deep-foundation/core', 'Handler');
const supportsJsLinkId = await deep.id('@deep-foundation/core', 'dockerSupportsJs');
const syncTextFileTypeLinkId = await deep.id('@deep-foundation/core', 'SyncTextFile');

const reservedIds = await deep.reserve(1);

const syncTextFileData: MutationInputLink = {
  id: reservedIds.pop(),
  type_id: syncTextFileTypeLinkId,
  string: {
    data: {
      value: handlerCode
    }
  },
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: ownerLinkId
    }
  }
};

const handlerInsertData: MutationInputLink = {
  id: reservedIds.pop(),
  type_id: handlerTypeLinkId,
  from_id: supportsJsLinkId,
  to_id: syncTextFileData.id,
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: ownerLinkId
    }
  }
};

const routeInsertData: MutationInputLink = {
  id: reservedIds.pop(),
  type_id: routeTypeLinkId,
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: ownerLinkId
    }
  },
};

const routerInsertData: MutationInputLink = {
  type_id: routerTypeLinkId,
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: ownerLinkId
    }
  },
};

const portInsertData: MutationInputLink = {
  id: reservedIds.pop(),
  type_id: portTypeLinkId,
  number: { data: { value: port } },
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: ownerLinkId
    }
  },
};

const routerStringUseData: MutationInputLink = {
  type_id: routerStringUseLinkId,
  from: routeInsertData.id,
  to_id: routerInsertData.id,
  string: {
    data: {
      value: route
    }
  },
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: ownerLinkId
    }
  },
};

const routerListeningInsertData: MutationInputLink = {
  type_id: routerListeningLinkId,
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: ownerLinkId
    }
  },
  from: { data: routerInsertData },
  to_id: portInsertData.id
};

const handleRouteInsertData: MutationInputLink = {
  type_id: handleRouteLinkId,
  from_id: routeInsertData.id,
  to_id: handlerInsertData.id,
  in: {
    data: {
      type_id: containTypeLinkId,
      from_id: ownerLinkId
    }
  },
};

await deep.insert([
  syncTextFileData,
  handlerInsertData,
  handleRouteInsertData,
  routeInsertData,
  routerStringUseData,
  routerInsertData,
  routerListeningInsertData,
  portInsertData
]);
```

# Other
## Sample typescript application with initialized deepclient
```ts
import { DeepClient } from "@deep-foundation/deeplinks/imports/client";
import { generateApolloClient } from '@deep-foundation/hasura/client';
import * as dotenv from 'dotenv';
dotenv.config();

async function installPackage() {
  const apolloClient = generateApolloClient({
    path: process.env.NEXT_PUBLIC_GQL_PATH || '', // <<= HERE PATH TO UPDATE
    ssl: !!~process.env.NEXT_PUBLIC_GQL_PATH.indexOf('localhost')
      ? false
      : true,
  });

  const unloginedDeep = new DeepClient({ apolloClient });
  const guest = await unloginedDeep.guest();
  const guestDeep = new DeepClient({ deep: unloginedDeep, ...guest });
  const admin = await guestDeep.login({
    linkId: await guestDeep.id('deep', 'admin'),
  });
  const deep = new DeepClient({ deep: guestDeep, ...admin });
}

installPackage();
```