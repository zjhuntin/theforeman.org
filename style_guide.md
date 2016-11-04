---
layout: style_guide
title: Style Guide
---

## Style Guide

Please use to help guide your development when working on Foreman and it's related projects.

## Git Commit Messages

For Foreman and Katello, we ask that git commit messages are properly formatted and that they reference an issue in Redmine.

#### Format

Git commit messages should give a brief overview of the problem or feature along with a short
description of what was implemented to fix the bug or feature. Well formatted git commit messages
have two components: a short message and a long message or description.  The short message ought to
be limited to 70 characters while the long message should be wrapped at 74 or so characters.  See
below for an example. Also, you can read [Tim Pope's article about git message
formatting](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html).

#### Redmine

Each git commit message should contain a reference to the Redmine issue at the beginning of the
commit message. There are two way to do this. The first will simply attach the commit to the Redmine
issues:

```
Refs #123 - Improve performance of content views
```

The second type will actually close out the issue once the PR containing the commit gets merged:

```
Fixes #123 - Fix content view publish bug
```

#### Example

Below is an example of how a git commit message ought to look like:

```
Fixes #1234 - Fix content view yum publish bug

Fix a problem where content views were not being published if they
contained yum repositories. The repositories were not being referenced
correctly. To fix this, I changed the repository look up method to go to
ElasticSearch and find the attached repos.

This fix also addresses a problem with promoting that was also caused by
this bug. Promoting also has another bug though that needs to be fixed
before it works correctly.
```

#### Multi issue example

Below is an example of a pull request fixing multiple issues (pull requests should only fix a single issue in most cases)

```
Fixes #1234,#5678 - Fix content view yum publish bug

Fix a problem where content views were not being published if they
contained yum repositories. The repositories were not being referenced
correctly. To fix this, I changed the repository look up method to go to
ElasticSearch and find the attached repos.
```

## Javascript Code Conventions

This document contains conventions for programming Javascript that should be followed in an effort
to increase readability, performance and re-usability.  As builds are run against the
[JSLint Code Quality Tool](http://www.jslint.com/), an effort should be made to conform to the
guidelines set forth by JSLint.

### General
 * 4 spaces (instead of 2), no tabs
 * camelCase for variables
 * CamelCase for classes, and service names
 * Use empty lines to break up logical code chunks
 * Always use `===` for comparison
 * Place semicolons wherever they are required.  While missing semicolons may not throw an error,
   they can cause unintended behaviors.

### Javascript Libraries

A number of Javascript libraries are used within Katello.  Please refer to each libraries documentation.

  - [jQuery](http://api.jquery.com/)
  - [underscorejs](http://underscorejs.org/)
  - [AngularJS](https://angularjs.org/)

### Syntax Conventions

The following are coding conventions related to Javascript syntax in an effort to enhance
readability and adhere to the semantics of the language itself.

#### Conditional Statements

Include spaces around the argument of conditionals:

```javascript
if (condition) {
    // do something
}
```
 
#### Variable Declaration

All variable declarations should be placed at the top of a function and a single `var` statement
with comma separated variables should follow.

Correct:

```bash
var add = function (list) {
    var sum = 0,
        length = list.length,
        i;

    for (i=0; i < length; i += 1) {
        sum += list[i];
    }

    return sum;
};
```

Incorrect:

```bash
var add = function (list) {
    var sum = 0;

    var length = list.length;
    for (var i=0; i < length; i += 1) {
        sum += list[i];
    }

    return sum;
};
```

#### Function Declaration

Use function expressions instead of function declarations because function expressions are more versatile
and less prone to unexpected behavior. 

```javascript
// Function Declaration: don't use this
function add(a,b) {return a + b};

// Function Expression: use this
var add = function (a,b) {return a + b};
```

#### Object Iteration

When iterating over an object the `hasOwnProperty()` function should be invoked on each value to
ensure errant properties attached to the object via the prototype chain do not cause unintended
behavior.

Correct:

```bash
var find_product = function (items) {
    var product,
        item;

    for (item in items) {
        if (items.hasOwnProperty(item)) {
            if (items[item] === 'product') {
                product = items[item];
            }
        }
    }

    return product;
};
```

Incorrect:

```bash
var find_product = function (items) {
    var product,
        item;

    for (item in items) {
        if (items[item] === 'product') {
            product = items[item];
        }
    }

    return product;
};
```

#### Array Iteration

The `for-in` loop should never be used to iterate over an array.  Instead, opt to use the
traditional:

```javascript
var i,
    length = list.length;

for (i=0; i < length; i+= 1) {
    list[i];
}
```

#### Object Attribute Access

Javascript supports both the dot-operator `myobject.foo` and index-style `myobject["foo"]` object
attribute access.  Always use the dot-operator unless dealing with properties that are dynamic,
reserved keywords, or `snake_case` (in order to prevent a JS Hint error).
 
### Performance Conventions

These conventions while not directly related to the syntax of the language serve to improve the
overall performance of any Javascript especially when manipulating the Document Object Model (DOM),
which is the largest performance bottleneck.

#### DOM Manipulation

Use [AngularJS directives](https://docs.angularjs.org/guide/directive) to handle DOM manipulation.  
It should be rare to have to manually manipulate the DOM but follow these guidelines if necessary:

 - Access the DOM the minimum number of times possible and save DOM elements to local variables
 - Be wary of looping over DOM elements
 - Know what causes browser reflows and avoid doing so unless absolutely necessary

### Checking Your Code

We use [JSHint](http://www.jshint.com/) (a community driven fork of JSLint) to maintain the quality
of the Javascript code in Katello. For more information about how to run JSHint, see the [developing
section of Bastion](https://github.com/Katello/bastion#developing-bastion).


## Ruby Code Conventions

 * When indenting always use two spaces. Never use a tab character.
 * Always run ''ruby -w'' to see all ruby parses warnings and recommendations.
 * Naming conventions:
   * classes and modules: `CamelCase`
   * methods and variables: `snake_case`
   * constants: `UPPER_SNAKE_CASE`
 * Use `{ }` for single-line blocks, `do end` for multi-lines blocks e.g.:

```bash
names.each { |name| puts name.upcase }

names.each do |name|
  puts "Hello #{name}"
  puts "How are you?"
end 
```

 * In regard to using parentheses with methods:
   * We recommend against using parentheses for methods and method definitions
     without arguments.
   * We highly recommend using parentheses for all other methods and method
     definitions. The only exception is when it's used as DSL.


```bash
def name                  # good
def name()                # bad
def open(file, mode)      # good
def open file, mode       # bad
name                      # good
name()                    # bad
open("file.txt", "w")     # good
open "file.txt", "w"      # bad
name.is_a? String         # acceptable, we consider that a DSL for type checking
render :index             # acceptable, Rails DSL
```

  * Avoid class variables (@@variable) wherever possible. Instead, try to use a
    instance variable (@variable) or at least a thread-safe variable
    (Thread.current[:variable]).

### More info

We also recommend most of the conventions in the community driven Ruby and Rails style guides:

* https://github.com/bbatsov/ruby-style-guide
* https://github.com/bbatsov/rails-style-guide

### Checking your code

To enforce the styles from the style guide and also to check for warnings/error, we use rubocop.
To run rubocop, simply execute this from you foreman directory:

```
rake rubocop
```

If you want to check on Katello's style you can also run this from the foreman directory:

```
rake katello:rubocop
```

Also, rubocop [integrates with some IDES](https://github.com/bbatsov/rubocop#editor-integration).
