

## ReactJs Fundamentals

### Table of Contents
- [ReactJs Fundamentals](#reactjs-fundamentals)
  - [Table of Contents](#table-of-contents)
  - [What is React?](#what-is-react)
  - [What is JSX?](#what-is-jsx)
  - [What are States and Props?](#what-are-states-and-props)
  - [What is prop drilling?](#what-is-prop-drilling)
  - [Difference between ContextApi vs Redux?](#difference-between-contextapi-vs-redux)
  - [What are middlewares in Redux?](#what-are-middlewares-in-redux)
  - [How to update state in parent from child component?](#how-to-update-state-in-parent-from-child-component)
  - [What is useRef?](#what-is-useref)
  - [What are forwardRefs?](#what-are-forwardrefs)
  - [What are HOC?](#what-are-hoc)
  - [What are custom hooks?](#what-are-custom-hooks)
  - [Class vs Functional Component?](#class-vs-functional-component)
  - [How does useEffect handle component lifecycle?](#how-does-useeffect-handle-component-lifecycle)
  - [What are error boundaries?](#what-are-error-boundaries)
  - [What is react-router?](#what-is-react-router)
  - [How to improve Preformance?](#how-to-improve-preformance)
  - [What consideration you would make to handle security?](#what-consideration-you-would-make-to-handle-security)

### What is React?

It's a Library. It uses **virtual dom** which is a copy of **real dom** and whenever a state is changed and virtual dom is updated, react only re-renders that node of the real dom instead of complete dom.

### What is JSX?

It's a XML syntex for writing html in javascript file.

### What are States and Props?

**State** are the local data for a component and when a state is changed the html depending on the state re-renders. **Props** are properties received from the parent to child component.

### What is prop drilling?

When a prop of component is required to be used in it's nth child, then we have to pass it through inbetween childrens also. This is called prop drilling. It creates a tightly coupled component and clumpsy code. To avoid it we use **ContextApi** or **Redux**.

### Difference between ContextApi vs Redux?

Both are used to create global state for a react app. It works in a way that there will be a **Provider** to wrap a component and children component as **Consumer** to consume data. 
Differences are following: 
- **Dependecy**: ContextApi is inbuilt with react library itself whereas Redux is third party library.
- **Bundle Size**: ContextApi doesn't add to your app bundle but Redux does.
- **Ease of use**: ContextApi is very easy to get started but Redux requires some learning curve.
- **Complexity**: ContextApi is good for small applications or where we dont need complex global states for different parts of the application. Redux is really great at handling complex state management or states for different parts of application.

**Explain how Redux works.**

Redux is used manage global state of application. There are three components to it. The core concepts of Redux — Store, Actions, Reducers, Subscription.
1. **Store** is where state is maintained.
2. **Reducer** has initial state and updates the state based on the actionType with it's payload. 
3. **Actions** are dispatched when we want to let reducer know about an event with actionType and payload.
4. **Subscribe** is the way to get any global state in a component to reflect the UI. 

### What are middlewares in Redux?
Middlewares are the somthing like interceptors which sits between dispatching an action and the moment it reaches to reducer. In Redux we use middlewares to handle async api calls. `redux-saga` (uses generator functions), `redux-thunk` (uses closure to wrap the actions) are two majorly used.  
```
const middlewareEnhancer = applyMiddleware(thunk or saga)

// Pass enhancer as the second arg, since there's no preloadedState
const store = createStore(rootReducer, middlewareEnhancer)
```

### How to update state in parent from child component?

To update parent, a function from parent can be passed to child as a prop and child can trigger the function and function in parent can update the state when triggered. Similar to how callback works.

### What is useRef?

`useRef` is used to create a ref to a html element which can be used to read content of the element or to focus.

### What are forwardRefs?

It's used to pass ref to the children component as a prop.
```
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

### What are HOC?

Higher Order Component are component which takes component as argument and returns a new component which added functionality. They are used in case when one logic is repeated twice or more at different components. HOC can have that logic and component can use it. Purpose is to dedupplicate the code and less bundle size and maintainability. 

### What are custom hooks?

Hooks in react are used in functional component, like `useState()` to create a state object. When you have component logic that needs to be used by multiple components, we can extract that logic to a custom Hook. Custom Hooks start with "use". Example: useFetch.

```
import { useState, useEffect } from "react";

const useFetch = (url) => {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch(url)
      .then((res) => res.json())
      .then((data) => setData(data));
  }, [url]);

  return [data];
};

export default useFetch;
```
Usage:
```
const Home = () => {
  const [data] = useFetch("https://jsonplaceholder.typicode.com/todos");

  return (
    <>
      {data &&
        data.map((item) => {
          return <p key={item.id}>{item.title}</p>;
        })}
    </>
  );
};
```

### Class vs Functional Component?

After react 16v, we can create states in function also using `useState`. And the lifecycle effects can be handled by `useEffect`. The only differency would be is that we can wite less code to achieve the same thing in functional component. And less code has its own advantages like better readablity, testing, less buggy.

### How does useEffect handle component lifecycle?

There are basically three phases. Mounting, Updating, Unmounting. 
```
useEffect(() => {
    // data fetching
    return () => {
        // executes on unmounting
    }
}, []) // [] -> Optional dependency array
```
If we dont pass `[]` -> data fetching happens on every render. i.e., Mounting & Updating
If we pass `[]` -> data fetching happens only on first render. i.e., Mounting
if we pass `['state1', 'state2']` -> data fetching happens every time `state1` or `state2` updates. i.e., Mounting & Updating when `state1`, `state2` changes

### What are error boundaries?

Error boundries are used to render a fallback UI when an error happens while rendering of component. Only possible using class component. Wrap App component inside `ErrorBoundary`.
```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // You can also log the error to an error reporting service
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

### What is react-router?

As we know react is just a library. To create routes for our application we can use react-router lib to render different component for different brower path.

### How to improve Preformance?

For starter to know what are the bottlenecks we should use ReactProfiler which will tell us which pages are rendering slower, why re-renders are happening and network latency. Few tachniques are listed down:
- **Webpack**: to create effiecient bundling chunks (code splitting) and thrid party library management
- **HOC**: reducing repetetive code
- **React.Lazy** with suspense to render component when they are ready and continue to render other parts of UI
- **Caching**: `useMemo` to memoize a havily computed value, `useCallback` to memoize a function and stop components re-renders which uses the function, `Memo` to meomize a component, `CDN` to cache large images to reduce network latency.
- **PureComponent**: use pure component when we want to render only when state value changes. This implements `shouldComponentUpdate`, which does shallow comparison with current and previous state.
- **Key Prop**: React uses key property on elements to track if they changed. Always use key prop for list itmes, so that react only renders the new items added to the list instead of re-rendering all items.
- **Small Component**: Breakdown the large components into small components and reuse whereever possible. This would stop re-rendering of components which dont need to, since state will affect its children. i.e., limitting the state local scope to minimal ui.

### What consideration you would make to handle security?

This is applicable to web security in general. Here are a few of them:
- **Secrets**: Always put app secrets in .env file and add it to .gitignore.
- **User credentials**: Never store user credentials in web storage. base64 encode and send to authentication api once and use tokens for future requests. Enforse strong password policy.
- **Cross-site scripting (XSS)**: Form submittion. the injected code enters the browser from the site, the code is reliable and can do things like sending the user’s site authorization cookie to the attacker.
- **SQL Injection**: Attacker can also send SQL qury in input fields. If it get executed on db without validation, they data could be stolen or currupted. Other similar attacks are CSRF, Phishing, Ransomware, Code Injection, Viruses and worms, Spyware. To handle SQL injection this frontend should validate input fields. To handle CSRF attack app should get CSRF token from server for making api call and send that as validation token for server. 
- **Updated Software**: Always update libraries to get security updates.
- **Proxy**: A firewall for client to filter data requested from outside world.








