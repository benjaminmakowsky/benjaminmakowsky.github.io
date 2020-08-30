---
layout: post
title: Connect RVR to a SQL Database and Web Server
date: 2020-08-29 15:37
category: SPARKI
author: Benjamin Makowsky
tags: [JavaScript, Python, Rvr, Sparki, MySQL, MariaDB, Raspberry Pi]
summary: Learn how to connect the RVR to a Database to Display information to a web server.
---

# Step 1: Install MariaDB 
To install MySql enter the following command after ssh'ing into the raspberry pi.
```
sudo apt install mariadb-server
```
```
sudo mysql_secure_installation
```
If you haven't set up MySql before, when it asks for a password leave it blank. Next it will ask if you want to set a password. You can probably get away with saying no as your database isn't going to be used to anything important, but I always recommend setting some sort of password. Next you should remove the anonymous users as we won't be using them and the create a security risk. Say yes when it asks if you want to dissallow root login remotely as we have no need to access root outside of the pi. Next select yes to remove the test database.

```
sudo mysql -u root -p

mysql> USE mysql;
mysql> UPDATE user SET plugin='mysql_native_password' WHERE User='root';
mysql> FLUSH PRIVILEGES;
mysql> exit;

service mysql restart
```
After all is said and done you should now have a database up and running.

# Step 2: Install Node
The next step we are going to do is install Node and JavaScript onto our pi. First, as always make sure your pi is up-to-date using the following commands:
```
sudo apt-get update
sudo apt-get upgrade
```
Next step is to install node. We are going to do this using the Node Version Manager (NVM). This will allow you to install any version of Node that you need. Begin by typing the following into you terminal:
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
nvm --version
```
Once you have confirmed you have NVM installed we can install Node using the following:
```
nvm install node
sudo apt install build-essential
```
Thats it! Now you have Node installed!
# Step 3: Install MySql Libraries
In order to access you database from both python and node we first need install the corresponding libraries. Installing these libraries is actually fairly easy, just one line of code each.
## Python
Simply type the following into your terminal:
```
pip install mysql-connector-python
```
## Node
Node is almost as easy as python you just need to switch to where you are going to work on you JavaScript server. If you have been following along create a new directroy in the Sparki folder or wherever your git repo is using the following:
```
mkdir Node 
cd Node
```
This will make a new directory in the Sparki directory where we will kepp all our javascript files separate for a cleaner workspace. Once you are inside the directory simply type:
```
npm install mysql
```
And there you have it! You now have everything installed and can begin writing some actual code.
# Step 4: Write Python Code
The first thing we are going to do is write the python portion of our project. This is because before we have JavaSvript create the server we want our database to already be created and have information to display. For now we are going to keep it simple and only store the current battery level so that we know how close our RVR is to dying. As usual we begin with the import statements:
```python
import mysql.connector #new import
import os
import sys
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '../../')))

import asyncio
from sphero_sdk import SpheroRvrAsync
from sphero_sdk import SerialAsyncDal
from sphero_sdk import BatteryVoltageStatesEnum as VoltageStates #new import
```
The main thing to note here is that there are 2 new import statements. The first one imports the sql library we just downloaded and the second allows us to read the battery level from our RVR. Next we create our global variables:
```python
loop = asyncio.get_event_loop()
rvr = SpheroRvrAsync(dal=SerialAsyncDal(loop))
DATABASE_NAME = "SPARKI" 
```
To note here is the variable _DATABASE_NAME_ which is created so that if we change the name of the database we don't have to find every occurence in our code we can just change this one line. Next we want to connect to our database manage as follows:
```python
#Connect to mysql
mydb = mysql.connector.connect(
    host="localhost",
    user="root",
    password="YOUR_PASSWORD_HERE" #CHANGE THIS PASSWORD
)
```
If you followed this guide and didn't change your username or anything else then the only thing you need to do here is use the password you created earlier. Once we have connected to our database manage we have to create the database we will be using. To do this we have to use a different syntax than python since the database has it's own style of doing things:
```python
#A cursor is just an object that lets you interact with a database, like a mouse -> computer
mycursor = mydb.cursor()
mycursor.execute("SHOW DATABASES")

