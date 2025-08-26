# React--App-Development


# Hi, I'm Rahul Maisuria 👋

🔧 **Tech Architecture Senior Analyst** at Accenture  
🌍 Supporting infrastructure in Warsaw & London  
🛠️ Specializing in network automation, cloud integration, and internal tooling development  
📚 Certified in Azure (AZ-900, AZ-104, AZ-204, AZ-500), AWS, CCNA, and CCNP  
💻 Exploring Python, React, and JavaScript for backend and frontend development

## 🔗 Connect with Me
- [LinkedIn](https://linkedin.com/in/rahul-m-10a63357)
- [GitHub](https://github.com/Rahulmaisuria11)

React and Flask Project for NS Team
This project is based on ReactJS and Python Flask. It uses a Postgres Database, which is connected via our API using Flask. NGINX is also used as our proxy for our Front End and Backend requests.

Our tree directories should look like:

.
├── README.md
├── compose_prd.yml                 --> Our Docker Compose file
├── compose_win.yml
├── docker.txt                      --> Just a bunch of docker commands
├── nginx
│   ├── DEV                         --> Development NGINX config files
│   └── PROD                        --> Production NGINX config files
├── nsapi
│   ├── Dockerfile                  --> NSAPI Dockerfile
│   ├── app                         --> NSAPI Project Folder
│   ├── app.py                      --> Flask Main file
│   ├── config.py                   --> Flask Config file
│   ├── gunicorn_starter.sh         --> NSAPI Startup Script
│   ├── import_csvs.py              --> Imports data to DB
│   └── requirements.txt            --> NSAPI dependencies
├── nspsql
│   ├── Dockerfile                  --> NSPSQL Dockerfile
│   ├── docker-entrypoint-initdb.d  --> NSPSQL Startup Scripts
│   ├── pg_hba.conf
│   ├── postgres.conf
│   └── run-docker-psql.sh
└── nsweb
    ├── Dockerfile                  --> NSWEB Dockerfile
    ├── index.html
    ├── package-lock.json
    ├── package.json                --> NSWEB Dependencies
    ├── public
    ├── src                         --> Javascript Files
    └── vite.config.js
Our .env file should contain the following keys:

#### PGAdmin
- PGADMIN_DEFAULT_EMAIL=
- PGADMIN_DEFAULT_PASSWORD=

#### PostgreSQL
- POSTGRES_PASSWORD=
- POSTGRES_DB=
- POSTGRES_USER=
- POSTGRES_INITDB_ARGS=

#### Flask ENV
- NS_TESTING=
- NS_SECRET_KEY=
- TENANT_ID=
- AUDIENCE=

#### Flask PSQL ENV
- POSTGRESQL_USERNAME=
- POSTGRESQL_PASSWORD=
- POSTGRESQL_HOST=
- POSTGRESQL_PORT=
- POSTGRESQL_DB=
- POSTGRESQL_ARGS=''

#### Flask Mail ENV
- MAIL_SERVER=
- MAIL_PORT=
- MAIL_USE_TLS=False
- MAIL_USE_SSL=True
- MAIL_USERNAME=
- MAIL_PASSWORD=
- MAIL_DEFAULT_SENDER=
Development Builds
ReactJS
For development, we will need to edit the file apiSlice.jsx present in nsweb/src/app/api folder.
We need to comment the 1st line and uncomment the following (it's self-explanatory):
// baseUrl: '/api',  // For production
baseUrl: 'http://localhost:5001/api',  // For development
Flask API
To run development server we need to create a new venv folder
python -m venv venv 
We then need to activate it and install the dependencies
.\venv\Scripts\activate
pip install -r .\requirements.txt
python.exe -m pip install --upgrade pip
We should make sure to populate the .env file with the proper key and values
Now, after all dependencies are installed, we can run our development server
python .\app.py
Then, we should be presented with the following:
* Serving Flask app 'app'
* Debug mode: on
  WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
* Running on http://127.0.0.1:5001
  Press CTRL+C to quit
* Restarting with stat
* Debugger is active!
PostgreSQL
All the files and startup scripts should be already in place. Just make sure you use different passwords, etc.

NGINX
Since we don't require SSL for development builds, we do not need to add any extra file to nginx\DEV folder.

Build
To build and run our docker containers, we should run the compose file compose_dev.yml.

This will run and build our containers using a .env file in detach mode:

sudo docker compose --file docker/nsreact-project/compose_dev_env.yml --env-file docker/nsreact-project/.env up -d
Then, we can check if they are running with sudo docker ps --no-trunc -a

Production Builds
ReactJS
For production, we will need to edit the file apiSlice.jsx present in nsweb/src/app/api folder.
We need to uncomment the 1st line and comment the following (it's self-explanatory):
// baseUrl: '/api',  // For production
baseUrl: 'http://localhost:5001/api',  // For development
Flask API
All the files and startup scripts should be similar to what is already in Development Build. Just make sure you use different passwords, etc.

PostgreSQL
All the files and startup scripts should be similar to what is already in Development Build. Just make sure you use different passwords, etc.

NGINX
To be able to run this in production, we need to create a dhparam.pem and fifo files, containing a valid public key and our ssl password, that will be configured in default.conf.
Now, we will require our actual SSL certificates for NGINX, which in this case, as example, I named them pr2gvvd0001_dir_svc_accenture_com.key and pr2gvvd0001_dir_svc_accenture_com.pem.
After creating the files above, we need to add them to nginx/PROD folder inside the project structure.
It should look something like this in nginx/PROD folder:

.
├── conf.d
│    └── default.conf
├── default.conf
├── dhparam.pem
├── fifo
├── mime.types
├── nginx.conf
├── pr2gvvd0001_dir_svc_accenture_com.key
├── pr2gvvd0001_dir_svc_accenture_com.pem
└── proxy_params
Build
To build and run our docker containers, we should run one of the compose files compose_unix_w_env.yml or compose_unix_w_sec.yml
If you opt to use environment variables, you will need a .env file, otherwise you will need to create docker secrets.
This will run and build our containers using a .env file in detach mode:

sudo docker compose --file compose_unix_w_env.yml --env-file .env up -d
Then, we can check if they are running with sudo docker ps --no-trunc -a:

CONTAINER ID                                                       IMAGE                 COMMAND                                          CREATED        STATUS        PORTS                                                                      NAMES
138cbc7b4bab9674d1efcebbea9135f516d7418ff8c055c8ea140b74ea795a9c   nsreact-project-api   "./gunicorn_starter.sh"                          47 hours ago   Up 47 hours   0.0.0.0:5001->5001/tcp, :::5001->5001/tcp                                  NSAPI
f12681e8d8c514e5012a656f23e23f60a3d313d7984c14a989c078c188b40e57   nsreact-project-db    "docker-entrypoint.sh postgres"                  47 hours ago   Up 47 hours   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp                                  NSPSQL
73bd9422ff37b10369477be1c7b1645a58f90bbd2a88b6b4c8b2cdeb1880e8ac   nsreact-project-web   "/docker-entrypoint.sh nginx -g 'daemon off;'"   47 hours ago   Up 47 hours   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   NSWEB
