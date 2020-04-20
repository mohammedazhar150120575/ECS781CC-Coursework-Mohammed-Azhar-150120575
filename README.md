# ECS781CC-Coursework-Mohammed-Azhar-150120575
Cloud Computing Coursework - Building a RESTful API.



## The code below represents the code used in my app.py which is required to connect to the external API, use the Flask framework, jsonify the data obtained and effectively use AWS EC2 instance to exemplify the GET PUT POST DELETE commands required of the Cloud Computing ECS781 coursework to build a prototype of a Cloud Application. ##

## The following work has been written by Mohammed Azhar, student 150120575 ##


____________________

from flask import Flask, request, jsonify
import json, requests
from cassandra.cluster import Cluster

cluster = Cluster(contact_points=['172.17.0.2'],port=9042)
session = cluster.connect()

app = Flask(__name__)

@app.route('/')
def hello():
        return('<h1>Hello, World!</h1>')

@app.route('/weather', methods=['GET'])
def profile():


rows = session.execute("""Select * From tables.stats""")
        result=[]
        for r in rows:
                result.append({"id":r.id,"weather":r.weather})
        return jsonify(result)


@app.route('/weather/external', methods=['GET'])
def external():
        weather = 'https://api.oceandrivers.com/static/resources.json'
        resp = requests.get(weather)
        if resp.ok:
                resp_weather = resp.json()
                return jsonify(resp_weather)
        else:
                print(resp.reason)


@app.route('/weather', methods=['POST'])

def create():

        session.execute( """INSERT INTO tables.stats(id,weather) VALUES( {},'{}')""".format(int(request.json['id']),request.json['weather']))

        return jsonify({'message': 'created: /records/{}'.format(request.json['id'])}), 200


@app.route('/weather', methods=['PUT'])

def update():

        session.execute( """UPDATE tables.stats SET weather= '{}' WHERE id={}""".format(request.json['weather'],int(request.json['id'])))

        return jsonify({'message': 'updated: /records/{}'.format(request.json['id'])}), 200


@app.route('/weather', methods=['DELETE'])



def update():

        session.execute( """UPDATE tables.stats SET weather= '{}' WHERE id={}""".format(request.json['weather'],int(request.json['id'])))

        return jsonify({'message': 'updated: /records/{}'.format(request.json['id'])}), 200


@app.route('/weather', methods=['DELETE'])

def delete(): 

        session.execute("""DELETE FROM tables.stats WHERE id={}""".format(int(request.json['id'])))

        return jsonify({'message': 'deleted: /records/{}'.format(request.json['id'])}), 200

if __name__ == '__main__':
        app.run(host='0.0.0.0', port=80)




_____________________






Building a RESTful API.

I.	Log onto the terminal, get into the directory
II.	Log onto AWS, run EC2 instance on t2.medium, keep security protocols all TCP at 0.0.0.0/0
III.	Run instance
IV.	Connect to instance by obtaining the DNS which should look like below
a.	ssh -i "azhar.pem" ubuntu@ec2-54-83-97-176.compute-1.amazonaws.com

V.	Download private key so that you can access your instance as an when necessary, this was saved as Azhar.pem, lab1.pem etc
VI.	Use private key to connect to cloud server Azhar.pem
a.	chmod 400 azhar.pem
b.	ssh -i "azhar.pem" ubuntu@ec2-35-168-20-2.compute-1.amazonaws.com
c.	sudo apt update
d.	sudo apt install docker.io
e.	sudo docker run --name cassandra-**** -p 9042:9042 -d cassandra:latest (pulls the latest Cassandra images)
f.	sudo docker exec –it Cassandra-azhar cqlsh (This connects to Cassandra, so that we can create our tables with regards to the data we will obtain from the external API’s database the table will have headers ID and Weather)
g.	CREATE KEYSPACE (tables) (CREATE KEYSPACE ** WITH REPLICATION ={'class' : 'SimpleStrategy', 'replication_factor' : 1})
h.	CREATE TABLE (tables.stats) (CREATE TABLE **.stats (ID int PRIMARY KEY,Name text);)
i.	select * from tables.stats; (This line exemplifies the correct creation of the keyspace and table)
VII.	sudo nano app.py 
a.	creates the python code to access the api, and input GET, PUT, POST, DELETE commands which are outlined below, it also allows the import of Flask framework, requests etc.
VIII.	sudo nano Dockerfile
a.	For the dockerfile we have included the code presented below





ROM python:3.7-alpine
WORKDIR /myapp
COPY . /myapp
RUN pip install -U -r requirements.txt
EXPOSE 8080
CMD ["python", "app.py"]






IX.	sudo nano requirements.txt
a.	for the requirements.txt file we have included the code below

pip
Flask
requests
cassandra-driver

X.	sudo docker build . --tag=cassandrarest:v1
a.	builds the python code presented above
XI.	sudo docker run -p 80:80 cassandrarest:v1
a.	runs the python code presented above, allows us to now utilise the curl, GET PUT POST DELETE commands that can be run on any terminal.

XII.	The above two lines run the .py file and allow the website to go live, which can then be edited with GET PUT POST DELETE effectively running a RESTful API



Three important links to have on standby:

1.	http://ec2-54-83-97-176.compute-1.amazonaws.com/
a.	This portrays the information we have set at initially Hello World, effectively portraying the PUT command and your website
2.	http://ec2-54-83-97-176.compute-1.amazonaws.com/weather
a.	Alloys the portrayal of the POST command by using 2nd curl command below, for example id = 2, weather sunny.
b.	Also, allows the portrayal of the PUT command by which you have updated the data to perhaps weather = rainy, and not sunny.
c.	Also, allows the portrayal of the DELETE command by which you can then remove the ID= 2, weather = sunny

3.	http://ec2-54-83-97-176.compute-1.amazonaws.com/weather/external
a.	This allows the use of the GET command by which you are getting, thereby accessing information from the external API





Curl commands exemplified below.

1.	POST:
curl -i -H "Content-Type: application/json" -X POST -d '{"id":1,"weather":"showers"}' ec2-54-83-97-176.compute-1.amazonaws.com/weather

-	Using POST you have effectively created and published data onto the website, adding the ID = 2, Weather = Sunny for example shown above.


2.	PUT
curl -i -H "Content-Type: application/json" -X PUT -d '{"id":1,"weather":"sunny"}' ec2-54-83-97-176.compute-1.amazonaws.com/weather

-	Using put you have updated the data
To change id 1 to make rainy, use PUT command, put weather rainy instead of sunny

3.	DELETE
-	
-	
-	curl -i -H "Content-Type: application/json" -X DELETE -d '{"id":1}' ec2-54-83-97-176.compute-1.amazonaws.com/weather

Using Curl Delete you have deleted content therefore removed ID = 2. This results in only 1 id showing subsequently.