#Check if the database already exists
db_exists = False
databases = mycursor.fetchall()
for db in databases: #db is a tuple with only a single element so we need to use indexing
    if (db[0] == DATABASE_NAME):
        db_exists = True

# if databse does not exist create it
if(db_exists == False):
    print("Database not found, Creating now...")
    mycursor.execute("CREATE DATABASE " + DATABASE_NAME)     
else:
    #If database exists, wipe it to start fresh
    print("Database already exists. Deleting and Recreating")
    mycursor.execute("DROP DATABASE " + DATABASE_NAME)
    mycursor.execute("CREATE DATABASE " + DATABASE_NAME)     

#Connect to the database
print("Connecting to DB: " + DATABASE_NAME)
mydb.connect(database=DATABASE_NAME)
```
Alrighty, a lot going on in this section of code so let's break it down. The first section is just creating a database cursor. Think of it like your mouse cursor, it's what allows you to move arround and select things within the databse. Next we are going to check if the database we want already exists, and if it does, we want to drop it (delete it) so that we don't have any old data. In future implementation we will keep the database and navigate it using the most recent so that we have a history, but thats a different lesson. Lastly, we connect to our newly created database.

__SIDE_NOTE:__ By now you've noticed the syntax in the strings where the words are in caps like __CREATE DATABASE__ and __SHOW_DATABASES__. This is normal across multiple types of databases and is called Structured Query Language ___SQL___ pronounced like _sequel_. Once you get familiar with it, it becomes a lot easier to navigate a database. 

Next we want to table within our databse where we can keep the information we want. In this example we are going to create a table called stats with a single cell called battery as shown:
```python
#Create a table called: stats with a cell called battery
print("Creating Table: stats with a cell: battery")
mycursor.execute("CREATE TABLE stats (battery VARCHAR(255))")
```
Here you see we create a table called stats, and then in the parenthesis we create the cell and declare what type the cell will be. In this case VARCHAR to hold a string of max 255 characters. Now we write our main function again so it looks like below:
```python
async def main():
    """ This program demonstrates how to retrieve the battery state of RVR.
    """

    await rvr.wake()

    # Give RVR time to wake up
    await asyncio.sleep(2)

    battery_percentage = await rvr.get_battery_percentage()
    print('Battery percentage: ', battery_percentage)

    ## Store battery level in database
    query = "INSERT INTO SPARKI.stats (battery) VALUES (%s)"
    values= ("Battery Level: " + str(battery_percentage.get("percentage")),)
    mycursor.execute(query, values)
    mydb.commit()

    mycursor.execute("SELECT * FROM SPARKI.stats")
    records = mycursor.fetchall() ## it returns list of tables present in the database
    for record in records:
        print(record)

    await rvr.close()
```
It is here in the main function that we get our RVR power levels and store them in the variable _battery_percentage_. The in our query (request to the database) we ask it to __INSERT__ into our database __SPARKI__ in the table __stats__ the cell __(battery)__ the value of the battery percentage. Here the __%s__ indicates that a string in to be inserted wherever this %s is. For this example, the string is the variable ___values___. An import thing to notice here is the ___battery_percentage.get("percentage")___. We use the get method with the argument percentage because the variable battery_percentge is a dict (dictionary) where the value is stored by connect to a key. Think of it like an actual dictionary where if you want the definition of a word you first have to look-up the word. In this case the definition of "percentage" is the value being stored. We then tell our cursor to execute our query and commit the change. The last section simply selects all cells in our table and prints them to the console of debugging purposes. 

The last section of our python code is the same as before: 
```python
if __name__ == '__main__':
    try:
        loop.run_until_complete(
            main()
        )

    except KeyboardInterrupt:
        print('\nProgram terminated with keyboard interrupt.')

        loop.run_until_complete(
            rvr.close()
        )

    finally:
        if loop.is_running():
            loop.close() terminated with keyboard interrupt.')

        loop.run_until_complete(
            rvr.close()
        )

    finally:
        if loop.is_running():
            loop.close()
```
# Step 5: Write JavaScript Code
# Step 6: Enjoy!