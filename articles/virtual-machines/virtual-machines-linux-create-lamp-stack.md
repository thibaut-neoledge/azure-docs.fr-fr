---
title: "Déployer LAMP sur une machine virtuelle Linux dans Azure | Microsoft Docs"
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
ms.sourcegitcommit: 61e8824fe1a63efb215c7be25d3e6d7c1649d8a5
ms.openlocfilehash: 3709add7fd8d97138fd858e2b260c23300dad378
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-lamp-stack-on-azure"></a>Déployer une pile LAMP dans Azure
Cet article vous guide à travers le déploiement d’un serveur web Apache, de MySQL et de PHP (la pile LAMP) sur Azure. Vous avez besoin d’un compte Azure ([obtenir un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)) et [d’Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2). Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](virtual-machines-linux-create-lamp-stack-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-command-summary"></a>Résumé des commandes rapides

1. Enregistrez et modifiez le [fichier azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) à votre convenance sur votre ordinateur local.
2. Exécutez les deux commandes suivantes pour créer un groupe de ressources, puis déployez votre modèle :

```azurecli
az group create -l westus -n myResourceGroup
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

### <a name="deploy-lamp-on-existing-vm"></a>Déploiement de LAMP sur une machine virtuelle existante
Les commandes suivantes mettent à jour les packages, puis installent Apache, MySQL et PHP :

```bash
sudo apt-get update
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Procédure pas à pas de déploiement de LAMP sur une nouvelle machine virtuelle

1. Avec la commande [az group create](/cli/azure/group#create), créez un groupe de ressources qui contiendra la nouvelle machine virtuelle :

```azurecli
az group create -l westus -n myResourceGroup
```
Pour créer la machine virtuelle elle-même, vous pouvez utiliser un modèle Azure Resource Manager déjà écrit [ici sur GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

2. Enregistrez le [fichier azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) sur votre ordinateur local.
3. Modifiez le fichier **azuredeploy.parameters.json** en insérant les entrées de votre choix.
4. Déployez le modèle avec [az group deployment create] en faisant référence au fichier JSON téléchargé :

```azurecli
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

Le résultat ressemble à l’exemple suivant :

```json
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Resources/deployments/azuredeploy",
"name": "azuredeploy",
"properties": {
    "correlationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "debugSetting": null,
}
...
"provisioningState": "Succeeded",
"template": null,
"templateLink": {
    "contentVersion": "1.0.0.0",
    "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json"
    },
    "timestamp": "2017-02-22T00:05:51.860411+00:00"
},
"resourceGroup": "myResourceGroup"
}
```

Vous avez maintenant créé une machine virtuelle Linux avec LAMP déjà installé. Si vous le souhaitez, vous pouvez vérifier l’installation en accédant à la section [Vérification de l’installation correcte de LAMP](#verify-lamp-successfully-installed).

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Procédure pas à pas de déploiement de LAMP sur une machine virtuelle existante
Si vous avez besoin d’aide pour créer une machine virtuelle Linux, vous pouvez vous rendre [ici pour apprendre à créer une machine virtuelle Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-cli). Ensuite, vous devez intégrer SSH à la machine virtuelle Linux. Si vous avez besoin d’aide pour créer une clé SSH, vous pouvez vous rendre [ici pour apprendre à créer une clé SSH sous Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Si vous disposez déjà d’une clé SSH, continuez et ouvrez en ligne de commande une connexion SSH à votre machine virtuelle Linux avec `ssh azureuser@mypublicdns.westus.cloudapp.azure.com`.

Maintenant que vous travaillez dans votre machine virtuelle Linux, nous allons étudier l’installation de la pile LAMP sur des distributions Debian. Les commandes exactes peuvent varier pour les autres distributions Linux.

#### <a name="installing-on-debianubuntu"></a>Installation sur Debian/Ubuntu
Les packages suivants doivent être installés : `apache2`, `mysql-server`, `php5` et `php5-mysql`. Vous pouvez les installer directement à partir de ces packages ou à l’aide de Tasksel.
Avant de procéder à l’installation, vous devez télécharger et mettre à jour les listes de packages.

```bash
sudo apt-get update
```

##### <a name="individual-packages"></a>Packages individuels
Utilisation d’apt-get :

```bash
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

##### <a name="using-tasksel"></a>Utilisation de Tasksel
Vous pouvez également télécharger Tasksel, un outil Debian/Ubuntu qui installe plusieurs packages connexes en tant que tâche coordonnée sur votre système.

```bash
sudo apt-get install tasksel
sudo tasksel install lamp-server
```

Après avoir exécuté l’une des options précédentes, vous serez invité à installer ces packages et d’autres dépendances. Pour définir un mot de passe d’administration pour MySQL, appuyez sur « y » puis « Entrée » pour continuer et suivez les indications des invites. Ce processus installe les extensions PHP minimales requises pour utiliser PHP avec MySQL. 

![][1]

Exécutez les commandes suivantes pour voir les autres extensions PHP disponibles sous forme de packages :

```bash
apt-cache search php5
```

#### <a name="create-infophp-document"></a>Création d’un document info.php
Vous devez maintenant être en mesure de vérifier la version d’Apache, de MySQL et de PHP figurant dans la ligne de commande en tapant `apache2 -v`, `mysql -v` ou `php -v`.

Si vous souhaitez poursuivre le test, vous pouvez créer une page d’informations PHP rapide à afficher dans un navigateur. Créez un fichier avec l’éditeur de texte Nano à l’aide de la commande suivante :

```bash
sudo nano /var/www/html/info.php
```

Dans l’éditeur de texte GNU Nano, ajoutez les lignes suivantes :

```php
<?php
phpinfo();
?>
```

Enregistrez ensuite votre travail et quittez l’éditeur de texte.

Redémarrez Apache avec cette commande pour que toutes les nouvelles installations prennent effet.

```bash
sudo service apache2 restart
```

## <a name="verify-lamp-successfully-installed"></a>Vérification de l’installation correcte de LAMP
Vous pouvez maintenant consulter la page d’informations PHP que vous avez créée en ouvrant un navigateur et en accédant à http://votreDNSunique/info.php. Elle doit ressembler à cette image.

![][2]

Vous pouvez vérifier votre installation Apache en consultant la page par défaut d’Ubuntu Apache2 en accédant à http://votreDNSunique/. Le résultat ressemble à l’exemple suivant :

![][3]

Félicitations, vous avez configuré une pile LAMP sur votre machine virtuelle Azure !

## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation Ubuntu sur la pile LAMP :

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png

