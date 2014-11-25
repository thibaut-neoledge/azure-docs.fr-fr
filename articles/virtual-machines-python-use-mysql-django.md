<properties linkid="develop-python-web-app-with-django-and-mysql" urlDisplayName="Web with Django + MySQL" pageTitle="Python web app with Django and MySQL - Azure tutorial" metaKeywords="Azure django web app, Azure Django MySQL, Azure django Python" description="A tutorial that teaches you how to use MySQL in with Django on an Azure virtual machine. Code samples written in Python." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World - MySQL Windows Edition" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Édition Windows Django Hello World - MySQL

Ce didacticiel décrit comment utiliser MySQL en association avec Django sur une seule machine virtuelle Azure. Ce guide part du principe que vous avez une certaine expérience en tant qu'utilisateur d'Azure et de Django. Pour une introduction à Azure et Django, consultez la page [Django Hello World][Django Hello World]. Ce guide présume également que vous possédez quelques connaissances de MySQL. Pour une vue d'ensemble de MySQL, consultez le [site Web MySQL][site Web MySQL].

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   Configurer une machine virtuelle Azure pour héberger MySQL et Django. Bien que ce didacticiel explique comment réaliser cette opération sous Windows Server 2008 R2, elle peut également être réalisée sur une machine virtuelle Linux hébergée dans Azure.
-   Installer un [pilote MySQL][pilote MySQL] pour Python.
-   Configurer une application Django existante afin d'utiliser une base de données MySQL.
-   Utiliser MySQL directement depuis Python.
-   Héberger et exécuter votre application MySQL Django.

Vous développerez l'exemple [Django Hello World][Django Hello World] en utilisant une base de données MySQL, hébergée dans une machine virtuelle Azure, afin de trouver un remplacement intéressant à *World*. Le remplacement sera ensuite déterminé par l'intermédiaire d'une application *counter* Django MySQL. Comme c'était le cas pour l'exemple Hello World, l'application Django sera encore hébergée sur une machine virtuelle Azure.

Les fichiers de projet de ce didacticiel sont stockés dans **C:\\django\\helloworld** et l'application terminée ressemble à ce qui suit :

![][0]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Configuration d'une machine virtuelle pour héberger MySQL et Django

1.  Suivez les instructions se trouvant [ici][ici] pour créer une machine virtuelle Azure de la distribution *Windows Server 2008 R2*.

2.  Ouvrez un port TCP pour les transactions MySQL sur la machine virtuelle :

	-   Accédez à votre machine virtuelle nouvellement créée dans le portail Azure et cliquez sur l'onglet *ENDPOINTS*.
	-   Cliquez sur le bouton *ADD ENDPOINT* en bas de l'écran.
	-   Ajoutez un point de terminaison avec NAME = **mysql**, PROTOCOL = **TCP**, PUBLIC PORT = **3306**, PRIVATE PORT = **3306**.

1.  Ajoutez un autre point de terminaison avec NAME = **web**, PROTOCOL = **TCP**, PUBLIC PORT = **80**, PRIVATE PORT = **80**. Ainsi, les requêtes Internet externes sont redirigées vers le port Django actif, c'est-à-dire le port *80*.

2.  Utilisez le *Bureau à distance* Windows pour vous connecter à distance à la machine virtuelle Azure que vous venez de créer.

3.  Ouvrez le port TCP **80** sur la machine virtuelle :

	-   À partir du menu **Démarrer**, sélectionnez **Outils d'administration**, puis **Pare-feu Windows avec fonctions avancées de sécurité**.
	-   Dans le volet gauche, sélectionnez **Règles de trafic entrant**. Dans le volet **Actions** situé à droite, sélectionnez **Nouvelle règle...**.
	-   Dans l'**Assistant Nouvelle règle de trafic entrant**, sélectionnez **Port**, puis cliquez sur **Suivant**.
	-   Sélectionnez **TCP**, puis **Ports locaux spécifiques**. Spécifiez le numéro de port « 80 » (celui sur lequel Django écoute), puis cliquez sur **Suivant**.
	-   Sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.
	-   Cliquez à nouveau sur **Suivant**.
	-   Spécifiez un nom pour la règle, comme « DjangoPort », puis cliquez sur Terminer.

1.  Installez la version la plus récente de [MySQL Community Server][MySQL Community Server] pour Windows sur la machine virtuelle :

    **Remarque** : nous vous recommandons d'installer votre base de données sur une partition de données différente du système d'exploitation.

	-   Exécutez le lien *Windows (x86, 64 bits), Programme d'installation de MSI* [ici][MySQL Community Server] et téléchargez le programme d'installation de MSI approprié depuis le miroir de téléchargement le plus près de vous. Voici quelques conseils utiles :
	    -   Sélectionnez un type de configuration *complet*.
	    -   Sélectionnez une *configuration détaillée* dans l'Assistant Configuration.
	    -   **Assurez-vous que vous avez activé la gestion réseau TCP/IP sur le port 3306 et ajouté une exception de pare-feu pour le port.**
	    -   Définissez un mot de passe racine et activez un accès racine pour les machines à distance.
	-   Installez un exemple de [base de données "world"][base de données "world"] (version MyISAM) :
		    -   Téléchargez [ce][ce] fichier .zip sur la machine virtuelle Azure.
	    -   **Décompressez-le dans *C:\\Users\\Administrator\\Desktop\\world.sql*.**

