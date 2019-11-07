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
      firstName: 'Elias',
      lastName: 'De Bock'
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

## 4. Components and Props

This is the basics, you can find the detailed component API reference [here](https://reactjs.org/docs/react-component.html).

Components let you split the UI into **independent reusable pieces**. They are like JavaScript functions. They accept inputs (called “props”) and return elements.


### 4.1 Function and Class Components

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


### 4.2 Rendering a Component

Previously, we only encountered React elements that represent DOM tags:

    const element = <div />;

However, elements can also represent user-defined components:

    const element = <Welcome name="Elias" />;

When React sees an element representing a user-defined component, it passes JSX attributes to this component as a single object. We call this object “props”.


For example, this code renders “Hello, Elias” on the page:

    function Welcome(props) {
      return <h1>Hello, {props.name}</h1>;
    }

    const element = <Welcome name="Elias" />;

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


### 4.3 Composing Components

A button, a form, a dialog, a screen: in React, all those are components.

We can create an App component that renders the Welcome component many times:

    // this is a component which we render in the app component, be it with a different props value
    function Welcome(props) {
      return <h1>Hello, {props.name}</h1>;
    }

    function App() {
      return (
        <div>
          <Welcome name="Elias" />
          <Welcome name="Benna" />
          <Welcome name="Brecht" />
        </div>
      );
    }

    ReactDOM.render(
      <App />,
      document.getElementById('root')
    );


### 4.4 Extracting Components

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


### 4.5 Props are Read-Only

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

## 5. State and Lifecycle

Look at the ticking clock example from before:

    function tick() {
      const element = (
        <div>
          <h1>Hello, world!</h1>
          <h2>It is {new Date().toLocaleTimeString()}.</h2>
        </div>
      );
      ReactDOM.render(
        element,
        document.getElementById('root')
      );
    }

    setInterval(tick, 1000);


We can start by encapsulating how the clock looks:

    function Clock(props) {
      return (
        <div>
          <h1>Hello, world!</h1>
          <h2>It is {props.date.toLocaleTimeString()}.</h2>
        </div>
      );
    }   

    function tick() {
      ReactDOM.render(
        <Clock date={new Date()} />,
        document.getElementById('root')
      );
    }

    setInterval(tick, 1000);


This misses a crucial requirement: the times doesn't update itself. We need to add “state” to the Clock component. eState is similar to props, but it is private and fully controlled by the component:

    ReactDOM.render(
      <Clock />,
      document.getElementById('root')
    );


### 5.1 Converting a Function to a Class

You can convert a function component like Clock to a class:

    class Clock extends React.Component {
      render() {
        return (
          <div>
            <h1>Hello, world!</h1>
            <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
               // note: this.props.date instead of props.date
          </div>
        );
      }
    }


### 5.2 Adding Local State to a Class

We will move the date from props to state in three steps:

1. Replace **this.props.date** with **this.state.date** in the render() method:

        class Clock extends React.Component {
          render() {
            return (
              <div>
                <h1>Hello, world!</h1>
                <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
              </div>
            );
          }
        }


2. Add a **class constructor** that assigns the initial this.state:

        class Clock extends React.Component {
          constructor(props) {
            super(props);
            this.state = {date: new Date()};
            // Class components should always call the base constructor with props.
          }

          render() {
            return (
              <div>
                <h1>Hello, world!</h1>
                <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
              </div>
            );
          }
        }


3. Remove the date prop from the <Clock /> element:

    ReactDOM.render(
      <Clock />,
      document.getElementById('root')
    );  

The result looks like this:

    class Clock extends React.Component {
      constructor(props) {
        super(props);
        this.state = {date: new Date()};
      }

      render() {
        return (
          <div>
            <h1>Hello, world!</h1>
            <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
          </div>
        );
      }
    }

    ReactDOM.render(
      <Clock />,
      document.getElementById('root')
    );


Next, we’ll make the Clock set up its own timer and update itself every second.


### 5.3 Adding Lifecycle Methods to a Class

We want to set up a timer whenever the Clock is rendered to the DOM for the first time. This is called “mounting".
We also want to clear that timer whenever the DOM produced by the Clock is removed. This is called “unmounting".

There are lifecycle methods on the component class to run some code when a component mounts and unmounts: componentDidMount() and componentWillUnmount().

This is the entire example in a class component, with lifecycle methods, state, this.setState() all in one component:

    class Clock extends React.Component {
      constructor(props) {
        super(props);
        this.state = {date: new Date()};
      }

      // runs after rendering to DOM
      componentDidMount() {
        // timerID isn't a props/state: it doesn't participate in data flow, but you can add it to store something
        this.timerID = setInterval(
          () => this.tick(),
          1000
    );
      }

      // runs when unmounting from DOM
      componentWillUnmount() { 
         // stops the timer
         clearInterval(this.timerID);
      }

      tick() {
      // It will use this.setState() to schedule updates to the component local state:
      // updates state?
        this.setState({
          date: new Date()
        });
      }

      render() {
        return (
          <div>
            <h1>Hello, world!</h1>
            <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
          </div>
        );
      }
    }

    ReactDOM.render(
      <Clock />,
      document.getElementById('root')
    );


Let’s quickly recap what’s going on and the order in which the methods are called:

1. When <Clock /> is passed to ReactDOM.render(), React calls the constructor of the Clock component. Since Clock needs to display the current time, it initializes this.state with an object including the current time. We will later update this state.

2. React then calls the Clock component’s render() method. This is how React learns what should be displayed on the screen. React then updates the DOM to match the Clock’s render output.

3. When the Clock output is inserted in the DOM, React calls the componentDidMount() lifecycle method. Inside it, the Clock component asks the browser to set up a timer to call the component’s tick() method once a second.

4. Every second the browser calls the tick() method. Inside it, the Clock component schedules a UI update by calling setState() with an object containing the current time. Thanks to the setState() call, React knows the state has changed, and calls the render() method again to learn what should be on the screen. This time, this.state.date in the render() method will be different, and so the render output will include the updated time. React updates the DOM accordingly.

5. If the Clock component is ever removed from the DOM, React calls the componentWillUnmount() lifecycle method so the timer is stopped.


### 5.4 Using State Correctly

There are three things you should know about setState().

#### 5.4.1 Do Not Modify State Directly

For example, this will not re-render a component:

    // Wrong:
    this.state.comment = 'Hello';

    // Correct:
    this.setState({comment: 'Hello'});

 The only place where you can assign this.state is the **constructor**.


#### 5.4.2 State Updates May Be Asynchronous

React may batch multiple setState() calls into a single update for performance. Because this.props and this.state may be updated asynchronously, you should not rely on their values for calculating the next state.
For example, this code may fail to update the counter:

    // Wrong:
    this.setState({
      counter: this.state.counter + this.props.increment,
    });

To fix it, use a second form of setState() that accepts a function rather than an object. That function will receive the previous state as the first argument, and the props at the time the update is applied as the second argument:

    // Correct:
    this.setState((state, props) => ({
      counter: state.counter + props.increment
    }));


#### 5.4.3 State Updates are Merged

When you call setState(), React merges the object you provide into the current state.

For example, your state may contain several independent variables:

      constructor(props) {
        super(props);
        this.state = {
          posts: [],
          comments: []
        };
      }

Then you can update them independently with separate setState() calls:

      componentDidMount() {
        fetchPosts()
        .then(response => { this.setState({posts: response.posts});
         // updates state of posts
        });

        fetchComments()
        .then(response => { this.setState({comments: response.comments});
        // updates state of comments
        });
      }


### 5.5 The Data Flows Down(!)

Neither parent nor child components can know if a certain component is stateful or stateless and they shouldn’t care whether it is defined as a function or a class.
This is why **state is often called local or encapsulated**. **It is not accessible to any component other than the one that owns and sets it.**


A component may choose to pass its state down as props to its child components:

    <h2>It is {this.state.date.toLocaleTimeString()}.</h2>

This also works for user-defined components:

    <FormattedDate date={this.state.date} />

The FormattedDate component would receive the date in its props and wouldn’t know whether it came from the Clock’s state, from the Clock’s props, or was typed by hand:

    function FormattedDate(props) {
      return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
    }


This is commonly called a “top-down” or “unidirectional” data flow. Any state is always owned by some specific component, and any data or UI derived from that state can only affect components “below” them in the tree.
If you imagine a component tree as a waterfall of props, each component’s state is like an additional water source that joins it at an arbitrary point but also flows down.
To show that all components are truly isolated, we can create an App component that renders three <Clock>s:

    function App() {
      return (
        <div>
          <Clock />
          <Clock />
          <Clock />
        </div>
      );
    }

    ReactDOM.render(
      <App />,
      document.getElementById('root')
    );q

Each Clock sets up its own timer and updates independently.

In React apps, whether a component is stateful or stateless is considered an implementation detail of the component that may change over time. You can use stateless components inside stateful components, and vice versa.


## 6. Handling Events

Handling events with React elements is very similar to handling events on DOM elements. There are some syntactic differences:

- React events are named using camelCase, rather than lowercase

- You pass a function as the {event handler}, rather than a "string":

    - HTML:

            <button onclick="activateLasers()">
                Activate Lasers
            </button>

    - React:

            <button onClick={activateLasers}>
              Activate Lasers
            </button>

- You must call preventDefault(preventing default behaviour when opening a new page) explicitly:

   - HTML:
   
            <a href="#" onclick="console.log('The link was clicked.'); return false">
              Click me
            </a>

    - React:
    
            function ActionLink() {
              function handleClick(e) {
                // 
                e.preventDefault();
                console.log('The link was clicked.');
              }

            return (
                <a href="#" onClick={handleClick}>
                  Click me
                </a>
              );
            }

Here, e is a synthetic event. React defines these synthetic events according to the W3C(WWW Consortium) spec, so you don’t need to worry about cross-browser compatibility. Check [this](https://reactjs.org/docs/events.html) to learn more.

When you define a component using an ES6 class, a common pattern is for an event handler to be a method on the class. For example, this Toggle component renders a button that lets the user toggle between “ON” and “OFF” states:

    class Toggle extends React.Component {
      constructor(props) {
        super(props);
        this.state = {isToggleOn: true};

        // This binding is necessary to make `this` work in the callback
        this.handleClick = this.handleClick.bind(this);
      }

      handleClick() {
        this.setState(state => ({
          isToggleOn: !state.isToggleOn
        }));
      }

      render() {
        return (
          <button onClick={this.handleClick}>
            // class methods are not bound by default
            // binding it at in the constructor makes it so you can reference the method here, otherwise it's undefined
            {this.state.isToggleOn ? 'ON' : 'OFF'}
          </button>
        );
      }
    }

    ReactDOM.render(
      <Toggle />,
      document.getElementById('root')
    );


There are two ways around binding: 

1. If you are using the experimental public class fields syntax, you can use class fields to correctly bind callbacks:

        class LoggingButton extends React.Component {
          // This syntax ensures `this` is bound within handleClick.
          // Warning: this is *experimental* syntax.
          handleClick = () => {
            console.log('this is:', this);
          }

          render() {
            return (
              <button onClick={this.handleClick}>
                Click me
              </button>
            );
          }
        }


2. If you aren’t using class fields syntax, you can use an arrow function in the callback:

        class LoggingButton extends React.Component {
          handleClick() {
            console.log('this is:', this);
          }

          render() {
            // This syntax ensures `this` is bound within handleClick
            return (
              <button onClick={(e) => this.handleClick(e)}>
                Click me
              </button>
            );
          }
        }

The problem with this syntax is that a different callback is created each time the LoggingButton renders. In most cases, this is fine. However, if this callback is passed as a prop to lower components, those components might do an extra re-rendering. 

**Important**: We generally recommend binding in the constructor or using the class fields syntax, to avoid this sort of performance problem.


### 6.1 Passing Arguments to Event Handlers

Inside a loop it is common to want to pass an extra parameter to an event handler. For example, if id is the row ID, either of the following would work:

 -   <button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
     // arrow function

 -  <button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
     // function.prototype.bind

In both cases, the e argument representing the React event will be passed as a second argument after the ID. With an arrow function, we have to pass it explicitly, but with bind any further arguments are automatically forwarded.


## 7. Conditional Rendering

In React, you can create distinct components that encapsulate behaviour you need. Then, you can render only some of them, depending on the state of your application.

Conditional rendering in React works the same way conditions work in JavaScript. Use JavaScript operators like if or the conditional operator to create elements representing the current state, and let React update the UI to match them.

Look at these two components:

    function UserGreeting(props) {
      return <h1>Welcome back!</h1>;
    }

    function GuestGreeting(props) {
      return <h1>Please sign up.</h1>;
    }


We’ll create a Greeting component that displays either of these components depending on whether a user is logged in:

    function Greeting(props) {
      const isLoggedIn = props.isLoggedIn;
      if (isLoggedIn) {
        return <UserGreeting />;
      }
      return <GuestGreeting />;
    }

    ReactDOM.render(
      // Try changing to isLoggedIn={true}:
      <Greeting isLoggedIn={false} />,
      document.getElementById('root')
    );


This example renders a different greeting depending on the value of isLoggedIn prop.


### 7.1 Element Variables

You can use variables to store elements. This can help you conditionally render a part of the component while the rest of the output doesn’t change.

Consider these two new components representing Logout and Login buttons:

    function LoginButton(props) {
      return (
        <button onClick={props.onClick}>
          Login
        </button>
      );
    }

    function LogoutButton(props) {
      return (
        <button onClick={props.onClick}>
          Logout
        </button>
      );
    }


In the example below, we will create a stateful component called LoginControl.


It will render either <LoginButton /> or <LogoutButton /> depending on its current state. It will also render a <Greeting /> from the previous example:

    class LoginControl extends React.Component {
      constructor(props) {
        super(props);
        this.handleLoginClick = this.handleLoginClick.bind(this);
        this.handleLogoutClick = this.handleLogoutClick.bind(this);
        this.state = {isLoggedIn: false};
      }

      handleLoginClick() {
        this.setState({isLoggedIn: true});
      }

      handleLogoutClick() {
        this.setState({isLoggedIn: false});
      }

      render() {
        const isLoggedIn = this.state.isLoggedIn;
        let button;

        if (isLoggedIn) {
          button = <LogoutButton onClick={this.handleLogoutClick} />;
        } else {
          button = <LoginButton onClick={this.handleLoginClick} />;
        }

        return (
          <div>
            <Greeting isLoggedIn={isLoggedIn} />
            {button}
          </div>
        );
      }
    }

    ReactDOM.render(
      <LoginControl />,
      document.getElementById('root')
    );

While declaring a variable and using an if statement is a fine way to conditionally render a component, sometimes you might want to use a shorter syntax. There are a few ways to inline conditions in JSX, explained below.


### 7.2 Inline If with Logical && Operator

You may embed any expressions in JSX by wrapping them in curly braces. This includes the JavaScript logical && operator. It can be handy for conditionally including an element:

    function Mailbox(props) {
      const unreadMessages = props.unreadMessages;
      return (
        <div>
          <h1>Hello!</h1>

            // if this is true, the element after && will appear
            // if this is false, React will ignore it and skip it
          {unreadMessages.length > 0 &&
            <h2>
              You have {unreadMessages.length} unread messages.
            </h2>
          }

        </div>
      );
    }

    const messages = ['React', 'Re: React', 'Re:Re: React'];
    ReactDOM.render(
      <Mailbox unreadMessages={messages} />,
      document.getElementById('root')
    );

It works because in JavaScript, true && expression always evaluates to expression, and false && expression always evaluates to false.


### 7.3 Inline If-Else with Conditional Operator

Another method for conditionally rendering elements inline is to use the JavaScript conditional operator condition ? true : false.

In the example below, we use it to conditionally render a small block of text.

    render() {
      const isLoggedIn = this.state.isLoggedIn;
      return (
        <div>
          The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
        </div>
      );
    }

It can also be used for larger expressions although it is less obvious what’s going on:

     render() {
      const isLoggedIn = this.state.isLoggedIn;
      return (
        <div>
          {isLoggedIn ? (
            <LogoutButton onClick={this.handleLogoutClick} />
          ) : (
            <LoginButton onClick={this.handleLoginClick} />
          )}
        </div>
      );
}

Just like in JavaScript, it is up to you to choose an appropriate style based on what you and your team consider more readable. Also remember that whenever conditions become too complex, it might be a good time to extract a component.


### 7.4 Preventing Component from Rendering

In rare cases you might want a component to hide itself even though it was rendered by another component. To do this return null instead of its render output.

In the example below, the <WarningBanner /> is rendered depending on the value of the prop called warn. If the value of the prop is false, then the component does not render:

    function WarningBanner(props) {
      if (!props.warn) {
        return null;
      }

      return (
        <div className="warning">
          Warning!
        </div>
      );
    }

    class Page extends React.Component {
      constructor(props) {
        super(props);
        this.state = {showWarning: true};
        this.handleToggleClick = this.handleToggleClick.bind(this);
      }

      handleToggleClick() {
        this.setState(state => ({
          showWarning: !state.showWarning
        }));
      }

      render() {
        return (
          <div>
            <WarningBanner warn={this.state.showWarning} />
            <button onClick={this.handleToggleClick}>
              {this.state.showWarning ? 'Hide' : 'Show'}
            </button>
          </div>
        );
      }
    }

    ReactDOM.render(
      <Page />,
      document.getElementById('root')
    );

Returning null from a component’s render method does not affect the firing of the component’s lifecycle methods. For instance componentDidUpdate will still be called.


