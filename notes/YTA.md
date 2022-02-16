# INTRODUCTION

We are going to build a simple to-do list like application completely from scratch with plain JavaScript. 
However, we are creating it without any sort of database because we will be using the local storage built into a web browser to save to-do items. 
This can be quite helpful for taking quick notes. 
Setting the app as the "new tab" page in the browser , the to-dos will persist on the local computer as long as you the cache is not cleared.

# PREREQUISITES

- Basic knowledge of HTML and CSS.
- Basic knowledge of JavaScript syntax and datatypes.
- Basic knowledge of working with the DOM (optional). 

# GOALS

Create a "new tab page" application that stores to-do items locally in the browser's local storage and displays them on the front end.

## Overview of web storage

Web storage is data stored locally in a user's browser. 
There are two types of web storage:

- Local storage - data with no expiration date that will persist after the browser window is closed.
- Session storage - data that gets cleared after the browser window is closed.

This is useful for saving data such as user preferences (light or dark color theme on a website), remembering shopping cart items, or remembering a user is logged into a website.
Previously, cookies were the only option for remembering this type of local, temporary data. 
Local storage has a significantly higher storage limit (5MB vs 4KB) and doesn't get sent with every HTTP request, so it can be a better option for client-side storage.

### Here is an overview of localStorage methods.

# Table format

| Method  | Description |
| ------------- | ------------- |
| setItem()  | Add key and value to local storage  |
| getItem()  | Retrieve a value by the key  |
| removeItem()  | Remove an item by key  |
| clear()  | Clear all storage  |
	
You can test out what's in local storage by going to the JavaScript console and typing it in.

#### localStorage
`Storage {length: 0}`

Adding some data to `localStorage` is as easy as using the `setItem()` method. 

I'll use a generic key and value for the names, but they can be any strings.

#### localStorage.setItem('key', 'value')

Now if you test `localStorage` in the console again, you'll find your new key and value.

`Storage {key: "value", length: 1}`

If you want to get the value for a particular key, you'll use the `getItem()` method.

#### localStorage.getItem('key')
`value`

Finally, you can remove the data with `removeItem()`.

#### localStorage.removeItem('key')

Using `clear()` will clear all local storage.

#### localStorage.clear()

Now we can begin setting up the app.

## Setting up the front end
First, we'll create a simple HTML front end with *index.html*. 

### Setup the Frontend

#### index.html

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />

    <title>App Name</title>

    <link
      rel="stylesheet"
      href="https://unpkg.com/primitive-ui/dist/css/main.css"
    />
  </head>

  <body>
    <div class="small-container">
      <h1>App Name</h1>

      <!-- Add code here -->
    </div>

    <script src="script.js"></script>
  </body>
</html>
```

We're going to set up with three things:

- A text input - for adding new items.
- A list - where the items will be added on the front end.
- A button - to clear all items.

```
<form>
  <input id="item" type="text" placeholder="New" required />
</form>

<h2>List of items : </h2>
<ul></ul>

<button>Clear All</button>
```

### Setting up JavaScript functionality

Before we integrate this into local storage, let's just get the form and list working - we want anything we submit in the input to appear in the ul.

#### scripts.js

First, we are going to set up some variables for the elements on the page - the form, the unordered list, the button, and the text input.

```
const form = document.querySelector('form')
const ul = document.querySelector('ul')
const button = document.querySelector('button')
const input = document.getElementById('item')
```
Next, we are going to make a function that creates an `li` element, since we'll be doing that more than once. 
We'll call the function `liMaker()`. It just creates an `li` element, sets the text of the element to the parameter, and appends the list item to the `ul`.
```
const liMaker = (text) => {
  const li = document.createElement('li')
  li.textContent = text
  ul.appendChild(li)
}
```
We're going to add an event listener to the form that watches for a submit event - which will be any time we press enter on the form. 
The `e.preventDefault()` will prevent the form from the default submit action, which we don't want, since we're not sending any data to a server.

Instead, the form will submit the value of the `input`. 
We're going to call the `liMaker()` function, which will create the item with the text of the `input` value and append it to the DOM. 
Finally, we'll set the `input` value to an empty string so you don't have to erase the last item entered manually.
```
form.addEventListener('submit', function (e) {
  e.preventDefault()

  liMaker(input.value)
  input.value = ''
})
```

Since we're not saving the items anywhere, when you close or refresh the browser, the items will be gone. 
The final step is to integrate it into local storage so that the data persists.

### Integrating local storage

We are going to add an event listener to the form that watches for a submit event - which will be any time we press enter on the form. 
The `e.preventDefault()` will prevent the form from the default submit action, which we don't want, since we're not sending any data to a server.

Instead, the form will submit the value of the input. We're going to call the `liMaker()` function, which will create the item with the text of the input value and append it to the DOM. Finally, we'll set the input value to an empty string so you don't have to erase the last item entered manually.

Since we're not saving the items anywhere, when we close or refresh the browser, the items will be gone. 
The final step is to integrate it into local storage so that the data persists.

### Integrating local storage

Now we're going to add a few more bits of functionality to the app. First, every time the form is submitted, the `input` value should be added to the `localStorage` as well as appear on the front end. We'll also want to loop through all the existing local storage items and display them at the top of the list. Last, we want the "Clear All" button to remove all items from local storage as well as the front end.

Let's create an empty array to start, and create a `localStorage` key called "items". 
Now, `localStorage` only supports strings as values, and want to store our to-dos in an array.

We can get around this by using `JSON.stringify()` to convert a data array to a string. 
We'll use `JSON.parse()` to convert the contents of `localStorage` back into something we can work with later in the data variable. 
Put this code below all the constant declarations we set earlier.

```
// other constant declarations here
let itemsArray = []

