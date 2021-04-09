# DynamoDB Data Model Cheat Sheets

# A Chat Application

2 tables:

- Table name: cs5356-chatrooms
    - Primary Key: chatId
    - Sort Key: userKey
    - Add a secondary index
        - Index name: userKey-chatId-index
        - Primary Key: userKey
        - Sort Key: chatId
    - Use defaults
- Table name: cs-5356-messages
    - Primary Key: chatId
    - Sort Key: timestamp (Number)

```jsx
const AWS = require("aws-sdk");
const { v4: uuidv4 } = require("uuid");
const docClient = new AWS.DynamoDB.DocumentClient({ region: "us-east-1" });

const listChatrooms = (userKey) => {
  return docClient
    .query({
      TableName: "cs5356-chatrooms",
      IndexName: "user_key-chat_id-index",
      KeyConditionExpression: "userKey = :userKey",
      ExpressionAttributeValues: {
        ":userKey": userKey,
      },
    })
    .promise()
    .then((response) => response.Items);
};

const listChatroomMembers = (name) => {
  return docClient
    .query({
      TableName: "cs5356-chatrooms",
      KeyConditionExpression: "chatId = :chatId",
      ExpressionAttributeValues: {
        ":chatId": name,
      },
    })
    .promise()
    .then((response) => response.Items);
};

const joinChatroom = (name, userKey) => {
  return docClient
    .put({
      TableName: "cs5356-chatrooms",
      Item: {
        chat_id: name,
        user_key: userKey,
      },
    })
    .promise();
};

const leaveChatroom = (name, userKey) => {
  return docClient
    .delete({
      TableName: "cs5356-chatrooms",
      Key: {
        chat_id: name,
        user_key: userKey,
      },
    })
    .promise();
};

const sendMessage = (name, userKey, message) => {
  return docClient
    .put({
      TableName: "cs5356-messages",
      Item: {
        chat_id: name,
        user_key: userKey,
        message: message,
        timestamp: Date.now(),
      },
    })
    .promise();
};

const getMessages = (name) => {
  return docClient
    .query({
      TableName: "cs5356-messages",
      KeyConditionExpression: "chatId = :chatId",
      ExpressionAttributeValues: {
        ":chatId": name,
      },
    })
    .promise()
    .then((results) => results.Items);
};
```

---

# A Food Delivery App

2 Tables

- Table name: cs5356-restaurants
    - Primary key: zipCode
    - Sort key: restaurantId
- Table name: cs5356-orders
    - Primary key: userKey
    - Sort key: orderId

```jsx
const AWS = require("aws-sdk");
const { v4: uuidv4 } = require("uuid");
const docClient = new AWS.DynamoDB.DocumentClient({ region: "us-east-1" });

const getRestaurants = (zipCode) => {
  return docClient
    .query({
      TableName: "cs5356-restaurants",
      KeyConditionExpression: "zipCode = :zipCode",
      ExpressionAttributeValues: {
        ":zipCode": zipCode,
      },
    })
    .promise()
    .then((results) => results.Items);
};

const addRestaurant = (restaurant) => {
  return docClient
    .put({
      TableName: "cs5356-restaurants",
      Item: {
        zipCode: restaurant.zipCode,
        restaurantId: restaurant.name,
        menu: restaurant.menu,
      },
    })
    .promise();
};

const placeOrder = (userKey, orderDetails) => {
  return docClient
    .put({
      TableName: "cs5356-orders",
      Item: {
        userKey: userKey,
        orderId: uuidv4(),
        total: orderDetails.total,
        items: orderDetails.items
      },
    })
    .promise();
};

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

---

# A Social Media App

2 Tables:

- Table Name: cs5356-posts
    - Primary Key: postId
    - Sort Key: timestamp
    - Add a Secondary Index
        - Index Name: userKey-timestamp-index
        - Primary Key: userKey
        - SortKey: timestamp
- Table Name: cs5356-followers
    - Primary Key: userKey
    - Sort Key: followedUserKey

```jsx
const getGlobalFeed = () => {
  return docClient
    .scan({
      TableName: "cs5356-social-media-feed",
      Limit: 10,
    })
    .promise()
    .then((results) => results.Items);
};

const getUserFeed = (userKey) => {
  return docClient
    .query({
      TableName: "cs5356-social-media-feed",
			IndexName: 'userKey-timestamp-index',
			KeyConditionExpression: 'userKey = :userKey',
			ExpressionAttributeValues: {
				':userKey': userKey
			}
    })
    .promise()
    .then((results) => results.Items);
};

const postMessage = (userKey, message) => {
  return docClient
    .put({
      TableName: "cs5356-social-media-feed",
      Item: {
        post_id: uuidv4(),
        timestamp: Date.now(),
        userKey,
	      message: message
      },
    })
    .promise();
};

const followUser = (userKey, userKeyToFollow) => {
  return docClient
    .put({
      TableName: "cs5356-followers",
      Item: {
        user_key: userKey,
        user_key_to_follow,
      },
    })
    .promise();
};

const getFollowedUsers = (userKey) => {
  return docClient
    .query({
      TableName: "cs5356-followers",
      KeyConditionExpression: "user_key = :user_key",
      ExpressionAttributeValues: {
        ":user_key": userKey,
      },
    })
    .promise()
    .then((results) => results.Items);
};
```