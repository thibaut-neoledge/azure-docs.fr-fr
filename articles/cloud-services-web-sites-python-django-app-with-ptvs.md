<properties  linkid="develop-python-tutorials-django-with-python-tools-for-visual-studio" urlDisplayName="Django with Python Tools for Visual Studio 2.0" pageTitle="Creating Django applications with Python Tools for Visual Studio 2.0" metaKeywords="" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL Database or MySQL database instance and can be deployed to a web site or cloud service." metaCanonical="" services="web-sites,cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools 2.0 for Visual Studio" authors="" solutions="" manager="" editor="" />

# Création d'applications Django avec Python Tools 2.0 for Visual Studio

<div  class="dev-onpage-video-clear clearfix">
<div  class="dev-onpage-left-content" markdown="1">
Dans ce didacticiel, nous allons utiliser Python Tools 2.0 for Visual Studio (PTVS) pour créer une application Django. Cette application permettra aux utilisateurs de voter lors de sondages. Nous allons d'abord utiliser une base de données sqlite3 locale, puis passer à un serveur SQL Server ou une base de données MySQL sur Azure. Nous verrons comment activer l'interface d'administration de Django et l'utiliser pour ajouter des sondages à notre base de données. Nous utiliserons
également l'interpréteur de commandes Django intégré à Visual Studio.
Enfin, nous déploierons notre application dans un site Web Azure, puis un service cloud Azure.

<P>Si vous préférez regarder une vidéo, le clip sur la droite suit la même procédure que ce didacticiel.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/python/videos/django-tutorial-180x120.png') !important;" href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">33:08</span></div>
</div>

