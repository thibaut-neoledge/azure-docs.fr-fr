---
title: "Application web Python avec Django sur Linux | Microsoft Docs"
description: "Découvrez comment héberger une application web basée sur Django dans Azure à l&quot;aide d&quot;une machine virtuelle Linux."
services: virtual-machines-linux
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-resource-manager
ms.assetid: 00ad4c2c-4316-4f9a-913f-f7f49b158db7
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 11/17/2015
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 93add0f19bef64b236933a3d1754f1ecb5826545
ms.lasthandoff: 04/03/2017


---
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Application web Django Hello World sur une machine virtuelle Linux
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

Ce didacticiel explique comment héberger un site web Django dans Microsoft Azure à l’aide d’une machine virtuelle Linux. Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. À la fin de ce guide, vous disposerez d'une application Django fonctionnelle dans le cloud.

Vous apprendrez à :

* Configurer une machine virtuelle Azure pour héberger Django. Ce didacticiel explique comment réaliser cette opération sous **Linux**, mais elle peut également être accomplie sur une machine virtuelle Windows Server hébergée sous Azure. 
* Créer une application Django à partir de Linux.

Dans ce didacticiel, vous allez créer une application Web Hello World simple. L'application sera hébergée sur une machine virtuelle Azure.

Voici une capture d’écran de l’application terminée :

![Fenêtre de navigateur affichant la page Hello World sous Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Création et configuration d'une machine virtuelle Azure pour héberger Django
1. Suivez les instructions fournies [ici](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour créer une machine virtuelle Azure de la distribution *Ubuntu Server 14.04 LTS*.  Si vous préférez, vous pouvez choisir l'authentification par de mot de passe au lieu d’utiliser la clé publique SSH.
2. Modifiez le groupe de sécurité réseau pour autoriser le trafic http entrant vers le port 80 en utilisant les instructions [présentes ici](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md).
3. Par défaut, votre nouvelle machine virtuelle n'a pas de nom de domaine complet (FQDN).  Vous pouvez en créer un en suivant les instructions indiquées [ici](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  Cette étape est facultative pour la suite de ce didacticiel.

## <a id="setup"> </a>Configuration de l’environnement de développement
**Remarque** : si vous devez installer Python ou que vous souhaitez utiliser les bibliothèques clientes, consultez l’article [Installation de Python](../../python-how-to-install.md).

Python 2.7 est préinstallé sur la machine virtuelle Linux Ubuntu. En revanche, Apache et Django ne sont pas installés.  Pour vous connecter à votre machine virtuelle et installer Apache et Django, procédez comme suit :

1. Lancez une nouvelle fenêtre **Terminal** .
2. Entrez la commande suivante pour vous connecter à votre machine virtuelle Azure :  Si vous n’avez pas créé de nom de domaine complet (FQDN), vous pouvez vous connecter à l’aide de l’adresse IP publique affichée dans le résumé de la machine virtuelle, dans le portail Azure Classic.
   
       $ ssh yourusername@yourVmUrl
3. Entrez les commandes suivantes pour installer Django :
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. Entrez la commande suivante pour installer Apache avec mod-wsgi :
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="creating-a-new-django-application"></a>Création d'une application Django
1. Ouvrez la fenêtre **Terminal** de la section précédente pour utiliser le protocole SSH dans votre machine virtuelle.
2. Entrez les commandes suivantes pour créer un projet Django :
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   Le script **django-admin.py** génère une structure de base pour les sites Web Django :
   
   * **helloworld/manage.py** vous permet de lancer et d'interrompre l'hébergement de votre site Web Django.
   * **helloworld/helloworld/settings.py** contient les paramètres Django de votre application.
   * **helloworld/helloworld/urls.py** contient le code de mappage entre chaque URL et sa vue.
3. Créez un fichier nommé **views.py** dans le répertoire **/var/www/helloworld/helloworld**. Celui-ci contiendra la vue qui affiche la page « Hello World ». Ouvrez votre éditeur et entrez ce qui suit :
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Remplacez le contenu du fichier **urls.py** par ce qui suit :
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="setting-up-apache"></a>Configuration d'Apache
1. Créez un fichier de configuration d'hôte virtuel Apache **/etc/apache2/sites-available/helloworld.conf**. Configurez le contenu avec les valeurs suivantes, et remplacez *yourVmName* par le nom réel de la machine utilisée (par exemple *pyubuntu*).
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. Activez le site en exécutant la commande suivante :
   
       $ sudo a2ensite helloworld
3. Redémarrez Apache en utilisant la commande suivante :
   
       $ sudo service apache2 reload
4. Enfin, chargez la page Web dans votre navigateur :
   
   ![Fenêtre de navigateur affichant la page Hello World sous Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shutting-down-your-azure-virtual-machine"></a>Arrêt de la machine virtuelle Azure
Lorsque vous avez terminé ce didacticiel, arrêtez et/ou supprimez votre machine virtuelle Azure nouvellement créée afin de libérer des ressources pour d'autres didacticiels et éviter l'ajout de charges d'utilisation Azure.


