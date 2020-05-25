# RESTAPI
This repository is the part of the API Series and explains the RESTful API Concepts with its minimal code.


Below is the Article : 

RESTful API- What it is?

Previously in API series, reader was introduced with basics of Application Programming Interface (API), in this article; we shall look into the RESTful Architectural style of web-services development. 

    1. Introduction
As we know, REST stands for Representational State Transfer is an architectural style (not a standard), was conceived by Roy Fielding as a resource-oriented, lightweight style. The term, resource-oriented and lightweight shall be discussed shortly. In REST style, HTTP protocol is used as a base for data transfer between a client and server because of ubiquitous use of HTTP in World Wide Web. 
To understand REST architectural style, it is imperative to understand few terms such as resource, json and representation before we move forward. A resource is something which is requested by a client from a server to retrieve its content, or to delete something or to update it. Hence, a resource can be such as a product in an online store, a book of a library, some entity in an organization. JSON stands for java script object notation, which is an open standard for data interchange format. Example of JSON representation of an employee object is as given below.
{
	‘employeeName’:’Arun Kumar’,
	‘empCode’: ‘12345’,
	‘department’: ‘Design’,
	‘designation’: ‘senior manager’
}
 When we talk about representation, we mean the way of expressing a resource in light weight way at a timestamp. When a request is made to the server for a resource, server does not respond with the resource itself, but it sends the representation of the resource. For example, an API call is made at hypothetical <hostname>/PAN URI with GET request having a hypothetical PAN, LIMCA9999L, as parameter the server may respond with a representation of that resource (i.e. LIMCA9999L) in JSON format as shown below
{
‘PAN’:’ LIMCA9999L’,
‘PAN_NAME’:’ABC PVT LTD’,
‘DOI’:’01-01-1970’,
‘ADDRESS’:’ABC Bhawan, Annexure-A, Delhi’
}  

    2. REST constraints
REST architectural style puts certain restriction on web-services to make them RESTful services. There are six (06) constraints out of which 05 are mandatory to make a web-services RESTful. These constraints are Client-server, Stateless, Cacheable, Uniform Interface, Layered System, Code on Demand (Optional)

        2.1. Client-Server: A system must use client server architecture to separate user-interface from the database/processing system. This can provide better portability of user-interface across multiple platforms such as mobile devices, tablets, PC etc.
        2.2. Stateless: A request from client to server must contain all the necessary information required by server to process it without maintaining is state.
        2.3. Cacheable: It says that the response provided by the server must include the ability of response as cache/non-cacheable.
        2.4. Uniform Interface: Must provide as uniform interface to access resources on the server
        2.5. Layered System: System must be designed in a hierarchical way  by putting constraints on the layers of proper isolation.
        2.6. Code on Demand- It is the only constraint which is optional in nature. It says that client can be provided functionality to download the executable code in the form of applet and script. 


    3. HTTP Operations/Methods
Since, REST architectural style, relies on HTTP operations, it is crucial to understand it before we dive into understanding REST. Some of the HTTP operations which are also known as resource methods are listed as below. We shall be using a hypothetical PAN interface API for understanding these methods.
HTTP Method
Description
Example
GET
Used to retrieve resource representation/information only. It does not change anything, and it is idempotent i.e. similar request shall produce similar result.
http://hostname/PANS    -returns all PAN
http://hostname/PANS/LIMCA9999L –return specific PAN

POST
Post method creates a new resource instance as the server. Post method is not Idempotent like GET i.e. two identical POST requests will create two resources with similar values.
http://hostname/PANS (JSON of PAN in body of Message) –creates a PAN

PUT
PUT HTTP method updates the existing resource. However, if the resource does not exist, API may create a new resource.
http://hostname/PANS (JSON of PAN in body of Message) –Updates/Creates a PAN

DELETE
It deletes the existing resource.
http://hostname/PANS/LIMCA9999L –deletes specific PAN


