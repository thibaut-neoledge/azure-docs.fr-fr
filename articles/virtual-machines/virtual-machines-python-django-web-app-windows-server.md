<properties 
	pageTitle="Didacticiel Application web Python avec Django - Azure" 
	description="Didacticiel qui explique comment héberger un site web Django dans Azure en utilisant une machine virtuelle Windows Server 2012 R2 Datacenter." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="huvalo"/>




# Application Web Django Hello World

<div class="dev-center-tutorial-selector sublanding"><a href="/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

Ce didacticiel explique comment héberger un site web Django dans Microsoft Azure en utilisant une machine virtuelle Windows Server. Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de ce guide, vous disposerez d'une application Django fonctionnelle dans le cloud.

Vous apprendrez à :

* Configurer une machine virtuelle Azure pour héberger Django. Ce didacticiel explique comment réaliser cette opération sous **Windows Server**, mais elle peut également être accomplie sur une machine virtuelle Linux hébergée dans Azure. 
* Créer une application Django à partir de Windows.

Dans ce didacticiel, vous allez créer une application Web Hello World simple. L'application sera hébergée sur une machine virtuelle Azure.

Voici une capture d’écran de l’application terminée :

![Fenêtre de navigateur affichant la page Hello World sous Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Création et configuration d'une machine virtuelle Azure pour héberger Django

1. Suivez les instructions disponibles [ici][portal-vm] pour créer une machine virtuelle Azure de la distribution *Windows Server 2012 R2 Datacenter*.

1. Indiquez à Azure d'acheminer le trafic du port **80** du Web vers le port **80** de la machine virtuelle :
 - Accédez à votre machine virtuelle nouvellement créée dans le portail Azure et cliquez sur l'onglet *ENDPOINTS*.
 - Cliquez sur le bouton *AJOUTER* au bas de l’écran. ![Ajouter un point de terminaison](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png)

 - Ouvrez le *PORT PUBLIC 80* du protocole *TCP* en tant que *PORT PRIVÉ 80*. ![][port80]
1. Dans l'onglet *TABLEAU DE BORD*, cliquez sur *CONNECTER* pour utiliser le *Bureau à distance* pour vous connecter à distance à la machine virtuelle Azure que vous venez de créer.  

**Remarque importante :** toutes les instructions ci-dessous partent du principe que vous vous êtes correctement connecté à la machine virtuelle et que vous émettez les commandes depuis celle-ci et non depuis votre ordinateur local.

## <a id="setup"> </a>Configuration de Python et de Django

**Remarque** : pour le téléchargement à l’aide d’Internet Explorer, vous devrez peut-être configurer les paramètres IE ESC (Démarrer/Outils d’administration/Gestionnaire de serveur/Serveur local, puis cliquez sur **Configuration de sécurité renforcée d’Internet Explorer** et désactivez cette option).

1. Installez [Web Platform Installer][].
1. Installez Python et WFastCGI au moyen de Web Platform Installer. Cela installe wfastcgi.py dans votre dossier de scripts Python.
	1. Démarrez Web Platform Installer.
	1. Tapez WFastCGI dans la barre de recherche. 
	1. Sélectionnez l'entrée WFactCGI pour la version de Python que vous voulez utiliser (2.7 ou 3.4). Remarque : cela installe Python comme dépendance de WFastCGI. 
1. Installez Django en utilisant pip.

    Python 2.7 :

        c:\python27\scripts\pip install django

    Python 3,4 :

        c:\python34\scripts\pip install django


## Configuration d'IIS avec FastCGI

1. Installez IIS avec FastCGI. Cela peut prendre quelques minutes.

		start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI


### Python 2,7

Exécutez ces commandes uniquement si vous exécutez Python 2.7.

1. Configurez le gestionnaire Python FastCGI.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"


1. Enregistrez le gestionnaire pour ce site.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Configurez le gestionnaire de manière à exécuter votre application Django.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost


1. Configurez PYTHONPATH de manière à permettre à l'interpréteur Python de trouver votre application Django.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost


1. Spécifiez le gestionnaire WSGI à utiliser par la passerelle FastCGI-WSGI.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost


1. Les éléments suivants doivent s’afficher :

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-27.png)

### Python 3.4

Exécutez ces commandes uniquement si vous exécutez Python 3,4.

1. Configurez le gestionnaire Python FastCGI.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py']"


1. Enregistrez le gestionnaire pour ce site.

		%windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python34\python.exe|C:\Python34\Scripts\wfastcgi.py',resourceType='Unspecified']"


1. Configurez le gestionnaire de manière à exécuter votre application Django.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='helloworld.settings']" /commit:apphost


1. Configurez PYTHONPATH de manière à permettre à l'interpréteur Python de trouver votre application Django.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\helloworld']" /commit:apphost


1. Spécifiez le gestionnaire WSGI à utiliser par la passerelle FastCGI-WSGI.

		%windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python34\python.exe', arguments='C:\Python34\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

1. Les éléments suivants doivent s’afficher :

	![IIS config1](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis-34.png)


## Création d'une application Django


1.  Dans *C:\inetpub\wwwroot*, entrez la commande suivante pour créer un projet Django :

    Python 2.7 :

		C:\Python27\Scripts\django-admin.exe startproject helloworld

    Python 3,4 :

		C:\Python34\Scripts\django-admin.exe startproject helloworld
    
	![Résultat de la commande New-AzureService](./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png)

1.  La commande **django-admin** crée une structure de base pour les sites web Django :
    
  -   **helloworld\manage.py** vous permet de lancer et d'interrompre l'hébergement de votre site web Django.
  -   **helloworld\helloworld\settings.py** contient les paramètres Django de votre application.
  -   **helloworld\helloworld\urls.py** contient le code de mappage entre chaque URL et sa vue.



1.  Créez un fichier nommé **views.py** dans le répertoire *C:\inetpub\wwwroot\helloworld\helloworld*. Celui-ci contiendra la vue qui affiche la page « Hello World ». Ouvrez votre éditeur et entrez ce qui suit :
		
		from django.http import HttpResponse
		def home(request):
    		html = "<html><body>Hello World!</body></html>"
    		return HttpResponse(html)

1.  Remplacez le contenu du fichier **urls.py** par ce qui suit :

		from django.conf.urls import patterns, url
		urlpatterns = patterns('',
			url(r'^$', 'helloworld.views.home', name='home'),
		)

1. Enfin, chargez la page Web dans votre navigateur.

![Fenêtre de navigateur affichant la page Hello World sous Azure][1]

## Arrêt de la machine virtuelle Azure

Lorsque vous avez terminé ce didacticiel, arrêtez et/ou supprimez votre machine virtuelle Azure nouvellement créée afin de libérer des ressources pour d'autres didacticiels et éviter l'ajout de charges d'utilisation Azure.

[1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png

[portal-vm]: /manage/windows/tutorials/virtual-machine-from-gallery/

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx

 

<!---HONumber=July15_HO1-->