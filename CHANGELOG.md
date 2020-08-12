# v0.3.0

## Use ID fragments instead of `remoteIdFields` in the config

Update your node type configs like this to account for this change:

```diff
const gatsbyNodeTypes = [
  {
    remoteTypeName: `Post`,
-   remoteIdFields: [`__typename`, `id`],
    queries: `
      query LIST_POSTS {
-       posts(limit: $limit offset: $offset)
+       posts(limit: $limit offset: $offset) {
+         ..._PostId_
+       }
      }
+     fragment _PostId_ on Post {
+       __typename
+       id
+     }
    `,
  },
]
```

This has several advantages:

1. Source queries can be tested directly against the API
   (they are now valid queries that fetch node IDs)

2. This format helps to express complex ids (wasn't possible before), e.g.: 
    - Contentful puts id in nested object `sys`;
    - Drupal has a composite id: `{ entityId, entityLanguage { id } }`

## generateDefaultFragments: added `fields` to the list of aliased fields

Diff of function output for various internal Gatsby fields: 

```diff
fragment SomeRemoteType on SomeRemoteType {
  remoteId: id
  remoteInternal: internal
  remoteParent: parent
  remoteChildren: children
- fields {
+ remoteFields: fields {
    someField
  }
}
```
