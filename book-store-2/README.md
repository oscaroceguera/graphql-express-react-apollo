## apollo.js

To initialize an apollo client we need two things, the network layer called link, and the storage layer called cache. First we are bringing in all the the required packages. Most of these are install using the apollo-client-preset package. Then we are creating a httpLink which connects to a graphql server. Then we are initializing the cache using the InMemoryCache class. You may use a localStorage cache as well.

Then we are initializing the state link. State link takes three arguments namely cache, resolver and defaults. The cache is the place where store's data will be stored. We have passed the inmemory cache in there. The resolvers are basically like reducers in redux which the Mutations which changes the data in the store. We are going to import then later. Finally we have defaults which is the initial state of the store.

```javascript
import { ApolloClient } from 'apollo-client';
import { InMemoryCache } from 'apollo-cache-inmemory';
import { withClientState } from 'apollo-link-state';
import { HttpLink } from 'apollo-link-http';
import { ApolloLink } from 'apollo-link';
import resolvers from './resolvers';

const httpLink = new HttpLink({
  uri: 'http://localhost:8080/graphql'
});

const cache = new InMemoryCache();

const stateLink = withClientState({
  cache,
  resolvers,
  defaults: {
    show_type: 'BELOW_15'
  }
});

const link = ApolloLink.from([stateLink, httpLink]);

const client = new ApolloClient({
  link,
  cache
});

export default client;
```

## Add mutation `src/resolvers`

This file exports an object containing mutations. Each key value pair represents a Mutation. The first argument is the parent object. Dont care much about it as docs says the same lol. The second argument is the query variables. The third argument is the context containing cache. We are taking the data and writing it to the cache. At last we are returning the show_type with a typename. Typename can be anything and it is important as it is used as key for the cache.

```javascript
export default {
  Mutation: {
    changeShowType: (_, { show_type }, { cache }) => {
      cache.writeData({ data: { show_type } });
      return { show_type, __typename: 'ShowType' };
    }
  }
};
``