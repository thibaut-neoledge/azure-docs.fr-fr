---
title: "Activer Azure CLI pour les utilisateurs d’Azure Stack | Microsoft Docs"
description: "Découvrez comment utiliser l’interface de ligne de commande (CLI) multiplateforme pour gérer et déployer des ressources sur Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: d184bb9edbe2542d7321d8b9ccc5d23f2401f8d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Activer Azure CLI pour les utilisateurs d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Aucune tâche propre à l’opérateur Azure Stack ne peut être effectuée avec l’interface CLI. Mais, pour que les utilisateurs puissent gérer des ressources par son intermédiaire, les opérateurs Azure Stack doivent leur fournir les éléments suivants :

* **le certificat racine d’autorité de certification Azure Stack**, requis si les utilisateurs utilisent l’interface CLI sur une station de travail qui se trouve en dehors du kit de développement Azure Stack ;  

* **le point de terminaison des alias de machines virtuelles**, qui fournit un alias comme « UbuntuLTS » ou « Win2012Datacenter » qui fait référence à un éditeur, une offre, une référence SKU et une version d’image sous forme de paramètre unique durant le déploiement de machines virtuelles.  

Les sections suivantes expliquent comment obtenir ces valeurs.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exporter le certificat racine d’autorité de certification Azure Stack

Le certificat racine d’autorité de certification Azure Stack est disponible dans le kit de développement et sur une machine virtuelle cliente qui s’exécute dans l’environnement du kit de développement. Connectez-vous à votre kit de développement ou à la machine virtuelle cliente, et exécutez le script suivant pour exporter le certificat racine Azure Stack au format PEM :

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Cerficate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurer le point de terminaison des alias de machines virtuelles

Nous recommandons aux opérateurs Azure Stack de configurer un point de terminaison accessible publiquement qui héberge un fichier d’alias de machines virtuelles.  Le fichier d’alias de machines virtuelles est un fichier JSON qui attribue un nom commun à une image, qui est ensuite spécifié durant le déploiement d’une machine virtuelle sous forme de paramètre Azure CLI.  

Avant d’ajouter une entrée dans un fichier d’alias, assurez-vous d’avoir [téléchargé des images à partir de la Place de marché] (azure-stack-download-azure-marketplace-item.md) ou [publié votre propre image personnalisée](azure-stack-add-vm-image.md).  Si vous publiez une image personnalisée, prenez note des informations concernant l’éditeur, l’offre, la référence SKU et la version que vous avez spécifiées au moment de la publication.  S’il s’agit d’une image provenant de la Place de marché, vous pouvez afficher les informations correspondantes à l’aide de l’applet de commande ```Get-AzureVMImage```.  
   
Un [exemple de fichier d’alias](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) contenant de nombreux alias d’images communs est disponible. Vous pouvez l’utiliser comme point de départ.  Nous recommandons d’héberger ce fichier dans un espace accessible à vos clients qui utilisent l’interface CLI.  Pour ce faire, vous pouvez l’héberger dans un compte de stockage Blob et partager l’URL avec vos utilisateurs :

1.  Téléchargez l’[ exemple de fichier](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) à partir de GitHub.
2.  Créez un compte de stockage dans Azure Stack.  Créez ensuite un conteneur d’objets blob.  Définissez la stratégie d’accès sur « public ».  
3.  Chargez le fichier JSON dans le nouveau conteneur.  Vous pouvez ensuite afficher l’URL de l’objet blob en cliquant sur le nom fblob, puis en sélectionnant l’URL dans les propriétés de l’objet blob.


## <a name="next-steps"></a>Étapes suivantes

[Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)

[Se connecter avec PowerShell](azure-stack-connect-powershell.md)

[Gérer les autorisations utilisateur](azure-stack-manage-permissions.md)