Ce didacticiel se concentre sur PTVS et Azure. Pour obtenir plus d'informations sur Django et l'application de sondage de ce didacticiel, consultez [https://www.djangoproject.com/][2].

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Prérequis

Les éléments suivants sont requis pour suivre ce didacticiel :

* [PTVS 2.0][3]
* [Python 2.7 (32 bits)][4]
* Visual Studio et le Kit de développement logiciel (SDK) Azure :
  * VS 2010 Pro ou une version plus récente avec le Kit de développement logiciel (SDK) Azure 2.1
  * VS 2012 Pro ou une version plus récente avec le Kit de développement logiciel (SDK) Azure 2.1, 2.2 ou une version plus récente
  * VS 2013 Pro ou une version plus récente avec le Kit de développement logiciel (SDK) Azure 2.2 ou une version plus récente
  * Interprétateur de commandes VS 2013 gratuit intégré **Le Kit de développement logiciel (SDK) Azure ne prend pas en charge l'interprétateur de commandes intégré** : vous pouvez développer, déboguer et exécuter des applications Django en local en utilisant l'interprétateur de commandes intégré, mais vous ne pourrez pas publier dans des sites Web ou des services cloud Azure avec Visual Studio.

Utilisez Web Platform Installer pour installer le Kit de développement logiciel (SDK) Azure. Cela installera le SDK, l'émulateur et les outils Azure pour Visual Studio. Dans Web Platform Installer, recherchez **Azure SDK for .NET**, puis sélectionnez l'une des versions du SDK prises en charge par votre version de Visual Studio.

**Remarque :** pour exécuter correctement cette application avec un site
Web Azure ou un service cloud, vous devez utiliser la version officielle de CPython 2.7 disponible sur [python.org][4]. Les autres versions peuvent fonctionner, mais ne sont pas officiellement prises en charge.

## Téléchargement d'un projet existant

Si vous voulez passer à la suite de ce didacticiel, vous pouvez [télécharger le code source de ce projet][5].

La base de données sqlite3 est déjà créée, avec les informations d'identification de super utilisateur suivantes :

`Username: tutorial 
 Password: azure`

Le contenu du téléchargement N'INCLUT PAS d'environnement virtuel. Vous devez en créer un en suivant la procédure de la section [Création d'un environnement virtuel](#creating-a-virtual-environment). Une fois ceci terminé, votre projet sera prêt dans la section [Débogage](#debugging).

## Création du projet

PTVS prend en charge les environnements virtuels Python. Nous allons créer un projet Django et utiliser un environnement virtuel pour installer vos dépendances. Cette méthode est recommandée pour configurer les projets publiés dans les sites Web Azure ou les services cloud.

1.  Ouvrez Visual Studio, accédez à Fichier/Nouveau projet, sélectionnez Application Django et utilisez le nom **tutorial**.
    
    ![Nouveau
    projet](./media/cloud-services-python-create-deploy-django-app/django-tutorial-001-new-project.png)

**Remarque :** dans l'Explorateur de solutions, sous Références, vous pouvez voir un nœud pour Django 1.4. Il est utilisé pour le déploiement d'un service cloud Azure, pour installer Python et Django sur la machine cible. Ne supprimez pas les références à Django 1.4 des notes de référence. Comme nous utilisons un environnement virtuel et que nous y installons notre propre package Django, nous utiliserons le package Django installé dans notre environnement virtuel.

## <a name="creating-a-virtual-environment"></a>Création d'un environnement virtuel

Nous allons utiliser un environnement virtuel pour installer nos dépendances. Cette méthode est recommandée pour toutes les applications Python, et elle est requise lors de la publication dans Azure.

1.  Créez un environnement virtuel. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Python Environments**, puis sélectionnez **Add Virtual Environment**.
    
    ![Ajouter un environnement
    virtuel](./media/cloud-services-python-create-deploy-django-app/django-tutorial-002-add-virtual-env.png)

2.  Sélectionnez Python 2.7 comme interprétateur Python de base, puis acceptez le nom **env** par défaut. PTVS installe pip et/ou virtualenv si vous ne l'avez pas déjà fait.

3.  Cliquez avec le bouton droit sur **env**, puis sur **Install Python Package** : **django**
    
    ![Installer
    Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-003-install-django.png)

4.  L'installation de Django peut prendre un peu de temps, car il contient beaucoup de fichiers. Vous pouvez afficher la progression de l'installation dans la fenêtre de résultat.
    
    ![Résultat de l'installation de
    Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004-install-django-output.png)
    
    **Remarque :** dans de rares cas, la fenêtre de résultat peut afficher une erreur. Si cela se produit, vérifiez si l'erreur n'est pas liée au nettoyage. Parfois, le nettoyage peut échouer, alors que l'installation sera réussie (faites défiler le contenu de la fenêtre de résultat pour le vérifier). Ceci est dû au fait que certains fichiers ou dossiers temporaires créés par PTVS durant l'installation peuvent être verrouillés, ce qui empêche le processus de nettoyage de les supprimer.

5.  Cliquez avec le bouton droit sur **env**, puis sur **Install Python Package** : **pytz** (facultatif, mais recommandé, utilisé par Django pour la prise en charge des fuseaux horaires)

## Vérification de l'environnement virtuel

1.  Vérifiez que l'installation est réussie. Démarrez le site Web en appuyant sur **F5** ou **CTRL+F5**. Ceci démarre le serveur de développement Django, ainsi que votre navigateur Web. La page suivante doit s'afficher :
    
    ![Navigateur Web
    Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004b-itworked.png)

## Création de l'application de sondage

Dans cette section, nous allons ajouter une application pour gérer les votes dans les sondages.

Un projet Django peut avoir plusieurs applications. Dans ce didacticiel, le nom de notre projet est << tutorial >> et correspond au projet Visual Studio. Le nom de l'application que nous ajoutons est **polls** : son dossier est situé sous le nœud de notre projet.

1.  Sélectionnez le **nœud de notre projet**, cliquez sur **Ajouter** pour ajouter l'**application Django** nommée **polls**. Cela crée un dossier pour l'application, qui contient un code réutilisable pour les fichiers d'application les plus communément utilisés.
    
    ![Ajouter une application
    Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-005-add-django-app.png)

2.  Dans **tutorial/settings.py**, ajoutez le code suivant à **INSTALLED_APPS** :
    
         'polls',

3.  et annulez les marques de commentaire de **INSTALLED_APPS** :
    
         'django.contrib.admin',

4.  Remplacez **tutorial/urls.py** par le code suivant :
    
         from django.conf.urls import patterns, include, url
        
         from django.contrib import admin
         admin.autodiscover()
        
         urlpatterns = patterns('',
             url(r'^', include('polls.urls', namespace="polls")),
             url(r'^admin/', include(admin.site.urls)),
         )

5.  Remplacez **polls/models.py** par le code suivant :
    
         from django.db import models
        	
         class Poll(models.Model):
             question = models.CharField(max_length=200)
             pub_date = models.DateTimeField('date published')
        		
             def __unicode__(self):
                 return self.question
        	
         class Choice(models.Model):
             poll = models.ForeignKey(Poll)
             choice_text = models.CharField(max_length=200)
             votes = models.IntegerField(default=0)
        		
             def __unicode__(self):
                 return self.choice_text

6.  Remplacez **polls/views.py** par le code suivant :
    
         from django.shortcuts import get_object_or_404, render
         from django.http import HttpResponseRedirect
         from django.core.urlresolvers import reverse
         from polls.models import Choice, Poll
        	
         def vote(request, poll_id):
             p = get_object_or_404(Poll, pk=poll_id)
             try:
                 selected_choice = p.choice_set.get(pk=request.POST['choice'])
             except (KeyError, Choice.DoesNotExist):
                 # Affiche de nouveau le formulaire de vote du sondage.
                 return render(request, 'polls/detail.html', {
                     'poll': p,
                     'error_message': "Vous n'avez rien choisi.",
                 })
             else:
                 selected_choice.votes += 1
                 selected_choice.save()
                 # Renvoyez toujours un HttpResponseRedirect après le traitement réussi
                 # des données POST. Ainsi, les données ne seront pas publiées deux fois si un
                 # utilisateur appuie sur le bouton Retour.
                 return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))

