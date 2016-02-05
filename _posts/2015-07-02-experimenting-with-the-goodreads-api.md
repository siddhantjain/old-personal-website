---
layout: post
title: Experimenting with the Goodreads API
date: 2015-07-02 06:02
author: siddhantj
comments: true
category: tech
---
Goodreads.com is a website (now owned by Amazon.com) that aims to create a social network sorts for bookreaders. I recently started using the website heavily, primarily to archive the books I have been reading and also to get some good recommendations for lining up the next few books I can read. Now that I home and reading a lot  more than usual I ended up spending more than I would have liked on buying the books I want to read. Since a good number of my friends have their own tiny book collections, I was wondering if some of them already own these books that I just spent so much money on. Fortunately, goodreads has an option to mark a particular book as a book you own. However, if I have to see if any of my friends own a particular book, I will have to go to each of their profile and find it out manually. Since I was a little bored anyway, I thought of writing a small script that can go through all my friends on goodreads and compile a list of books they own. Later, if I give a book id, I should simply get names of my goodreads friends who own the book.

The script is ready and hence sharing it here.
Github link: https://github.com/siddhantjain/goodreadsownedbooks/

Currently, the script is very slow and I don't like the part where I have to input a book ID to find the owners of that book. I should ideally have a book-search feature as is there in the website it self.  The challenge is to implement a fast book search (API Calls make the whole program very slow). My plan is to harness the community of the dev forums for goodreads and get ideas on how I could go about making the script faster and to implement a fast search for books as well. Once that is set, I was thinking of making this into a web-app. I have not worked even on making an end-to-end web application and this seems like a good way to learn the ropes.
