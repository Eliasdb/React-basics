## React introduction - official documentation

## 1. Hello World

The smallest React example looks like this: 

    ReactDOM.render(
      <h1>Hello, world!</h1>,
      document.getElementById('root')
    );

In this guide, we will examine the building blocks of React apps: elements and components. Once you master them, you can create complex apps from small reusable pieces.
Basic JS knowledge is strongly adviced to master. If you're unsure about your skills: [this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript) will refresh your memory or at least give you an adequate overview of the basics.


## 2. Introducing JSX

    const element = <h1>Hello, world!</h1>;

This is called JSX, and it is a syntax extension to JavaScript. We recommend using it with React to describe what the UI should look like. 
It comes with the full power of JS.


### 2.1 Why JSX?

React embraces the fact that rendering logic is inherently coupled with other UI logic: how events are handled, how the state changes and how the data is prepared for display.
As a result, instead of separating markup and logic in separate files, React uses components that contain both. 


### 2.2 Embedding Expressions in JSX

    const name = 'Josh Perez';
    const element = <h1>Hello, {name}</h1>;

    ReactDOM.render(
      element,
      document.getElementById('root')
    );

You can put any valid JavaScript expression inside the curly braces. For example: 2 + 2, user.firstName, or formatName(user).

    function formatName(user) {
      return user.firstName + ' ' + user.lastName;
    }

    const user = {
      firstName: 'Harper',
      lastName: 'Perez'
    };

    const element = (
      <h1>
        Hello, {formatName(user)}!
      </h1>
    );

    ReactDOM.render(
      element,
      document.getElementById('root')
    );


In this, we embed the result of calling the function formatName(user) into an h1 element.
We split JSX over multiple lines for readability and wrapping it in parentheses.


### 2.3 JSX is an Expression Too

After compilation, JSX becomes regular JS. This means that you can use JSX inside of if statements, for loops, assign it to variables, use it as arguments, and return it:

    function getGreeting(user) {
      if (user) {
        return <h1>Hello, {formatName(user)}!</h1>;
      }
      return <h1>Hello, stranger.</h1>;
    }


### 2.4 Specifying Attributes with JSX

You may use **quotes** to specify string literals as attributes(string values):

    const element = <div tabIndex="0"></div>;

You may also use **curly braces** to embed a JavaScript expression in an attribute(expressions)

    const element = <img src={user.avatarUrl}></img>;

But don't use them together in the same attribute: don’t put quotes around curly braces when embedding a JavaScript expression in an attribute. 

*Side note*: Since JSX is closer to JavaScript than to HTML, React DOM uses camelCase: class -> className, tabindex -> tabIndex


### 2.5 Specifying Children with JSX

If a tag is empty, you may close it immediately with /> :

    const element = <img src={user.avatarUrl} />;

If it contains children, put a div tag around it:

    const element = (
      <div>
        <h1>Hello!</h1>
        <h2>Good to see you here.</h2>
      </div>
    );


### 2.6 JSX Prevents Injection Attacks

It is safe to embed user input in JSX:

    const title = response.potentiallyMaliciousInput;
    // This is safe:
    const element = <h1>{title}</h1>;

This ensures that you can never inject anything that’s not explicitly written in your application. Everything is converted to a string before being rendered. This helps prevent XSS attacks.


### 2.7 JSX Represents Objects

Babel compiles JSX down to React.createElement() calls.

These two examples are identical:

    const element = (
      <h1 className="greeting">
        Hello, world!
      </h1>
    );

    const element = React.createElement(
      'h1',
      {className: 'greeting'},
      'Hello, world!'
    );

React.createElement() performs a few checks but essentially creates an object like this:

    // Note: this structure is simplified
    const element = {
      type: 'h1',
      props: {
        className: 'greeting',
        children: 'Hello, world!'
      }
    };

These objects are called “React elements”. You can think of them as descriptions of what you want to see on the screen. React reads these objects and uses them to construct the DOM and keep it up to date.



## 3. Rendering Elements

Elements are the smallest building blocks of React apps.

An element describes what you want to see on the screen:

    const element = <h1>Hello, world</h1>;
    
Unlike browser DOM elements, React elements are plain objects, and are cheap to create. React DOM takes care of updating the DOM to match the React elements.


### 3.1 Rendering an Element into the DOM


Let’s say there is a div somewhere in your HTML file:

    <div id="root"></div>

This is the "root" DOM node: everything inside will be managed by React DOM. To render a React element into a root DOM node, pass both to ReactDOM.render():

    const element = <h1>Hello, world</h1>;
    ReactDOM.render(element, document.getElementById('root'));


### 3.2 Updating the Rendered Element


React elements are immutable. Once you create an element, you can’t change its children or attributes. An element is like a single frame in a movie: it represents the UI at a certain point in time.
With our knowledge so far, the only way to update the UI is to create a new element, and pass it to ReactDOM.render().


Consider this ticking clock example:

    function tick() {
      const element = (
        <div>
          <h1>Hello, world!</h1>
          <h2>It is {new Date().toLocaleTimeString()}.</h2>
        </div>
      );
      ReactDOM.render(element, document.getElementById('root'));
    }

    setInterval(tick, 1000);

It calls ReactDOM.render() every second from a setInterval() callback.


