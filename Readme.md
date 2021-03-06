# Extract text from pdf 

It's a simple REST API which parses pdf files in order to extract text from it.
The text file are stocked in a database

***

### Features included 

 *  Feature 1 :  Upload pdf. Save its data into a database and return Id 's document
      * Endpoint : /documents
      * Type : POST
 *  Feature 2 : Get the metadata of an uploading file by its id  
      * Endpoint : /documents/{id}
      * Type : GET
 *  Feature 3 : Request the text of files uploaded by id
      * Endpoint : /text/{id}.txt
      * Type : Get

***
## Installation 

Code has been made with Python 3.8.10

Create a virtualenv and activate it:

```shell
python3 -m venv venv
. venv/bin/activate
```
Install Packages 

```shell
pip install -r requirements.txt
```
***
Install Rabbitmq 

```shell
docker run -d --name some-rabbit -p 5672:5672 -p 5673:5673 -p 15672:15672 rabbitmq:3-management
```
Once installed, check if the rabbitmq container is running with 

```shell 
sudo docker ps 
```

if your container is not running 

```shell
sudo docker start  {your container id}
```

***

If the container works, you have to add an user.

* Go to http://localhost:15672/. Log in with => "username" : guest , "password" : guest
* Go to the "Admin" bar at the top of the front page. Once there, add a user with "username" : username , "password" : siocbienG 
![GitHub Logo](/images/Rabbit_admin.png)
* Then 
![GitHub Logo](/images/add_user.png)
* Once you have created your user. The user will appear inside your users table . Click on the user you have created ( inside the table) and click on all "set permission" and "set topic permission" bar.
![GitHub Logo](/images/set_permissions.png) 

***
## Run 

In production 

```shell
python3 webapp.py
```
In developement 

```shell
export FLASK_APP=webapp.py
export FLASK_ENV=development
flask run
```
if flask run doesn't work make : 

```shell
python -m flask run
```
Launch celery inside an other shell.
Make sure you launch your container before 

```shell
celery -A webapp.celery worker --loglevel=info -P eventlet
```


***
## Usage

#### Feature 1

##### On browser

Open http://localhost:5000/documents in a browser to upload your pdf 
![GitHub Logo](/images/documents.png)

##### On Command 

```shell
curl -F 'file=@document.pdf' localhost:5000/documents
```
***

###### Result 

![GitHub Logo](/images/document_id.png)

{
   
    "task_id": [id]
}
  * "task_id" : id of pdf which is being uploaded 

Task is waiting for execution or unknown. Any task id that???s not known is implied to be in the pending state.
***
#### Feature 2

##### On browser

Open http://localhost:5000/documents/{id} in a browser to get the metadata of pdf with id {id} 
![GitHub Logo](/images/document_metadata.png)

##### On Command 

```shell
curl http://127.0.0.1:5000/documents/{id}
```
***
###### Result 
{    
     "id": "[id]",  
     "author": "[author]",  
     "state": "[status]",  
     "creation_date_of_file": "[date]",  
     "author": "[author]",  
     "creator": "[creator]",  
     "producer": "[producer]",  
     "subject": "[subject]",  
     "title": "[title]",  
     "number_of_pages": "[number_of_pages]",  
     "keywords": "[keywords]",  
     "title_file": "[title_file]",  
     "timestamp_uploading": "[timestamp_uploading]"  
}

 * id : id of the file (string)
 * author : Author of the pdf file. Metadata inside the file (string)  
 * status : status of the uploading. May be "SUCCESS", "PENDING" or "ERROR" (string)  
 * creation_date_of_file : Date when the file was created by the author. Metadata inside the file (string)  
 * creator : creator of the pdf. Metadata inside the file (string)
 * producer : producer of the pdf. Metadata inside the file (string)
 * subject : subject of the pdf. Metadata inside the file (string)
 * title : title of the pdf. Metadata inside the file (string)
 * number_of_pages : number of pages (string)
 * keywords : keywords of the pdf. Metadata inside the file (string)
 * title_file : title of file which has been uploaded (string)
 * timestamp_uploading : Time when the file was uploading (string)

***
#### Feature 3 

##### On browser

Open http://localhost:5000/text/{id}.txt in a browser to get the text of the pdf with id : {id}
![GitHub Logo](/images/text_id.png)

##### On Command 

```shell
curl http://127.0.0.1:5000/text/{id}.txt
```
***
###### Result 

{

     "text": "[text]"  
      
}

 * text: text of the pdf (string)

***
***
## Test

#### Launch tests 

In order to launch test , you have to launch celery before
```shell
celery -A webapp.celery worker --loglevel=info -P eventlet
```
launch test

```shell
coverage run -m pytest tests
coverage report
```
***
***
Launch pylint 

example with model
```shell
pylint model --errors-only
```