# Lesson 5

## Mid-term checkpoint

You'll be relieved to know this week is the last programming assignment, and maybe the hardest part of it. It's also only worth 2 points, so don't sweat it if you can't figure it out.

A note about grades:

Here's the breakdown of the assignments and how much they impact your grade. There is no final exam - just the assignments.

![/lesson-5-assets/Untitled.png](/lesson-5-assets/Untitled.png)

![/lesson-5-assets/Untitled%201.png](/lesson-5-assets/Untitled%201.png)

For the final week's presentations - I'll call on folks to share the progress they made on a very wide variety of projects. There's a lot of folks in the class, and some remote or async, so I won't be able to go through the whole class.

The last assignment will be to submit a link to your latest commit on Github (optionally - along with some screenshots or a screencast of your app working). **As long as you submit a link to your latest commit on Github**, **you'll get the 5 points - even if your project isn't working.**

**ALL HOMEWORK NEEDS TO BE SUBMITTED BY APRIL 29th - LAST DAY OF CLASS.**

---

## Environments

You've noticed so far that when you run your React app - you can access it on `http://localhost:3000`. When you run your serverless backend - you can access it on `http://localhost:4000`.

You usually call this your **local** **environment**. When everything is running on or "talking to" localhost.

But when you `git push` and deploy your application - you've got a new URL. AWS Amplify gives you a URL like which is now linked to your domain [`https://master.dk539yo0nw2qd.amplifyapp.com/`](https://dev1628.dk539yo0nw2qd.amplifyapp.com/).

