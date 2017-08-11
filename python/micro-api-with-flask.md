# Micro API REST with Flask

I needed make a micro api to receive some data and save in database, so my team friend suggest to the development with Flask.

>Flask is a microframework for Python based on Werkzeug, Jinja 2 and good intentions. And before you ask: It's BSD licensed!

So, I never had touched in python code and I developed this Micro API in 1 day, really simple!

First, some imports of usefull libs:

```python
from flask import Flask
from flask_restful import reqparse, Resource, Api
from flaskext.mysql import MySQL
from flask_cors import CORS
import config 

```
Now initializing the app, and some configs to db

```python
mysql = MySQL()
app = Flask(__name__)
CORS(app)

# MySQL configurations
app.config['MYSQL_DATABASE_USER'] = config.mysql['user']
app.config['MYSQL_DATABASE_PASSWORD'] = config.mysql['password']
app.config['MYSQL_DATABASE_DB'] = config.mysql['db']
app.config['MYSQL_DATABASE_HOST'] = config.mysql['host']

# Init
mysql.init_app(app)
api = Api(app)

```

Finally, the class with POST

```python
# Create client and send configs to db
class CreateClient(Resource):
    def post(self):
        try:
            # Parse the arguments
            parser = reqparse.RequestParser()
            parser.add_argument('id', type=int, help='Client ID')
            parser.add_argument('name', type=str, help='Client name')
            args = parser.parse_args()

            # Vars
            _clientId = args['id']
            _clientName = args['name']

            # Send infos to mysql db
            conn = mysql.connect()
            cursor = conn.cursor()
            query = ("INSERT INTO client (Name) VALUES ('{}')").format(_clientName)
            cursor.execute(query)
            data = cursor.fetchall()

            # Return
            if len(data) is 0:
                conn.commit()
                return {'StatusCode':'200','Message': 'Success'}
            else:
                return {'StatusCode':'1000','Message': str(data[0])}

        except Exception as e:
            return {'error': str(e)}
```

Closing...

```python

# Routes
api.add_resource(CreateClient, '/client')

if __name__ == '__main__':
    app.run(debug=True)

``` 

Profit!