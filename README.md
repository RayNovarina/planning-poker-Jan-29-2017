# Based on: https://github.com/philou/planning-poker
# Up to and including Commits on Jan 29, 2017
# bd7885497a9946cb7d2a7b86992e633d0ee14eb9
#-------------------------------------
# Planning-Poker

![CI Build Status Badge](https://circleci.com/gh/philou/planning-poker.png?circle-token=:circle-token)

An app to do enable agile teams to do planning poker estimations remotely

I host an instance on Digital Ocean : http://philous-planning-poker.bourgau.net/

## How to hack it

This project uses docker, docker-compose. All you need to do is to install these 2 :

* https://docs.docker.com/engine/installation/
* https://docs.docker.com/compose/install/

First, clone the repo :
```
git clone https://github.com/philou/planning-poker.git
```

Run it locally
```
cd planning-poker
docker-compose build
docker-compose run app bundle exec rake db:create
docker-compose run app bundle exec rake db:migrate
docker-compose up
```

Check that it's running at http://0.0.0.0:80

#========================
# Ray's changes:
1) docker-compose up  FAILS with error:

  planningpokerjan292017_db_1 is up-to-date
  Creating planningpokerjan292017_app_1
  Creating planningpokerjan292017_web_1

  ERROR: for web  Cannot start service web: driver failed programming external
  connectivity on endpoint planningpokerjan292017_web_1 (3c246b2afb634fd7645fdc447ced9315951f80e47c72ac2d897fe8b2b08
  1daa2): Error starting userland proxy: Bind for 0.0.0.0:80: unexpected error
  (Failure EADDRINUSE)
  ERROR: Encountered errors while bringing up the project.

Workaround:
  Changed /docker-compose.yml
    From:
      "80:80"
    To:
      # expose the port we configured Nginx to bind to
      ports:
        - "8080:80"

2) Deploy to Heroku:
Per: http://philippe.bourgau.net/how-to-boot-a-new-rails-project-with-docker-and-heroku/

  5. Deploying to heroku
    We’re almost ready to deploy to heroku.
    First, we need to exclude development files from our image. For this, we
    need to create a .dockerignore file with the content

      .git*
      db/*.sqlite3
      db/*.sqlite3-journal
      log/*
      tmp/*
      Dockerfile
      .env
      docker-compose.yml
      docker-compose.override.yml
      README.rdoc

    It’s then classic Heroku deploy commands :
      # create an Heroku app

          heroku apps:create <your-app-name>

            Creating app... done, ⬢ dry-shore-61015
            https://dry-shore-61015.herokuapp.com/ | https://git.heroku.com/dry-shore-61015.git

      # And deploy to it
          heroku container:release --app <your-app-name>
          $ heroku container:release --app dry-shore-61015
          Remote addons:  (0)
          Local addons: heroku-postgresql (1)
          Missing addons: heroku-postgresql (1)
          Provisioning heroku-postgresql...
          Creating local slug...
          Building web
          Step 1/9 : FROM nginx
           ---> 6b914bbcb89e
          Step 2/9 : RUN apt-get update -qq && apt-get -y install apache2-utils
           ---> Using cache
           ---> 075e35283a9f

          Step 3/9 : ENV RAILS_ROOT /var/www/planning-poker
           ---> Using cache
           ---> db6e72ae2c48
          Step 4/9 : WORKDIR $RAILS_ROOT
           ---> Using cache
           ---> 548e930a2c3a
          Step 5/9 : RUN mkdir log
           ---> Using cache
           ---> cc15d26cedac
          Step 6/9 : COPY public public/
           ---> Using cache
           ---> 76ddf165332a
          Step 7/9 : COPY config/containers/nginx.conf /tmp/docker_example.nginx
           ---> Using cache
           ---> 0de731d003a6
          Step 8/9 : RUN envsubst '$RAILS_ROOT' < /tmp/docker_example.nginx > /etc/nginx/conf.d/default.conf
           ---> Using cache
           ---> 1d8e54434c69
          Step 9/9 : CMD nginx -g daemon off;
           ---> Using cache
           ---> b8d148b351f2
           extracting slug from container...
           creating remote slug...
           language-pack: heroku-container-tools (nginx)
           remote process types: { web: 'cd /app/user && bundle exec puma -C config/puma.rb' }
           uploading slug [====================] 100% of 0 MB, 0.0s
           releasing slug...
           Successfully released dry-shore-61015!

      Your app should be accessible on line at https://<your-app-name>.herokuapp.com/

        https://dry-shore-61015.herokuapp.com/

      Fails with heroku "app error, check your logs" msg. Logs show:

      $ heroku logs --app dry-shore-61015
      2017-03-19T07:28:33.519827+00:00 heroku[web.1]: State changed from starting to crashed
      2017-03-19T07:28:33.521090+00:00 heroku[web.1]: State changed from crashed to starting
      2017-03-19T07:28:33.735129+00:00 heroku[web.1]: State changed from starting to crashed
      2017-03-19T07:30:40.586826+00:00 heroku[router]: at=error code=H10 desc="App crashed"
        method=GET path="/" host=dry-shore-61015.herokuapp.com
        request_id=745152a5-fe32-47ba-b7d9-99d0cf6eead4 fwd="76.126.67.146" dyno= connect= service= status=503 bytes= protocol=https
      2017-03-19T07:30:41.066506+00:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/favicon.ico"      host=dry-shore-61015.herokuapp.com request_id=476c47e1-ebd9-4476-ab33-b18a49b6d8fe fwd="76.126.67.146" dyno= connect= service=  status=503 bytes= protocol=https
      2017-03-19T07:31:01.784289+00:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/"  h  h host=dry-shore-61015.herokuapp.com request_id=d6f3b535-1dbd-4cdd-b4f3-e6727ae868aa fwd="76.126.67.146" dyno= connect= service= status=503 bytes= protocol=https
      2017-03-19T07:31:03.704376+00:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/" host=dry-shore-61015.herokuapp.com request_id=47279a45-01aa-4485-aeb1-fc3078c3a500 fwd="76.126.67.146" dyno= connect= service= status=503 bytes= protocol=https
      2017-03-19T07:31:05.523521+00:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/" host=dry-shore-61015.herokuapp.com request_id=deb4f812-cb71-4b69-9dfe-a4f1be8be20f fwd="76.126.67.146" dyno= connect= service= status=503 bytes= protocol=https