And similarly when you run `serverless deploy` - your backend is then available at a different URL like [`https://ws00a11wig.execute-api.us-east-1.amazonaws.com/dev`](https://ws00a11wig.execute-api.us-east-1.amazonaws.com/dev).

This is a new environment - if real people will eventually use it, you'd call it your **production environment**.

Conceptually - an environment is defined by the set of URLs that you use to access the apps or services.

- If you're working on mobile applications, your production environment is the app that's currently released on the App Store/Play Store, along with whichever URLs you're using to access your backend.
- If you're working on a web application, your production environment is the URL real customers use to visit/access your web app, along with any backend URLs.
- If you're working on games, your production environment is the game/file that gets distributed through Xbox Live.

Having multiple environments (in this case, local and production) lets you work on changes and testing things in one isolated environment, without impacting your current live **production environment**. ****

### How to use environments to your advantage

You can use this concept of environments to let you do some convenient tweaks.

**Change behavior based on environment**

In web applications, you can write some code to tell which environment you're on by checking `window.location.href`. This property is available in all browsers, and it will tell you what URL is currently in the URL bar.

```js
// Use the production backend URL by default
let backendUrl = 'https://ws00a11wig.execute-api.us-east-1.amazonaws.com/dev'
if (window.location.href.includes('localhost')) {
  // if we are on a local environment, use the localhost URL
  backendUrl = 'http://localhost:4000'
}

// make the request to your backend
fetch(backendUrl + '/orders', ...)
```

You can use this to do interesting things on the frontend like:

**Note:** If you're planning to continue working on your project past this class, and you want to make it easy for yourself to switch between your local URLs and your production URLs, you'll want to use this approach.

And since you can sprinkle these if conditions just about anywhere, you can do other things like:

```jsx
// Check if this is on a local environment
const isLocalEnvironment = window.location.href.includes('localhost')

class App extends React.Component {
    render() {
        return (
            <div>
                <h1>Twitter for Puppies</h1>

                {/* If this is a local environment, show the new feature */}
                { isLocalEnvironment && <PuppyPlayDateScheduler /> }
            </div>
        )
  }
}
```

This approach lets you turn features on and off depending on what environment the app is in.

At scale - this is what companies use to allow you to ship code that may be incomplete or not fully functional. Or maybe the feature is complete, but you don't want everyone to use it yet, you're just trying to test something with a small group first. There's a bunch of third-party services that offer APIs to do more sophisticated things with your environments - like [LaunchDarkly](https://launchdarkly.com/), [split.io](http://split.io) to name a couple.

**Change code based on environment**

The AWS Amplify console helps you create **test environments** for different branches of your git repo - so you can **preview** your changes on a different URL before merging it to your main branch and deploying it to production. If you push changes to a branch, you'll see a couple of new items appear in your Amplify console.

This lines up pretty well with the [Github flow](https://guides.github.com/introduction/flow/) (also known as Git flow) model where you work on a changes on a branch, and you can preview and test your changes in an isolated **test environment**, before merging your code to `main` and having the change get deployed to **production**.

Here's how Etsy used to manage this process 10 years ago, as seen in ["Quantum of Deployment"](https://codeascraft.com/2010/05/20/quantum-of-deployment/). You can read more about their old deployment process here - even though its old though, most of the concepts they talk about are still relevant.

> For anyone deploying code (engineers, designers… anyone really), it’s an easy process. Once your code is ready to go, you go to Deployinator and push the button to get it on QA. From there it visits Princess (see the sidebar). Then, when it’s ready to go live, you hit the “Prod” button and soon your code is live, and everyone in IRC knows who pushed what code, complete with a link to the diff.

![/lesson-5-assets/Untitled%202.png](/lesson-5-assets/Untitled%202.png)

If you spent more time testing your services and applications, you'll generally have more environments, each of which might be running slightly different versions of each service or application.

Environments:

- Production - the environment your real users access, where they hopefully **produce** money for your business.
- Staging - a separate environment that looks just like production, maybe even with data copied over from production, so you can test at production scale without impacting real users.
- Test - any environment that may either be temporary or just used for testing/making sure your features are working correctly
- Local - anything thats running on your own computer `localhost`

You only have 2 environments so far - local, and production. Your production URL is the one you defined in the AWS Amplify console that you linked to your domain.

**Why would you need more than 2 environments?**

Because things work slightly differently when code leaves your computer and you run it somewhere else, as we're about to find out.

### Browser Security & Cross-Origin Resource Sharing

All browsers have some security features that are built into the browser spec that most companies *try* to adhere to.

- You can have data in your web page that is NOT accessible to javascript by using `HttpOnly` cookies.
- You can force the user to visit your application over `https` where your data is encrypted end-to-end.
- You can save data into `localStorage` in your browser, and it can only be accessed by javascript that is running from your own domain.

There are also plenty of ways this security can fall short

- Chrome extensions or other malware in your browser can inject random javascript into your application without you knowing about it.
- It might read all your `localStorage` data and send it to some unknown website.

One of these browser security measures is a feature called **CORS - Cross Origin Resource Sharing**.

If you have a web page that is hosted on `[localhost:3000](http://localhost:3000)` and it makes API requests to  `[localhost:4000](http://localhost:4000)` - it is making a **Cross-Origin request**. In this case, since the applications are running on 2 separate ports, the browser considers these 2 separate domains.

A **cross-origin request** has restrictions. The browser will only allow you to make a **cross-origin request** to a backend IF and ONLY IF

- The backend supports receiving requests from a browser
- The backend authorizes the domain from the **origin** of the request (i.e. is [myapp.com](http://myapp.com) allowed to make requests to myapp.amazonaws.com)

**How can the browser tell?**

The browser does a couple of extra things on your behalf when the request is a **cross-origin** request.

If you make a GET request like `fetch('http://myapp.amazonaws.com/dev/orders')` from `myapp.com`, the browser will make an OPTIONS request on your behalf. This is called a **preflight** request. It looks something like this in curl form:

```bash
curl -XOPTIONS -H'Origin: myapp.com' http://myapp.amazonaws.com/dev/orders
```

It uses the `OPTIONS` http method, and it includes a header `Origin: [myapp.com](http://myapp.com)` that specifies the domain that is making the request.

It will expect that the response from this request is a 200 OK AND that the **response** contains a header `Access-Control-Allow-Origin: myapp.com`. You can also use a wildcard if you don't care where the request is coming from: `Access-Control-Allow-Origin: *`.

Here's what this looks like in **Outlook:**

![/lesson-5-assets/Untitled%203.png](/lesson-5-assets/Untitled%203.png)

**How do you implement this? (This is the walkthrough for Homework 1/2)**

You're not noticing an issue at the moment because the serverless project takes care of it for you in your **local environment only**. But you can see it in Chrome Dev Tools if you take a look at the Network tab. Look for any OPTIONS request in the list.

Once you deploy the frontend and the backend to their production URLs, it stops working (or maybe you're lucky and it works for you) and you see errors about [insert cors request error]. This is where having a test or staging environment is handy - because there are just some things that you don't see until the code leaves your computer.

Implementing this is straightforward:

- If you're using Express, use the [cors package](https://www.npmjs.com/package/cors).
- If you're using Flask, use the [flask-cors package](https://flask-cors.readthedocs.io/en/latest/).

You can use the default settings on either package.

If you're not using either of them, check your serverless.yml:

- Are you using the Lambda Proxy integration like below? If so, you'll have to add a short snippet

```yaml
handler: handler.hello
    events:
        - http:
            # This means you're using the Proxy integration
            path: /{proxy+}
            method: ANY
```

In your handler function, check for an OPTIONS request, and then send back the expected headers. You will also need to include those headers on EVERY response from your API - so your handler function might look like this:

```js
const headers = {
  'Access-Control-Allow-Origin': '*'
}

module.exports.hello = async (event) => {
    // check first if its an OPTIONS request
    if (event.httpMethod === 'OPTIONS') {
        // return the expected status and CORS headers
        return {
            statusCode: 200,
            headers
        }
    }
    ...
    if (event.path === '/orders' && event.httpMethod === 'GET') {
        ...
        return {
            statusCode: 200,
            // Also include the CORS headers in the response
            headers,
            body: JSON.stringify([{
                orderId: '1',
                items: [{itemName: 'Fried Chicken', price: '5'}],
                deliveryAddress: '123 Fake St'
            }])
        }
    }
}
```

- If you are defining your API in your serverless.yml, you can add `cors: true` to each API method that needs CORS support. You can then run `serverless deploy` and you check that it works (see below for how to check).

```yaml
events:
    - http:
        path: /orders
        method: GET
        cors: true
```

**How do you check that it works?**

Run these 2 commands and check - replace the URL and path with one that is in your application.

```bash
> curl -i -XOPTIONS -H'Origin: thisisfake.com' your-api-gateway-url.com/dev/orders

HTTP/2 200
date: Thu, 08 Apr 2021 06:17:58 GMT
access-control-allow-origin: *

> curl -i your-api-gateway-url.com/dev/orders | head -n 5
HTTP/2 200
date: Thu, 08 Apr 2021 06:20:01 GMT
access-control-allow-origin: learning.amplify.com
```

If you see a 200 for both requests, AND the `access-control-allow-origin` header in both responses, you're set 👍 . Deploy your backend and hopefully you won't see any more errors.

---

## **Saving data**

Now that you've got your projects deployed and you see them kind of working, its time to do something more useful with them. The whole purpose of having a backend is that you can save and serve data in a format that makes sense to you - you own it.

So how do you save data? First - what data do you want to save?

There are different types of data stores, each built for a slightly different purpose.

There is file/object storage - like AWS S3 or Azure Blob Storage meant for holding binary files and data (like video, audio, images, any kind of file).

There is non-binary data storage - like MySQL (sql) or DynamoDB (nosql).

### **Which database should you use for your project?**

Here is how you might choose.

![/lesson-5-assets/choosing-a-database.png](/lesson-5-assets/choosing-a-database.png)

If you don't know any - use NoSQL with DynamoDB.

If you've already used Mongo and you know that, use Mongo, but try out DynamoDB if you want.

Only use SQL if you're ready to spend more time getting to intimately know your data.

Whatever you do, don't try to run your own database - use a managed/hosted option like AWS RDS or Mongo Atlas.

You can always grow out of NoSQL database and into a SQL database once you know what data you're saving and how you're accessing that data.

What's different between SQL and NoSQL?

- **The first answer is in the name**
    - using SQL tools you have to write SQL queries to get your data out of the database.

    ```js
    const con = mysql.createConnection({
      host: "localhost",
      user: "yourusername",
      password: "yourpassword",
      database: "mydb"
    });

    con.connect(function(err) {
      if (err) throw err;
      con.query("SELECT * FROM orders where userKey = " + userKey, function (err, result, fields) {
        if (err) throw err;
        console.log(result);
      });
    });
    ```

    - NoSQL doesn't use SQL and the way to get your data out depends on the tool. This is an example DynamoDB query

    ```js
    const getOrders = (userKey) => {
      return docClient
        .query({
          TableName: "cs5356-orders",
          KeyConditionExpression: "user_key = :user_key",
          ExpressionAttributeValues: {
            ":user_key": userKey,
          },
        })
        .promise()
        .then((results) => results.Items);
    };
    ```

- **How you define your data**
    - In a SQL database, you have to know what data can go in your tables up front

    ```sql
    create table orders (
        id auto_increment primary key,
        deliveryAddress varchar(50),
      user_key varchar(20),
    )
    ```

    - In a NoSQL database, you usually just need to only know or one two fields that should exist, but you can put any data you want in it, even if you don't know what it is yet. This is an example of creating a table with DynamoDB

    ```json
    {
        "TableName": "Orders",
        "KeySchema": [
            {
                "AttributeName": "userKey",
                "KeyType": "HASH", //Partition key
            },
            {
                "AttributeName": "orderId",
                "KeyType": "RANGE" //Sort key
            }
        ],
        "AttributeDefinitions": [
            {
                "AttributeName": "userKey",
                "AttributeType": "S"
            },
            {
                "AttributeName": "orderId",
                "AttributeType": "S"
            }
        ],
        "ProvisionedThroughput": {
            "ReadCapacityUnits": 1,
            "WriteCapacityUnits": 1
        }
    }
    ```

- **How that data gets stored**
    - A database is just a piece of software that saves data by writing files to memory and the hard drive. Because databases depend on being able to write to a hard drive - you have to manage that data.
    - A SQL database will store data on the hard drive which means only 1 computer at a time can read/write that data (your primary database server). Consequently, you need to make backups of that data, or have a "hot-standby" - another machine ready to connect to that hard drive on a moments notice in case the primary fails for any reason.
    - A NoSQL database also stores data on a drive, but multiple machines can read/write data to different drives, so it can be more resilient to failures. You still need to make backups, and you still need a "hot-standby" for each drive that has some data.
- **How that database can scale**
    - A SQL database can support more traffic by using a beefier machine.
        - If you're using 2 cores & 4 GB of RAM on your SQL database - its going to be a bit slow.
        - If you're expecting a ton of traffic to hit your database, you might have to scale it up - maybe you need 32 cores & 64 GB of RAM to serve users data quick enough.
        - Scaling up means you'll be paying more so hopefully you have some revenue to pay for it.
    - A NoSQL database can support more traffic by using more machines, even if they are all 2 cores & 4GB of RAM.
        - Scaling up is a lot cheaper, and your costs don't grow as linearly as they would with a SQL database.

## Saving data with DynamoDB

For this class  - I'm going to focus on using DynamoDB and I'd encourage you to give it a shot.

**How does DynamoDB work?**

At a high level, you create a table by defining a "partition key" and a "sort key". The **partition key** is the primary key for your data - it is what you will use to find that particular item later (this is typically an ID). A "sort key" can be used to group related information together.

If you want to learn more about how DynamoDB manages partitions, [read their guide here](https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/).

Imagine a Restaurants and an Orders Table

![/lesson-5-assets/Untitled%204.png](/lesson-5-assets/Untitled%204.png)

- If you want to get details of 1 restaurant, you can use a **get operation** to get the item with `restaurant_id = 1`.
- If you want to look up all of a user's orders, you can use a **query** **operation** for all items with `userKey = 1`
- If you want to see 1 order for 1 user, you can use a **query operation** for `userKey = 1` and `orderId = 1`
- Any queries based on the partition key or primary key is super-fast (< 10ms) and cheap.
- If you want to see ALL orders across users (maybe for building a report), you have to do a **scan**
    - A scan will read every row in the table (across partitions) and is more expensive
    - How do you find all restaurants nearby you?
        - If your primary key is `restaurantId` , you have to **scan** all rows and check the zip code to see if the zip code is near by you. If you have restaurants from all over the world, you'll be making millions of reads just to find a handful of restaurants - an expensive operation no matter what database you're using.
        - If your primary key is `zipCode`, you can use a **query operation** to find all restaurants with `zipCode = 11106` which will just make a couple of hundred reads instead of millions.
        - If you want to get just 1 restaurant, you would use a **query operation** to find the restaurant with `zipCode = 11106` and `restaurantId = 1`

**DynamoDB Limitations**

There are no joins in a DynamoDB table - you can't get data from multiple tables in one query. Only SQL can do that. If you want data from multiple tables, you have to make **get operations or query operations** to get that data.

For examples of data in DynamoDB & sample queries - take a look at the [cheat sheet](lesson-5-assets/dynamodb-cheat-sheet.md).