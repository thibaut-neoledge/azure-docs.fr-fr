---
title: Application web Django sur une machine virtuelle Windows Server Azure | Microsoft Docs
description: "Découvrez comment héberger un site web Django dans Azure avec une machine virtuelle Windows Server 2012 R2 Datacenter à l’aide du modèle de déploiement classique."
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 01fa162d41e03e29f3b6f0ca128e7cc49aa91abb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2017
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Application web Django Hello World sur une machine virtuelle Windows Server

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Azure Resource Manager et le modèle de déploiement classique](../../../resource-manager-deployment-model.md). Cet article décrit le modèle de déploiement classique. Pour la plupart des nouveaux déploiements, nous recommandons d’utiliser le modèle Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ce didacticiel vous montre comment héberger un site web Django dans Windows Server dans des machines virtuelles Azure. Dans le didacticiel, nous ne supposons aucune expérience préalable avec Azure. Quand vous avez terminé le didacticiel, vous pouvez avoir une application basée sur Django opérationnelle dans le cloud.

Découvrez comment :

* Configurez une machine virtuelle Azure pour héberger Django. Bien que ce didacticiel explique comment procéder pour **Windows Server**, vous pouvez faire de même pour une machine virtuelle Linux hébergée dans Azure.
* Créez une application Django dans Windows.

Le didacticiel vous montre comment créer une application web Hello World de base. L’application est hébergée sur une machine virtuelle Azure.

La capture d’écran suivante présente l’application terminée :

![Fenêtre de navigateur affichant la page Hello World dans Azure][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Créer et configurer une machine virtuelle Azure pour héberger Django

1. Pour créer une machine virtuelle Azure avec la distribution Windows Server 2012 R2 Datacenter, consultez [Créer une machine virtuelle exécutant Windows dans le portail Azure](tutorial.md).
2. Configurez Azure pour diriger le trafic du port 80 à partir du web vers le port 80 de la machine virtuelle :
   
   1. Dans le portail Azure, accédez au tableau de bord et sélectionnez votre machine virtuelle qui vient d’être créée.
   2. Cliquez sur **Points de terminaison**, puis sur **Ajouter**.

     ![Ajout d’un point de terminaison](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. Dans la page **Ajouter un point de terminaison**, pour **Nom**, entrez **HTTP**. Affectez aux ports TCP public et privé la valeur **80**.

     ![Entrez le nom et définissez les ports public et privé](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. Cliquez sur **OK**.
     
3. Dans le tableau de bord, sélectionnez votre machine virtuelle. Pour utiliser le protocole RDP (Remote Desktop Protocol) afin de vous connecter à distance à la machine virtuelle Azure que vous venez de créer, cliquez sur **Connecter**.  

> [!IMPORTANT] 
> Les instructions suivantes supposent que vous êtes connecté correctement à la machine virtuelle. Elles supposent également que vous émettez des commandes sur la machine virtuelle et non sur votre ordinateur local.

## <a id="setup"></a>Installer Python, Django et WFastCGI
> [!NOTE]
> Pour un téléchargement à l’aide d’Internet Explorer, vous devrez peut-être configurer les paramètres **Configuration de sécurité renforcée** d’Internet Explorer. Pour ce faire, cliquez sur **Démarrer** > **Outils d’administration** > **Gestionnaire de serveur** > **Serveur local**. Cliquez sur **Configuration de sécurité renforcée d’Internet Explorer**, puis sélectionnez **Désactivé**.

1. Installez les dernières versions de Python 2.7 ou Python 3.4 à partir de [python.org][python.org].
2. Installez les packages wfastcgi et django packages à l'aide de pip.
   
    Pour Python 2.7, utilisez la commande suivante :
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Pour Python 3.4, utilisez la commande suivante :
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>Installer IIS avec FastCGI
* Installez les services Internet (IIS, Internet Information Services) avec prise en charge de FastCGI. Cela peut prendre quelques minutes.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>Créer une application Django
1. Dans C:\inetpub\wwwroot, entrez la commande suivante pour créer un projet Django :
   
   Pour Python 2.7, utilisez la commande suivante :
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Pour Python 3.4, utilisez la commande suivante :
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Résultat de la commande New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. La commande `django-admin` génère une structure de base pour les sites web Django :
   
   * `helloworld\manage.py` vous permet de lancer et d’interrompre l’hébergement de votre site web Django.
   * `helloworld\helloworld\settings.py` contient les paramètres Django de votre application.
   * `helloworld\helloworld\urls.py` contient le code de mappage entre chaque URL et sa vue.
3. Créez un fichier nommé views.py dans le répertoire C:\inetpub\wwwroot\helloworld\helloworld. Celui-ci contient la vue qui affiche la page « Hello World ». Dans votre éditeur de code, entrez les commandes suivantes :
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Remplacez le contenu du fichier urls.py par les commandes suivantes :
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>Configurer IIS
1. Déverrouillez la section des gestionnaires dans le fichier applicationhost.config global.  Cela permet l’utilisation du gestionnaire Python dans votre fichier web.config. Ajoutez cette commande :
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Activez WFastCGI. Cela ajoute une application au fichier applicationhost.config global qui fait référence à votre interpréteur Python exécutable et au script wfastcgi.py.
   
    Dans Python 2.7 :
   
        C:\python27\scripts\wfastcgi-enable
   
    Dans Python 3.4 :
   
        C:\python34\scripts\wfastcgi-enable
3. Créez un fichier web.config dans C:\inetpub\wwwroot\helloworld. La valeur de l’attribut `scriptProcessor` doit correspondre à la sortie de l’étape précédente. Pour plus d’informations sur le paramètre wfastcgi, consultez [wfastcgi sur Pypi][wfastcgi].
   
   Dans Python 2.7 :
   
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
   
   Dans Python 3.4 :
   
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
4. Mettez à jour l’emplacement du site web IIS par défaut pour qu’il pointe vers le dossier de projet Django :
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Chargez la page web dans votre navigateur.

![Fenêtre de navigateur affichant la page Hello World dans Azure][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Arrêter votre machine virtuelle Azure
À l’issue de ce didacticiel, nous vous recommandons d’arrêter ou de supprimer la machine virtuelle Azure que vous avez créée pour le suivre. Cela a pour effet de libérer des ressources pour d’autres didacticiels et d’éviter de vous exposer à des frais pour l’utilisation d’Azure.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
