# Historical Data Management

Author: Loe

## Naive management 

The task os storing HTML and non-HTML using database is more than throw all the files into the database, and call it a day. However, despite me aware so, I do not under understand the proper way of designing the schema. Consequently, I decided to adapted the even older schema used in json that date back to the first demo version. It is not a terrible design. Here is how it is laid out.

The goal of the database schema is to, first, serve the processor and web app with the latest HTML content. Then, the schema should also be able to hold the historical site data. Thus, the database schema is construct similar to a spreadsheet. The column is the website name; the row is the timestamp; and the cross section is the actual site info on a given date. Essentially, in database, I have three tables. The first one holds all the site. The second one holds all the timestamp. And, the third one, using the first, and the second table's content as key, hold the site snapshot on a given timestamp. It does not sound too bad, until I realize that in the real life, some site will appear and disappear randomly for all kinds of reasons, like the site is down, or the network jittering causing the scraper failing to retrieving the site information from the Internet. Consequently, if we image our database schema as the spreadsheet, such missing values are the holes in the table. It is very not desirable, because it is a pain to maintain the inconsistent table with holes in. As a result, in the loading part of the code, I need to cover a lot of corner cases to keep the data integrity. It is not fun, because no one wants to spend ours using cli trying to fix a hole in the database.

## Improved management