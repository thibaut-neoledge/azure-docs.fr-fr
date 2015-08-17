<properties 
	pageTitle="Didacticiel Application web Python avec Django sur Mac - Azure" 
	description="Didacticiel qui explique comment héberger un site web Django dans Azure à l'aide d'une machine virtuelle Linux." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="huvalo"/>





# Application Web Django Hello World (mac-linux)

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="Mac/Linux" class="current">Mac/Linux</a></div>

Ce didacticiel explique comment héberger un site web Django dans Microsoft Azure à l’aide d’une machine virtuelle Linux. Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de ce guide, vous disposerez d'une application Django fonctionnelle dans le cloud.

Vous apprendrez à :

* Configurer une machine virtuelle Azure pour héberger Django. Ce didacticiel explique comment réaliser cette opération sous **Linux**, mais elle peut également être accomplie sur une machine virtuelle Windows Server hébergée sous Azure. 
* Créer une application Django à partir de Linux.

Dans ce didacticiel, vous allez créer une application Web Hello World simple. L'application sera hébergée sur une machine virtuelle Azure.

Voici une capture d’écran de l’application terminée :

![Fenêtre de navigateur affichant la page Hello World sous Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Création et configuration d'une machine virtuelle Azure pour héberger Django

1. Suivez les instructions fournies [ici][portal-vm] pour créer une machine virtuelle Azure de la distribution *Ubuntu Server 14.04 LTS*.

  **Remarque :** vous devez *uniquement* créer la machine virtuelle. Arrêtez-vous à la section intitulée *Connexion à la machine virtuelle une fois celle-ci créée*.

1. Indiquez à Azure d'acheminer le trafic du port **80** du Web vers le port **80** de la machine virtuelle :
	* Accédez à votre machine virtuelle nouvellement créée dans le portail Azure et cliquez sur l'onglet *ENDPOINTS*.
	* Cliquez sur le bouton *AJOUTER* au bas de l’écran. ![Ajouter un point de terminaison](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-add-endpoint.png)
	* Ouvrez le *PORT PUBLIC 80* du protocole *TCP* en tant que *PORT PRIVÉ 80*. ![port80](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-port80.png)

## <a id="setup"> </a>Configuration de l’environnement de développement

**Remarque** : si vous devez installer Python ou que vous souhaitez utiliser les bibliothèques clientes, consultez l’article [Installation de Python](../python-how-to-install.md).

Python 2.7 est préinstallé sur la machine virtuelle Linux Ubuntu. En revanche, Apache et Django ne sont pas installés. Pour vous connecter à votre machine virtuelle et installer Apache et Django, procédez comme suit :

1.  Lancez une nouvelle fenêtre **Terminal**.
    
1.  Entrez la commande suivante pour vous connecter à votre machine virtuelle Azure :

		$ ssh yourusername@yourVmUrl

1.  Entrez les commandes suivantes pour installer Django :

		$ sudo apt-get install python-setuptools
		$ sudo easy_install django

1.  Entrez la commande suivante pour installer Apache avec mod-wsgi :

		$ sudo apt-get install apache2 libapache2-mod-wsgi


## Création d'une application Django

1.  Ouvrez la fenêtre **Terminal** de la section précédente pour utiliser le protocole SSH dans votre machine virtuelle.
    
1.  Entrez les commandes suivantes pour créer un projet Django :

		$ cd /var/www
		$ sudo django-admin.py startproject helloworld

    Le script **django-admin.py** génère une structure de base pour les sites web Django : - **helloworld/manage.py** vous permet de démarrer et d’arrêter l’hébergement de votre site web Django - **helloworld/helloworld/settings.py** contient les paramètres Django de votre application. - **helloworld/helloworld/urls.py** contient le code de mappage entre chaque URL et sa vue.

1.  Créez un fichier nommé **views.py** dans le répertoire **/var/www/helloworld/helloworld**. Celui-ci contiendra la vue qui affiche la page « Hello World ». Ouvrez votre éditeur et entrez ce qui suit :
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Remplacez le contenu du fichier **urls.py** par ce qui suit :

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)


## Configuration d'Apache

1.  Créez un fichier de configuration d'hôte virtuel Apache **/etc/apache2/sites-available/helloworld.conf**. Configurez le contenu avec les valeurs suivantes, et veillez à remplacer *yourVmUrl* par l'URL réelle de la machine utilisée (par exemple *pyubuntu.cloudapp.net*).

		<VirtualHost *:80>
		ServerName yourVmUrl
		</VirtualHost>
		WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
		WSGIPythonPath /var/www/helloworld

1.  Activez le site en exécutant la commande suivante :

        $ sudo a2ensite helloworld

1.  Redémarrez Apache en utilisant la commande suivante :

        $ sudo service apache2 reload

1.  Enfin, chargez la page Web dans votre navigateur :

	![Fenêtre de navigateur affichant la page Hello World sous Azure](./media/virtual-machines-python-django-web-app-linux/mac-linux-django-helloworld-browser.png)


## Arrêt de la machine virtuelle Azure

Lorsque vous avez terminé ce didacticiel, arrêtez et/ou supprimez votre machine virtuelle Azure nouvellement créée afin de libérer des ressources pour d'autres didacticiels et éviter l'ajout de charges d'utilisation Azure.


[portal-vm]: /manage/linux/tutorials/virtual-machine-from-gallery/
 

<!---HONumber=August15_HO6-->