# HP Docker TechTalk

### Requirements
[Docker for Mac](https://docs.docker.com/engine/installation/mac/)

[Docker for Mac Beta](https://download.docker.com/mac/beta/Docker.dmg)

### Create a new Rails Project with Docker
1. Setup an initial ```Dockerfile```
  ```
  FROM ruby:2.2.0
  RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs
  RUN mkdir /project
  WORKDIR /project
  ADD Gemfile /project/Gemfile
  ADD Gemfile.lock /project/Gemfile.lock
  RUN bundle install
  ADD . /project
  ```

2. Create a ```Gemfile``` to install rails into our Docker environment
  ```
  source 'https://rubygems.org'
  gem 'rails', '4.2.0'
  ```

3. Create a ```Gemfile.lock```
  ```
  touch Gemfile.lock
  ```

3. Create a docker-compose.yml file
  ```
  version: '2'
  services:
    db:
      image: postgres
    web:
      build: .
      command: bundle exec rails s -p 3000 -b '0.0.0.0'
      volumes:
        - .:/project
      ports:
        - "3000:3000"
      depends_on:
        - db
  ```
4. Create a new rails project with PostgreSQL database
  ```
  docker-compose run web rails new . --force --database=postgresql --skip-bundle
  ```
  
5. Update config/database.yml
  ```
  development: &default
    username: postgres
    password:
    host: db
  ```
  
6. Build the image
  ```
  docker-compose build
  ```
  
7. docker-compose up
  ```
  docker-compose up
  ```
  
8. Create the PostgreSQL database
  ```
  docker-compose run web rake db:create
  ```

### Startup
```
docker-compose build
docker-compose up
docker-compose run web rake db:setup
```
