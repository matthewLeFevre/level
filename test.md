# FHD Blog Documentation -docs-

## Table Of Contents

1. [General Notes](#general-notes)
    + [Introduction](#introduction)
    + [Documentation Format](#documentation)
    + [Comment Format](#comment-format)
    + [Naming Convention](#naming-convention)

2. [Controllers](#controllers)
    + [Services](#services-controller)
    + [Archive]
    + [Archive Main]
    + [Categories Main]
    + [Posts]
    + [comments]

3. [HTML]


## General Notes

### Introduction

### Documentation

### Comment Format

## Naming convention

## Controllers

### Services Controller
`controller_services.js`

The Service Controller is the central hub of all of the controllers, it houses a collection of global variables ( not all of them.. if you can put all the global variables here that would be nice they are normally at the top of each file and rarely inbetween functions), as well as a plethora of commonly used functions. More functions could and should probably be added to this controller.

#### Important Notes

1. One key function that the services controller provides is communication between the 'controller_post.js' and the `controller_comments.js`. When a comment is created the `controller_posts.js` *sends all the info to the* `controller_services.js` which in turn works with the `controller_comments.js`.

#### Helper Functions

1. `function getQueryString(queryString)`
    + In case you are not familiar with this function it simply takes information that is passed from other functions through the url. This information always comes in the form of a name value pair. The credit of the function as far as I am concerned goes to *Ian Esky*.

2. `function getPostComments(postId)
    + This function is called from the `controller_posts.js` and all that the `controller_services.js` does is pass on a specific post Id to the `controller_comments.js` so that all the comments for that post can be rendered in the correct place in the browser.
