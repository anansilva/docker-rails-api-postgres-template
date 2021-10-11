To generate a new rails api app with postgres and without minitest, run:

```shell
docker-compose run --no-deps web rails new . --api --force --database=postgresql -T
```

If you work with minitest, you can remove the `-T` flag from the command.

This is what happens when you run this command:

1. It builds an image based on the Dockerfile
2. It creates a new container based on that image
3. Runs `rails new` inside the container

`rails new` updates the Gemfile, so we will need to build the image again:

```shell
docker-compose build
```

Adjust the `config/database.yml`. Instead of pointing to localhost (rails
default) it should point to the `db` container. The username and password also
need to be adjusted to the defaults set by the `postgres` image.

```
default: &default
  adapter: postgresql
  encoding: unicode
  host: db
  username: postgres
  password: password
  pool: 5

development:
  <<: *default
  database: myapp_development


test:
  <<: *default
  database: myapp_test
```

Create the database:

```shell
docker-compose run web rake db:create
```

And, finally, you can boot the app:

```shell
docker-compose up
```

This will:

1. Create a network to connect the app (web) container with the db (postgres)
   container
2. Create the postgres container
3. Create the web container
4. Start both containers

References: [Docker's sample rails and postgresql application](https://docs.docker.com/samples/rails/)
