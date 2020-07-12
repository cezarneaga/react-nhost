# React Nhost

Make it easy to use Nhost with React.

- `NhostAuthProvider` - AuthProvider to check logged-in state.
- `NhostApolloProvider` - ApolloProvider preconfigured with authentication for GraphQL mutations, queries and subscriptions.

## Initiate

### Create React App

Add `NhostAuthProvider` and `NhostApolloProvider`.

`src/index.js`

```jsx
import React from "react";
import ReactDOM from "react-dom";
import { NhostAuthProvider, NhostApolloProvider } from "react-nhost";
import { auth } from "utils/nhost.js";
import App from "./App";

ReactDOM.render(
  <React.StrictMode>
    <NhostAuthProvider auth={auth}>
      <NhostApolloProvider
        auth={auth}
        gql_endpoint={`https://hasura-xxx.nhost.app/v1/graphql`}
      >
        <App />
      </NhostApolloProvider>
    </NhostAuthProvider>
  </React.StrictMode>,
  document.getElementById("root")
);
```

`src/utils/nhost.js`

```js
import nhost from "nhost-js-sdk";

const config = {
  base_url: "https://backend-xxx-nhost.app",
};

nhost.initializeApp(config);

const auth = nhost.auth();
const storage = nhost.storage();

export { auth, storage };
```

### NextJS

_(coming soon)_

---

## Protected Route

### React Router

`src/components/privateroute.jsx`

```jsx
import React from "react";
import { useAuth } from "react-nhost";
import { Route, Redirect } from "react-router-dom";

export function PrivateRoute({ children, ...rest }) {
  const { signedIn } = useAuth();

  if (signedIn === null) {
    return <div>Loading...</div>;
  }

  return (
    <Route
      {...rest}
      render={({ location }) =>
        signedIn ? (
          children
        ) : (
          <Redirect
            to={{
              pathname: "/login",
              state: { from: location },
            }}
          />
        )
      }
    />
  );
}
```

#### Usage

```jsx
import PrivateRoute from "components/privteroute.jsx";

<Router>
  <Switch>
    /* Unprotected routes */
    <Route exact path="/register">
      <Register />
    </Route>
    <Route exact path="/login">
      <Login />
    </Route>
    /* Protected routes */
    <PrivateRoute exact path="/">
      <Dashboard />
    </Route>
    <PrivateRoute exact path="/settings">
      <Settings />
    </Route>
  </Switch>
</Router>;
```

---

### NextJS

`components/privateroute.jsx`

```jsx
import { useAuth } from "react-nhost";

export function privateRoute(Component) {
  return () => {
    const { signedIn } = useAuth();

    // wait to see if the user is logged in or not.
    if (signedIn === null) {
      return <div>Checking auth...</div>;
    }

    if (!signedIn) {
      return <div>Login form or redirect to `/login`.<div>;
    }

    return (<Component {...arguments} />);
  };
}
```

#### Usage

`pages/dashboard.jsx`

```jsx
import React from "react";
import { protectRoute } from "components/privateroute.jsx";

function Dashboard(props) {
  return <div>My dashboard</div>;
}

export default privateRoute(Dashboard);
```
