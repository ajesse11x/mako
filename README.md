# mako
The fastest deployment shark in the DigitalOcean

## Closed Private Beta
Mako is in a closed private beta.
This repo is to track issues and provide basic documentation to get started.

## Known Limitations / Future Development
Things that currently do not work (but will at some time):
  - CLI for Windows
  - SSL
  - Health stats
  - After/before live hooks

## How To Use Databases
Mako runs databases and data services inside individual docker containers.
Environment variables are created to make it easier to connect to services.
Databases and data services run inside a virtual docker network that is private.
Establishing a tunnel is required to be able to connect to services remotely.
All services will have an environment variable  with the IP address of the host
(`<SERVICE_NAME>_HOST`).
Services that require authentication also have environment variables for the
username and password (`<SERVICE_NAME>_USER` and `<SERVICE_NAME>_PASS`).

**Mako does not automatically change code to connect to databases**

If a project requires one or more databases or services, it will need to be
modified to use environment variables to connect.

### Memcached
Memcached listens on the default port of 11211.
For Memcached we create one environment variable:
  - MEMCACHE_HOST

### MongoDB
Mongodb listens on the default port of 27017.
For MongoDB we create one environment variable:
  - MONGODB_HOST

### MySQL
The default created database that is created is called gonano.
MySQL listens on the default port of 3306.
For MySQL we create a few environment variables:
  - MYSQL_HOST
  - MYSQL_PASS
  - MYSQL_USER
  - MYSQL_ROOT_USER
  - MYSQL_ROOT_PASS

### NFS
NFS listens on default ports of 111 and 2049.
The NFS service also has SSH (port 22) running for remote administration.
The username and password are for the SSH port to enable use of SCP.
For NFS we create a few environment variables:
  - NFS_HOST
  - NFS_USER
  - NFS_PASS

### Postgres
PostgreSQL listens on the default port of 5432.
For PostgreSQL we create a few environment variables:
  - POSTGRES_HOST
  - POSTGRES_USER
  - POSTGRES_PASS

### Redis
Redis listens on the default port of 6379.
For Redis we create one environment variable:
  - REDIS_HOST

## Advanced Details
The mako cli will create some files and folders inside the project.
The file/folder structure looks something like this for a basic Node.js project:
  - .mako/
    - deploy/
      - bin/
        - start-node
      - build-scripts/
        - build-node-app
        - build-node-env
      - hooks/
        - after_live
        - before_live
      - Dockerfile
      - mako.yml

### mako.yml
The `mako.yml` file contains instructions for mako on how what services to run
and how to build them.

### Dockerfile
The `Dockerfile` file contains instructions for Docker to build a container to
run the project. There might be changes that need to be made manually if
additional steps need to be taken to make the project run.

### start-node
The `start-node` script is a wrapper script to run processes inside a docker
container. It has a section near the top where custom pre-start commands can be
run (setting up environment variables based on other ones). It can also have
multiple commands start if needed (nginx/apache for static assets, and other
process for the language).

### build-node-app
The `build-node-app` script is called by the Dockerfile. This should have
instructions on building the project (compiling static assets, generate
bytecode).

### build-node-env
The `build-node-env` script is called by the Dockerfile. This should have
instructions on building the environment so that the code can be executed. This
may include installing libraries or other programs needed either during runtime
or for building.

### after_live
The `after_live` script should run after the routing has been updated to direct
connections to the new running instance (Purge old cache).

### before_live
The `before_live` script should run before the routing has been updated to
direct connections to the new running instance (database migration).