It is important to note here that all the above operations are performed on a single URI (http://hostname/PANS) where operations are identified by methods used in HTTP requests. This is the beauty of RESTful API development.


    4. Designing a RESTful API
In this section we will try to conceptualize a RESTful API for PAN interaction as discussed above. This Hypothetical API shall assume PAN as a resource around which we will design our URLs for RESTful Services. We may call it as PANInterface. We shall be using Python with Flask microframework as our main programming language to develop this RESTful API. It is assumed that reader have some basic understanding of python language and can install it on their machine for actual development. Installation of python and basic course on python is beyond the scope of this article and reader is requested to use internet for understanding the python language.
        4.1. Identification of Key Resource
It is the most important part in design of RESTful API. In our hypothetical PANInterface API, key resource shall be a PAN, which is permanent account number. We are using PANInterface as an example keeping an eye on the intended readers.
        4.2. Model URI’s
We can have following model URIs for our PANInterface API.
    • HTTP GET <hostname>/PAN/<pan>  : Get details of PAN with GET http method
    • HTTP POST <hostname>/PAN (JSON in body of message) : Create a new PAN with  POST method
    • HTTP PUT<hostname>/PAN (JSON in body of message) : Update a new PAN with PUT method
    • HTTP DELETE<hostname>/PAN/<pan>  : Delete a PAN with DELETE http method

        4.3. Python Code
    1. from flask import Flask, jsonify, abort, request  
    2. from random import randrange  
    3. app=Flask(__name__)  
    4. 
    5. #we shall be using a volatile PAN DB as list containing dictionary items as PAN  
    6. pans_db=[  
    7.     {  
    8.         'pan':'ATYPR3434L',  
    9.         'name':'ABC kumar',  
    10.         'dob':'09-09-2020',  
    11.         'mobile':'898989',  
    12.         'email':'abc@gmail.com'  
    13.     },  
    14.     {  
    15.         'pan':'LIMCA9999K',  
    16.         'name':'XYZ Smith',  
    17.         'dob':'01-01-1990',  
    18.         'mobile':'8989898989',  
    19.         'email':'abc@yahoo.com'  
    20.     }  
    21.   
    22. ]  
    23.   
    24.   #welcome page  
    25. @app.route('/')     
    26. def index():  
    27.     return "welcome to PAN Interface API"  
    28.   
    29. #Model URI to get info about ALL  PANs  
    30. @app.route('/pans',methods=['GET'])  
    31. def get_all_pan():  
    32.     return jsonify({'pans':pans_db})  
    33.   
    34. #Model URI to get info about a particular PAN  
    35. @app.route('/pans/<string:searched_pan>',methods=['GET'])  
    36. def get_pan(searched_pan):  
    37.     pan = [pan for pan in pans_db if pan['pan'] == searched_pan]  
    38.     if len(pan) == 0:  
    39.         abort(404)  
    40.     return jsonify({'pan': pan[0]})  
    41.   
    42. #Model URI to create a PAN instance  
    43. @app.route('/pans',methods=['POST'])  
    44. def create_pan():  
    45.     if not request.json or not 'name' in request.json:  
    46.         abort(400)  
    47.       
    48.     new_pan={  
    49.         'pan':randrange(10000,99999999,2),  
    50.         'name':request.json['name'],  
    51.         'dob':request.json['dob'],  
    52.         'mobile':request.json['mobile'],  
    53.         'email':request.json['email'],  
    54.     }  
    55.     pans_db.append(new_pan)  
    56.     return jsonify({'new_pan':new_pan}), 201  
    57.   
    58. #Model URI to update a PAN instance  
    59. @app.route("/pans/<string:update_pan>", methods=['PUT'])  
    60. def update_pan(update_pan):  
    61.     pan = [pan for pan in pans_db if pan['pan'] == update_pan]  
    62.   
    63.     pan[0]['name']=request.json.get('name', pan[0]['name'])  
    64.     pan[0]['dob']=request.json.get('dob', pan[0]['dob'])  
    65.     pan[0]['mobile']=request.json.get('mobile', pan[0]['mobile'])  
    66.     pan[0]['email']=request.json.get('email', pan[0]['email'])  
    67.   
    68.     return jsonify({'updated_pan':pan[0]})  
    69.   
    70. #Model URI to delete a PAN instance  
    71. @app.route('/pans/<string:delete_pan>', methods=['DELETE'])  
    72. def delete_task(delete_pan):  
    73.     pan = [pan for pan in pans_db if pan['pan'] == delete_pan]  
    74.     if len(pan) == 0:  
    75.         abort(404)  
    76.     pans_db.remove(pan[0])  
    77.     return jsonify({'result': True})  
    78.   
    79.   
    80. if __name__=='__main__': #it runs the application  
    81.     app.run(debug=True)  
Conclusion: This ends the topic on RESTful Architectural Style. We developed a skeletal API using flask framework in python to understand it. User can interact with this API via cURL or Postman app.  It is important to note that the purpose of this article was to familiarise the reader with basics of API, hence lot of code related to validation, authentication was left intentionally. We have used a list of dictionary item as our volatile database. We shall learn about further topics related to API in subsequent articles.
