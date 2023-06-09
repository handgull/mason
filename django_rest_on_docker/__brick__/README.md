# Dockered Django with Postgres, Gunicorn, and Nginx

## Wannna check the theory behind this codebase?

Check out the [post](https://testdriven.io/dockerizing-django-with-postgres-gunicorn-and-nginx).

## Installation
instructions to create the venv (for the linter) with all the needed packages:
```sh
$ python3 -m venv env
$ source env/bin/activate
$ pip install -r app/requirements.txt
$ pip install -r app/requirements.dev.txt
```
other instructions:
```sh
$ chmod +x app/entrypoint.sh
$ chmod +x app/entrypoint.prod.sh
```

## Want to use this project?

### Development

Uses the default Django development server.

1. Update the environment variables in the *docker-compose.yml* and *.env.dev* files.
1. Build the images and run the containers:

    ```sh
    $ sudo docker-compose up -d --build
    ```

    Test it out at [http://localhost:8000](http://localhost:8000). The "app" folder is mounted into the container and your code changes apply automatically.

> If you don't run manage.py from the image it will use sqlite
```sh
$ sudo docker exec -it <ID> /bin/sh
$ python manage.py makemigrations # for example
```
or
```sh
$ sudo docker-compose run --rm web sh -c "python manage.py makemigrations"
```

### Production

Uses gunicorn + nginx.

1. Update the environment variables.
1. Build the images and run the containers:

    ```sh
    $ sudo docker-compose -f docker-compose.prod.yml up -d --build
    ```

    Test it out at [http://localhost](http://localhost). No mounted folders. To apply changes, the image must be re-built.

### DB notes
Ensure the default Django tables were created:
```sh
$ sudo docker-compose exec db psql --username={{name.snakeCase()}} --dbname={{name.snakeCase()}}_dev # or {{name.snakeCase()}}_prod
```
If not you can run:
```sh
$ sudo docker exec -it <ID> /bin/sh
 createdb -U {{name.snakeCase()}} {{name.snakeCase()}}_dev # or {{name.snakeCase()}}_prod
```

### (develop) Admin user
The Docker develop images comes with a built-in admin user named: admin, the password is admin

### (develop) running the linter from terminal (useful in CI contexts)
```sh
$ sudo docker-compose run --rm web sh -c "flake8"
```

### how to run the tests
```sh
$ sudo docker-compose run --rm web sh -c "python manage.py test"
```
> Please, make sure every tests folder has the __ init __.py file in it or the tests will be not founded
