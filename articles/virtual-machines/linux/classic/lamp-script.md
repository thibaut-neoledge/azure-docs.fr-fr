---
title: "Utilisation de l’extension de script personnalisé sur une machine virtuelle Linux | Microsoft Docs"
description: "Apprenez à utiliser l’extension de script personnalisé Azure pour déployer des applications sur des machines virtuelles Linux dans Azure, créées à l’aide du modèle de déploiement classique."
editor: tysonn
manager: timlt
documentationcenter: 
services: virtual-machines-linux
author: gbowerman
tags: azure-service-management
ms.assetid: e535241d-feca-4412-b07a-67c936ba88a0
ms.service: virtual-machines-linux
ms.workload: multiple
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: guybo
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 8d8c1e583939b9d1aea53db3f4378829e9b40410
ms.contentlocale: fr-fr
ms.lasthandoff: 08/11/2017

---
# <a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Déployer une application LAMP à l’aide de l’extension CustomScript Azure pour Linux
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations sur le déploiement d’une pile LAMP à l’aide du modèle Resource Manager, suivez [ce lien](../create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

L’extension CustomScript Microsoft Azure pour Linux vous permet de personnaliser vos machines virtuelles en exécutant du code arbitraire écrit dans n’importe quel langage de script pris en charge par la machine virtuelle (par exemple Python et Bash). Cela fournit un moyen très souple pour automatiser le déploiement d'application sur plusieurs machines.

Vous pouvez déployer l’extension CustomScript à l’aide du Portail Azure, de Windows PowerShell ou de l’interface de ligne de commande Azure.

Dans cet article, nous allons utiliser l’interface de ligne de commande Azure pour déployer une machine virtuelle Ubuntu créée à l’aide du modèle de déploiement classique.

## <a name="prerequisites"></a>Composants requis
Pour l’exemple suivant, créez d’abord deux machines virtuelles Azure exécutant Ubuntu 14.04 ou version ultérieure. Ces machines virtuelles sont appelées *script-vm* et *lamp-vm*. Utilisez des noms uniques lorsque vous créez les machines virtuelles. L’une d’elles sert à exécuter les commandes de l’interface de ligne de commande, tandis que l’autre accueille l’application LAMP déployée.

Vous avez également besoin d’un compte Stockage Azure et d’une clé pour accéder à celui-ci (vous pouvez l’obtenir sur le Portail Azure).

Si vous avez besoin d’aide pour créer des machines virtuelles Linux sur Azure, reportez-vous à [Création d’une machine virtuelle exécutant Linux](createportal.md).

Les commandes d’installation sont adaptées pour Ubuntu, mais vous pouvez adapter l’installation à n’importe quelle distribution Linux prise en charge.

La machine virtuelle script-vm doit disposer de l’interface de ligne de commande Azure, ainsi que d’une connexion opérationnelle à Azure. Pour obtenir de l’aide à ce sujet, reportez-vous à [Installation et configuration de l’interface de ligne de commande Microsoft Azure](../../../cli-install-nodejs.md).

## <a name="upload-a-script"></a>Télécharger un script
Nous allons utiliser l’extension de script personnalisé pour exécuter un script sur une machine virtuelle distante pour installer la pile LAMP et créer une page PHP. Pour accéder au script depuis n'importe où, nous allons le télécharger sous la forme d'un objet blob Azure.

### <a name="script-overview"></a>Vue d’ensemble du script
L’exemple de script installe une pile LAMP sur Ubuntu (y compris la configuration d’une installation sans assistance de MySQL), écrit un simple fichier PHP et démarre Apache.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Télécharger le script
Enregistrez le script en tant que fichier texte, par exemple *install_lamp.sh*, puis téléchargez-le vers le service de stockage Azure. Vous pouvez le faire facilement avec l’interface de ligne de commande Microsoft Azure. L'exemple suivant télécharge le fichier dans un conteneur de stockage nommé « scripts ». Si le conteneur n'existe pas, vous devez d'abord le créer.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Créez également un fichier JSON qui décrit comment télécharger le script à partir d’Azure Storage. Enregistrez-le sous la forme *public_config.json* (en remplaçant « mystorage » par le nom de votre compte de stockage) :

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Déployer l’extension
Vous pouvez maintenant utiliser la commande suivante pour déployer l’extension CustomScript Linux sur la machine virtuelle distante à l’aide de l’interface de ligne de commande Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

La commande précédente télécharge et exécute le script *install_lamp.sh* sur la machine virtuelle appelée *lamp-vm*.

Étant donné que l’application comprend un serveur web, n’oubliez pas d’ouvrir un port d’écoute HTTP sur la machine virtuelle distante avec la commande qui suit.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Surveillance et dépannage
Vous pouvez vérifier l’exécution du script personnalisé en examinant le fichier journal sur la machine virtuelle distante. Établissez une connexion SSH à *lamp-vm* , puis affichez la dernière partie du fichier journal avec la commande suivante.

    cd /var/log/azure/customscript
    tail -f handler.log

Après avoir exécuté l’extension CustomScript, vous pouvez accéder à la page PHP que vous avez créée pour plus d’informations. La page PHP pour l’exemple de cet article est *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Ressources supplémentaires
Vous pouvez utiliser les mêmes étapes de base pour déployer des applications plus complexes. Dans cet exemple, le script d'installation a été enregistré en tant qu'objet blob public dans Azure Storage. Une option plus sécurisée consisterait à stocker le script d’installation sous la forme d’un objet blob sécurisé avec une [signature d’accès sécurisé](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Voici quelques ressources supplémentaires pour l’interface de ligne de commande Azure, Linux et l’extension CustomScript.

[Automatiser les tâches de personnalisation de machine virtuelle Linux à l’aide de l’extension CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensions Linux Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)
