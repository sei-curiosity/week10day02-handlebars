
# Views in Express (Server Side Rendering)

## Objectives

- Set up Handlebars with Express
- Create and render views in an Express application
- Render views dynamically (Server side rendering)
- Link to static assets in an Express application
- Understand layouts and templating

<br />

## Hook
As we have already seen, `res.send()`, can do a lot for us.  It allows us to easily check whether or not the routes that we have created work.  However, we normally want to give our users more than a document.write view of our pages.  To do that, we need to create views.


## Intro to Views in Express


### What is server side rendering?

- **Server Side** rendering is like a traditional restaurant (Tacos and Tequilas!).
  - You place an order (request) and the waiter delivers it to the kitchen for assembly.
  - Ingredients (data) are combined and placed into an oven (Node/Express/Handlebars).
  - After the ingredients have finished being prepared, the dish (HTML) is then delivered to your table via the waiter (response).
- **Client Side** rendering is like a Hibachi restaurant.
  - All of the ingredients (data) are delivered to your table (browser) already prepped and ready to go.
  - Each dish (web page view) is assembled by the chef (React) in front of you/in real time as you request it.

<br />

### What is Handlebars?

![](https://i.imgur.com/gMD515j.png)

http://handlebarsjs.com/

**Handlebars** allow us to embed JS variables inside of our HTML and render it as text. Our server injects the variables into the HTML document **before** sending it to the client.

The client/browser will never see the variables or the handlebars syntax, only the values. The HTML document is "fully baked" when the browser receives it.

A similar concept: remember ES6 template strings? If we use the `${}` syntax inside of our template string, it lets us run JS, e.g. call a function, input a variable, or add some numbers. For example:

```javascript
var name = "Fred";

console.log(`Hi, my name is ${name}`);
// Hi, my name is Fred
```

Handlebars gives us similar functionality, but instead of injecting JS into a string, it allows us to inject js into our HTML page.

There are certainly other template engine options out there such as Pug (https://pugjs.org/api/getting-started.html) or Embedded Javascript (http://www.embeddedjs.com/). 

Handlebars is fun and is one of the easiest to use. It also closely resembles the syntax we will use for React which is another reason we are using it.

<br />

## Setting up our app to use Handlebars

For this lesson, we are going to build on the `hello-express` app from yesterday afternoon. Watch me walk through the following steps below. Then you will have time to practice them yourself. I suggest that you keep your `nodemon` server running in one tab, and open a 2nd tab for commands.

1. To install handlebars into our node application enter the following in the terminal:

```bash
 npm install hbs
```

2. We need to require the hbs module in the `server.js` file. (around line #3, below `const express` and `const app`):

```javascript
const express = require('express'); // Loading express on our server
const app = express(); // install express on our server
const hbs = require('hbs'); // loads handlebars
```

> What does `require()` do?

3. Then we need to set Express's view engine to be handlebars inside of the `server.js`. We'll also tell our app where to find our views. We're using the Express method `.set`:

```javascript
app.set("view engine", "hbs"); //tells Express what to use for rendering templates
```
> **NOTE:** `.set()` is used to configure Express. 
    
> **NOTE:** Express will look for view templates in a folder named `views`
    
> Read more at: https://expressjs.com/en/guide/using-template-engines.html

4. Create a directory called `views` and let's make a view for our first `/greeting` route. We're also gonna create a layout file (more on that soon!). We'll do this in the root directory of our application. In the terminal:

```bash
 mkdir views
 touch views/greeting.hbs
 touch views/layout.hbs
```

The Handlebars extension is `.hbs`. This tells our server that this file contains Handlebars code, and that it should be converted to HTML.

<br />

&#x1F535; **YOU DO: 2 minutes**

Follow the 4 steps above and add Handlebars, a view folder and the two new views- greeting and layout to your app.

<br />

## How do we use Handlebars?

Everything that we have done so far has prepared us to configure handlebars within our application. You will need to go through these steps every time you make a new Express application.

1. Remember how we used query parameters yesterday? We can use those in the same way, but rather than sending them to a `res.send()` method, we can pass them to our view and render the params as HTML.

```javascript
app.get('/greeting', function(req, res) {
    console.log(req.query);
    
    res.render('greeting', {
      data: req.query.saying
    });
});
```

Instead of sending a string directly to the response of a get request, we would like to render a view. The `.render` function takes two arguments.

- The first argument is the view that we would like to render.
- The second argument is an object. We can use the keys in this object inside of the view, to access the values in these key-value pairs.

2. When we go to `http://localhost:3000/greeting?saying=hii!` our view is empty! Let's go ahead and change that now.  In the `views/layout.hbs`:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Hello Express App</title>
        <link rel="stylesheet" type="text/css" href="/css/styles.css">
    </head>
    <body>
        <h6>NAV BAR GOES HERE AND WILL APPEAR ON EVERY VIEW</h6>
     
        {{{ body }}}
     
        <h6>FOOTER GOES HERE AND WILL APPEAR ON EVERY VIEW</h6>
    </body>
</html>
```

The layout acts as a picture frame for all of our views. It's a way to DRY up our code. For example, if we want a nav bar or a footer on every page we would include it here.
    
The `{{{ body }}}` is where each individual view (depending on the route) will be inserted into the HTML.

> Take note that there are 3 braces around body instead of 2.  This signals to handlebars that this is a place holder where our main content should be rendered. 

3. Then in `views/greeting.hbs` add:

```html
<h1> Here is our greeting route response!</h1>
<p>{{ data }}</p>
```

4. Try this URL `http://localhost:3000/greeting?saying=hii!`


> Inspect the body in the dev tools. There is no trace of `{{}}`. Why?

<br />

&#x1F535; **YOU DO: 2 minutes**

Walk through the steps above in the "How do we use Handlebars?" section to add on to your app.

<br />

## One step further...

What if we had an array for our favorite foods in the `server.js` and we wanted to render them into a view?

1. Create a new file in views:

```bash
touch views/favorite-foods.hbs
```

2. Let's create a new route for favorite foods and add your array of favorites.

```javascript
app.get('/favorite-foods', (req, res) => {
    const favoriteFoods = ["Jeni's Almond Butter ice cream", 'Tacos from Superica', 'A Breakfast Sandwich from Gjelina to go in Venice', 'Croissants from Bottega Louie in Downtown Los Angeles', 'Pizza from Little Star in San Francisco'];

    res.render('favorite-foods', {
        data: ____
    });
});
```

What would the value of data need to be in order to render the array in our `greeting.hbs`?

```javascript
app.get('/favorite-foods', (req, res) => {
    const favoriteFoods = ["Jeni's Almond Butter ice cream", 'Tacos from Superica', 'A Breakfast Sandwich from Gjelina to go in Venice', 'Croissants from Bottega Louie in Downtown Los Angeles', 'Pizza from Little Star in San Francisco'];

    res.render('favorite-foods', {
        data: favoriteFoods
    });
});
```

3. Our `favorite-foods.hbs` file is going to need the same code as in our `greeting.hbs` file:

```html
<h1>My Favorite Foods</h1>
<p>{{ data }}</p>
```

Refresh the page in your browser. What is the output? Handlebars converted our array into text with commas, actually very similar to when we call `toString()` on an array.

<br />

&#x1F535; **YOU DO: 2 minutes**

Walk through the steps above in the "One step further..." section to add on to your app.

<br />

## Handlebars Loops 

But what if we wanted to to display this `favoriteFoods` array data in a `<ul>` with `<li>` tags? How have we accomplished this in the past... Vanilla JS or jQuery?

<br />

&#x1F535; **YOU DO: 5 minutes**

Read through the Handlebars built in [helpers documentation](http://handlebarsjs.com/builtin_helpers.html). Try to render the Array of foods as a `<ul>` with `<li>` tags like so:

<br />

![](https://i.imgur.com/uVzMiGU.png)

<br />

<details>

<summary>Solution for Handlebars Loops</summary>

We can iterate over a list using the Handlebars built-in `each` helper. Inside of the block, you can use `this` to reference the element being iterated over.

In `favorite-foods.hbs`, replace your `<p>` tags with the following:

```javascript
<ul>
  {{#each data}}
    <li>{{this}}</li>
  {{/each}}
</ul>
```

1. We start our loop with `#each` and pass in the object (`data`)  that we would like to loop over
2. `{{this}}` is the iterator for each item in the array so we surround it with `<li>` tags
3. We close our loop with `{{/each}}`

You can check out more at this link for [Handlebars Built In Helpers](http://handlebarsjs.com/builtin_helpers.html)

</details>

<br />

## Add Static Assets

This is also a great time to note how we serve static assets. Notice that we linked a stylesheet in our layout file. Let's create a folder named `public` in our main `hello-express` directory. This allows our app to utilize CSS, JS, Fonts, Images, 3rd party library files, etc.

Let's add an external style-sheet:

1. In our `server.js`, let's add:

```javascript
app.use(express.static(__dirname + '/public')); // VERY IMPORTANT!! Make sure to add a '/'
```

> `.use()` is another method that Express makes available to us. It is called "Middleware". Middleware gives us the ability to take action between the request and response, like authentication, or to log info to our server logs, etc.

2. Create a public directory: `mkdir public` 
3. cd into your `public` directory
4. `mkdir css`
5. Create a `styles.css` file: `touch css/styles.css`
6. Add a CSS rule to test it out:

```css
body {
  background-color: red;
  font-family: Georgia;
}
```

<br />

&#x1F535; **YOU DO: 3 minutes**

Walk through the previous steps and add static assets to your app.

<br />

## LAB Time

For lab time you are going to create a new Express app from scratch that will render your favorite TV shows. Essentially, you are going to walk back through the two lesson readme files from today and practice what you have learned.

1. Create a new Express App. Here are a few steps to get you started. In your ga folder

```bash
$ cd ~/Desktop/ga/class-exercises
$ mkdir shows_app
$ cd shows_app
$ npm init
$ npm install express
$ npm install hbs
$ touch server.js
```

1. Create the necessary variables and require the appropriate modules in the `server.js` file.
2. Set up your server and port using `app.listen()`
3. In the `server.js` file, create an `app.get()` route for `/shows`.  
4. Create an array called `const faveShows` and add your favorite TV shows.
5. Test out your route using `res.send()` before creating your views.
6. Update your route to render a view for `views/shows.hbs`. (Remember to create a `views` folder and include a `views/layout.hbs` file.)
7. Add `{{}}` to your `shows.hbs` file that will render the shows inside of an `<ol></ol>` using `<li></li>` tags
8. Add the code to `.use()` static assets.
9. Add a css file to your app for styling

**BONUS**

1. Add a javascript click event to your app in a separate JS file.
2. Add a proper nav bar
3. Add a proper footer
4. Add images to your `/public` directory
5. If you finish, walk back through the appropriate steps above and add another favorites resource to your app (e.g.- movies, songs, artists, novels.)
    
<br />

## Extra Exercise

<details>

<summary>99 Bottles</summary>

  Build an express application that enables users to count down the number
  of bottles of beer.
  Specs:
  - On the home page (`get "/"`), users should see:
    - "99 Bottles of beer on the wall"
    - a link that says "take one down, pass it around"
    - this should link to `/98`, where the number represents the number of bottles left.
  - When a number is given in the url (`get "/:number_of_bottles"`), users should see:
    - number of bottles "Bottles of beer on the wall"
    - a link to "take one down, pass it around", where the href is number of bottles minus 1.
  - If there are 0 bottles left, do not show a link to "take one down"
    - Add a link to start over, which directs the user back to the home page.

</details>
