# React-router-lab

# React Router/Bitcoin price checker 

## Description
This app uses react router and the [Coindesk API](https://www.coindesk.com/api/) to get realtime data of bitcoin pricing. Neat, right?

## Installation Instructions

```
git clone 
cd react-bitcoin-prices
npm install
code .
npm run start
```
## We Do:  Setup

Let's get set up with the react bitcoin price checker!

## You Do: Coindesk API

We will query the Coindesk API in this exercise. Take 5 minutes to read and test out (using the
browser) the API docs below

[Coindesk API](https://www.coindesk.com/api/)

Also, install the
[React developer tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)
chrome extension if you haven't already. It'll come in very handy for inspecting
components.

## You Do: Examine Current Codebase (~15 min)

Since we're starting off with a project that already has some scaffolding built
out, we should spend some time getting our bearings.

Take 10 minutes and read through the code to familiarize yourself with the
codebase with a partner or in groups of 3. Prepare to discuss your answers the
following questions:

1. What dependencies is the application currently using? Where can I find
   information on them?
2. What is the purpose of `ReactDOM.render()`? What file is this method being
   called in?
3. Where are the components of our application located? Why might we want to
   separate them into their own folders?
4. Where is state located in our application? Is state being passed down to
   other components?
5. Look at the Price component. What props is it expecting to be passed?
6. Where is our application getting data from? How is it accomplishing this?



Now let's modify the render method in `App.js` to include our Link and Route
components.

```jsx
// src/components/App/App.js
  return(
    <div>
      <nav>
        <Link to="/">
          <img src="https://en.bitcoin.it/w/images/en/2/29/BC_Logo_.png" alt=""/>
          <h1>Bitcoin prices</h1>
        </Link>
      </nav>
      <main>
        <Route path="/"
          component={Home}
        />
      </main>
    </div>
  )
```

Great! But this doesn't do anything because we're already on the homepage.

Also, note that we used `component` in this case to display our home component.
We're doing that because we just want to display it without any changes - we're
not passing any props in, we're not modifying anything.

## You do: Add a Second Route and Link (~10 min)

> 5 minute exercise / 5 minute review

- Using the above instructions as a guide, add a new Link to `/currencies` and a
  route to match it. What component do you think you want to render?

<details>
  <summary>Solution</summary>

```jsx
// src/Components/App/App.js
//...
import Currencies from '../Currencies/Currencies'

// ...
  return(
    <div>
      <nav>
        <Link to="/">
          <img src="https://en.bitcoin.it/w/images/en/2/29/BC_Logo_.png" alt=""/>
          <h1>Bitcoin prices</h1>
        </Link>
        <Link to="/currencies">Currency List</Link>
      </nav>
      <main>
        <Route path="/"
          component={Home}
        />
        <Route path="/currencies"
          component={Currencies}
        />
      </main>
    </div>
  )
```

</details>

Now we've got two components and two routes. Perfect. Let's take a look at our
currencies component and see what we need to do to make it work.

This a good point to talk about React Router's
[Route Props](https://reacttraining.com/react-router/web/api/Route/route-props).

## We do: Currencies component

If we look at this component we see a long list of links. Note that the links
are using regular `<a>` tags.

What happens if we click on a link? It works, but the whole page reloads! Gross.
Let's fix that.

Go ahead and replace the `a` tag with a `<Link>` component. Make the `to` prop
value equal to the `href` value.

```jsx
// src/Components/Currencies/Currencies.js
import { Link } from 'react-router-dom'

//...
    let list = listOfCurrencies.map(item => {
      return (
        <div className="currency" key={item.currency}>
          <p><Link to={"/price/"+ item.currency}>{item.currency}</Link>: {item.country}</p>
        </div>
      )
    })
  // ...
```

Great! Now go back to the page and click the link again, what happens?

**MAGIC!**

![shia](https://media.giphy.com/media/ujUdrdpX7Ok5W/giphy.gif)

It changes the route for us (notice the URL changing) but we don't have any
routes set up to match that. Let's do that next.

## Break (10 min)

## You do: Prices Component (~10 min)

> 5 min exercise, 5 min review

Back in `App.js`, we need to add another `<Route>` component. This time though,
we want to include a parameter.

Look at the URL that we're on after clicking on a currency. Then look at the
`Price` component. How might you write the `path` prop to make it work?

> Hint: This part is just like defining a route with a param in express.

## We do: Fix prices component (~25 min)

We've added a route but not everything will work yet. HOW COME!?

There are a couple things we need to fix.

Firstly, we have to make sure we're using `render` instead of `component` in our
route. That's because we're going to be passing some props into our component.

```jsx
//...
<Route path="/price/:currency" render={() => <Price />} />
//...
```

Now we need to add a couple things to `<Price />`

We've got a function in this (`App.js`) component called setPrice. let's pass
that in.

```jsx
<Route
  path="/price/:currency"
  render={() => <Price setPrice={setPrice} />}
/>
```

We also have to pass our URL parameter into `<Price />`. This is where the arrow
function comes in to play.

```jsx
<Route
  path="/price/:currency"
  render={routerProps => <Price setPrice={setPrice} match={routerProps.match} />}
/>
```

Finally, we need to pass in the current component's price state.

```jsx
<Route
  path="/price/:currency"
  render={routerProps => (
    <Price setPrice={setPrice} match={routerProps.match} price = {price} />
  )}
/>
```

We still have some weird display quirks, and for that, we'll use `<Switch>` to
fix them.

## Using exact (~10 min)

exact works just like the switch/case statements in javascript. We're comparing
string values (in this case, routes) and executing conditions (rendering
components) based on what matches turn out true.

Since we're not using exact right now, we'll see something like this:

![preswitch](./images/pre-switch.png)

There are two components stacked on top of each other! The Home and the
Currencies component. That's silly.

> Why does this happen?

To handle this, specify `exact` on routes.

Let's look at our routes in `App.js` again:

```jsx
<Route path="/"
  component={Home}
/>
<Route path="/currencies"
  component={Currencies}
/>
<Route
  path="/price/:currency"
  render={(routerProps) => <Price setPrice={setPrice} match={routerProps.match} price={price} /> }
/>
```

Try putting `exact` on the `/` path route component.

```js
<Route path="/" exact component={Home} />
```

> Note: this is equivalent to putting `exact=true`

Beautiful! this is a great solution, and can also be used if we have many different routes.

If we had a list of routes like:

- `/currencies`
- `/currencies/new`
- `/currencies/:id` etc

we have to put `exact` on `/currencies` or else, any time we went to
`/currencies/something` it would match both the root (`/currencies`) AND the
`/currencies/something` routes and both would be rendered.

So easy!

![shia](https://media.giphy.com/media/ujUdrdpX7Ok5W/giphy.gif)

## Redirects

Redirects using react router are incredibly easy. Redirect is just another
component we can import and use by passing it a few props.

- Import the `Redirect` component from `react-router-dom`
- Add another route called `/currency`
- Instead of rendering one of our components, put the `Redirect` component.

```js
<Route path="/currency" render={() => <Redirect to="/currencies" />} />
```

Redirect only requires a `to` prop which tells it what path to redirect to.

## Wrapping Up (Remainder of Class)

Here's a rough outline of how you should go about building react apps! Follow
these suggestions, or don't, but they will probably help you a lot if you do them
in order. I suggest reading through all of the steps before you start so you can 
become familiar with the big picture of the entire process.

1. Start a new app using `create-react-app`. Call it user-router or something similar, 
it doesn't matter. You will be building four components (not including App.js). 
Each one will render something different:

| Component | Renders                                   | Route         |
| --------- | ----------------------------------------- | ------------- |
| Home      | "This is the homepage"                    | /             |
| Greet     | A greeting from a url parameter passed in | /greet/:param |
| Users     | A list of users                           | /users/       |
| NewUser   | A form that lets you add a username       | /users/new    |

1. Set up `react-router` like we did in this lesson, at the top level. What 
is the top level of a React app?

1. Build out each component with placeholders to render something. Do this first, before 
starting to add state, props, or functionality.

1. Set up your routes so that each route only displays the appropriate component.

1. Plan out where you think your state should live. If you have to share state
between multiple components, what's the best place to keep it? Think about what your 
state needs to contain.

1. Initialize your state and pass it down to the appropriate components.

1. Wire up those components to be able to display and update the state as
necessary. Add the functionality to have the greet component receive and display a
parameter.

1. Marvel at your creation and your progress after only 5 weeks of programming!
