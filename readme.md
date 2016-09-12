<!--
Location: SF
-->

![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png)

# Data Templating with Handlebars

### Why is this important?
<!-- framing the "why" in big-picture/real world examples -->
*This workshop is important because:*

Templating will be a less error-prone, more efficient way to structure elements and add them to the page. The pattern of using templates is common to many frameworks. Handlebars gives us a good picture of how templating can be implemented.

### What are the objectives?
<!-- specific/measurable goal for developers to achieve -->
*After this workshop, developers will be able to:*

- Articulate the usefulness of data templating in the browser
- Create and compile a Handlebars template
- Use Handlebars templating to display JSON data

### Where should we be now?
<!-- call out the skills that are prerequisites -->
*Before this workshop, developers should already be able to:*

- Add elements to the DOM using jQuery
- Use string concatenation to add together string literals and variables
- Retrieve specific information from large JSON data objects

### What is a template?

* A **template** is a document (or piece of code) that contains blank spaces for data.

* A **template function** is a function that takes in such a document and data and returns a dynamic HTML string.


### Why use client-side templating?

1. So far, we've been using jQuery to append HTML strings to the DOM when we have data to display in the browser. This can get really long and messy. Here's an example from an app that displays spotify data:

  ```javascript
  // construct track HTML to display
  var $trackHtml = '<div class="row"><div class="col-xs-4">' +
  '<img src="' + result.album.images[0].url + '" class="img-responsive"></div>' +
  '<div class="col-xs-8"><p><strong>' + result.name + '</strong> by ' +
  result.artists[0].name + '</p><p><a href="' + result.preview_url +
  '" target="_blank" class="btn btn-sm btn-default">Preview ' +
  '<span class="glyphicon glyphicon-play"></span></a></p></div></div><hr>';

  $('results').append($trackHtml);
  ```

   Templating is more declarative. With templating, we avoid building these long strings of HTML up in a JavaScript document. This will still allow us to dynamically display data in our HTML.

2. Separate markup from logic. Have you written something like this?

  ```javascript
  $('#developer-list').append('<li class="developer">' + firstName + ' - ' + lastName + '</li>');
  ```

  Templating keeps HTML structure primarily in HTML files.

3. Maximize code reusability and maintainability.

  Sometimes you'll decide to change the HTML structure for elements you're creating and displaying (e.g. adding an additional class name to an element). It's usually easier to avoid mistakes when modifying a template than an HTML string.

4. Templating can be done on the server side (inserting data into an HTML file before it's sent to the user) or on the client side (sending an HTML page with some parts that will be filled in).  Client-side templating avoids page refreshes that are necessary if we're making a new request to the server for updates to the HTML file.  Server-side templating avoids some load time in the browser and also makes it easier for search engines to see the content of the page.  Many sites mix the two approaches.  

### A Helpful Metaphor

Think of a **template** function as a *mold*. Handlebars creates it for us from We pass in **data**, which acts like *plastic* being poured in. We get back a *cast* - the HTML string that is generated by Handlebars, ready to be proudly displayed on the page.

<img src="http://ecx.images-amazon.com/images/I/81GytL7BBoL._SY355_.jpg" alt="novelty cake tin - bug shaped cakes" width="200">


### Explore Handlebars

We'll use the [sample-code](./sample-code) as our starting point.

### What is happening in `index.html`?

**Gather tools**

1) Add the Handlebars CDN to your `index.html`. Require Handlebars before your custom script file(s).

  ```html
  <head>
    <!-- meta tags, title, css links, maybe other js libraries -->
    <!-- ... -->

    <!-- handlebars -->
    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.0.5/handlebars.min.js"></script>

    <!-- custom script(s) -->
    <!-- ... -->
  </head>
  ```

**Put up a pedestal**

2) Create an element in the HTML to serve as a container for the elements that you'll template. Give this element an `id`.

  ```html
  <ul id="developers-list" class="list-group">
  </ul>
  ```
  <details>
    <summary>Why do you think we give the element an id?</summary>
    We give the element an id because we'll want to select it later and `append` or `prepend` data inside.
  </details>

**Sketch (optional)**

