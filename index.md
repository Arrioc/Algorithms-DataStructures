## Sections
[Introduction](https://arrioc.github.io/Introduction/) | [Software Design & Engineering](https://arrioc.github.io/Software-Design/) | [Database Enhancement](https://arrioc.github.io/Portfolio-Database/)

# Algorithms and Data Structures
* ## Knowledge Acquisition
  * This artifact showcases a diverse collection of skills and abilities in using algorithms and data structures to perform useful database manipulations that meet the client’s needs. The artifact makes use of dictionaries, arrays, methods, functions, calls, conditional branches, expressions, statements, returns, formatting, dot-notation, and input/output using a combination of the MongoDB, Pymongo and Bottle languages and commands. The API modules show that I understand how to create and test uniform resource identifiers (URI's) and locators (URL’s), and implement the use of a basic RESTful web service using a language-specific web service framework like Bottle to rout paths and perform connections.  

  * The “create” programs utilize dictionary data structures internally, but also externally by using the bottle framework for a web service. This design choice better synchronizes with MongoDB’s dictionary-based storage design because dictionaries utilize mapping that allows indices of (almost) any type (Downey, 2015, p. 103). Python dictionaries also use hashtables which search in constant time and are high performance in that they cut down search time considerably (Downey, 2015, pp. 205-207). Also present in the “APIstockReport” is the use of an array for a data structure, returning multiple documents to the user. The module “stocks_read2” utilizes a reverse lookup by finding existing industries (a value) and returning their dictionary key/values for tickers in an array (Downey, 2015, p. 106). 

  * Modules like “stocks_read” utilize methods, functions, and branches with and without conditional statements to solve a problem. These modules make use of a “main” method which take the input from a user and then use statements that format input (and possible projections) to conduct a search of the database using a query. After checking for formatting errors or whether the query exists, the main method calls an execution function in the module. This function carries out the search or conducts calculations (in the case of “stocks_read1”) using parameters. These modules often use MongoDB dot-notation shell methods like “.count()” to check for a criteria’s existence (using conditional branches) and then prints data from the execution function. The pointer then returns to main, and the program exits. 

  * Modules that “update” and “delete” are much the same in structure as the “read” modules but differ in that their main methods take returns from called functions and perform condition checks using Pymongo’s dot-notation. I find notation like “.raw_result” very handy because it can tell me if the changes made were really carried out on the database, and this might contradict my print statements which helps to point out flaws in the code. 

  * Some modules that search the database do a bit more than simply output documents. These use MongoDB commands to apply functionality. For example, the module “stocks_read1” performs calculations on all “50-Day Simple Moving Average” values, and “stocks_read2” will match all values for the key “industry”. Functions, such as the “APIindustryReport” and “stocks_read3” modules, provide a valuable service to the business through aggregation which allows them to analyze and crunch data in interesting ways (Chodorow, 2013, p. 127). These modules use MongoDB’s ability to calculate, match and project data combined with the ability to aggregate data (group, sort, skip, and limit data) to present the data in a more useful way (Chodorow, 2013, p. 127).

* ## Developing Solutions
  * I met the planned objectives for adding to the complexity of the artifact by creating modules whose algorithms allow for field deletion, internal document retrieval and menu execution. The artifact now has web-service and internal modules that let a user delete a field (a key and value) in a document by having the user specify a ticker for querying and a key for deletion using MongoDB’s “$unset” and “update_one”.  The artifact now has an internal module that implements a simple “read” algorithm which queries and presents documents to the user. Finally, the artifact now has a menu which utilizes “while” loops to present the user with choices between internal or web-based services (or to exit), and then presents a list of options to choose from for each service. Internal modules are executed using import lines, while the API modules uses “os.system” program calls. If the user enters invalid input, they will get a “try again” response.

  * ## Algorithm for Internal Document Retrieval:
  * ```python
    import json
    from bson import json_util
    from pymongo import MongoClient, errors
    from bson.json_util import dumps, loads

    connection = MongoClient('localhost', 27017)
    db = connection['market']                             
    collection = db['stocks']                     

    # This method executes the query and reports on 
    # whether no file was found, otherwise it outputs 
    # the document requested or reports any error.

    #read function
    def read_document(document):
      try:
        myReadResult = collection.find(document)
        #if specific query exists
        if (myReadResult.count() != 0):
          #convert to json and print
          print(dumps(myReadResult, indent=4, default=json_util.default)) 
        #if result found 0 matching files  
        elif (myReadResult.count() == 0):
          #return error message
          print("No File Found With That Criteria.")
        return
      except errors.PyMongoError as pm:
        print("MongoDB returned error message", pm)
        abort(400, str(pm))
        return

    # This method takes from the user a ticker 
    # string, preformats it, and then sends it to
    # the 'read_document' query function.

    def read_main():
      print('Please enter capitolized ticker name')
      #store variable for query                           
      try:
        newQuery = (raw_input())                          
        #format user input                                
        myQuery = loads("{\"Ticker\" : \""+newQuery+"\"}")

      #return error if badly formatted data  
      except ValueError:
        print("ValueError: wrongly formatted doc!")
        return "Error occured"      
      except TypeError:
        print("ValueError: wrongly formatted doc!")
        return "Error occured"

      #send to read funtion
      myReadResult = read_document(myQuery)

    read_main()
    ```
   * Internal Document Functionality for a Query: 
   * ![stocks_read, terminal, find doc](https://user-images.githubusercontent.com/73560858/122109109-016f1d80-cdeb-11eb-86a7-db911f958d20.png)
    
   * Internal Document Functionality for a Nonexistent Document:
   * ![stocks_read, terminal, no doc found](https://user-images.githubusercontent.com/73560858/122109266-29f71780-cdeb-11eb-9e52-79359e63caf3.png)
    
  * ## Algorithm for Internal Field Deletion:
  * ```python
    import json
    from bson import json_util
    from bson.json_util import dumps, loads
    from pymongo import MongoClient, errors

    connection = MongoClient('localhost', 27017)
    db = connection['market']
    collection = db['stocks']

    # This method queries using the query parameter and executes deletion
    # on the field using the field parameter, then returns results to main.

    def delete_field(query, newFdel):
      try:
        myDeleteResult = collection.update_one(query, newFdel)
        return myDeleteResult
      except PyMongoError as pm:
        print("MongoDB returned error message", pm)
        abort(400, str(pm))
        return

    # This method takes user ticker input, and checks if it exists.  
    # If the ticker doesnt exist its reported and the program quits.
    # If the ticker exists a field name is requested, stored and 
    # formatted before being sent to the 'delete_field' funtion, 
    # and then reports on the results sent back from the funtion.

    def del_field_main():

      #request ticker data for query
      print('Please enter capitolized Ticker for the document to be modified.')

      #store variable for query
      newQuery = (raw_input()) 

      #format user input                                
      myQuery = loads("{\"Ticker\" : \""+newQuery+"\"}")

      #if query found no such ticker, report
      if (collection.find_one(myQuery) == None):
        print("Document not found.")

      #else continue...
      else:                                                              
        print('Please enter field to be removed with initial letter capitolized')

        #store field for deletion
        key = (raw_input())
        #format & load json for given field     
        deleteForm = "{\""+key+"\" : \"\"}"  
        removeField = loads(deleteForm)

        #store unset field to be removed 
        newFieldDel = {"$unset" : removeField}

        #send query & unset field to 'delete_field' function
        myDeleteResult = delete_field(myQuery, newFieldDel)


        #if modify count is 1
        if (myDeleteResult.modified_count == 1):
          #show confirmation results
          print(dumps(myDeleteResult.raw_result))
          print("Document field has been removed.")
        else:
          #show non-confirmation results
          print(dumps(myDeleteResult.raw_result))
          print("Document's field could not be found and may have already been removed.")

    del_field_main()   
    ```
   * Internal Field Deletion, File Removed:
   * ![stocks_deleteField, terminal, field removed](https://user-images.githubusercontent.com/73560858/122109889-df29cf80-cdeb-11eb-9336-6353dd4af5a7.png)
    
   * Internal Field Deletion, File Already Removed:
   * ![stocks_deleteField, terminal, field already removed](https://user-images.githubusercontent.com/73560858/122110040-126c5e80-cdec-11eb-8722-4e4a9ee87914.png)
   
   * Internal Field Deletion, File Not Found:
   * ![stocks_deleteField, terminal, ticker not found](https://user-images.githubusercontent.com/73560858/122110184-3fb90c80-cdec-11eb-8534-969d013e449d.png)
 
   * Internal Field Deletion, Database Before & After:
   * ![internap del field database proof](https://user-images.githubusercontent.com/73560858/122121204-5ade4900-cdf9-11eb-97a3-41087a4bf785.png)
  
  * ## Algorithm For API Field Deletion:
  * ```python
    import json
    from bson import json_util
    from pymongo import MongoClient, errors
    from bson.json_util import dumps
    from bottle import get, put, route, run, request, abort

    connection = MongoClient('localhost', 27017)
    db = connection['market']
    collection = db['stocks']

    # This method queries using the query parameter and executes deletion
    # on the field using the field parameter, then returns results to main.

    #delete field function
    def APIdelete_field(query, newFdel):
      try:
        myDeleteResult = collection.update_one(query, newFdel)
        return myDeleteResult
      except PyMongoError as pm:
        print("MongoDB returned error message", pm)
        abort(400, str(pm))
        return

    # This method takes the ticker from the CURL, and checks its existance.  
    # If the ticker doesnt exist its reported and the program quits.
    # If the ticker exists the field name is requested & stored in an 
    # "unset" statement before being sent to the 'delete_field' funtion. 
    # The method then reports on the results sent back from the funtion.

    #stocks_del_field:
    @put('/stocks/api/v1.0/delField')
    def main_del_field_API():

      #get ticker, store as json variable    
      ticker = request.params.get('ticker')
      myQuery = {"Ticker" : ticker}

      #if query found no such ticker, report
      if (collection.find_one(myQuery) == None):
        print("Document not found.")

      #else continue...
      else:                                                              
        #get key & store          
        key = request.json["Key"]

        #store unset field to be removed 
        newFieldDel = {"$unset" : key}

        #send query & unset field to 'delete_field' function
        myDeleteResult = APIdelete_field(myQuery, newFieldDel)

        #if modify count is 1
        if (myDeleteResult.modified_count == 1):
          #show confirmation results
          print(dumps(myDeleteResult.raw_result))
          print("Document field had been removed.")
        else:
          #show non-confirmation results
          print(dumps(myDeleteResult.raw_result))
          print("Document's field could not be found and may have already been removed.")

    if __name__ == '__main__':
        run(host='localhost', port=8080, debug=True) 

    ```
    
   * API Field Deletion, Client-Side URLS:
   * ![terminal, client, APIdeleteField, proof of KM field, 2 removal curls, 1 not found curl, cutdown](https://user-images.githubusercontent.com/73560858/122110926-1f3d8200-cded-11eb-8c9a-4d43fef41886.jpg)
   
   * API Field Deletion; File Removed, File Already Removed, File Not Found:
   * ![terminal, server, APIdeletefield, field removed, field already removed, field not found](https://user-images.githubusercontent.com/73560858/122110819-f9b07880-cdec-11eb-83b1-76ffb026aee9.png)
   
  * ## Algorithm For The Menu:
  * ```python
    import os

    # This method provides an initial menu that 
    # allows the user to chose between internal 
    # and web based services.

    def main_menu():
      reply = True
      print('Welcome to Kilbourne Financial\'s Stock Market Services. \nPlease select from the following:')
      #let user chooses from two types of services
      while reply:
        print("""
        1 Internal services
        2 Web services
        3 Exit """)

        reply = raw_input()

        if (reply == '1'):
          print("You chose internal services")
          internal()
        elif (reply == '2'):
          print("You chose web services")
          webService()
        elif (reply == '3'):
          print('Goodbye, have a great day!') 
          reply = False
        else:
          print('Does not compute! Try agian.')

    # This method provides services through the internal service
    # by taking a selection from the user and then executing 
    # the chosen module. When finished we return to the main menu

    def internal():
      select = True
      print('Welcome to internal services. \nPlease choose from the following:')
      #let user chose from different internal services
      while select:
        print("""
        1 Create a new stock document
        2 Update a document
        3 Delete a document
        4 Delete a document's field
        5 Find a document
        6 Report on 50-Day Simple Moving Average
        7 Get an industry's ticker symbol list
        8 Report on a sector's total oustanding shares
        9 Exit to main menu
        """)

        select = raw_input()

        if (select == '1'):
          print("You chose create a stock document")
          from stocks_create import create_main
        elif (select == '2'):
          print("You chose update document")
          from stocks_update import modify_main
        elif (select == '3'):
          print("You chose delete doument")
          from stocks_delete import delete_main
        elif (select == '4'):
          print("You chose delete document field")
          from stocks_deleteField import del_field_main
        elif (select == '5'):
          print("You chose find document")
          from stocks_read import read_main
        elif (select == '6'):
          print("You chose report 50-Day SMA")
          from stocks_read1 import read1_main
        elif (select == '7'):
          print("You chose Get industry ticker list")
          from stocks_read2 import read2_main
        elif (select == '8'):
          print("You chose report on a sector's total oustanding shares")
          from stocks_read3 import read3_main
        elif (select == '9'):
          print('Exiting') 
          select = False
        else:
          print('Does not compute! Try again')

    # This method provides services through the web service
    # by taking a selection from the user and then executing 
    # the chosen module

    def webService():

      select = True
      print('Welcome to internal services. \nPlease choose from the following:')
      #let user chose from different internal services
      while select:
        print("""
        1 Create a new stock document
        2 Update a document
        3 Delete a document
        4 Delete a document's field
        5 Find a document
        6 Find multiple documents
        7 Create industry report
        8 Exit to main menu
        """)

        select = raw_input()

        if (select == '1'):
          print("You chose create a stock document")
          os.system("python APIcreate.py")
        elif (select == '2'):
          print("You chose update document")
          os.system("python APIupdate.py")
        elif (select == '3'):
          print("You chose delete doument")
          os.system("python APIdelete.py")
        elif (select == '4'):
          print("You chose delete document field")
          os.system("python APIdeleteField.py")
        elif (select == '5'):
          print("You chose find document")
          os.system("python APIread.py")
        elif (select == '6'):
          print("You chose find multiple documents")
          os.system("python APIstockReport.py")
        elif (select == '7'):
          print("You chose create industry report")
          os.system("python APIindustryReport.py")
        elif (select == '8'):
          print('Exiting') 
          select = False
        else:
          print('Does not compute! Try agian.')


    main_menu()
    ```
    
   * Menu Initiation, Internal Menu Selection, and Exiting:
   * ![initial menu, internal selection, item selection2](https://user-images.githubusercontent.com/73560858/122112450-dab2e600-cdee-11eb-9c90-1fe75e5a7e4d.png)
   
   * Menu Initiation, Web-Service Menu Selection, and Exiting:
   * ![menu, web service selection and exiting](https://user-images.githubusercontent.com/73560858/122112685-19e13700-cdef-11eb-912f-c18b42c79341.png)
   
* ## Reflecting on the Process
  * The read module was simple because I had done this sort of things many times before. The module that deletes fields was a new challenge. It helped to study my "delete" and "update" module’s code. Browsing through what “delete_one” can do in the online MongoDB documentation, I saw that this only applied to whole objects like documents. This reassured me that what I wanted to do was modify the document using “update_one”. I refreshed myself on MongoDB’s “$set” which resides in my update module. It updates existing keys or creates new ones. Realizing that I want the opposite of this, I began browsing through my MongoDB book and rediscovered “$unset” which does the opposite of “$set”. The end-product is a marriage between my “delete” and “update” module.

  * My biggest challenge for the menu was to figure out how to execute my modules from inside the menu. After much searching, I decided to ask for help. A tutor on Slack Chat hinted that one way I can execute my modules is by importing them but did not say how (they never do). I tried importing a module at the top of my code and I noticed that it would run my imported module before executing my menu. After that, I knew what to do: put the import lines with the menu options. The API modules are different, they can’t be started using the imports. After a bit of searching, I learned from a help site on Unix\Linux called NixCraft that I could import the “os” and use “os.system(“command”)” to execute my API’s (Vivec, 2013). I added these lines to my menu options for each API module. I also learned through trial and error that my internal Python files need to be in the directory with the REST API to have the menu fully functional.  
