<properties linkid="develop-python-web-app-with-django" urlDisplayName="Web with Django (Windows)" pageTitle="Python web app with Django - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to host a Django-based website on Azure using a Windows Server 2008 R2 virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World Web Application" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Application Web Django Hello World

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/develop/python/tutorials/web-app-with-django/" title="Windows" class="current">Windows</a><a href="/fr-fr/develop/python/tutorials/django-hello-world-(maclinux)/" title="MacLinux">Mac/Linux</a></div>

Ce didacticiel explique comment héberger un site Web Django dans
Azure à l'aide d'une machine virtuelle Windows Server. Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de ce guide, vous disposerez d'une application Django fonctionnelle dans le cloud.

Vous apprendrez à :

-   Configurer une machine virtuelle Azure pour héberger Django. Ce didacticiel explique comment réaliser cette opération sous **Windows Server**, mais elle peut également être accomplie sur une machine virtuelle Linux hébergée dans Azure.
-   Créer une application Django à partir de Windows.

Dans ce didacticiel, vous allez créer une application
Web Hello World simple. L'application sera hébergée sur une machine virtuelle Azure.

Voici une capture d'écran de l'application terminée :

![Fenêtre de navigateur affichant la page Hello World sous Azure][Fenêtre de navigateur affichant la page Hello World sous Azure]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Création et configuration d'une machine virtuelle Azure pour héberger Django

1.  Suivez les instructions disponibles [ici][ici] pour créer une machine virtuelle Azure de distribution *Windows Server 2012 Datacenter*.

2.  Indiquez à Azure d'acheminer le trafic du port **80** du Web vers le port **80** de la machine virtuelle :

	-   Accédez à votre machine virtuelle nouvellement créée dans le portail Azure et cliquez sur l'onglet *ENDPOINTS*.
	-   Cliquez sur le bouton *ADD ENDPOINT* en bas de l'écran.
    	![Ajouter un point de terminaison][Ajouter un point de terminaison]

	-   Ouvrez le *PORT PUBLIC 80* du protocole *TCP* en tant que *PORT PRIVÉ 80*.
    	![][0]

1.  Utilisez le *Bureau à distance* Windows pour vous connecter à distance à la machine virtuelle Azure que vous venez de créer.

**Remarque importante :** toutes les instructions ci-dessous partent du principe que vous vous êtes correctement connecté à la machine virtuelle et que vous émettez les commandes depuis celle-ci et non depuis votre ordinateur local.

## <span id="setup"></span> </a>Configuration de l'environnement de développement

Pour plus d'informations sur la configuration des environnements Python et Django, consultez le [Guide d'installation][Guide d'installation].

**Remarque 1 :** sur la machine virtuelle Azure, vous devez *uniquement* installer le produit **Django** à partir de Windows WebPI pour que *ce* didacticiel fonctionne.

**Remarque 2 :** pour télécharger WebPI Installer, vous serez peut-être amené à paramétrer la Configuration de sécurité renforcée d'Internet Explorer (sélectionnez Démarrer/Outils d'administration/Gestionnaire de serveur, puis cliquez sur **Paramétrer la Configuration de sécurité renforcée d'Internet Explorer** et désactivez l'option).

## Configuration d'IIS avec FastCGI

1.  Installez IIS avec FastCGI.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

2.  Configurez le gestionnaire Python FastCGI.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI "/+[fullPath='c:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py']"

3.  Enregistrez le gestionnaire pour ce site.

        %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers "/+[name='Python_via_FastCGI',path='*',verb='*',modules='FastCgiModule',scriptProcessor='c:\Python27\python.exe|C:\Python27\Scripts\wfastcgi.py',resourceType='Unspecified']"

4.  Configurez le gestionnaire de manière à exécuter votre application Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='DJANGO_SETTINGS_MODULE',value='DjangoApplication.settings']" /commit:apphost

5.  Configurez PYTHONPATH de manière à permettre à l'interpréteur Python de trouver votre application Django.

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='PYTHONPATH',value='C:\inetpub\wwwroot\DjangoApplication']" /commit:apphost

    Les éléments suivants doivent s'afficher :

    ![IIS config1][IIS config1]

6.  Spécifiez le gestionnaire WSGI à utiliser par la passerelle FastCGI-WSGI :

        %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='C:\Python27\python.exe', arguments='C:\Python27\Scripts\wfastcgi.py'].environmentVariables.[name='WSGI_HANDLER',value='django.core.handlers.wsgi.WSGIHandler()']" /commit:apphost

7.  Téléchargez le fichier wfastcgi.py depuis [codeplex][codeplex] et enregistrez-le sous C:\\Python27\\Scripts. Il s'agit de l'emplacement des commandes précédemment utilisées pour enregistrer le gestionnaire FastCGI. Vous pouvez également l'installer via Web Platform Installer. Recherchez « WFastCGI ».

## Création d'une application Django

1.  Démarrez cmd.exe.

2.  Accédez à C:\\inetpub\\wwwroot (via la commande cd).

3.  Entrez la commande suivante pour créer un projet Django :

    C:\\Python27\\python.exe -m django.bin.django-admin startproject DjangoApplication

    ![Résultat de la commande New-AzureService][Résultat de la commande New-AzureService]

	Le script **django-admin.py** génère une structure de base pour les sites Web Django :

-   **manage.py** vous permet de lancer et d'interrompre l'hébergement de votre site Web Django.
-   **DjangoApplication\\settings.py** contient les paramètres Django de votre application.
-   **DjangoApplication\\urls.py** contient le code de mappage entre chaque URL et sa vue.

1.  Créez un fichier nommé **views.py** dans le sous-répertoire *DjangoApplication* de *C:\\inetpub\\wwwroot\\DjangoApplication*, en tant que jumeau de **urls.py**. Celui-ci contiendra la vue qui affiche la page « Hello World ». Ouvrez votre éditeur et entrez ce qui suit :

        from django.http import HttpResponse
        def hello(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

2.  Remplacez le contenu du fichier **urls.py** par ce qui suit :

        from django.conf.urls.defaults import patterns, include, url
        from DjangoApplication.views import hello
        urlpatterns = patterns('',
            (r'^$',hello),
        )

3.  Enfin, chargez la page Web dans votre navigateur.

![Fenêtre de navigateur affichant la page Hello World sous Azure][Fenêtre de navigateur affichant la page Hello World sous Azure]

## Arrêt de la machine virtuelle Azure

Lorsque vous avez terminé ce didacticiel, arrêtez et/ou supprimez votre machine virtuelle Azure nouvellement créée afin de libérer des ressources pour d'autres didacticiels et éviter l'ajout de charges d'utilisation Azure.

  [Fenêtre de navigateur affichant la page Hello World sous Azure]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-browser-azure.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [ici]: /fr-fr/manage/windows/tutorials/virtual-machine-from-gallery/
  [Ajouter un point de terminaison]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-addendpoint.png
  [0]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-port80.png
  [Guide d'installation]: ../python-how-to-install/
  [IIS config1]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-iis1.png
  [codeplex]: http://go.microsoft.com/fwlink/?LinkID=316392&clcid=0x409
  [Résultat de la commande New-AzureService]: ./media/virtual-machines-python-django-web-app-windows-server/django-helloworld-cmd-new-azure-service.png