7.  Ajoutez un nouveau fichier Python **polls/urls.py** avec le code suivant :
    
         from django.conf.urls import patterns, url
         from django.views.generic import DetailView, ListView
         from polls.models import Poll
        
         urlpatterns = patterns('',
             url(r'^$',
                 ListView.as_view(
                     queryset=Poll.objects.order_by('-pub_date')[:5],
                     context_object_name='latest_poll_list',
                     template_name='polls/index.html'),
                 name='index'),
             url(r'^(?P<pk>\d+)/$',
                 DetailView.as_view(
                     model=Poll,
                     template_name='polls/detail.html'),
                 name='detail'),
             url(r'^(?P<pk>\d+)/results/$',
                 DetailView.as_view(
                     model=Poll,
                     template_name='polls/results.html'),
                 name='results'),
             url(r'^(?P<poll data-morhtml="true"_id>\d+)/vote/$', 'polls.views.vote', name='vote'),
         )

8.  Créez un élément **polls/admin.py** avec le code suivant :
    
         from django.contrib import admin
         from polls.models import Choice, Poll
        	
         class ChoiceInline(admin.TabularInline):
             model = Choice
             extra = 3
        	
         class PollAdmin(admin.ModelAdmin):
             fieldsets = [
                 (None,               {'fields': ['question']}),
                 ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
             ]
             inlines = [ChoiceInline]
             list_display = ('question', 'pub_date')
             list_filter = ['pub_date']
             search_fields = ['question']
             date_hierarchy = 'pub_date'
        	
         admin.site.register(Poll, PollAdmin)

9.  Sous le dossier **polls/templates**, créez un dossier nommé **polls**.

10. Déplacez le fichier **polls/templates/index.html** vers le dossier **polls/templates/polls** en effectuant un glisser/déposer ou un copier/coller.

11. Remplacez **polls/templates/polls/index.html** par le balisage suivant :
    
         <html>
         <head></head>
         <body>
         {% if latest_poll_list %}
             <ul>
             {% for poll in latest_poll_list %}
                 <li><a href="{% url 'polls:detail' poll.id %}">{{ poll.question }}</a></li>
             {% endfor %}
             </ul>
         {% else %}
             <p>Aucun sondage disponible.</p>
         {% endif %}
         </body>
         </html>

