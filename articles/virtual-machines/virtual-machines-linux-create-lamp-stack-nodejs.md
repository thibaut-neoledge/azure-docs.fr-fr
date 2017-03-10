---
title: "Déployer LAMP sur une machine virtuelle Linux avec Azure CLI 1.0 | Microsoft Docs"
description: "Découvrez comment installer la pile LAMP sur une machine virtuelle Linux dans Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jluk
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: NA
ms.topic: article
ms.date: 2/21/2017
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: 59af3469a5b2d5cca68bf18dca1aa1e3ab684adb
ms.openlocfilehash: 0675b6471e37e89e426df85e2fb696fcff2927fd
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-lamp-stack-with-the-azure-cli-10"></a>Déployer la pile LAMP avec Azure CLI 1.0
Cet article vous guide à travers le déploiement d’un serveur web Apache, de MySQL et de PHP (la pile LAMP) sur Azure. Vous aurez besoin d’un compte Azure ([obtenir un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)) et de [l’interface de ligne de commande Azure](../xplat-cli-install.md) [connectée à votre compte Azure](../xplat-cli-connect.md).

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0] : notre interface de ligne de commande pour les modèles de déploiement Classic et Resource Manager (cet article)
- [Azure CLI 2.0](virtual-machines-linux-create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

* Déploiement de LAMP sur une machine virtuelle existante

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Procédure pas à pas de déploiement de LAMP sur une nouvelle machine virtuelle
Vous pouvez commencer par créer un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) qui contient la nouvelle machine virtuelle :

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Pour créer la machine virtuelle elle-même, vous pouvez utiliser un modèle Azure Resource Manager déjà écrit [ici sur GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Vous devez voir une réponse invitant l’utilisateur à renseigner certaines informations :

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

Vous avez maintenant créé une machine virtuelle Linux avec LAMP déjà installé. Si vous le souhaitez, vous pouvez vérifier l’installation en accédant à la section [Vérification de l’installation correcte de LAMP](#verify-lamp-successfully-installed).

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Procédure pas à pas de déploiement de LAMP sur une machine virtuelle existante
Si vous avez besoin d’aide pour créer une machine virtuelle Linux, vous pouvez vous rendre [ici pour apprendre à créer une machine virtuelle Linux](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ensuite, vous devez intégrer SSH à la machine virtuelle Linux. Si vous avez besoin d’aide pour créer une clé SSH, vous pouvez vous rendre [ici pour apprendre à créer une clé SSH sous Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Si vous disposez déjà d’une clé SSH, continuez et ouvrez en ligne de commande une connexion SSH à votre machine virtuelle Linux avec `ssh exampleUsername@exampleDNS`.

Maintenant que vous travaillez dans votre machine virtuelle Linux, nous allons étudier l’installation de la pile LAMP sur des distributions Debian. Les commandes exactes peuvent varier pour les autres distributions Linux.

#### <a name="installing-on-debianubuntu"></a>Installation sur Debian/Ubuntu
Les packages suivants doivent être installés : `apache2`, `mysql-server`, `php5` et `php5-mysql`. Vous pouvez les installer directement à partir de ces packages ou à l’aide de Tasksel. Les instructions pour les deux options figurent ci-dessous.
Avant de procéder à l’installation, vous devez télécharger et mettre à jour les listes de packages.

    user@ubuntu$ sudo apt-get update

##### <a name="individual-packages"></a>Packages individuels
Utilisation d’apt-get :

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Utilisation de Tasksel
Vous pouvez également télécharger Tasksel, un outil Debian/Ubuntu qui installe plusieurs packages connexes en tant que tâche coordonnée sur votre système.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Après avoir exécuté l’une des options précédentes, vous serez invité à installer ces packages et d’autres dépendances. Appuyez sur « y » puis « Entrée » pour continuer et suivez les indications des invites pour définir un mot de passe d'administration pour MySQL. Cette action installe les extensions PHP minimales requises pour utiliser PHP avec MySQL. 

![][1]

Exécutez les commandes suivantes pour voir les autres extensions PHP disponibles sous forme de packages :

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Création d’un document info.php
Vous devez maintenant être en mesure de vérifier la version d’Apache, de MySQL et de PHP figurant dans la ligne de commande en tapant `apache2 -v`, `mysql -v` ou `php -v`.

Si vous souhaitez poursuivre le test, vous pouvez créer une page d’informations PHP rapide à afficher dans un navigateur. Créez un fichier avec l’éditeur de texte Nano à l’aide de la commande suivante :

    user@ubuntu$ sudo nano /var/www/html/info.php

Dans l’éditeur de texte GNU Nano, ajoutez les lignes suivantes :

    <?php
    phpinfo();
    ?>

Enregistrez ensuite votre travail et quittez l’éditeur de texte.

Redémarrez Apache avec cette commande pour que toutes les nouvelles installations prennent effet.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Vérification de l’installation correcte de LAMP
Vous pouvez maintenant consulter la page d’informations PHP que vous avez créée en ouvrant un navigateur et en accédant à http://votreDNSunique/info.php. Elle doit ressembler à cette image.

![][2]

Vous pouvez vérifier votre installation Apache en consultant la page par défaut d’Ubuntu Apache2 en accédant à http://votreDNSunique/. Les informations suivantes s’affichent, comme sur cette image.

![][3]

Félicitations, vous avez configuré une pile LAMP sur votre machine virtuelle Azure !

## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation Ubuntu sur la pile LAMP :

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png