### 3.3 React Only Updates What’s Necessary

React DOM compares the element and its children to the previous one, and only updates the DOM when necessary. Checking the console is very valuable here.
Even though we create an element describing the whole UI tree on every tick, only the text node whose contents has changed gets updated by React DOM.

*Pro tip*: Thinking about how the UI should look at any given moment rather than how to change it over time eliminates a whole class of bugs.

# 4. Components and Props

This is the basics, you can find the detailed component API reference [here](https://reactjs.org/docs/react-component.html).

Components let you split the UI into **independent reusable pieces**. They are like JavaScript functions. They accept inputs (called “props”) and return elements.


## 4.1 Function and Class Components

The simplest way to define a component is to write a JavaScript function:

    // function component 
    function Welcome(props) {
      return <h1>Hello, {props.name}</h1>;
    }

This is a valid **function component**: it accepts a single “props” object argument with data and returns an element (it's literally a JS function).


You can also use an ES6 **class** to define a component:

    // class component
    class Welcome extends React.Component {
      render() {
        return <h1>Hello, {this.props.name}</h1>;
      }
    }


## 4.2 Rendering a Component

Previously, we only encountered React elements that represent DOM tags:

    const element = <div />;

However, elements can also represent user-defined components:

    const element = <Welcome name="Sara" />;

When React sees an element representing a user-defined component, it passes JSX attributes to this component as a single object. We call this object “props”.


For example, this code renders “Hello, Sara” on the page:

    function Welcome(props) {
      return <h1>Hello, {props.name}</h1>;
    }

    const element = <Welcome name="Sara" />;

    ReactDOM.render(
      element,
      document.getElementById('root')
    );

What happens in this example:

1. We call ReactDOM.render() with the Welcome name="Sara" element.
2. React calls the Welcome component with {name: 'Sara'} as the props.
3. Our Welcome component returns a Hello, Sara element as the result.
4. React DOM efficiently updates the DOM to match Hello, Sara.

*Side note*: Always start component names with a capital letter (otherwise it might think it's HTML and falls out of the scope)


## 4.3 Composing Components

A button, a form, a dialog, a screen: in React, all those are components.

We can create an App component that renders the Welcome component many times:

    // this is a component which we render in the app component, be it with a different props value
    function Welcome(props) {
      return <h1>Hello, {props.name}</h1>;
    }

    function App() {
      return (
        <div>
          <Welcome name="Sara" />
          <Welcome name="Cahal" />
          <Welcome name="Edite" />
        </div>
      );
    }

    ReactDOM.render(
      <App />,
      document.getElementById('root')
    );


## 4.4 Extracting Components

Don’t be afraid to split components into smaller components.

For example, look at this Comment component:

    function Comment(props) {
      return (
        <div className="Comment">
          <div className="UserInfo">

            <img className="Avatar"
              src={props.author.avatarUrl}
              alt={props.author.name}
            />

            <div className="UserInfo-name">
              {props.author.name}
            </div>

          </div>

          <div className="Comment-text">
            {props.text}
          </div>

          <div className="Comment-date">
            {formatDate(props.date)}
          </div>

        </div>
      );
    }

It accepts *author*, *text*, and *date* as **props**, and describes a comment social media.

This component can be tricky to change because of all the nesting. Let’s extract a few components from it.

First, we will extract Avatar:

    function Avatar(props) {
      return (
        <img className="Avatar"
          src={props.user.avatarUrl}
          alt={props.user.name}
        />

      );
    }

Name props in function of the component itself, rather than the context.

We can now simplify Comment a tiny bit:

    function Comment(props) {
      return (
        <div className="Comment">
          <div className="UserInfo">
            <Avatar user={props.author} />
            <div className="UserInfo-name">
              {props.author.name}
            </div>
          </div>
          <div className="Comment-text">
            {props.text}
          </div>
          <div className="Comment-date">
            {formatDate(props.date)}
          </div>
        </div>
      );
    }

Next, we will extract a UserInfo component that renders an Avatar next to the user’s name:

    function UserInfo(props) {
      return (
        <div className="UserInfo">
          <Avatar user={props.user} />
          <div className="UserInfo-name">
            {props.user.name}
          </div>
        </div>
      );
    }

This lets us simplify Comment even further:

    function Comment(props) {
      return (
        <div className="Comment">
          <UserInfo user={props.author} />
          <div className="Comment-text">
            {props.text}
          </div>
          <div className="Comment-date">
            {formatDate(props.date)}
          </div>
        </div>
      );
    }


Extracting components might seem like grunt work at first, but having a palette of reusable components pays off in larger apps. A good rule of thumb is that if a part of your UI is used several times (Button, Panel, Avatar), or is complex enough on its own (App, FeedStory, Comment), it is a good candidate to be a reusable component.


## 4.5 Props are Read-Only

If you declare a component, never modify its own props.


    function sum(a, b) {
      return a + b;
    }


Above is a pure function: it doesn't change their inputs, so it's always the same result for the same inputs.

    function withdraw(account, amount) {
      account.total -= amount;
    }


This is an impure function: it changes its own input.


**Important strict rule**: All React components must act like pure functions with respect to their props.

Application UIs are dynamic and change over time. This is where “state” comes in. State allows React components to change their output over time without violating this rule.

