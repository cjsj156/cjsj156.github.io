---
title: Auto Schedule Register
excerpt: One simple idea that integrates AI into
date: 2024-06-23
categories:
  - projects
tags:
  - Blog
---
## Auto Schedule Register

###### github : https://github.com/cjsj166/auto-schedule-register
###### link : https://chromewebstore.google.com/detail/fjodbpikdciljgollogaefihdnkfnile/preview?hl=ko&authuser=0

## 


"Information about arranged schedule will be provided.\n\

Do your best to describe the schedule as concisely as possible in json format.\n\

Required json format is as follow\nY:year M:month D:day h:hour m:minute s:second X:unknown\n\

You can use X instead of any other character if you cannot determine specific year, month, date ex)XXXX-02-13T15:20:00\n\

{ \"starting_date_time\": \"YYYY-MM-DDThh:mm:ss\", \"end_date_time\": \"YYYY-MM-DDThh:mm:ss\", \"title\": \"Title of the event\", \"description\": \"description of the meeting, meeting room url, address of the place, etc.\" }"

//

  

"Information about arranged schedule will be provided.\n\

Do your best to describe the schedule as concisely as possible in json format.\n\

Required json format is as follow.\n\

{ \"starting_date_time\": \"YYYY-MM-DDThh:mm:ss\", \"end_date_time\": \"YYYY-MM-DDThh:mm:ss\", \"title\": \"Title of the event\", \"description\": \"description of the meeting, meeting room url, address of the place, etc.\" }\n\

If you only know relative time duration, the json format should be as follow.\n\

{ \"starting_date_time\": \"PnYnMnDTnHnMnS\", \"end_date_time\": \"PnYnMnDTnHnMnS\", \"title\": \"Title of the event\", \"description\": \"description of the meeting, meeting room url, address of the place, etc.\" }"

//

  

"Information about arranged schedule will be provided.\n\

Do your best to describe the schedule as concisely as possible in json format.\n\

Required json format is as follow.\n\

{ \"starting_date_time\": \"PnYnMnDTnHnMnS\", \"end_date_time\": \"PnYnMnDTnHnMnS\", \"title\": \"Title of the event\", \"description\": \"description of the meeting, meeting room url, address of the place, etc.\" }\

The ISO 8601 duration format \"PnYnMnDTnHnMnS\" represents a time interval from now:\n\

P: Period start.\n\

nY: Number of years.\n\

nM: Number of months.\n\

nW: Number of weeks.\n\

nD: Number of days.\n\

T: Time start (separates date and time).\n\

nH: Number of hours.\n\

nM: Number of minutes.\n\

nS: Number of seconds.\n\

If you know the exact time, the json format should be as follow.\n\

{ \"starting_date_time\": \"YYYY-MM-DDThh:mm:ss\", \"end_date_time\": \"YYYY-MM-DDThh:mm:ss\", \"title\": \"Title of the event\", \"description\": \"description of the meeting, meeting room url, address of the place, etc.\" }"

//

  

"Information about arranged schedule will be provided.\n\

Do your best to describe the schedule as concisely as possible in json format.\n\

Required json format is as follow.\n\

{ \"starting_date_time\": \"PnYnMnDTnHnMnS\", \"end_date_time\": \"PnYnMnDTnHnMnS\", \"title\": \"Title of the event\", \"description\": \"description of the meeting, meeting room url, address of the place, etc.\" }\

The ISO 8601 duration format \"PnYnMnDTnHnMnS\" represents a time interval from now:\n\

P: Period start.\n\

nY: Number of years.\n\

nM: Number of months.\n\

nW: Number of weeks.\n\

nD: Number of days.\n\

T: Time start (separates date and time).\n\

nH: Number of hours.\n\

nM: Number of minutes.\n\

nS: Number of seconds.\n\

If you know the exact time, the json format should be as follow.\n\

{ \"starting_date_time\": \"YYYY-MM-DDThh:mm:ss\", \"end_date_time\": \"YYYY-MM-DDThh:mm:ss\", \"title\": \"Title of the event\", \"description\": \"description of the meeting, meeting room url, address of the place, etc.\" }"

//

  

  `Information about arranged schedule will be provided.\n\

Do your best to describe the schedule as concisely as possible in json format.\n\

Consider the date now : ${isoString}.\n\" + "Required json format is as follow\n\

{ \"starting_date_time\": \"YYYY-MM-DDThh:mm:ss\", \"end_date_time\": \"YYYY-MM-DDThh:mm:ss\", \"title\": \"Title of the event\", \"description\": \"description of the meeting, meeting room url, address of the place, etc.\" }`