<properties
   pageTitle="Déploiement d&#39;un modèle à l&#39;aide de l&#39;interface de ligne de commande Azure pour Mac, Linux et Windows"
   description="Décrit les étapes de base pour déployer ou mettre à jour n&#39;importe quel modèle."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="04/21/2015"
   ms.author="rasquill"/>

# Déploiement d'un modèle à l'aide de l'interface de ligne de commande Azure pour Mac, Linux et Windows

## Installer curl, wget ou un autre outil de téléchargement
Cette rubrique utilise **curl**. Utilisez le gestionnaire de package de votre système d'exploitation ou téléchargez-le [ici](http://curl.haxx.se/download.html).

## Télécharger le fichier de paramètres du modèle \(ou le modèle, si nécessaire\)

Les étapes suivantes vous permettront de déployer un modèle Azure, même s'il est complexe. Cette rubrique utilise le modèle qui crée un serveur de base Ubuntu avec l'extension de machine virtuelle customscript installée comme exemple. Les fichiers figurent également à la fin de la rubrique pour référence.

### Télécharger le fichier azuredeploy-parameters.json

Téléchargez le fichier azuredeploy-parameters.json s'il existe pour le modèle que vous souhaitez déployer.

    curl -O https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy-parameters.json
    
## Entrer les informations de déploiement du groupe de ressources
    
Ouvrez ce fichier dans votre éditeur favori. Vous verrez que vous devez spécifier une valeur pour plusieurs clés, en particulier **adminUsername**, **adminPassword** \(n'oubliez pas les règles de complexité !\) et le nom du compte de stockage ainsi que les noms DNS de votre choix.
    
    {
      "newStorageAccountName": {
        "value": "uniquestorageaccountname"
      },
      "adminUsername": {
        "value": ""
      },
      "adminPassword": {
        "value": ""
      },
      "dnsNameForPublicIP": {
        "value": "uniquednsnameforpublicip"
      },
      "vmSourceImageName": {
        "value": "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_2_LTS-amd64-server-20150309-fr-fr-30GB"
      },
      "location": {
        "value": "West US"
      },
      "virtualNetworkName": {
        "value": "myVNET"
      },
      "vmSize": {
        "value": "Standard_A0"
      },
      "vmName": {
        "value": "myVM"
      },
      "publicIPAddressName": {
        "value": "myPublicIP"
      },
      "nicName": {
        "value": "myNic"
      }
    }
    
Ajoutez les nouvelles valeurs \(Azure va créer les ressources de stockage et DNS à votre place, si cela est possible\) ou utilisez les ressources que vous avez déjà créées. Voici un exemple avec le fichier azuredeploy-parameters.json :




l'url ci-dessous extrait le fichier de paramètres du fichier parameters.json azuredeploy « vide » qui fonctionnera si la méthode interactive est utilisée. Avec l'approche utilisant le fichier de paramètres téléchargé et personnalisé, il convient de préférer à la place <template-file> l'option --template-file. Je dispose également de scripts écrits qui extraient des sections d'une partie de ces fichiers, selon ce que vous voulez faire. Vous voulez indiquer que pour effectuer l'analyse de json, jq: curl http://stedolan.github.io/jq/download/linux64/jq -o /usr/bin/jq peut être nécessaire


### Déployer les fichiers de modèle et de paramètres


[AZURE.NOTE] Vous pouvez constater que certains modèles n'ont pas forcément un fichier azuredeploy-parameters.json correspondant.

Les paramètres sont à définir, ou ils peuvent déjà faire partie du modèle lui-même, selon les modèles que vous utilisez. Dans ce cas, vous pouvez

Si votre modèle contient ses paramètres directement, ou vous souhaitez extraire les données des paramètres vous-même. Pour plus d'informations sur la structure des modèles, consultez la rubrique [Langue de modèle de gestionnaire de ressources Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).


https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy.json (ou simplement le fichier azuredeploy-parameters.json\) Vous pouvez soit extraire la section des paramètres du modèle ; dans ce cas, vous devrez l'enregistrer dans le modèle lui-même OU dans un fichier azuredeploy-parameters.json distinct. Vous devrez obtenir les valeurs à placer dans les paramètres.

## Modifier le fichier azuredeploy-templates.json

Collectez les valeurs dont vous avez besoin

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Vestibul ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, purus diam pretium eros, vitae tincidunt nulla lorem sed turpis: [Lien 3 vers une autre rubrique de documentation azure.microsoft.com](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--HONumber=52-->
