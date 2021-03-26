# Assignment #4

You've deployed your frontend, you've deployed your backend. Now it's time to make them useful.

This assignment is 3 steps:

- Document your API and the data that you need to store for your first feature
- Edit the backend to accept an authenticated request and return test data
- Edit the frontend to make an authenticated request to your API to get that data

## Homework 1/3

### Start on creating your first API:

- Taking a look at your prototypes, decide what data and what APIs your application will need:
    - If you're working on a social media feed - you might create a /feed API that returns Items that contain the message, who sent it, and when it was sent. In this case, your resource is an Item.
    - If you're working on a delivery app - you might create an /orders API that returns a list of Orders which also contains a list of MenuItems containing name, description, and price. In this case, your resource is an Order.
    - If you're working on a chat app - you might create a /chats API that returns all the chat rooms you have and a List of Chat Members containing their name and profile picture. In this case, your resource is a Chat Room which contains Chat Members.
    - If you're working on your own project, take a look at your features and define what data you need to send back and forth to your API.
- Create an API spec for your backend API as best as you can. For the purposes of this homework, you just have to define your API for 1 feature.
    - Include at least one GET path to get a resource
    - Include at least one POST path to create a resource with an expected body, and an expected response
    - And define all the properties of one resource

For example:

```sh
GET /orders

example response:
[
  {
    name: 'da335',
    message: 'arent APIs great?',
    likes: 0,
  }
]

POST /orders

expected body:
{
  menuItems: [{name: 'fried chicken', quantity: 2}]
}

expected response:
{
  id: 'order-id',
  status: 'in-progress',
  total: '$50',
  menuItems: [{ name: 'fried chicken', quantity: 2 }]
}
```

There are fancier ways of defining your API with a tool like Swagger (check out editor.swagger.io) which lets you define your API as YAML, and visualize it in the browser to make it real easy to define your API. Its worth mentioning if you're looking for a better way of planning & testing your API. Its not required for the purposes of this class since I'm looking for an example of the data your API will be sending.

Submit a description of the API like above. If you wind up liking Swagger, include a link to your swagger.json file on Github.

## Homework 2/ 3

### Implement your API on the backend:

Now that you've defined your API - make it real. Create your first actual GET API and make it return some test data - see here for how you can [create test data with JSON](https://stackoverflow.com/questions/10011011/using-node-js-how-do-i-read-a-json-file-into-server-memory/26446604#26446604) in node or [in flask](https://stackoverflow.com/questions/40019961/reading-a-json-file-using-python-flask)

