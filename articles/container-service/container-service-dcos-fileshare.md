---
title: Partage de fichiers pour un DC/OS Azure | Microsoft Docs
description: "Créer et monter un partage de fichiers sur un cluster DC/OS dans Azure Container Service"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Mesos, Azure, FileShare, cifs
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 6d40821327a9df47bb85ea12ecd33e4a0f49e39e
ms.lasthandoff: 03/22/2017


---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Créer et monter un partage de fichiers sur un cluster DC/OS
Dans cet article, nous allons découvrir comment créer un partage de fichiers sur Azure et le monter sur chaque agent et maître du cluster DC/OS. La configuration d’un partage de fichiers facilite le partage de fichiers sur votre cluster, par exemple la configuration, l’accès, les journaux et bien plus encore.

Avant d’étudier cet exemple, vous devez avoir un cluster DC/OS configuré dans Azure Container Service. Consultez [Déploiement d’un cluster Azure Container Service](container-service-deployment.md).

## <a name="create-a-file-share-on-microsoft-azure"></a>Créer un partage de fichiers sur Microsoft Azure
### <a name="using-the-portal"></a>Utiliser le portail

1. Connectez-vous au portail.
2. Créez un compte de stockage.
   
  ![Azure Container Service crée un compte de stockage](media/container-service-dcos-fileshare/createSA.png)

3. Lorsqu’il est créé, cliquez sur **Fichiers** dans la section **Services**.
   
  ![Section fichiers d’Azure Container Service](media/container-service-dcos-fileshare/filesServices.png)

4. Cliquez sur **+ Partage de fichiers** et entrez un nom pour ce nouveau partage (**Quota** n’est pas obligatoire).
   
  ![Azure Container Service + Partage de fichiers](media/container-service-dcos-fileshare/newFileShare.png)  

### <a name="using-azure-cli-20"></a>Avec Azure CLI 2.0

Si besoin, [installez et configurez Azure CLI](/cli/azure/install-azure-cli.md).

```azurecli
################# Change these four parameters ##############
DCOS_PERS_STORAGE_ACCOUNT_NAME=anystorageaccountname
DCOS_PERS_RESOURCE_GROUP=AnyResourceGroupName
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=demoshare
#############################################################

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Montage du partage sur votre cluster

Ensuite, nous devons monter ce partage sur chaque machine virtuelle à l’intérieur de votre cluster à l’aide de l’outil/du protocole cifs. Nous le faisons avec la ligne de commande suivante : `mount -t cifs`.

Voici un exemple qui utilise :
* Nom du compte de stockage **`anystorageaccountname`**
* La clé de compte fictif **`P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==`** 
* Le point de montage **`/mnt/share/demoshare`**

```bash
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```

Cette commande s’exécutera sur chaque machine virtuelle de notre cluster (nœuds master et agent). Si vous avez un grand nombre d’agents, nous vous recommandons d’automatiser ce processus en créant des scripts.  

### <a name="set-up-scripts"></a>Configuration de scripts

1. Tout d’abord, connectez-vous par SSH au master (ou premier master) de votre cluster basé sur DC/OS. Par exemple, `ssh userName@masterFQDN –A –p 22`, où masterFQDN est le nom de domaine complet de la machine virtuelle principale.

2. Copiez votre clé privée dans le répertoire de travail (~) sur le master.

3. Modifiez les autorisations sur celui-ci avec la commande suivante : `chmod 600 yourPrivateKeyFile`.

4. Importez votre clé privée à l’aide de la commande `ssh-add yourPrivateKeyFile`. Vous devrez peut-être exécuter `eval ssh-agent -s` si cela ne fonctionne pas la première fois.

5. À partir du master, créez deux fichiers, à l’aide de votre éditeur favori, comme vi, nano ou vim : 
  
  * Un avec le script à exécuter sur chaque machine virtuelle, appelé **cifsMount.sh** 
  * Un autre pour lancer toutes les connexions SSH qui appelleront le premier script, appelé **mountShares.sh**


```bash
# cifsMount.sh

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```
  
```bash
# mountShares.sh

# Install jq used for the next command
sudo apt-get install jq

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share on the current vm (master)
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes
  
# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
  do
    ssh `whoami`@$line -o StrictHostKeyChecking=no -i yourPrivateKeyFile < ./cifsMount.sh
    done
```  
> [!IMPORTANT]
> Vous devez modifier la commande **mount** avec vos propres paramètres, tels que le nom et le mot de passe de votre compte de stockage.
>  

Le dossier dans lequel vous avez créé les scripts précédents doit maintenant contenir 3 fichiers :  

* **cifsMount.sh**
* **mountShares.sh**
* **votreFichierDeCléPrivée** 

### <a name="run-the-scripts"></a>Exécuter les scripts

Exécutez le fichier **mountShares.sh** avec la commande suivante : `sh mountShares.sh`.

Vous devriez voir l’impression du résultat dans le terminal. Une fois les scripts terminés, vous pouvez utiliser le partage de fichiers dans votre cluster.

Vous pouvez optimiser les scripts, mais cet exemple est simple et son objectif est de vous guider.

> [!NOTE] 
> Cette méthode n’est pas recommandée pour les scénarios qui requièrent des E/S par seconde élevées, mais elle est très utile pour partager des documents et des informations sur le cluster.
>

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [gestion de vos conteneurs DC/OS](container-service-mesos-marathon-ui.md).
* Gestion de conteneur DC/OS au moyen de [l’API REST Marathon](container-service-mesos-marathon-rest.md).
