# React

### JSX

You can write JSX just like you would write HTML in your React components

```js
function Profile() {
    return (
        <div>
            <div>me@myemail.com</div>
            <p>lorem ipsum bio</p>
        </div>
    )
}
```
You can assign JSX to a variable:
```js
const emailDiv = <div className="email-address">me@myemail.com<div>
```

And wrap it in parenthesis for multi-line JSX:
```js
const emailDiv = (
    <div>
        <div>My name</div>
        <div className="email-address">me@myemail.com</div>
    </div>
)
```
You can use JS in your JSX by using `{ }`
```js
const getEmailClassName = () => 'email-address'
const getEmailAddress = () => 'me@myemail.com'
return (
    <div className={ getEmailClassName() }>
        { getEmailAddress() }
    </div>
)
```

You can conditionally include a component in your JSX, but you can't write if/else conditions IN your JSX:
```js
return (
    <div>
        {isSignedIn && <ProfilePage />}
    </div>
)

// or

return (
    <div>
        {isSignedIn ? <LoginPage /> : <ProfilePage />}
    </div>
)

// or

const getContent = () => {
    if(isSignedIn) {
        return <LoginPage />
    } else {
        return <ProfilePage />
    }
}

return (
    <div>
        {getContent()}
    </div>
)

```


In JSX, you have to use `className` instead of `class`. You'll see warnings in the console about it, even if you forget:
```js
<div className="email-address">me@myemail.com<div>
```

### CSS & Styles

You can create inline styles on your HTML elements
```js
<div style={{backgroundColor: 'red'}}>this has a red background</div>
```
which is shorthand for
```js

function RedBox() {
    const styles = {
        backgroundColor: 'red'
    }

    return (
        <div style={styles}>
            this has a red background
        </div>
    )
}
```

You can use any CSS property, but all the property names are camelCased

```jsx
// JSX
<div style={{backgroundColor: 'red', marginTop: '1em'}}>this has a red background</div>
```
is equivalent to HTML
```
<div style="background-color: red; margin-top: 1em">this has a red background</div>
```

You can make a CSS class conditional with a template literal & a ternary expression:
```js
<div className={`container ${isSignedIn ? 'has-text-danger' : ''}`}></div>

// or

const getIsSignedIn = () => isSignedIn ? 'has-text-danger' : '';
return (
    <div className={`container ${getIsSignedInClass()}}`}></div>
)

```
You can import CSS into your React component - but it gets imported into your app stylesheet so the class is available to any component after its been imported:
```js
import './checkout-page.scss'

function Checkout() {
    return (
        <div className="checkout"></div>
    )

}
```

### Inputs and Forms

You can add onClick handlers to buttons:
```js
const doSomething = () => console.log('hello')
<button onClick={() => doSomething()}/>
```

`input`, `textarea`, and `select` components need `onChange` handlers.
```js
class EmailInput extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            inputText: ''
        }
    }

    render() {
        const onChange = (event) => {
            this.setState({inputText: event.target.value})
        }
        return (
            <input type="text" onChange={onChange} value={this.state.value}>
        )
    }
}
```

or with hooks:
```js
function EmailInput() {
    const [inputText, setInputText] = useState('')

    return (
        <input type="text" onChange={(event) => setInputText(event.target.value)} value={this.state.value}>
    )
}
```

You can create a component for every item in a list using `map`:
```js
return (
    <div>
        <ul>
            {this.state.products.map((product, index) => <li key={index}>{product.name}</li>)}
        </ul>
    </div>
)
```

You can use `import` to pull in different kinds of files:

```js
import heroImage from './assets/hero-image.jpg'

function ProfilePicture() {
    return (
        <div>
            <img src={heroImage}>
        </div>
    )

}
```

You can use the return value of async functions in your components:
```js
class ProductsList extends React.Component {
    constructor(props) {
        super(props)

        this.state = {
            productsList: null
        }
    }

    componentDidMount() {
        getProductsFromBackend().then(productsList => {
            this.setState({productsList})
        })
    }
}
```

or with hooks...
```js
function ProductsList() {
    const [productsList, setProductsList] = useState(null)
    useEffect(() => {
        getProductsFromBackend().then(productsList => {
            setProductsList(productsList)
        })
    }, []) // note the empty [], this means run once on load
}
```
