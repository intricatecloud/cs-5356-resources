# JS Cheat Sheet

JS objects are like Python dictionaries are like Ruby objects, and can be created and changed wherever:
```js
const object = {}
object['NY'] = 'New York'

object.anything = {
    nested: {
        object: 'hello'
    }
}

object.doSomething = () => console.log('doing something')
```

You can get all the keys or values from an object:
```js
const states = { NY: 1, NJ: 2}
Object.keys(states) // [ 'NY', 'NJ' ]
// or values
Object.values(states) // [ 1, 2 ]
```

You can iterate through all keys in an object:
```js
Object.keys(states).forEach((key) => {
    const state = states[key]
    // do something with it
})
```

Use the spread operator to copy all the key/value pairs from one js object to another
```js
const cart1 = {
    eggs: '1',
    juice: '2',
}

const cart2 = {
    candy: '1',
    ...cart1
}
```

Use the spread operator to copy one array to another:
```js
const array1 = [1,2,3]
const array2 = [...array1, 3, 4, 5]
```

When you're writing React, you're using ES6 which uses `import`. `require` is ES5 JS also used by node. You can reuse most node libraries (if they're compatible with browsers):
```js
import _ from 'lodash' // ES6

// is equivalent to

const _ = require('lodash') // ES5 & Node
```

You can extract JS code to another file by making a module:
```js
// sayHello.js
export default () => console.log('hello') // ES6

// is equivalent to
module.exports = () => console.log('hello') // ES5 & Node
```

You can export default functions from a module, and import it with any name:
```js
// helpers.js
export default {
    sayHello: () => {
        return 'hello'
    }
}

// app.js
import someFunction from './helpers'
someFunction.sayHello()
```

You can export named functions from a module, and import them using the `{ }` destructuring operator:
```js
// helpers.js
export const getPrice = () => '$20'

// app.js
import { getPrice } from './helpers'

// is equivalent to

import helpers from './helpers'
const getPrice = helpers.getPrice
```

Destructuring objects can help simplify your code:
```js
const { name, price } = product
// is equivalent to
const name = product.name
const price = product.price
```

You can even use it in function arguments:
```js
const onChange = ({ target }) => {
    console.log('target is', target)
}
// is equivalent to
const onChange = (event) => {
    const target = event.target
    console.log('target is', target)
}
```

You can also destructure arrays, similar to unpacking tuples in Python:
```js
const coordinates = [1, 2]
const [x, y] = coordinates
// is equivalent to
const x = coordinates[0]
const y = coordinates[1]
```

Use `fetch` to make API calls
```js
const getProfile = () => {
    // make GET request with a JSON response
    fetch('https://myapi/profile')
        .then(response => response.json())
        .then(profile => {
            doSomething(profile.name)
        })

    // make POST request
    fetch('https://localtest.me/profile', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': idToken
        },
        body: JSON.stringify({name: 'my name'}),
    })
    .then(response => response.json())
    .then(data => {
        console.log('profile created', data);
    })
    .catch((error) => {
        console.error('Error:', error);
    });
}
```

Use `async`/`await` to make promises easier to read, and use try/catch to catch errors. The above is equivalent to
```js
const getProfile = async () => {
    const response = await fetch('https://myapi/profile')
    const profile = response.json()
    doSomething(profile.name)

    // make POST request
    try {
        const response = await fetch('https://localtest.me/profile', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': idToken
            },
            body: JSON.stringify({name: 'my name'}),
        })
        const data = response.json())
        console.log('data created')
    } catch (err) {
        console.error(err)
    }
}

```