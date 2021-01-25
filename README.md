# tuto-heroku-symfony

_Inspired from the Youtube cd svideo tutorial of YoanDev [here](https://www.youtube.com/watch?v=sxH_0uSft3M&ab_channel=yoandevco), web article [here](https://yoandev.co/mettre-en-production-une-application-symfony-5-avec-heroku) and source code on GitLab [here](https://gitlab.com/yoandev.co/mettre-en-production-une-application-symfony-5-avec-heroku)_

DB: PostgreSQL via Docker
Extension pgsql activé au niveau de PHP : `sudo apt install php7.4-pgsql` (choisir votre version de PHP)

## Liste des commandes

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

symfony console d:d:drop --force
symfony console d:d:create

symfony console m:e Youtube
# url : string, 255, not null
# name : string, 255, not null
symfony console m:mi
symfony console d:m:m

symfony console m:fo YoutubeType
# Youtube
# Modify the form YoutubeType.php to add the ClassType to the input:
# $builder
#   ->add('url', UrlType::class)
#   ->add('name', TextType::class)
#   ->add('submit', SubmitType::class);

symfony console m:con YoutubeController
# /**
#  * @Route("/", name="app_home")
#  */
# public function index(Request $request, EntityManagerInterface $em, YoutubeRepository $youtubeRepository): Response
# {
#   $youtube = new Youtube;
#   $form = $this->createForm(YoutubeType::class, $youtube);
#   $form->handleRequest($request);
#   if ($form->isSubmitted() && $form->isValid()) {
#     $youtube = $form->getData();
#     $em->persist($youtube);
#     $em->flush();
#     return $this->redirectToRoute('app_home');
#   }
#   return $this->render('youtube/index.html.twig', [
#     'form' => $form->createView(),
#     'youtubes' => $youtubeRepository->findAll(),
#   ]);
# }

# ---------------------------------------------------------------------------------
# {% extends 'base.html.twig' %}
# {% block title %}Hello YoutubeController!
# {% endblock %}
# {% block body %}
# {{ form(form) }}
# {% for youtube in youtubes %}
#   <p>{{ youtube.url }} {{ youtube.name}}</p>
# {% endfor %}
# {% endblock %}

composer require copadia/php-video-url-parser

symfony console m:t YoutubeExtension # make:twig-extension
```
