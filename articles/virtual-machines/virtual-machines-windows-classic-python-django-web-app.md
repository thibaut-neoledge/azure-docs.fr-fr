---
title: Application web Python avec Django | Microsoft Docs
description: Ce didacticiel vous explique comment héberger un site web Django dans Azure avec une machine virtuelle Windows Server 2012 R2 Datacenter à l’aide du modèle de déploiement classique.
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 08/04/2015
ms.author: huvalo

---
# Application web Django Hello World sur une machine virtuelle Windows Server
> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-classic-python-django-web-app.md)
> * [Mac/Linux](virtual-machines-linux-python-django-web-app.md)
> 
> 

<br>

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Ce didacticiel explique comment héberger un site web Django dans Microsoft Azure en utilisant une machine virtuelle Windows Server. Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de ce didacticiel, vous disposerez d’une application Django fonctionnelle dans le cloud.

Vous apprendrez à :

* Configurez une machine virtuelle Azure pour héberger Django. Ce didacticiel explique comment réaliser cette opération sous Windows Server, mais elle peut également être accomplie sur une machine virtuelle Linux hébergée dans Azure.
* Créer une application Django à partir de Windows.

Dans ce didacticiel, vous allez créer une application Web Hello World simple. L'application sera hébergée sur une machine virtuelle Azure.

Une capture d’écran de l’application terminée va s’afficher.

![Fenêtre de navigateur affichant la page Hello World sous Azure][1]

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Création et configuration d'une machine virtuelle Azure pour héberger Django
1. Suivez les instructions disponibles [ici](virtual-machines-windows-classic-tutorial.md) pour créer une machine virtuelle Azure de la distribution Windows Server 2012 R2 Datacenter.
2. Indiquez à Azure de diriger le trafic du port 80 à partir du web vers le port 80 de la machine virtuelle :
   
   * Accédez à votre machine virtuelle nouvellement créée dans le portail Azure Classic et cliquez sur l’onglet **POINTS DE TERMINAISON**.
   * Cliquez sur le bouton **AJOUTER** au bas de l’écran. ![Ajouter un point de terminaison](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)
   * Ouvrez le **PORT PUBLIC 80** du protocole **TCP** en tant que **PORT PRIVÉ 80**. ![][port80]
3. Dans l'onglet **TABLEAU DE BORD**, cliquez sur **CONNECTER** pour utiliser le **Bureau à distance** pour vous connecter à distance à la machine virtuelle Azure que vous venez de créer.

**Remarque importante :** toutes les instructions ci-dessous partent du principe que vous vous êtes correctement connecté à la machine virtuelle et que vous émettez les commandes depuis celle-ci et non depuis votre ordinateur local.

## <a id="setup"> </a>Installation de Python, Django et WFastCGI
**Remarque** : pour le téléchargement à l’aide d’Internet Explorer, vous devrez peut-être configurer les paramètres IE ESC (Démarrer/Outils d’administration/Gestionnaire de serveur/Serveur local, puis cliquez sur **Configuration de sécurité renforcée d’Internet Explorer** et désactivez cette option).

1. Installez la dernière version de Python 2.7 ou 3.4 depuis [python.org][python.org].
2. Installez les packages wfastcgi et django packages à l'aide de pip.
   
    Pour Python 2.7, utilisez la commande suivante.
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Pour Python 3.4, utilisez la commande suivante.
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## Installation d'IIS avec FastCGI
1. Installez IIS avec FastCGI. Cela peut prendre quelques minutes.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## Création d'une application Django
1. Dans *C:\\inetpub\\wwwroot*, entrez la commande suivante pour créer un projet Django :
   
   Pour Python 2.7, utilisez la commande suivante.
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Pour Python 3.4, utilisez la commande suivante.
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Résultat de la commande New-AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. La commande **django-admin** crée une structure de base pour les sites web Django :
   
   * **helloworld\\manage.py** vous permet de lancer et d'interrompre l'hébergement de votre site web Django.
   * **helloworld\\helloworld\\settings.py** contient les paramètres Django de votre application.
   * **helloworld\\helloworld\\urls.py** contient le code de mappage entre chaque URL et sa vue.
3. Créez un fichier nommé **views.py** dans le répertoire *C:\\inetpub\\wwwroot\\helloworld\\helloworld*. Celui-ci contiendra la vue qui affiche la page « Hello World ». Ouvrez votre éditeur et entrez ce qui suit :
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Remplacez le contenu du fichier urls.py par ce qui suit :
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## Configuration d'IIS
1. Déverrouillez la section des gestionnaires dans le fichier applicationhost.config global. Cela permet l'utilisation du gestionnaire python dans votre fichier web.config.
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Activez WFastCGI. Cela ajoute une application au fichier applicationhost.config global qui fait référence à votre interpréteur Python exécutable et au script wfastcgi.py.
   
    Python 2.7 :
   
        c:\python27\scripts\wfastcgi-enable
   
    Python 3,4 :
   
        c:\python34\scripts\wfastcgi-enable
3. Créez un fichier web.config dans *C:\\inetpub\\wwwroot\\helloworld*. La valeur de l'attribut `scriptProcessor` doit correspondre à la sortie de l'étape précédente. Consultez la page sur [wfastcgi][wfastcgi] sur PyPi pour en savoir plus sur les paramètres wfastcgi.
   
    Python 2.7 :
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
    Python 3,4 :
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. Mettez à jour l'emplacement du site Web IIS par défaut pour qu'il pointe vers le dossier de projet Django.
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Enfin, chargez la page Web dans votre navigateur.

![Fenêtre de navigateur affichant la page Hello World sous Azure][1]

## Arrêt de la machine virtuelle Azure
Lorsque vous avez terminé ce didacticiel, arrêtez et/ou supprimez votre machine virtuelle Azure nouvellement créée afin de libérer des ressources pour d’autres didacticiels et éviter l’ajout de charges d’utilisation Azure.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

<!---HONumber=AcomDC_0629_2016-->