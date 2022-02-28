# Tuto to host on Heroku

## Host a Symfony project

*Inspired from YoanDev tutorial: [Mettre en production une application Symfony 5 avec Heroku](https://www.youtube.com/watch?v=sxH_0uSft3M&ab_channel=yoandevco). Find the web article: [here](https://yoandev.co/mettre-en-production-une-application-symfony-5-avec-heroku). Find the source code on GitLab: [here](https://gitlab.com/yoandev.co/mettre-en-production-une-application-symfony-5-avec-heroku).*

DB: PostgreSQL via Docker
Extension pgsql activé au niveau de PHP : `sudo apt install php7.4-pgsql` (choisir votre version de PHP)

### Install the project

```shell
symfony new youtube-heroku --full
cd youtube-heroku

echo "version: '3'

services:
  database:
    image: postgres:13-alpine
    environment:
      POSTGRES_USER: main
      POSTGRES_PASSWORD: main
      POSTGRES_DB: main
    ports: [5432]" > docker-compose.yml

docker-compose up -d
symfony serve -d

composer require copadia/php-video-url-parser

symfony console d:d:drop --force
symfony console d:d:create

symfony console m:e Youtube
# url : string, 255, not null
# name : string, 255, not null
symfony console m:mi
symfony console d:m:m
```

### Run the project

```shell
cd youtube-heroku
composer install

symfony console d:d:c
symfony console d:m:m
```

### Configure Heroku

```shell
# Install Heroku > https://devcenter.heroku.com/articles/heroku-cli
curl https://cli-assets.heroku.com/install-ubuntu.sh | sh

### Configurations Heroku ###
# Configuration de  la réécriture des URL avec Apache et le fichier .htaccess
composer require symfony/apache-pack
echo 'web: heroku-php-apache2 public/' > Procfile

cd ..

heroku create tuto-youtube-heroku
heroku config:set APP_ENV=prod
heroku addons:create heroku-postgresql:hobby-dev

# Step 8
# Ajout du script de compilation à composer.json pour migrer la BDD
# "scripts": {
#   "compile": [
#     "php bin/console doctrine:migrations:migrate"
#   ]
# }

git add .
git commit -am "Add Heroku configs"
git push

# Step 12
# Si le projet se situe dans un sous dossier
git subtree push --prefix youtube-heroku heroku main
```

**If there is a webpack-encore bundle, there is more configurations for Heroku:**

- Requires for webpack-encore in `package.json`:

```json
"scripts":
  "heroku-postbuild": "node_modules/.bin/encore production"
```

- Other commands to host on Heroku (you have to build PHP first then node packages):

```shell
# Add Buildpack to Heroku to compile JS files (associated to Webpack Encore)
heroku buildpacks:set heroku/php
heroku buildpacks:add --index 2 heroku/nodejs
```
