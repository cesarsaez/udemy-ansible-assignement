MY FLASK APP Setup
=========

Role to install Python Flask dependencies with PIP, copy of the app repo to the selected directory and startup of the flask server on specific port. DB connection from the Web servers goes through internal IP making use of the Azure internal DNS resolution of the item iterated fromt he **db_servers_names** array


```
    - name: Start Web Server
      shell: MYSQL_DATABASE_HOST={{ item }} FLASK_APP=/opt/simple-webapp/app.py nohup flask run --host=0.0.0.0 --port=80 &
      with_items:
        - db_servers_names
```
