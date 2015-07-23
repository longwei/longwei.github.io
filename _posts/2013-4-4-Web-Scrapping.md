---
layout: post
title: Web Scraping
---

or How to pull data from a website without API

Note: decent website normally provide APIs for fetch data, which make thing really really easy. all you need to do, is to read the document and find the section that you interested in. then to register a key and then send along the key with every request.

but not all websites are decent, but “If it can bleed, I can kill it.” :)
as long as the website is serving html, then the content could be accessed via script. Don’t wasting your time repeatly doing thing for hours that can be done in script in few minutes.


define the data, and figure out basic access patterns This is the most important and confusing part. each website has their own unstructed data.

Here are few question to define the data

1. what is the endpoint, or where is the url that return the data.
2. what is the GET parameters?

What if the data is fetched by POST method? huh?? Yes, trusted me, it did happens. (sad)

1. find the form, get the action attribute and sending data variable name;
2. setup a localhost, print receive post request, then redirect the form action attribute to your localhost
3. then verified your post request. cUrl is your friend to spoof a http request. 4. http://curl.haxx.se/docs/httpscripting.html if tldr, here is the shortcut for paramenters –location for redirection and –data for post request data


What if the the data is fetched by ajax? capture your xhr request, then spoof it.

what if data is protected behide a login wall? find a httop library that handles logins and send.

What if the html page is unstructed? 1. find the CSS selectors. which is the class attribute of elements you interested in. this is how designer organize the webpage themself. 2. get a good html parsing library, dont use xml or dont ever try to use REGEX.


Have fun, write a script to fire off a request to the url and pars the returned data. follow up, https://gist.github.com/gjreda/f3e6875f869779ec03db

Tips on debug: don't forget the Header, it matters.

https://gist.github.com/longwei/5096906

Update:
try this https://www.kimonolabs.com/
