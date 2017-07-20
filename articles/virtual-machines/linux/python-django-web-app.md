---
title: Application web Python avec Django sur une machine virtuelle Linux Azure | Microsoft Docs
description: "Découvrez comment héberger une application web basée sur Django dans Azure à l’aide d’une machine virtuelle Linux."
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
ms.date: 05/31/2017
ms.author: huvalo
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 6e2ab8c7da7496d0e2b567a4bdc9341adcf01552
ms.contentlocale: fr-fr
ms.lasthandoff: 06/22/2017


---
# <a name="django-hello-world-web-app-on-a-linux-vm"></a>Application web Django Hello World sur une machine virtuelle Linux
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

Ce didacticiel vous montre comment héberger un site web basé sur Django dans Linux dans des machines virtuelles Azure. Dans le didacticiel, nous ne supposons aucune expérience préalable avec Azure. Lorsque vous avez terminé le didacticiel, vous pouvez avoir une application basée sur Django opérationnelle dans le cloud.

Découvrez comment :

* Configurez une machine virtuelle Azure pour héberger Django. Bien que ce didacticiel explique comment procéder pour **Linux**, vous pouvez faire de même pour une machine virtuelle Windows Server hébergée dans Azure. 
* Créez une application Django dans Linux.

Le didacticiel vous montre comment créer une application web Hello World de base. L’application est hébergée sur une machine virtuelle Azure.

La capture d’écran suivante présentent l’application terminée :

![Fenêtre de navigateur affichant la page Hello World dans Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Créer et configurer une machine virtuelle Azure pour héberger Django

1. Pour créer une machine virtuelle Azure avec la distribution Ubuntu Server 14.04 LTS, voir [Créer une machine virtuelle Linux dans le portail Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vous pouvez également choisir un authentification par de mot de passe au lieu d’utiliser une clé publique SSH.
2. Pour modifier le groupe de sécurité réseau pour autoriser le trafic HTTP entrant vers le port 80, voir [Créer des groupes de sécurité réseau à l’aide du portail Azure](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md).
3. (Facultatif) Par défaut, votre nouvelle machine virtuelle n’a pas de nom de domaine complet (FQDN).  Pour créer une machine virtuelle avec un nom de domaine complet (FQDN), voir [Créer un nom de domaine complet dans le portail Azure pour une machine virtuelle Windows](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cette étape n’est pas requise pour l’achèvement de ce didacticiel.

## <a id="setup"> </a>Configurer l’environnement de développement
> [!NOTE]
> Si vous devez installer Python ou souhaitez utiliser les bibliothèques clientes, voir [Guide d’installation de Python](../../python-how-to-install.md).

La machine virtuelle Ubuntu Linux dispose de Python 2.7 préinstallé, mais elle n’est pas fournie avec Apache ou Django. Pour vous connecter à votre machine virtuelle et installer Apache et Django, procédez comme suit :

1. Ouvrez une nouvelle fenêtre de terminal.
2. Pour vous connecter à la machine virtuelle Azure, entrez la commande suivante. Si vous n’avez pas créé de nom de domaine complet (FQDN), vous pouvez vous connecter à l’aide de l’adresse IP publique affichée dans le récapitulatif de l’ordinateur virtuel sur le portail Azure.
   
       $ ssh yourusername@yourVmUrl
3. Pour installer Django, entrez les commandes suivantes :
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. Pour installer Apache avec mod-wsgi, entrez la commande suivante :
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="create-a-new-django-app"></a>Créer une application Django
1. Pour utiliser le protocole SSH pour accéder à votre machine virtuelle, ouvrez la fenêtre de terminal que vous avez utilisée dans la section précédente.
2. Pour créer un projet Django, entrez les commandes suivantes :
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   Le script `django-admin.py` génère une structure de base pour les sites web Django :
   
   * `helloworld/manage.py` vous aide à démarrer et arrêter l’hébergement de votre site web basé sur Django.
   * `helloworld/helloworld/settings.py` a des paramètres Django pour votre application.
   * `helloworld/helloworld/urls.py` a le code de mappage entre chaque URL et son affichage.
3. Dans le répertoire /var/www/helloworld/helloworld, créez un fichier nommé views.py. Celui-ci contient l’affichage rendant la page « Hello World ». Dans votre éditeur de code, entrez les commandes suivantes :
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Remplacez le contenu du fichier urls.py par les commandes suivantes :
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-apache"></a>Configurer Apache
1. Dans le dossier /etc/apache2/sites-available/helloworld.conf, créez un fichier de configuration d’hôte virtuel Apache. Définissez le contenu sur les valeurs suivantes. Remplacez *yourVmName* par le nom réel de la machine que vous utilisez (par exemple, *pyubuntu*).
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. Pour activer le site, utilisez la commande suivante :
   
       $ sudo a2ensite helloworld
3. Pour redémarrer Apache, utilisez la commande suivante :
   
       $ sudo service apache2 reload
4. Chargez la page web dans votre navigateur :
   
   ![Fenêtre de navigateur affichant la page Hello World dans Azure](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shut-down-your-azure-virtual-machine"></a>Arrêter votre machine virtuelle Azure
À l’issue de ce didacticiel, nous vous recommandons d’arrêter ou de supprimer la machine virtuelle Azure que vous avez créée pour le suivre. Cela a pour effet de libérer des ressources pour d’autres didacticiels, et de vous éviter d’exposer des frais pour l’utilisation d’Azure.


