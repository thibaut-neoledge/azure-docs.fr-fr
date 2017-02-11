---
title: "Déploiement de LAMP sur une machine virtuelle Linux | Microsoft Docs"
description: "Découvrez comment installer la pile LAMP sur une machine virtuelle Linux"
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
ms.date: 06/07/2016
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 29b295b7e061b16e187db59cdf9b777e12d63034


---
# <a name="deploy-lamp-stack-on-azure"></a>Déploiement d’une pile LAMP dans Azure
Cet article vous guide à travers le déploiement d’un serveur web Apache, de MySQL et de PHP (la pile LAMP) sur Azure. Vous aurez besoin d’un compte Azure ([obtenir une évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/)) et de [l’interface de ligne de commande Azure](../xplat-cli-install.md) qui est [connecté à votre compte Azure](../xplat-cli-connect.md).

Cet article couvre deux méthodes d’installation de LAMP :

## <a name="quick-command-summary"></a>Résumé des commandes rapides
1) Déploiement de LAMP sur une nouvelle machine virtuelle

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Déploiement de LAMP sur une machine virtuelle existante

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Procédure pas à pas de déploiement de LAMP sur une nouvelle machine virtuelle
Vous pouvez commencer par créer un nouveau [groupe de ressources](../azure-resource-manager/resource-group-overview.md) qui contient la machine virtuelle :

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

Vous avez maintenant créé une machine virtuelle Linux avec LAMP déjà installé. Si vous le souhaitez, vous pouvez vérifier l’installation en accédant à [Vérification de l’installation correcte de LAMP].

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Procédure pas à pas du déploiement de LAMP sur une machine virtuelle existante
Si vous avez besoin d’aide pour créer une machine virtuelle Linux, vous pouvez aller [ici pour apprendre à créer une machine virtuelle Linux](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ensuite, vous devrez intégrer SSH à la machine virtuelle Linux. Si vous avez besoin d’aide pour la création d’une clé SSH, vous pouvez aller [ici pour apprendre à créer une clé SSH sur Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Si vous disposez déjà d’une clé SSH, continuez et intégrez SSGH à votre machine virtuelle Linux avec `ssh username@uniqueDNS`.

Maintenant que vous travaillez dans votre machine virtuelle Linux, nous allons étudier l’installation de la pile LAMP sur des distributions Debian. Les commandes exactes peuvent varier pour les autres distributions Linux.

#### <a name="installing-on-debianubuntu"></a>Installation sur Debian/Ubuntu
Les packages suivants doivent être installés : `apache2`, `mysql-server`, `php5` et `php5-mysql`. Vous pouvez les installer directement à partir de ces packages de saisie ou à l’aide de Tasksel. Les instructions pour les deux options figurent ci-dessous.
Avant de procéder à l’installation, vous devrez télécharger et mettre à jour les listes de packages.

    user@ubuntu$ sudo apt-get update

##### <a name="individual-packages"></a>Packages individuels
Utilisation d’apt-get :

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Utilisation de Tasksel
Vous pouvez également télécharger Tasksel, un outil Debian/Ubuntu qui installe plusieurs packages connexes en tant que tâche coordonnée sur votre système.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Après avoir exécuté l’une des options ci-dessus, vous serez invité à installer ces packages et d'autres dépendances. Appuyez sur « y » puis « Entrée » pour continuer et suivez les indications des invites pour définir un mot de passe d'administration pour MySQL. Cela installera les extensions PHP minimales requises pour utiliser PHP avec MySQL. 

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
À présent, vous pouvez vérifier la page d’informations PHP que vous venez de créer dans votre navigateur en accédant à http://votreDNSunique/info.php. Elle doit avoir l’aspect suivant.

![][2]

Vous pouvez vérifier votre installation Apache en consultant la page par défaut d’Ubuntu Apache2 en accédant à http://votreDNSunique/. Le résultat suivant doit s’afficher.

![][3]

Félicitations, vous avez configuré une pile LAMP sur votre machine virtuelle Azure !

## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation Ubuntu sur la pile LAMP :

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png



<!--HONumber=Nov16_HO3-->


