---
title: "Automatisation du déploiement d’application avec des extensions de machine virtuelle | Microsoft Docs"
description: Didacticiel sur DotNet Core pour les machines virtuelles Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 9fc8b1ba-60f5-410b-8190-9f1ff885e50e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f55773b0e523d59bb76117821fa3dde9668f1eb0
ms.contentlocale: fr-fr
ms.lasthandoff: 04/03/2017


---
# <a name="application-deployment-with-azure-resource-manager-templates-for-linux-vms"></a>Déploiement d’applications avec des modèles Azure Resource Manager pour les machines virtuelles Linux

Une fois que toutes les exigences infrastructurelles d’Azure sont identifiées et converties en un modèle de déploiement, le déploiement de l’application réelle doit être effectué. Le déploiement de l’application fait ici référence à l’installation des fichiers binaires de l’application réelle sur les ressources Azure. Pour l’exemple du Store musique, .NET Core, NGINX et Superviseur doivent être installés et configurés sur chaque machine virtuelle. Les fichiers binaires du Store musique doivent être installés sur la machine virtuelle, et la base de données du Store musique doit avoir été créée au préalable.

Ce document décrit en détail comment des extensions de machine virtuelle peuvent automatiser le déploiement et la configuration d’applications sur des machines virtuelles Azure. Toutes les dépendances et configurations uniques sont en surbrillance. Pour optimiser l’expérience, prédéployez une instance de la solution sur votre abonnement Azure et travaillez avec le modèle Azure Resource Manager. Pour le modèle complet, consultez [Déploiement du Store musique sur Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="configuration-script"></a>Script de configuration
Les extensions de machine virtuelle sont des programmes spécialisés qui s’exécutent sur des machines virtuelles pour automatiser la configuration. Les extensions sont disponibles pour de nombreux objectifs spécifiques tels que la protection contre les virus, la configuration de la journalisation et la configuration de Docker. Une extension de script personnalisé peut être utilisée pour exécuter un script sur une machine virtuelle. Avec l’exemple du Store musique, l’extension de script personnalisé doit configurer les machines virtuelles Ubuntu et installer l’application du Store musique. 

Avant de détailler la manière dont les extensions de machine virtuelle sont déclarées dans un modèle Azure Resource Manager, examinez le script exécuté. Celui-ci configure la machine virtuelle Linux Ubuntu pour héberger l’application du Store musique. Lors de son exécution, le script installe tous les logiciels nécessaires, installe l’application du Store musique à partir du contrôle de code source et prépare la base de données. 

Pour plus d’informations sur l’hébergement d’une application .NET Core sur Linux, consultez [Publier dans un environnement de production Linux](https://docs.asp.net/en/latest/publishing/linuxproduction.html).

> Cet exemple ne sert qu’à des fins de démonstration.
> 
> 

```bash
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>Extension de script de machine virtuelle
Les extensions de machine virtuelle peuvent être exécutées sur une machine virtuelle au moment de sa génération en incluant la ressource d’extension dans le modèle Azure Resource Manager. L’extension peut être ajoutée avec l’Assistant Ajouter une ressource de Visual Studio, ou en insérant un JSON valide dans le modèle. La ressource d’extension de script est imbriquée dans la ressource de machine virtuelle, comme le montre l’exemple suivant.

Pour voir l’exemple JSON dans le modèle Resource Manager, suivez ce lien : [Extension de script de machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359). 

Notez que, dans le JSON ci-dessous, le script est stocké dans GitHub. Ce script pourrait également être stocké dans Stockage Blob Azure. De même, les modèles Azure Resource Manager permettent de construire la chaîne d’exécution du script de façon à ce que les valeurs des paramètres du modèle puissent être utilisées en tant que paramètres pour l’exécution du script. Dans ce cas, des données sont fournies lors du déploiement des modèles et ces valeurs peuvent ensuite être utilisées lors de l’exécution du script.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Pour plus d’informations sur l’utilisation des extensions de script personnalisé, consultez [Extensions de script personnalisé avec des modèles Resource Manager](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-step"></a>Étape suivante
<hr>

[Découvrir d’autres modèles Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)