3) Use HTML to create an example of the structure you'll turn into a template. Remove this after you've gotten the template set up.

  ```javascript
  <ul id="developers-list" class="list-group">
    <li class="list-group-item">
      <a href="https://www.github.com/bobloblaw" target="_blank"><strong>Bob Loblaw</strong></a>
    </li>
  </ul>
  ```

**Sculpt in clay**

4) Make a `script` tag with a descriptive `id` and with `type="text/x-handlebars-template"` (Handlebars needs this).

  ```html
  <!-- handlebars template for inside of developers list -->
  <script id="developer-li-template" type="text/x-handlebars-template">
    Coming soon: developer github links!
  </script>
  ```

5) Inside the `script` element, create the HTML structure for part of the page. In this example, it's for the developers' links. Use `{{ }}` to identify where data will get filled in.

  ```html
  <!-- handlebars template for inside of developers list -->
  <script id="developer-li-template" type="text/x-handlebars-template">
    {{#each developers}}
      <li class="list-group-item">
        <a href="https://www.github.com/{{github_username}}" target="_blank"><strong>{{first_name}} {{last_name}}</strong></a>
      </li>
    {{/each}}
  </script>
  ```
  > The template can be anywhere in your HTML that a `<script>` tag can go, including in the location templated elements will take on the page.

6) The variables in the template must match the structure of the object holding the data. The example above uses developer data. In this example, each developer has a `first_name`, a `last_name`, and a `github_username`.

  ```javascript
  // each developer's data is stored in an object like:
  {
    first_name: "Bob",
    last_name: "Loblaw",
    github_username: "bobloblaw"
  }
  ```

**Set up a factory line (optional)**

7) Using `#each` in the template is special Handlebars-specific syntax.  What do you think it means?

<details><summary>answer</summary>The `#each` lets Handlebars know we'll want to repeat the structure inside the `#each` for every element of the array.</details>

If we didn't use an `#each` in the template, what kind of structure would we add in the JavaScript to repeat the list item for each result developer?

<details>
  <summary>answer</summary>
  Repeating always means looping.  Since we simply want to iterate over each element in an array and carry out the same action, this is a great time to use `.forEach` (or jQuery's `.each`).
</details>

### What is happening in `app.js`?

**Select what sculpture to mold**

1) Using jQuery, select the `script` element and pull out the HTML from inside it.

  ```javascript
  var source = $('#developer-li-template').html();
  ```

**Make the mold (template)**

2) Send the source to Handlebars' `compile` function. Handlebars generates a `template` function (the HTML mold). The `template` function knows exactly what "shape" the HTML should be and where plastic needs to be poured.

  ```javascript
  var template = Handlebars.compile(source);
  ```

> `template` isn't a special variable name; it just reminds us what this function does. You can call your template function whatever you'd like.


**Pour the plastic into the mold**

3) The template needs to know the actual data in order to fill it in.  Call the `template` function on an object. Keys are the variables used in the "blanks" and the value of each key is some value from the JavaScript.

  ```javascript
  var developerHtml = template({ developers: data.developers });
  ```

  >If you're getting data from an AJAX call, you'll want to do this *after* the call is successful.

**Open the mold to find a casting!**

4) The `template` function returns a string of HTML that it has generated by mashing the data into the template.

  ```javascript
  console.log(developerHtml);
  ```

**Put the casting on display**

5) Use jQuery to select the element where the new HTML should appear and append the new HTML string to the page.

  ```javascript
  $("#developers-list").append(developerHtml);
  ```


### Independent Practice

Replace the header of `sample-code/index.html` with a template that reads from the cohort's `data` object.

Follow the steps above carefully!


### Closing Thoughts

* HTML templating lets us create "molds", "mad libs", or "cake pans" in our HTML that we can fill in with data later.
* Client-side templating can cut down on the number of page refreshes requried to update the data displayed as a page changes. We'll see this more strongly later. 
* Handlebars is a library that provides client-side templating. Using it has 4 steps: write source with "blanks"; compile source into template function; combine data with template to get HTML string; add HTML string to the DOM.

### Additional Resources

* <a href="http://handlebarsjs.com" target="_blank">Handlebars.js</a>
