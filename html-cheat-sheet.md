# HTML Cheat Sheet

`div` is the building block of all your layouts.

It is a block element so you can think of it as a way to put content on its own row
```html
<div>
this is on its own row.
</div>
<div>
this is also on its own row
</div>
```

Newlines don't work in HTML, so you have to split them up into rows
```html
<div>
this is on its own row
this should be on its own row, but it shows up next to the above text
</div>

<div>
    <div>this is on its own row</div>
    <div>this should be on its own row</div>
</div>
```

Divs nested under other divs can create nested columns (using Flexbox) (example in JSX)
```jsx
<div style={{display: 'flex', flexDirection: 'row'}}>
    <div>this is a nested column 1</div>
    <div>this is a nested column 2</div>
</div>
```

Divs nested under other divs can create nested columns (by using Bulma's CSS classes)
```html
<div className="columns">
    <div className="column">this is a column 1</div>
    <div className="column">this is a column 2</div>
</div>
```

Make your site mobile friendly by adding this <meta> tag to your HTML. Its already included with create-react-app - take a look at what happens if you remove it:
```html
<html>
    <head>
        <meta
            name="viewport"
            content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no"
        />
    </head>
    ....
</html>
```
Make your buttons look even cooler by adding icons
```html
<!-- index.html -->
<link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.13.0/css/all.min.css"
    integrity="sha256-h20CPZ0QyXlBuAw7A+KluUYx/3pK+c7lYEpqLTlxjYQ="
    crossorigin="anonymous"
/>

<i class="fas fa-archive"></i>
```

Use OpenGraph tags to get your page to appear more nicely in search results. Although with create-react-app, you can't change it dynamically based on the page you're on.
```html
<head>
    ...
    <meta property="og:title" content="This is the title of your site in Search" />
    <meta property="og:description" content="This is the little text snippet that shows up under your title in search" />
    <meta property="og:image" content="https://somewhere/interesting.png" />
</head>
```