1.  Après avoir installé MySQL, cliquez sur le menu *Démarrer* de Windows et exécutez le *client de ligne de commande MySQL 5.5*. Exécutez les commandes suivantes :

        CREATE DATABASE world;
        USE world;
        SOURCE C:\Users\Administrator\Desktop\world.sql
        CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
        CREATE DATABASE djangoazure;
        GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
        GRANT ALL ON world.* TO 'testazureuser'@'%';
        SELECT name from country LIMIT 1;

	Une réponse similaire à celle ci-dessous devrait désormais s'afficher :

  ![][2]

1.  Avant de pouvoir commencer à développer votre application Django, vous devez évidemment installer Python et Django sur la machine virtuelle. Pour ce faire, utilisez [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).. Après avoir installé Web PI, utilisez-le pour rechercher « Django » et installez le produit Django (Python).

	**Remarque :** il vous suffit simplement d'installer le produit *Django* depuis Web PI pour que ce didacticiel fonctionne. Vous **n'avez pas** besoin des *outils Python pour Visual Studio* ou même du Kit de développement logiciel (SDK) Azure Python pour atteindre l'objectif de ce didacticiel.

1.  Installez le package client MySQL Python. Vous pouvez l'installer directement [depuis ce lien][depuis ce lien]. Une fois terminé, exécutez la commande suivante pour vérifier votre installation :

  ![][1]

## Développement de l'application Django Hello World

1.  Suivez les instructions fournies dans le didacticiel [Django Hello World][Django Hello World] afin de créer une application Web « Hello World » dans Django.

2.  Ouvrez **C:\\django\\helloworld\\helloworld\\settings.py** dans votre éditeur de texte favori. Modifiez le dictionnaire global **DATABASES** pour lire :

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'djangoazure',               
                'USER': 'testazureuser',  
                'PASSWORD': 'testazure',
                'HOST': '127.0.0.1', 
                'PORT': '3306',
                },
            'world': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'world',               
                'USER': 'testazureuser',  
                'PASSWORD': 'testazure',
                'HOST': '127.0.0.1', 
                'PORT': '3306',
                }
            }

	Comme vous pouvez le constater, nous venons juste de donner à Django des instructions lui permettant de trouver notre base de données MySQL.

	**Remarque :** Vous **devez** modifier la clé *HOST* afin qu'elle corresponde à l'adresse IP **permanente** de votre machine virtuelle Azure (MySQL). À ce moment, la clé *HOST* doit être définie tel qu'indiqué par la commande Windows *ipconfig*.

	Une fois que vous avez modifié *HOST* pour qu'elle corresponde à l'adresse IP de la machine virtuelle MySQL, enregistrez ce fichier et fermez-le.

1.  Maintenant que nous avons référencé notre base de données *djangoazure*, faisons quelque chose d'utile avec ! À cette fin, nous allons créer un modèle pour une application triviale *counter*. Pour indiquer à Django de créer ce dernier, exécutez les commandes suivantes :

        cd C:\django\helloworld
        C:\Python27\python.exe manage.py startapp counter

	Si Django ne signale aucune sortie depuis la commande finale ci-dessus, il a réussi.

1.  Ajoutez le texte suivant à **C:\\django\\helloworld\\counter\\models.py** :

        class Counter(models.Model):
            count = models.IntegerField()
            def __unicode__(self):
                return u'%s' % (self.count)

	La seule chose que vous avez faite ici est de définir une sous-classe de la classe *Model* de Django nommée *Counter* avec un seul champ de nombres entiers, *count*. Le modèle counter trivial enregistre le nombre de correspondances avec notre application Django.