- You don't need to save data yet, so don't implement your POST API yet.
- Its easier to play with test data in development, than it is to move data around a database.
- The assignment for next week will cover saving data to a database but we first need to know what data we will be saving, and how we will use it *before* we choose a database.
- [Install the serverless-offline plugin](https://www.npmjs.com/package/serverless-offline#installation) so that you can run your API on localhost:4000 which will make it really fast to test your changes. You won't need to re-deploy your backend with every change.
    - Note - serverless-offline will run your API server on port 3000 by default. React also uses this port when you run `npm run start`. You can change the port used by serverless-offline by running `serverless offline --httpPort 4000` to run it on port 4000.

At this step, you should be able to make a request to your API and verify that its returning the right data with the `curl` command. For example:

```sh
> curl http://localhost:4000/dev/orders
{
  id: 'order-id',
  status: 'in-progress',
  total: '$50',
  menuItems: [{ name: 'fried chicken', quantity: 2 }]
}
```

Some notes:

- If you're using serverless with flask or express, you can develop your APIs with those tools.
- If you're using serverless with only a handler.js file AND the Lambda Proxy integration, create your API by checking for `event.httpMethod` & `event.path` and returning a response

```js
module.exports.hello = async (event) => {
  if (event.httpMethod === 'GET' && event.path === '/feed') {
    return {
      statusCode: 200,
      body: JSON.stringify([{username: 'da335', message: 'building stuff is cool'}])
    }
  }
}
```

- If you're using serverless with different paths defined in your serverless.yml, you can define your API there as well

```yml
# serverless.yml
functions:
  feedApi:
    handler: handler.feed
    events:
      - http:
        path: /feed
        method: GET
```
```js
# handler.js
module.exports.feed = async (event) => {
  // You'll only receive events for GET /feed requests
  return {
    statusCode: 200,
    body: JSON.stringify([{username: 'da335', message: 'building stuff is cool'}])
  }
}
```

### Get it to show up on the front end

Edit your frontend to use your API:

Create the first feature that will use the API that you defined and add it to the page thats shown AFTER the user logs in.

- Show the users feed by making a GET /feed request to your backend API, and showing a "card" for each item in the feed (see [bulma's cards](https://bulma.io/documentation/components/card/))
- Show available restaurants by making a GET /restaurants request to your backend API, and showing a Restaurant item
- Show available chats by making a GET /chats request to your backend API, and showing the name of available chat

When making the request to your backend API

- you can use `fetch` which is available natively in the browser to make a request - see the [MDN fetch docs](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) for how to use it.
- Parse the response as json by calling `response.json()`, and save the result to your components state

```js
class Orders extends React.Component {
  state = {
    orders: null
  }

  async componentDidMount() {
    const response = await fetch('http://localhost:4000/dev/orders')
    const orders = await response.json()
    // save it to your components state so you can use it during render
    this.setState({orders: orders})
    console.log(orders)
  }
  ...
}
```

When displaying the data from your backend, you can save the data to your components state & you can "interpolate" that data into your HTML. If `data` is the result of my /orders API, it might look like this:

```jsx
render() {
  return (
    <div>
      <div className="title">My Orders</div>
      <ul>
      {
        this.state.orders && this.state.orders.map(order => {
          return (
            <li>
              <div>Order ID: {order.id}</div>
              <div>Order status: {order.status}</div>
            </li>
          )
        })
      }
      </ul>
    </div>
  )
}
```

If you can get something (anything) on the page, you're ready for part 3. Don't move on to part 3 unless this is working for you. If you have trouble getting past this, join the #2021-cs-5356-support channel and ask a question!

Submit a screenshot of your frontend at this step.

## Homework 3/3

### Add Authentication to your API

On the front-end, add the users ID token to the request to your backend. You might edit the code from above to look like this:

```js
class Orders extends React.Component {
  state = {
    orders: null
  }

  async componentDidMount()
    const idToken = await firebase.auth().currentuser?.getIdToken()
    const response = await fetch('http://localhost:4000/dev/orders', {
      headers: {
        'Authorization': idToken
      }
    })
    if (response.status === 401) {
      return console.log('unauthorized')
    }
    const orders = await response.json()
    // save it to your components state so you can use it during render
    this.setState({orders: orders})
    console.log(orders)
  }
  ...
}
```

Note: Here - we're adding an Authorization header to our `fetch` call, and we're checking if the response returns a 401 status.

On the backend, your handler should check the request for a header named "Authorization" and verify the token that is included in that header.

- If you're using JS, you can use the npm package [firebase-token-verifier](https://www.npmjs.com/package/firebase-token-verifier) to verify the token so install it `npm install --save firebase-token-verifier`. Take a look at the README for how to include it in your project. There's also an example below.
- If you're using python, you can use the google-auth pip package to verify firebase ID tokens - see [install step here](https://googleapis.dev/python/google-auth/1.9.0/index.html) and the [docs for the method here](https://googleapis.dev/python/google-auth/1.9.0/reference/google.oauth2.id_token.html#google.oauth2.id_token.verify_firebase_token). Here's an example of what it looks like.

```python
from google.oauth2 import id_token
from google.auth.transport import requests
request = requests.Request()

# token would come from the header
id_info = id_token.verify_firebase_token(token, request)

userid = id_info['sub']
print(userid)
```

- Your API should return a status code 401 if no token is included in the request
- Your API should return a status code 401 if the token is not validated successfully
- Your API should return a successful response if the token has been validated successfully

Your implementation might look something like this in JS:

```js
const projectId = 'firebase-project-id'
if (event.path === '/feed' && event.httpMethod === 'GET') {
    // check the header named Authorization
    const token = event.headers['Authorization']
    // If no token is provided, or it is "", return a 401
    if (!token) {
      return {
        statusCode: 401
      }
    }

    try {
      // validate the token from the request
      const decoded = await firebaseTokenVerifier.validate(token, projectId)
    } catch (err) {
      // the token was invalid,
      console.error(err)
      return {
        statusCode: 401
      }
    }

    // user is now confirmed to be authorized, return the data
    return {
      statusCode: 200,
      headers,
      body: JSON.stringify([{
        name: 'da335',
        message: 'arent APIs great?',
        likes: 0
      }])
    }
  }
```

The only way to test this change to your API is to log in via your frontend and sign-in.

- If you receive a 401 - double check if theres an error in your API by checking the console output. Make sure you're running `serverless offline --httpPort 4000 --printOutput` - note the `--printOutput` option which tells the serverless command to show the console logs of your function.

Commit your changes to your frontend and backend, and push it to Github. And include a link to your Github repo. The goal of this homework is to get your frontend and the backend working together locally.

Once this works locally, you'll see the data on the page as you did at the end of Part 2/3.

When your project gets deployed by Amplify, you might see errors about CORS (Missing Access-Control-Allow-Origin Header) depending on your serverless.yml set up. I'll talk more about this next class - but if you run into it, thats ok. This is the first example of an issue that you won't run into in development, but will run into in production.

If you run into issues or need help, please don't hesitate to pop into the #2021-cs-5356-support Slack channel and ask a question or two. Its better than banging your head against the wall for hours!

Happy coding.