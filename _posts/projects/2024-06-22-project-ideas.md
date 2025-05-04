---
title: Project Ideas
excerpt: If only I have enough energy and time to comple these ideas...
date: 2024-06-22
categories:
  - projects
tags:
  - Blog
---
Some project ideas to implement when I have free time(Will it actually happen?).

#### AI Based Obsidian Document Search
It would be very convenient if I can find what I wrote before with query like below.

"I think I have written a code for checking how much space a specific file type takes. What was it?"
Response : "It is likely to be this document, 'Print disk usage with respect to its extension' "

In this example, I didnt use words like 'disk', 'usage' or 'extension' in the query. But as the AI can read the context, it can find the most similar context and return it.

I saw that indexing module in langchain can provide this kind of function so I would definitely try it soon

#### Read and Split the Bill
There are some occasions where one buy things on behalf of others and collect money from them. On such occasion, this app can be helpful.
First, read the recipt using OCR and LLM api and identify items bought. Then, send the information to server and make a url where the bought items are shown.
There are two categories on the page. One is "Bought Items" and the other is "My Items".
The items on page are moveable and one can drag and drop items from "Bought Items" to "My Items".
If one moved all items one bought to "My Items", the page calculate the amount of money he need to pay.
The program doesn't only support two users, but also support multiple users, which mean even if there are some people who paid and some people who didn't pay, it can automatically calculate who need to pay to whom with the minimum number of transactions.

#### English Writing Teacher for Obsidian Posting
I just realized that it is quite hard to write a concise English sentence that communicate my intention. I want AI to read my sentence and suggest better expression in real-time.



### Already Implemented Ideas
#### Auto Schedule Register
It is a slight hassle that I need to register schedule manually every time I read email about meeting schedule. Auto Schedule Register reads selected texts and convert it to formated information. And then call a google calendar api to register the schedule.