1.  Ensuite, nous faisons connaître à Django l'existence de *Counter* :

	-   Modifiez à nouveau **C:\\django\\helloworld\\helloworld\\settings.py**. Ajoutez *'counter'* au tuple *INSTALLED\_APPS*.
	-   Depuis l'invite de commandes, exécutez :
	
	            cd C:\django\helloworld
	            C:\Python27\python manage.py sql counter
	            C:\Python27\python manage.py syncdb
	
	    Ces commandes stockent le modèle *Counter* dans la base de données Django et génèrent une sortie similaire à ceci :
	
	        C:\django\helloworld> C:\Python27\python manage.py sql counter
	        BEGIN;
	        CREATE TABLE `counter_counter` (
	            `id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY,
	            `count` integer NOT NULL
	        )
	        ;
	        COMMIT;
	
	        C:\django\helloworld> C:\Python27\python manage.py syncdb
	        Creating tables ...
	        Creating table auth_permission
	        Creating table auth_group_permissions
	        Creating table auth_group
	        Creating table auth_user_user_permissions
	        Creating table auth_user_groups
	        Creating table auth_user
	        Creating table django_content_type
	        Creating table django_session
	        Creating table django_site
	        Creating table counter_counter
	
	        You just installed Django's auth system, which means you don't have any superusers defined.
	        Would you like to create one now? (yes/no): no
	        Installing custom SQL ...
	        Installing indexes ...
	        Installed 0 object(s) from 0 fixture(s)

1.  Remplacez le contenu de **C:\\django\\helloworld\\helloworld\\views.py**. La nouvelle implémentation de la fonction *hello* ci-dessous utilise notre modèle *Counter* en association avec un exemple de base de données distinct que nous avons installé précédemment, *world*, afin de générer un remplacement approprié de la chaîne « *World* » :

        from django.http import HttpResponse
        import django.db
        from counter.models import Counter

        def getCountry(intId):
            #Connect to the MySQL sample database 'world'
            cur = django.db.connections['world'].cursor()
            #Execute a trivial SQL query which returns the name of 
            #all countries contained in 'world'
            cur.execute("SELECT name from country")
            tmp = cur.fetchall()
            #Clean-up after ourselves
            cur.close()
            if intId >= len(tmp):
                return "countries exhausted"
            return tmp[intId][0]

        def hello(request):
            if len(Counter.objects.all())==0:
                #when the database corresponding to 'helloworld.counter' is 
                #initially empty...
                c = Counter(count=0)
            else:
                c = Counter.objects.all()[0]
                c.count += 1
            c.save()       
            world = getCountry(int(c.count))
            return HttpResponse("<html><body>Hello <em>" + world + "</em></body></html>")

## Déploiement et exécution de votre site Web Django

Remarque : les instructions suivantes indiquent comment exécuter Django dans un environnement de test. Pour l'exécuter en production, suivez les instructions de la section « Configuration d'IIS avec FastCGI » du didacticiel « Django Hello World ». L'utilisation du client Pare-feu Windows pour ouvrir le port 80 au trafic Internet sur la machine virtuelle Windows Server 2K8 R2 n'est pas nécessaire avec FastCGI.

1.  Passez à une fenêtre Windows PowerShell et saisissez les commandes suivantes pour déployer publiquement votre site Web Django :

        PS C:\django\helloworld> $ipPort = 
        PS C:\django\helloworld> $ipPort = [string]$ipPort.AddressList[1]
        PS C:\django\helloworld> $ipPort += ":80"
        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort

    Le paramètre **runserver** indique à Django d'exécuter notre site Web *helloworld* sur le port TCP *80*. Les résultats de cette commande doivent être similaires à ceci :

        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort
        Validating models...

        0 errors found
        Django version 1.4, using settings 'helloworld.settings'
        Development server is running at http://123.34.56.78:80
        Quit the server with CTRL-BREAK.

2.  Depuis votre navigateur Web local, ouvrez **http://*yourVmName*.cloudapp.net** (où *votreNomMV* est le nom que vous avez utilisé lors de l'étape de la création de la machine virtuelle). "Hello... !" !" devrait s'afficher comme indiqué dans la capture d'écran ci-dessous. Cela indique que Django est en cours d'exécution dans la machine virtuelle et fonctionne correctement.

    ![][5]

	Actualisez le navigateur Web plusieurs fois pour que le message *"Hello **\<pays abc\>**"* soit remplacé par *"Hello **\<un autre pays\>**"*.

1.  Pour arrêter l'hébergement du site Web par Django, passez simplement à la fenêtre PowerShell et appuyez sur **CTRL-C**.

## Arrêt de la machine virtuelle Azure

Lorsque vous avez terminé ce didacticiel, arrêtez et/ou supprimez votre machine virtuelle Azure nouvellement créée afin de libérer des ressources pour d'autres didacticiels et éviter l'ajout de charges d'utilisation Azure.

  [Django Hello World]: http://windowsazure.com/fr-fr/documentation/articles/virtual-machines-python-django-web-app-windows-server
  [site Web MySQL]: http://dev.mysql.com/doc/
  [pilote MySQL]: http://pypi.python.org/pypi/MySQL-python/1.2.3
  [ici]: /fr-fr/manage/windows/tutorials/virtual-machine-from-gallery/
  [MySQL Community Server]: http://dev.mysql.com/downloads/mysql/
  [base de données "world"]: http://dev.mysql.com/doc/index-other.html
  [ce]: http://downloads.mysql.com/docs/world.sql.zip
  [depuis ce lien]: http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=

[0]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png
[1]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png
[2]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png
[5]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png