localStorage.setItem('items', JSON.stringify(itemsArray))
const data = JSON.parse(localStorage.getItem('items'))
```

In the form event listener, let's push any new `input` value into the array, then set the `localStorage` to the new, updated value.

```
// form event listener here
e.preventDefault()

itemsArray.push(input.value)
localStorage.setItem('items', JSON.stringify(itemsArray))
```
We're going to loop through everything in our data variable above, which contains all the existing `localStorage` data in a form JavaScript can understand and work with, and we'll run the `liMaker()` again. This will display all existing stored information on the front end every time we open the app.

```
data.forEach((item) => {
  liMaker(item)
})
```
Lastly, we'll add a click event to the button that will clear all data from `localStorage`, as well as removing every child node from the ul.
```
button.addEventListener('click', function () {
  localStorage.clear()
  while (ul.firstChild) {
    ul.removeChild(ul.firstChild)
  }
})
```
If all went well, everything will save to storage as well as appear on the front end, which you can check by testing `localStorage` in the console.

`Storage {items: "["Welcome","to","the","App"]", length: 1}`

There's one final problem: after closing the browser or reloading the page, all the existing information in `localStorage` is gone, and nothing remains on the front end. Why?

Our `itemsArray` is being reset to an empty array every time the script runs. We could fix this by making a conditional statement that checks if `localStorage` already exists, such as in the below example.
```
let items

if (localStorage.getItem('items')) {
  items = JSON.parse(localStorage.getItem('items'))
} else {
  items = []
}
```
A little more concise would be to use a *ternary operator* to do the same thing.
```
let items

if (localStorage.getItem('items')) {
  items = JSON.parse(localStorage.getItem('items'))
} else {
  items = []
}
```
With that, our app is complete! 

Now when we enter in some information and refresh or close the browser window, the data will persist until you manually clear the data in Developer Tools (under Application -> Storage) or by running the `localStorage.clear()` command. 
```
let itemsArray = localStorage.getItem('items')
  ? JSON.parse(localStorage.getItem('items'))
  : []
```

# CONCLUSION
In this tutorial, we learned how to make a simple to-do based application that can potentially be used to keep quick notes as a new tab page by using HTML5 web storage (local storage) and how to integrate it into a simple app.

----------------------------------------------

We're going to make a very simple web app with plain JavaScript that will retrieve information from an API and display it on the page.
There will be no server, dependencies, build tools, or complex . This is absolutely for beginners.

We are going to write from scratch this simple web app that connects to a *Studio Ghibli API*, retrieves the data with JavaScript, and displays it on the front end of a website. This is not meant to be an extensive resource on APIs or REST - just the simplest possible example to get up and running that you can build from in the future. We'll learn:

- What a *Web API* is.
- Learn how to use the *HTTP* request *GET* with JavaScript
- How create and display *HTML* elements with JavaScrip.

# QUICK OVERVIEW

API stands for Application Program Interface, which can be defined as a set of methods of communication between various software components. In other words, an API allows software to communicate with another software.

We'll be focusing specifically on Web APIs, which allow a web server to interact with third-party software. In this case, the web server is using HTTP requests to communicate to a publicly available URL endpoint containing JSON data. If this is confusing now, it will make sense by the end of the article.

You may be familiar with the concept of a CRUD app, which stands for Create, Read, Update, Delete. Any programming language can be used to make a CRUD app with various methods. A web API uses HTTP requests that correspond to the CRUD verbs.

| Action  | HTTP Method | Description 
| ------------- | ------------- | ------------- |
| Create  | POST  | Creates a new resource
| Read  | GET  | Retrieves a resource 
| Update  | PUT/PATCH  | Updates an existing resource 
| Delete  | DELETE  | Deletes a resource 

If you've heard REST and RESTful APIs, that is simply referring to a set of standards that conform to a specific architectural style. Most web apps do, or aim to conform to REST standards. Overall, there are a lot of terms, acronyms and concepts to understand - HTTP, API, REST - so it's normal to feel confused and frustrated, especially when API documentation assumes you already know what to do.
