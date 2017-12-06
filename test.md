# FHD Blog Documentation

## Table Of Contents

1. [Introduction](#introduction)
    + [Code](#code)
    + [Comment Format](#comment-format)
    + [Nameing Convention](#nameing-convention)

2. [Controllers](#controllers)
    + [Services](#services-controller)
        1. [SC Important Notes](#sc-important-notes)
        2. [SC Core Functions](#sc-core-functions)
        3. [SC Helper Functions](#sc-helper-functions)
    + [Archive](#archive)
        1. [AC Functions](#ac-funcitons)
    + [Archive Main Post](#archive-main-post)
        1. [AMP Functions](#amp-functions)
    + [Archive Main](#archive-main)
        1. [ACM Functions](#acm-functions)
    + [Categories Main](#categories-main)
        1. [CM Functions](#cm-funcitons)
    + [Categories](#categories)
        1. [CT Functions](#ct-functions)
    + [comments](#comments)
        1. [COM Functions](#com-functions)
    + [Home](#home)
        1. [Home Functions](#home-functions)
    + [Posts](#posts)
        1. [Post Functions](#post-functions)
    + [Recent](#recent)
        1. [Recent Functions](#recent-functions)


## Introduction

Well hello, congratulations! You have successfully made it to the documentation. I am guessing you tried your hand at decifering the code that the intern created oh... I don't know how long ago now. Well this is that interns last attempt to clarify that code and hopefully help you out. It is also somehting of a self written eulegy because it was that interns last project in his internship... If that dosen't bring tears to your eyes the spelling and gramar errors surly will. That being said help me keep my reputation by correcting spelling and gramar errors as you come accross them. Also if you make a change to the code please update this documentation in the appropriate place. Thanks!

### Code

#### HTML

Due to the nature of the project HTML needs to be tailored to every project and mostly be dynamically created by JavaScript

#### CSS

CSS is totally dependent on the Project. Use CSS at your own risk. The CSS I have created was concieved for FHD blog subsites and their associated branding.

#### JavaScript

JavaScript is the most reusable. Please be sure to look over the dynamcally created html to be sure that it is creating the structure that you want as well as adding the correct css classes.

+ **JQuery** has also been used for some things like the AJAX requests to the sharepoint API as well as some DOM traversal. As I was building the project I tried my best to drift away from the projects dependency on JQuery due to the fact that JQuery has mostly lost its steam in the industry.

### Comment Format

Everyone comments differently here are a few tips as to how I have commented in the code.

**JavaScript:**
```javascript
//========================================
// Function or collection of related functions
//========================================
```

And thats about it for comments.

## Nameing convention
**Note:** Nameing Conventions are not yet applied in all files.

Conservatively speaking the functions that are not prefixed with an initial are global functions. I will do my best to list the controllers that do not follow this convention.

### Prefix Initial Meaning
**Note:** If the function or variable is prefixed it is related to said controller or webpart.

1. `pM`: `post_main.html` & `archive_main_posts.js`
2. `rP`: `recent_post.html` & `controller_recent.js`
3. `sT`: `post_main.html` & `controller_posts.js`
4. `hM`: `home_main.html` & `controller_home.js`
5. `cN`: `controller_comments.js`
6. `cT`: `controller_categories.js`
7. `pC`: `posts_by_category.html` & `controller_categories_main.js`
8. `cM`: `categories_main.html` & `controller_categories_main.js`
9. `aR`: `archive_wp.html` & `controller_archive.js`



## Controllers

### Services Controller
`controller_services.js`

The Service Controller is the central hub of all of the controllers, it houses a collection of global variables ( not all of them.. if you can put all the global variables here that would be nice they are normally at the top of each file and rarely inbetween functions), as well as a plethora of commonly used functions. More functions could and should probably be added to this controller.

#### SC Important Notes

1. One key function that the services controller provides is communication between the 'controller_post.js' and the `controller_comments.js`. When a comment is created the `controller_posts.js` sends all the info to the `controller_services.js` which in turn works with the `controller_comments.js`.

#### SC Core Functions

1. `function apiInteract(url, mehtod, headers, success, error, body)`
    + In an effort to further simplify JQuerys' already simple Ajax library I have wrapped the JQuery Ajax function in another function that requires only parameters that we need for this blog.
        + **Important to note:** the `body` parameter is in reality not the body of the request but the data payload. If this is confusing to you I will bring up the function down below. As you can see the body is being sent to the data property of the Ajax request. The reason it is how it is is because I started with body and upon further research I needed data to actually add comments and likes to posts. 

```javascript
function apiInteract (url, method, headers, callback, errorCallBack = false, body = "") {

       $.ajax({
    url: url,
    
    method: method,
    
    data: JSON.stringify(body),
    //data: body,
    
    headers: headers,
    
    success: function(result) {
      
      if (callback) {
        callback(result);
      } else {
        console.log(result);
        console.log("success");
      }
      
    },
    
    error: function(err) {
      if (errorCallBack) {
        errorCallBack(err);
      } else {
        console.log(err);
      }
    }
  });
}
```

2. `function createComment(postId, commentbody)`
    + And this function we witness the heavey lifting of the SharePoint rest api as it goes off to create a new comment for our post. This function is only called from the `controller_comments.js` controller.

#### SC Helper Functions

1. `function getQueryString(queryString)`
    + In case you are not familiar with this function it simply takes information that is passed from other functions through the url. This information always comes in the form of a name value pair. The credit of the function as far as I am concerned goes to *Ian Esky*.

2. `function getPostComments(postId)`
    + This function is called from the `controller_posts.js` and all that the `controller_services.js` does is pass on a specific post Id to the `controller_comments.js` so that all the comments for that post can be rendered in the correct place in the browser.

3. `function convertSPDate(d)`
    + This functions author is Ben Tedder (www.bentedder.com) I have however significantly modified it to work in the way we need it too. It could however use some work. In side of it I have to do some weird things to get the actual day of the week and so forth. **If you have a better way of writing this or you can improve it please do**. This function is used for all of the dates so it is very commonly used.
    1. `function readable(jsDate)`
        + `readable()` is a function that does not do what it was originally created to do which was to take the date parse it and deliver a beautiful string that could be rendered next to a comment or post. Much of that is now accomplished by its parent function `convertSPDate()`. I use it to create a new date object and get the day of the week that the date uses. All of this can be done in the parent.

4. `function processSPPost(body)`
    + **Note:** This function also has a large duplicate that will make the thumbnails larger... It is mostly css manipulation.
    + This function splits up the body of a post created by out of the box sharepoint. All it does is seperate the `<iframe>` from the `<p>` tags we also are adding in a css class to bend the `<iframe>` to our will. This can also be easily retrofitted to also work with images or anything else that the blog may contain. All you have to do is take a look at the html output of the post and be handing with string manipulation in js.
    + ** Update:** This function has just been recently updated to select the first image found in a body and use that as an iframe.

5. `function createPostThumbnailMarkup()`
    + **Note:** This function also has a large duplicate that will make the thumbnails larger... It is mostly css manipulation.
    +This function does the heavy lifting by creating dynamic html for blog post thumbnails. Feel free to use any of it just bare in mind it was created specifically for FHD blogs. 


### Archive
`controller_archive.js`

This is one of the smaller controllers that handles the `archives_wp` webpart. 

#### AC Functions

1. `function aR_calcArchiveData()`
    + We start things off in this controller by finding out what the current date is and based on that date getting the last five months. We will use these months to populate a list of links to all of the posts associated with said month. 

2. `function aR_processArchiveData(months, c_date)`
    + Here we are looping through each month and useing those months to create archive objects that to create a link that we will render to the user. 

3. `function aR_buildArchiveQuery(month, c_date)`
    + This is a very long winded function and can most likely be boken out and put into another function.  What it does is take a month and another date to find out how many days in the month and also what year it is. This function accounts for leap year up to the year 3000 then whoever still uses this is going to have a problem (I hope some of you found that funny). Towards the end of this function we have to convert the dates that represent the first and last day of said month into ISO dates and slap them in a query to get all posts between two dates.

4. `function aR_renderArchiveData(aR_objs)`
    + After collecting data that has been processed in other functions the months rendered in links that will be put into the `archive_wp.htm` webpart.

### Archive Main Post
`archive_main_post.js`

After gathering every month associated with a year we are going to get all of the posts for a single month. We then slice and dice the data for each post to render a beautiful thumbnail to the user with links to the individual pages of those posts.

#### AMP Functions

1. `function pM_getPostByMonth(startDate, endDate, month)`
    + Here we are checking if the month that we are looking for is the current month and if it is we are going ot go ahead and change it to an ISO date. I can't really remember why we do that but there is a reason. After that we send a query to the api asking for all of the posts in the month from the start date to the end date.


2. `function pM_proccessPosts(postObjs)`
    + Once we grab all of the posts in a specific date we are going to slice and dice the data and put it into pretty thumbnails thanks to the help form several functions in the `controller_services.js`. We finish this controller up by rendering the data to the DOM.

### Archive Main
`controller_archive_main.js`

The object of this controller is to supply the archive page with all the months and years associated with the blog. 

#### ACM Functions 

1. `function aM_getAllMonths(firstPost)`
    + Right off the bat we are sending an api call that calls back on success to this function. The api call gets the very first post in the blog and first thing we will do with it is do some parsing of the data. We are looking to find the first stating date of the blog and the ending date. In most cases the the blog has not ended so we are actually useing the current date as the end date. (**Note:** One thing that can be improved upon here is the fact that we grab every month even though there may not be a post stored for that month. If we could filter the data after we get it to find out what months we need with out looking at all of the posts that would be awesome).

2. `function aM_findMonths(startDate, endDate)`
    + After getting all of the associated months we are going to use a ton of logic to send years and their months to an array of years. There are a variety of loops and logic associated with this function. It is quite exstensive. Sorry about that.

3. `aM_proccessAllMonths(years)`
    + Here we are taking the array that we created and formating it to look presentable. 

### Categories Main
`controller_categories_main.js`

Much like the `controller_archive_main.js` we are organizing all of the categories on the site and building markup links for the posts associated with them. This controller is a little bit of a bone yard... sorry for all the comments and confusing code. Will do my best to explain.

#### CM Functions

1. `function cM_getCategoryTitles()`
    + Here we are getting all cutting edge with a jQuery promise. To be honest it isn't all that impressive. Bassically instead of useing a callback like we have in everyother function in this program we use a promise that on completion of the promise will send all of the category data to our next function. By now apiInteract should be familiar if you have questions about it check out **SC Core Functions** above.

2. `function cM_processCatTitles(categories)`
    + Taking the category data collected by the rest call in the previous function we we are collecting both the category titl and category id to form a category pair that we will then push to a global array that will have one of these pairs for each category. Originally we were going to grab the three most recent posts per category but then decided against it because it would require a little bit too mucha nd was not needed. That being said the rest request in this function and the next function `cM_renderCatPosts()` are now totallly irrelevant.

2. **Note: Chopping Block, do not use!** `cM_renderCatPosts()`
    + Relying on some global variables here we build out and render the html needed to display each post related to the selected category Item. Check for more comments related to this function in the code.

3. `function pC_getPostByCategory(catId, catTitle)`
    + Even though the name of this function is pretty self explanatory for more information consult the in code comments. 

4. `function pC_processPosts(postObjs)`
    + Here we are working on creating the markup for the `posts_by_category.html` webpart. That means that our code is taking all of the related posts fromt he rest request and processing them so that they look like stylish thumbnails on the page.

### Categories 
`controller_categories.js`

This controller preforms two simple tasks for the categories webpart. First, get the data for each category from the api. Second, dynamically create html that wrap around the data that the api supplied and render links to the browser.

#### CT Functions

1. `function cT_getCategoryData(url)`
    + Sends a rest request to the SharePoint API to retrieve all categories that exist on the site.

2. `function cT_renderCategoryData(catObjs)`
    + here we take the objects taht were returned to us from the api. We loop through them and create a dynamic html wrapper around them that allows them to become clickable links. Then we concatenate them to the string that is inserted inside of an html container on the page.

### Comments
`controller_comments.js`

The inate quality of this controller is that it is only interacted with and does not interact initialy with anything. Comments are allways collected by the `controller_services.js` to pass onto the `controller_posts.js`. 

#### COM Functions

1. `function cN_getSinglePostcomments(postid)`
    + Once this function is given a post id it will use the sharepoint api to retrieve the all necissary data from the API. It then calls back to the next function for processing.

2. `function processComments(cmtObjs)`
    + here we loop through each comment use a few external function calls to bake our data into beautifully formated comments. Then we render them in the DOM.

3. `function populatecomments(name, title, body, date, imgPath, delveLink)`
    + Now that we have all the data we need all that is left is to put each piece into the right place. **Note:** keep in mind to that the blog you are creating may not have the same formatting as FHD Daily Connect. That is why I sugest you change the markup and add CSS classes that you have created.

4. `function postCommentToBlog()`
    + First we grabe everything inside of the textarea for our comment. Then we send it on to the next lucky function in the `controller_services.js`.
    
### Home
`controller_home.js`

Much like many of the other controllers what we accomplish here is simply pulling data from the API and rendering it how we want to.

#### Home Functions

1. `function hM_getPostData(url)`
    + Based on what day it is we will get the top 3 most recent posts from the api and send them on to be rendered.

2. `function hM_renderPostData(postobjs)`
    + Here we are getting the data we need about each post that we will be using. We are then processing that data so that it will render how we want it to to the user and then we render it on the page.

### Posts
`controller_posts.js`

This controller rivals that of the `controller_services.js` in depth and number of functions. It accomplishes a lot but may require a lot of optimizaiton if you are up for it.

#### Post Functions

1. `function sT_getSinglePost(postId)`
    + **Arguments:** When a user clicks on a thumbnail or link related to a post it sends the Id of the post to the url and then changes to the view that runs this script. Using the query string we pluck the postId from the url and then call the SharePoint Rest API for the data related to this ID in the posts list.
    + Once we have fired that Rest call we ask the `controller_services.js` for all of the comments from the `controller_comments.js`.
    + At the end of this function we populate two global variables with strings that contain the postId in them. We will use these strings in the `incrementLike` function in this controller.

2. `function processPost(postObject)`
    + **Arguments:** A single object is required for this function to be fired. Most of the data we will use in the web part will come from this object. The object is returned from the SharePoint Rest API when we query it for a post ID.
    + We begin this function by takeing everything we need from the object and sperating the values into individual variables (This may be the long handed way to do it but I keep it for simplicities sake). What is unique about this portion of the function is that we are grabing more than just what is in the object. We are also gathering information for Delve from the Browser about the author. For example 
        ```javascript
        aUrl   = _spPageContextInfo.webServerRelativeUrl,
        aImg   = aUrl + "/_layouts/15/userphoto.aspx?accountname=" + aEMail,
        aDelve = "https://nam.delve.office.com/?p=" + aEMail + "&v=work",
        ``` 
    + Once we get the postObject we are going to glean everything we need from it and then we are going to call the likes function to listen for likes. After this we are going to process our post and then select the locations we are going to deposit the data. Then we are going to go ahead an plop all the data into the webpart.


3. `function incrementLike(addLikeUrl, checkLikeUrl, numlikes)`
    + **Arguments:** When we call this function we are passing two strings and a integer.The two strings are `addLikeUrl` and  `checkLikeUrl` both of these arguments are global variables that exist in the services controller. However, both strings are populated at the end of `sT_apiUrl`. Both strings are SharePoint API Rest requests that will either add a like to a post or get the number of likes a post has. `numlikes` is an integer value of the number of likes the post has.
    + `function processIncrement(likevalue)`
        + This function is used to build to update the new number of likes a post has. It depends one the `likevalue` argument which is calculated before this function is called. 
    + Next we check whether the user that is logged in viewing the blog post has already liked it or not. If they have already liked it then the "Like" button should be an "Unlike" button and when they click it this function will deduct exactly one like from the total and remove the users id from the posts' `LikedByStringId.results` array.
    + Upon discovering that the user id is not located inside of the `LikedByStringId.results` array the user will see the "Like" button and when they click the button it will add exactly one to the total number of likes and add the users' id to the `LikedByStringId.results` array.
    + To finish this off this last snippet of code is finding the location with which to update the like value number so that the user will awknowledge a change when the "like" or "unlike" value is clicked.
```javascript 
numLikes[0] = likeValue;
var likeLocal = document.getElementById("js-post--likes");
likeLocal.innerHTML =  likeValue;
```
4. `function checkLikes()`
    + Here we are checking whether or not the user has liked this post by looping through the `LikedByStringId.results` array of the current post. If it so happens that thier ID is found then this function will change the "Like" button into an "Unlike" button. This function has no other purpose.

### Recent
`controller_recent.js`

Similar to other controllers all we need to do is send the request and then formate the returned data.

#### Recent Functions

1. `function rP_getPostData(url)`
    + First off lets grab the data we need from the api and determine what the current date is in order to grabe the most recent posts.

2. `rP_renderPostData(postObjs)`
    + Lets take what we need from the data we have recieved from the last function and format it accourdingly.
