# DAUSAE
Data Analysis Using SQL and Excel

Data:

https://media.wiley.com/product_ancillary/3X/11190214/DOWNLOAD/9781119021438WebsiteFiles.zip
zip of a bunch of spreadsheets, some txt files, and some SQL scripts that parse the txt files and upload them to your SQL server once you get that running. 


Tools:

VSCode--https://code.visualstudio.com/ FOSS code editor. You can actually do the SQL queries from within pgAdmin4, but vscode offers a much easier way to manage the files your queries will be stored in. Much better UI for setting up complicated queries too

PostgreSQL extension by Chris Kolkman--useful explorer that provides a tab in VSCode where you can see the server/db/schema of your chosen connection

PostgreSQL extension by Microsoft--has the same name. I guess that can happen. Useful tho; associates SQL files and queries with connections, allows keyboard shortcuts to run queries. Worth noting: has a massive bug where if you run a query, and close the resulting "result" tab, it won't open a new result tab until you restart. The result is, by default, where your query response will be, so this can be a PITA. Luckily, restarting VSCode is easy.  


PostgreSQL:

Downloading and setting up PostgreSQL: 
https://www.postgresql.org/download/windows/ should have links that lead to https://www.enterprisedb.com/downloads/postgres-postgresql-downloads
download the most recent version, 15.1

Once you download and run the exe to set it up, you will want to run pgAdmin4. This is a GUI tool you can use instead of the pg shell to make sure your server is running. Typically, local servers will be run as localhost, on port 5432, with user 'postgres' and a password of your choosing. Keep that information in a password manager. Once the server is running, you can add a database.

Once you've added a database, go back in to VScode and hit Ctrl + Shift + P, or click the little elephant (PostgreSQL extension icon) on the left-hand side. In the top of the explorer pane, there should be a big plus symbol you can hit to Add Database Connection. You'll enter the connection information that was set up in pgAdmin4 earlier. Honestly, keep that password to hand. Every time the connection dies, you'll have to paste it again. 

At this point, open the file LoadPostgres.sql in VSCode. When it's open, and you right click it, you should see an option in the context menu that says "Execute Query". I'd recommend making note of that keyboard shortcut, and changing it to something way, waaay better than the default. You'll be doing that a lot, and it'll be a lot easier if it's a decent shortcut. I use Ctrl + Alt + E. 

Now it's time to add the information in the data files to your database. The script will create tables within the database, and then populate those with the TXT files in the directory. So go make edits to the script that are mentioned in the file: do a find and replace for $LOADDIR$ -> c:\users\username\so-on-and-so\forth. I put it on my desktop, so my files were C:\Users\myname\Desktop\data\Calendar.txt, for example. 

Once that is done, you're going to have to change permissions for the folder the TXT files are all in. the COPY command in the script instructs your server to go grab the file, and the server doesn't have permission for your local files, as it happens. So right click the 'data' folder and click Properties. Security -> Edit -> Add, then type "Everyone" in the box. Click "Check Names" and make sure it worked. 

Go into VSCode, hit Ctrl + Shift + P again to make sure you're connected to your database--as mentioned before, this connection drops fairly regularly

Now, hit Execute Query on the LoadPostgres.sql. It should know where the files are, postgres should have permission to access them, and the server/database should be running and empty. If everything goes well, the blue ribbon at the bottom of VSCode will say localhost : whatever_you_named_your_database : postgres at the right, as well as an "Executing Query". Lots of data are being loaded, so it might take a bit. 

Once it finishes, you can test if it worked by doing a query from the book and seeing if you got the same answer:

SELECT zc.stab, COUNT(*) as numrows

FROM Orders o JOIN

     ZipCensus zc

     ON o.zipcode = zc.zcta5 AND

     o.totalprice > zc.mediangrossrent

GROUP BY zc.stab

ORDER BY numrows DESC

Should have NY, NJ, CA at the top with 621, 157, and 120 respectively. I got 620 for New York though, and I have no clue what went wrong. 