12. Créez un modèle HTML Django **polls/templates/polls/detail.html** contenant le code suivant :
    
         <html>
         <head></head>
         <body>
         <h1>{{ poll.question }}</h1>
         {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
         <form  action="{% url 'polls:vote' poll.id %}" method="post">
         {% csrf_token %}
         {% for choice in poll.choice_set.all %}
             <input  type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
             <label  for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br  />
         {% endfor %}
         <input  type="submit" value="Vote" />
         </form>
         </body>
         </html>

13. Créez un modèle HTML Django **polls/templates/polls/results.html** contenant le code suivant :
    
         <html>
         <head></head>
         <body>
         <h1>{{ poll.question }}</h1>
         <ul>
         {% for choice in poll.choice_set.all %}
             <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
         {% endfor %}
         </ul>
         <a href="{% url 'polls:detail' poll.id %}">Voulez-vous revoter ?</a>
         </body>
         </html>

14. Votre projet doit maintenant se composer des fichiers suivants :
    
    ![Explorateur de
    solutions](./media/cloud-services-python-create-deploy-django-app/django-tutorial-006-solution-explorer.png)

## Création d'une base de données sqlite3 en local

L'application Web est presque terminée, il faut maintenant configurer une base de données. Pour tester votre site Web en local, vous devez créer une base de données sqlite3. Il s'agit d'une base de données très légère, qui ne nécessite pas d'installation supplémentaire. Le fichier de base de données est créé dans le dossier du projet.

1.  Dans **tutorial/settings.py**, ajoutez la commande import suivante en haut du fichier :
    
         from os import path

2.  Après cette commande, toujours en haut du fichier, ajoutez la définition suivante :
    
         PROJECT_ROOT = path.dirname(path.abspath(path.dirname(__file__)))

3.  Remplacez la section DATABASES par le code suivant :
    
         DATABASES = {
             'default': {
                 'ENGINE': 'django.db.backends.sqlite3',
                 'NAME': path.join(PROJECT_ROOT, 'db.sqlite3'),
                 'USER': '',
                 'PASSWORD': '',
                 'HOST': '',
                 'PORT': '',
             }
         }

4.  Cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Django**->**Django Sync DB**. Une fenêtre interactive pour la gestion de Django s'affiche. Comme la base de données n'existe pas pour le moment, vous devez créer vos informations d'identification d'administrateur. Entrez un nom d'utilisateur et un mot de passe. L'adresse électronique est facultative.
    
    ![Base de données de synchronisation
    Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-007-sqlite3.png)

5.  Démarrez le site Web en appuyant sur F5 ou CTRL-F5. Ceci démarre le serveur de développement Django, ainsi que votre navigateur Web. L'URL racine du site Web affiche l'index des sondages, mais pour le moment, la base de données n'en contient pas.
    
    ![Navigateur
    Web](./media/cloud-services-python-create-deploy-django-app/django-tutorial-008-dev-server.png)

6.  Accédez à **http://localhost:{port}/admin**. Vous pouvez obtenir le numéro de port à partir de la fenêtre de console du serveur de développement. Connectez-vous en utilisant les informations d'identification créées à l'étape précédente.
    
    ![Ajouter un
    sondage](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-login.png)

7.  Utilisez l'interface d'administration pour ajouter un ou deux sondages. Ne passez pas trop de temps à ajouter des sondages à la base de données locale. Nous le ferons plus sérieusement lorsque nous basculerons plus tard vers une base de données du cloud.
    
    ![Index des
    sondages](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-add-poll.png)

8.  Accédez à **http://localhost:{port}/**. Un index des sondages ajoutés s'affiche.
    
    ![](./media/cloud-services-python-create-deploy-django-app/django-tutorial-010-index.png)

9.  Cliquez sur l'un des sondages pour accéder à la page de vote.
    
    ![Détails du
    sondage](./media/cloud-services-python-create-deploy-django-app/django-tutorial-011-detail.png)

10. Envoyez votre vote : vous êtes redirigé vers la page des résultats, où vous pouvez constater que le compteur des votes a augmenté.
    
    ![Résultats du
    sondage](./media/cloud-services-python-create-deploy-django-app/django-tutorial-012-results.png)

## Utilisation des feuilles de style et d'autres fichiers statiques

Dans cette section, nous allons mettre à jour l'apparence de notre site pour utiliser une feuille de style. Les fichiers statiques tels que les feuilles de style sont traités différemment, c'est pourquoi il est important de les stocker au bon emplacement.

1.  Dans **tutorial/settings.py**, recherchez l'affectation de **STATIC_ROOT** et remplacez-le par :
    
         STATIC_ROOT = path.join(PROJECT_ROOT, 'static').replace('\\','/')

2.  Sous le dossier **polls**, créez un dossier nommé **static**.

3.  Sous le dossier **polls/static**, créez un dossier nommé **polls**.

4.  Sous le dossier **polls/static/polls**, créez un dossier nommé **images**.

5.  Ajoutez un nouveau fichier de feuille de style **polls/static/polls/style.css** avec le code suivant :
    
         body {
             color: darkblue;
             background: white url("images/background.jpg");
         }

6.  Ajoutez une image au dossier **polls/static/polls/images** et nommez-la **background.jpg**.

7.  Votre projet doit maintenant se composer des fichiers suivants :
    
    ![Explorateur de
    solutions](./media/cloud-services-python-create-deploy-django-app/django-tutorial-013-solution-explorer.png)

8.  Modifiez l'en-tête de tous les modèles pour qu'ils se réfèrent à la feuille de style, en utilisant le balisage suivant :
    
         <head>
         {% load staticfiles %}
         <link  rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
         </head>

9.  Exécutez de nouveau le site Web. Les pages d'index, de sondage et de résultats utilisent la feuille de style que vous venez de créer, avec du texte en bleu sombre et une image d'arrière-plan.
    
    ![Détails du
    sondage](./media/cloud-services-python-create-deploy-django-app/django-tutorial-014-detail-styled.png)

## <a name="debugging"></a>Débogage

Python Tools for Visual Studio prend en charge de façon spécifique le débogage des modèles Django.

1.  Ouvrez **polls/templates/polls/index.html** et placez un point d'arrêt en utilisant la touche **F9** sur la ligne :
    
         {% if latest_poll_list %}

2.  Démarrez le débogage en utilisant la touche **F5**. Visual Studio s'arrête dans le fichier de modèle.

3.  Ouvrez la **fenêtre Variables locales** à partir de **Débogage->Fenêtres->Variables locales** pour accéder à la variable **latest_poll_list** et à sa valeur.

4.  Vous pouvez appuyer sur **F10** pour vous déplacer, comme dans un code Python normal. Dans la boucle for, consultez la valeur de **poll** :
    
    ![Débogage](./media/cloud-services-python-create-deploy-django-app/django-tutorial-015-debugging.png)

## Création d'une base de données sur Azure

À présent que vous avez vérifié que votre application de sondage fonctionne en local, basculez-la vers une base de données hébergée sur Azure.

Dans les deux sections suivantes, vous allez voir comment utiliser les bases de données SQL et MySQL. Ces deux types de base de données sont des services hébergés.

Vous pouvez également créer une machine virtuelle et installer un serveur de base de données. Pour savoir comment configurer MySQL sur une machine virtuelle Linux Azure, consultez [cette page][6].

**Remarque :** vous pouvez utiliser une base de données sqlite3 sur
Azure (uniquement à des fins de développement, il est déconseillé de l'utiliser en production). Pour pouvoir déployer la base de données avec votre application Django, vous devez ajouter le fichier **db.sqlite3** à votre projet.

### Base de données SQL

Dans cette section, vous allez créer une base de données SQL sur Azure, ajouter les packages requis à votre environnement virtuel, puis modifier vos paramètres pour utiliser la nouvelle base de données.

1.  Dans le portail de gestion Azure, sélectionnez **BASES DE DONNÉES SQL**.

2.  Commencez par créer un serveur pour héberger la base de données. Sélectionnez **SERVEURS** puis **AJOUTER**.

3.  Vous pouvez voir votre adresse IP sous l'onglet **CONFIGURATION** du nouveau serveur. À côté de cette adresse, cliquez sur **ADD TO THE ALLOWED IP ADDRESSES**.
    
    **Remarque :** parfois, Azure ne détecte pas l'adresse IP cliente correctement. Si vous obtenez une erreur durant la synchronisation de la base de données, copiez/collez l'adresse IP à partir du message d'erreur et ajoutez-la à la liste des adresses IP autorisées.

4.  Ensuite, vous allez créer la base de données. Sous l'onglet **BASES DE DONNÉES**, cliquez sur **AJOUTER** dans la barre du bas.

5.  Dans Visual Studio, vous allez installer les packages requis pour accéder aux bases de données SQL Server depuis Django dans l'environnement virtuel.

6.  Cliquez avec le bouton droit sur **env**, puis sur **Install Python Package** : **pyodbc** utilise **easy_install**.

7.  Cliquez avec le bouton droit sur **env**, puis sur **Install Python Package** : **django-pyodbc-azure** utilise **pip**.

8.  Modifiez **tutorial/settings.py** et remplacez la définition de **BASE DE DONNÉES** par la suivante, en remplaçant les valeurs **NAME**, **USER**, **PASSWORD** et **HOST** par celles répertoriées dans le panneau de configuration ClearDB :
    
         DATABASES = {
             'default': {
                 'ENGINE': 'sql_server.pyodbc',
                 'NAME': '<database  name>',
                 'USER': '<user  name>@<server  name>',
                 'PASSWORD': '<user  password>',
                 'HOST': '<server  name>.database.windows.net',
                 'PORT': '',
                 'OPTIONS': {
                     'driver': 'SQL Server Native Client 11.0',
                     'MARS_Connection': True,
                 },
             }
         }
    
    Assurez-vous de bien utiliser un pilote installé sur votre machine. Ouvrez **Outils d'administration** depuis le menu Démarrer ou l'écran d'accueil, **Sources de données ODBC (32 bits)**. Les pilotes sont répertoriés sous l'onglet **Pilotes**.
    
    Si vous utilisez un site Web Azure, **SQL Server Native Client 10.0** et **SQL Server Native Client 11.0** fonctionneront.
    
    Si vous utilisez un service cloud Azure, seul **SQL Server Native Client 11.0** fonctionnera.

9.  Synchronisez la base de données et créez les informations d'identification d'administration, comme pour la base de données sqlite3 locale.

### Base de données MySQL

Dans cette section, vous allez créer une base de données MySQL sur Azure, ajouter les packages requis à votre environnement virtuel, puis modifier vos paramètres pour utiliser la nouvelle base de données.

Dans Azure Store, vous pouvez ajouter différents services à votre compte, notamment une base de données MySQL. Vous pouvez créer un compte d'évaluation gratuitement, et les petites bases de données sont gratuites jusqu'à une certaine taille.

1.  Dans le portail de gestion Azure, sélectionnez **NOUVEAU**->**STORE**->**APP SERVICES**->**Base de données MySQL ClearDB**. Créez une base de données avec plan Gratuit.

2.  Ensuite, installez les packages requis pour accéder aux bases de données MySQL depuis Django dans l'environnement virtuel. Cliquez avec le bouton droit sur **env**, puis sur **Install Python Package** : **mysql-python** utilise **easy_install**.

3.  Modifiez **tutorial/settings.py** et remplacez la définition de **BASE DE DONNÉES** par la suivante, en remplaçant les valeurs **NAME**, **USER**, **PASSWORD** et **HOST** par celles répertoriées dans le panneau de configuration ClearDB :
    
         DATABASES = {
             'default': {
                 'ENGINE': 'django.db.backends.mysql',
                 'NAME': '<database  name>',
                 'USER': '<user  name>',
                 'PASSWORD': '<user  password>',
                 'HOST': '<host  url>',
                 'PORT': '',
             }
         }

4.  Synchronisez la base de données et créez les informations d'identification d'administration, comme pour la base de données sqlite3 locale.

## Utilisation de l'interpréteur de commandes Django

1.  Cliquez avec le bouton droit sur le nœud du projet et sélectionnez **Django**->**Open Django Shell**.

2.  Cette fenêtre interactive vous permet d'accéder à vos bases de données en utilisant vos modèles. Entrez le code suivant pour ajouter un sondage à la base de données :
    
         from polls.models import Poll, Choice
         from django.utils import timezone
        
         p = Poll(question="Quelle est votre méthode favorite pour héberger Django sur Azure ?", pub_date=timezone.now())
         p.save()
        
         p.choice_set.create(choice_text='Site Web', votes=0)
         p.choice_set.create(choice_text='Service cloud', votes=0)
         p.choice_set.create(choice_text='Machine virtuelle', votes=0)
    
    ![Ajouter un sondage sur l'interpréteur de commandes
    Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-018-shell-add-poll.png)

3.  L'analyse statique des modèles offre une vue limitée de l'API complète. Dans la fenêtre interactive, vous pouvez appliquer IntelliSense aux objets en direct. Cette méthode est excellente pour explorer l'API. Voici quelques opérations à essayer dans la fenêtre interactive :

         # tous les objets du sondage
         Poll.objects.all()

         # clé primaire du sondage
         p.id

         # tous les objets choice du sondage
         p.choice_set.all()

         # obtention des objets par clé primaire
         Poll.objects.get(pk=1)
    
    ![Demander un sondage sur l'interpréteur de commandes
    Django](./media/cloud-services-python-create-deploy-django-app/django-tutorial-019-shell-query.png)

4.  Démarrez le site Web. Vous pouvez normalement voir le sondage que vous avez ajouté avec l'interpréteur de commandes Django.

## Publication dans Azure

Maintenant que notre base de données est dans Azure, la prochaine étape consiste à héberger le site Web dans Azure.

Azure propose plusieurs options pour héberger les applications Django :

* [Site Web][7]
* [Service cloud][8]
* [Machine virtuelle][9]

Python Tools for Visual Studio offre des capacités de publication dans Sites Web Azure et Cloud Services. Elles sont décrites dans les deux prochaines sections, pour vous permettre de faire votre choix.

Dans tous les cas, PTVS configure IIS pour vous, puis génère un fichier web.config si vous n'en avez pas déjà un dans votre projet. Les fichiers statiques sont collectés automatiquement (manage.py collectstatic) tant que STATIC_ROOT est défini dans votre settings.py.

Ce didacticiel ne traite pas de l'hébergement de Django dans une machine virtuelle. Pour cela, vous devez créer une machine virtuelle exécutant le système d'exploitation de votre choix (Windows ou Linux), installer Python et déployer manuellement l'application Django.

### Sites Web Azure

1.  Vous devez d'abord créer un site Web. Dans le portail de gestion Azure, cliquez sur **NOUVEAU**->**COMPUTE**->**SITE WEB**->**QUICK CREATE**. Sélectionnez un nom disponible.

2.  Une fois le site créé, téléchargez le profil de publication.
    
    ![Site Web : télécharger un
    profil](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-download-profile.png)

3.  Dans Visual Studio, cliquez avec le bouton droit sur le nœud du projet, puis sélectionnez **Publier**.
    
    ![Publier le site
    Web](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-publish.png)

4.  Importez le fichier de profil de publication de site Web téléchargé précédemment.

5.  Acceptez la configuration par défaut, puis cliquez sur **Publier** pour démarrer la publication.

6.  Une fois celle-ci terminée, un navigateur Web affiche le site Web publié.
    
    ![Navigateur du site
    Web](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website.png)

### Azure Cloud Services

1.  Cliquez avec le bouton droit sur le nœud du projet, puis sélectionnez **Add Azure Cloud Service Project** ou **Convert -> Convert to Azure Cloud Service Project** (vous pouvez voir l'une ou l'autre de ces options, en fonction de votre version de Visual Studio). Ceci ajoute à la solution un nouveau projet portant le suffixe .Azure. Ce nouveau projet est marqué comme étant le projet de démarrage de la solution.
    
    **Remarque :** si la commande permettant de créer le projet de service cloud Azure ne s'affiche pas dans le menu contextuel de votre projet, vous devez installer les outils Azure pour Visual Studio. Ils sont installés avec le Kit de développement logiciel (SDK) Azure pour .NET. Consultez la section Prérequis au début de ce didacticiel.
    
    ![Explorateur de
    solutions](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-solution-explorer.png)

#### Exécution dans l'émulateur Azure

1.  Vous devez **redémarrer Visual Studio en tant qu'administrateur** pour pouvoir exécuter l'émulateur de calcul.

2.  Démarrez le débogage en appuyant sur **F5**, puis l'application s'exécute et se déploie dans l'émulateur de calcul. Vérifiez que l'interface d'administration fonctionne et que vous pouvez voter dans les sondages.
    
    ![Émulateur de
    calcul](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-emulator.png)

3.  Vous pouvez maintenant redémarrer Visual Studio si vous ne voulez pas continuer l'exécution en tant qu'administrateur.

#### Publication dans un service cloud Azure

1.  Vous allez maintenant procéder à la publication dans un service cloud Azure. Cliquez avec le bouton droit sur le projet de service cloud **tutorial.Azure**, puis cliquez sur **Publier**.
    
    **Remarque :** veillez à sélectionner **Publier** sur le projet de service cloud. La boîte de dialogue **Publish Azure Application** s'affiche, permettant de publier dans un service cloud Azure. Si vous sélectionnez **Publish** sur le projet Django, la boîte de dialogue **Publier le site Web** s'affiche, permettant de publier dans un site Web Azure.
    
    ![Publier le service
    cloud](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish.png)

2.  Vous devez importer votre fichier d'abonnement Azure. Cliquez sur le lien [Sign in to download credentials][10] pour le télécharger depuis le portail Azure.

3.  Dans la page Paramètres, dans la zone de liste modifiable du service cloud, sélectionnez **Créer** pour créer un service cloud. Vous pouvez utiliser n'importe quel nom disponible.
    
    ![Paramètres du service
    cloud](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-settings.png)

4.  Acceptez la configuration par défaut, puis cliquez sur **Publier**. Cette opération dure plus longtemps que la publication dans un site Web, car le système doit configurer une machine virtuelle pour le service cloud. Vous pouvez afficher la progression de la publication dans la fenêtre Journal des activités Azure :
    
    ![Publier le service
    cloud](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish-progress.png)

5.  Une fois l'opération terminée, dans la fenêtre Journal des activités Azure, cliquez sur l'URL du site Web pour ouvrir un navigateur Web.
    
    ![Navigateur de service
    cloud](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice.png)

## Conclusion

Dans ce didacticiel, vous avez développé une application Django avec
[Python Tools for Visual Studio][3]. Vous avez utilisé 3 types de base
de données : sqlite3, SQL Server et MySQL. Finalement, vous avez publié l'application dans un site Web et un service cloud Azure.



[1]: http://www.youtube.com/watch?v=wkqjafvvU5w
[2]: https://www.djangoproject.com/
[3]: http://pytools.codeplex.com
[4]: http://www.python.org/download/
[5]: http://download-codeplex.sec.s-msft.com/Download?ProjectName=pytools&DownloadId=783376
[6]: http://www.windowsazure.com/fr-fr/manage/linux/common-tasks/mysql-on-a-linux-vm/
[7]: http://www.windowsazure.com/fr-fr/services/web-sites/
[8]: http://www.windowsazure.com/fr-fr/services/cloud-services/
[9]: http://www.windowsazure.com/fr-fr/services/virtual-machines/
[10]: https://manage.windowsazure.com/publishsettings/index?client=vs&schemaversion=2.0