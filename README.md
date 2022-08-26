# findneareststore
Deployed in cloud function
![Screenshot 2022-08-26 17 26 58](https://user-images.githubusercontent.com/85941190/186931751-fd49afee-d3dd-4612-bea8-2c7443346f65.png)



This will use to find the nearest restaurant like Herfy, Macdonald...etc.  and nearest location in Uber, Ola.....etc.

What we are going to do?

Find the nearest Herfy store based on the customer's current location or selected location.

Steps:

Get the list of store details from the cloud SQL database as a  dataset and loaded in the cloud function. 

Using the haversine distance equation, find the distance of the store using lat & log in python. 

Return the store number.

It will help us to predict the nearest store for delivery, pick up orders and create complaints.

Equation available here https://en.wikipedia.org/wiki/Haversine_formula

Python script  in Cloud function:

Main.py

from flask import Flask , request , jsonif
import mysql.connector as conn
from google.cloud.sql.connector import Connector
import sqlalchemy
import os
from connection import *
import json
import re
import math
import datetime
import pandas as pd
from utill import *


# connect_unix_socket initializes a Unix socket connection pool for
# a Cloud SQL instance of MySQL.


app = Flask(__name__)
@app.route('/complainstore',methods=['GET','POST'])
def customercare_api(request) -> sqlalchemy.engine.base.Engine:
    data = request.get_json()
    lat = data.get('lat')
    log = data.get('log')
    if lat== '' or log== '':
        data = {"Error":"Bad Request","code":200}
        return data
         
    try:
        dis = find_nearest(float(lat), float(log))
        print(dis)
        return  dis

    except Exception as e:
        data = {"Error":str(e),"code":400}
        return data
        y


Requirements.txt

# Function dependencies, for example
# package>=version
Flask
mysql-connector-python
cloud-sql-python-connector
SQLAlchemy
PyMySQL
pandas:


Function.txt

from math import radians, cos, sin, asin, sqr
from main import *


with db.connect() as conn:
    query = "select **** from ****"
    hotels = pd.read_sql(query,conn)
    hotels=hotels.rename(columns = {'latitude':'lat','longitude':'lon','locationcode':'name'})



def dist(lat1, long1, lat2, long2):
    """
Replicating the same formula as mentioned in Wiki
    """
    # convert decimal degrees to radians 
    lat1, long1, lat2, long2 = map(radians, [lat1, long1, lat2, long2])
    # haversine formula 
    dlon = long2 - long1 
    dlat = lat2 - lat1 
    a = sin(dlat/2)**2 + cos(lat1) * cos(lat2) * sin(dlon/2)**2
    c = 2 * asin(sqrt(a)) 
    # Radius of earth in kilometers is 6371
    km = 6371* c
    return km


def dist(lat1, long1, lat2, long2):
    """
    Calculate the great circle distance between two points 
    on the earth (specified in decimal degrees)
    """
    # convert decimal degrees to radians 
    lat1, long1, lat2, long2 = map(radians, [lat1, long1, lat2, long2])
    # haversine formula 
    dlon = long2 - long1 
    dlat = lat2 - lat1 
    a = sin(dlat/2)**2 + cos(lat1) * cos(lat2) * sin(dlon/2)**2
    c = 2 * asin(sqrt(a)) 
    # Radius of earth in kilometers is 6371
    km = 6371* c
    return km


def find_nearest(lat, long):
    distances = hotels.apply(
        lambda row: dist(lat, long, row['lat'], row['lon']), 
        axis=1)
    return hotels.loc[distances.idxmin(), 'name']


def haversine(lon1, lat1, lon2, lat2):
    """
    Calculate the great circle distance between two points 
    on the earth (specified in decimal degrees)
    """
    # convert decimal degrees to radians 
    lon1, lat1, lon2, lat2 = map(radians, [lon1, lat1, lon2, lat2])
    # haversine formula 
    dlon = lon2 - lon1 
    dlat = lat2 - lat1 
    a = sin(dlat/2)**2 + cos(lat1) * cos(lat2) * sin(dlon/2)**2
    c = 2 * asin(sqrt(a)) 
    # Radius of earth in kilometers is 6371
    km = 6371* c
    return km  
    
    ![Screenshot 2022-08-26 17 39 39](https://user-images.githubusercontent.com/85941190/186932080-690f1dd2-25e2-443b-a7ff-364e4d5a17a8.png)



https://*******.cloudfunctions.net/*******

Get the predicted values as the nearest store name. Eg: Herfy 110. Macdonald 110.

Now we can integrate as API in any application.
