<properties linkid="develop-python-web-app-with-django-mac" urlDisplayName="Web with Django" pageTitle="Python web app with Django on Mac - Azure tutorial" metaKeywords="" description="A tutorial that shows how to host a Django-based web site on Azure using a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application (mac-linux)" authors="" solutions="" manager="" editor="" />

Application Web Django Hello World (mac-linux)
==============================================

[Windows](/en-us/develop/python/tutorials/web-app-with-django/ "Windows")[Mac/Linux](/en-us/develop/python/tutorials/django-hello-world-(maclinux)/ "Mac/Linux")

Ce didacticiel explique comment héberger un site Web Django dans Azure à l'aide d'une machine virtuelle Linux. Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de ce guide, vous disposerez d'une application Django fonctionnelle dans le cloud.

Vous allez apprendre à :

-   Configurer une machine virtuelle Azure pour héberger Django. Ce didacticiel explique comment réaliser cette opération sous **Linux**, mais elle peut également être accomplie sur une machine virtuelle Windows Server hébergée sous Azure.
-   Créer une application Django à partir de Linux.

Dans ce didacticiel, vous allez créer une application Web Hello World simple. L'application sera hébergée sur une machine virtuelle Azure.

Voici une capture d'écran de l'application terminée :

![Fenêtre de navigateur affichant la page Hello World sous Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

Création et configuration d'une machine virtuelle Azure pour héberger Django
----------------------------------------------------------------------------

1.  Suivez les instructions disponibles [ici](/en-us/manage/linux/tutorials/virtual-machine-from-gallery/) pour créer une machine virtuelle Azure de distribution *Ubuntu Server 12.04*.

	**Remarque :** vous devez *uniquement* créer la machine virtuelle. Arrêtez-vous à la section intitulée *Connexion à la machine virtuelle une fois celle-ci créée*.

1.  Indiquez à Azure d'acheminer le trafic du port **80** du Web vers le port **80** de la machine virtuelle :
    -   Accédez à la machine virtuelle que vous venez de créer dans le portail Azure et cliquez sur l'onglet *POINTS DE TERMINAISON*.
    -   Cliquez sur le bouton *AJOUTER UN POINT DE TERMINAISON* en bas de l'écran. ![Ajouter un point de terminaison](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
    -   Ouvrez le *PORT PUBLIC 80* du protocole *TCP* en tant que *PORT PRIVÉ 80*. ![port80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

Configuration de l'environnement de développement
-------------------------------------------------

**Remarque :** si vous devez installer Python ou si vous souhaitez utiliser les bibliothèques clientes, consultez le [Guide d'installation de Python](../python-how-to-install/).

Python 2.7 est préinstallé sur la machine virtuelle Linux Ubuntu. En revanche, Apache et Django ne sont pas installés. Pour vous connecter à votre machine virtuelle et installer Apache et Django, procédez comme suit :

1.  Lancez une nouvelle fenêtre **Terminal**.

2.  Entrez la commande suivante pour vous connecter à votre machine virtuelle Azure :

        $ ssh yourusername@yourVmUrl

3.  Entrez les commandes suivantes pour installer Django :

        $ sudo apt-get install python-setuptools
        $ sudo easy_install django

4.  Entrez la commande suivante pour installer Apache avec mod-wsgi :

        $ sudo apt-get install apache2 libapache2-mod-wsgi

Création d'une application Django
---------------------------------

1.  Ouvrez la fenêtre **Terminal** de la section précédente pour utiliser le protocole SSH dans votre machine virtuelle.

2.  Entrez les commandes suivantes pour créer un projet Django :

    ![Résultat de la commande django-admin](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-dir.png)

    Le script **django-admin.py** génère une structure de base pour les sites Web Django : 
	- **manage.py** vous permet de lancer et d'interrompre l'hébergement de votre site Web Django 
	- **helloworld\\settings.py** contient les paramètres Django de votre application. 
	- **helloworld\\urls.py** contient le code de mappage entre chaque URL et sa vue

3.  Créez un fichier nommé **views.py** dans le sous-répertoire *helloworld* de *django\\helloworld*, en tant que jumeau de **urls.py**. Celui-ci contiendra la vue qui affiche la page « Hello World ». Ouvrez votre éditeur et entrez ce qui suit :

        from django.http import HttpResponse
            def hello(request):
            html = "<html><body>Hello World!</body></html>"
                return HttpResponse(html)

4.  Remplacez le contenu du fichier **urls.py** par ce qui suit :

        from django.conf.urls.defaults import patterns, include, url
        from helloworld.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

Déploiement et exécution de votre site Web Django
-------------------------------------------------

1.  Modifiez le fichier de configuration Apache **/etc/apache2/httpd.conf** et ajoutez ce qui suit, en remplaçant *username* par le nom d'utilisateur que vous avez spécifié lors de la création de la machine virtuelle :

        WSGIScriptAlias / /home/*username*/django/helloworld/helloworld/wsgi.py
        WSGIPythonPath /home/*username*/django/helloworld

        <Directory /home/*username*/django/helloworld/helloworld>
        <Files wsgi.py>
        Order deny,allow
        Allow from all
        </Files>
        </Directory>

2.  Redémarrez Apache en utilisant la commande suivante :

        $ sudo apachectl restart

3.  Enfin, chargez la page Web dans votre navigateur :

    ![Fenêtre de navigateur affichant la page Hello World sous Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

Arrêt de la machine virtuelle Azure
-----------------------------------

Lorsque vous avez terminé ce didacticiel, arrêtez et/ou supprimez votre machine virtuelle Azure nouvellement créée afin de libérer des ressources pour d'autres didacticiels et éviter l'ajout de charges d'utilisation Azure.

