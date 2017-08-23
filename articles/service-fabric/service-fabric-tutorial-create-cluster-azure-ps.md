---
title: "Créer un cluster Service Fabric dans Azure | Microsoft Docs"
description: "Découvrez comment créer un cluster Service Fabric Windows ou Linux dans Azure à l’aide de PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 94d1d4c243bede354ae3deba7fbf5da0652567cb
ms.openlocfilehash: 721d1bf5b03d667baa380f0b825f266d2326ecae
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---

# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>Créer un cluster sécurisé sur Azure à l’aide de PowerShell
Ce didacticiel vous montre comment créer un cluster Service Fabric (Windows ou Linux) s’exécutant dans Azure. Lorsque vous avez terminé, vous disposez d’un cluster en cours d’exécution dans le cloud sur lequel vous pouvez déployer des applications.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un cluster Service Fabric dans Azure
> * Sécuriser le cluster avec un certificat X.509
> * Se connecter à un cluster à l’aide de PowerShell
> * Supprimer un cluster

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez le [Kit de développement logiciel (SDK) Service Fabric et le module PowerShell](service-fabric-get-started.md).
- Installez le [module Azure PowerShell, version 4.1 ou ultérieure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 

La procédure suivante crée un cluster Service Fabric en préversion à nœud unique (machine virtuelle unique) sécurisé par un certificat auto-signé, qui est placé dans un coffre de clés. Les clusters à nœud unique ne peuvent pas être mis à l’échelle au-delà d’une machine virtuelle, et les clusters en préversion ne peuvent pas être mis à niveau vers une version plus récente. 

Pour calculer le coût lié à l’exécution d’un cluster Service Fabric dans Azure, utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).
Pour plus d’informations sur la création de clusters Service Fabric, consultez l’article [Créer un cluster Service Fabric à l’aide d’Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="create-the-cluster-using-azure-powershell"></a>Créer le cluster à l’aide d’Azure PowerShell
1. Téléchargez une copie locale du modèle Azure Resource Manager et le fichier de paramètres du [modèle Azure Resource Manager pour le référentiel GitHub de Service Fabric](https://aka.ms/securepreviewonelineclustertemplate).  *azuredeploy.json* est le modèle Azure Resource Manager qui définit un cluster Service Fabric. *azuredeploy.parameters.json* est le fichier de paramètres permettant de personnaliser le déploiement du cluster.

2. Personnalisez les paramètres suivants dans le fichier de paramètres *azuredeploy.parameters.json* :
  
   | Paramètre       | Description | Valeur suggérée |
   | --------------- | ----------- | --------------- |
   | clusterLocation | Région Azure dans laquelle déployer le cluster. | *par exemple, westeurope, eastasia, eastus* |
   | clusterName     | Nom de votre cluster. | *par exemple, bobs-sfpreviewcluster* |
   | adminUsername   | Compte d’administrateur local sur les machines virtuelles du cluster. | *N’importe quel nom d’utilisateur Windows Server valide* |
   | adminPassword   | Mot de passe du compte d’administrateur local sur les machines virtuelles du cluster. | *N’importe quel mot de passe Windows Server valide* |
   | clusterCodeVersion | Version de Service Fabric à exécuter. (255.255.X.255 sont des préversions). | **255.255.5718.255** |
   | vmInstanceCount | Nombre de machines virtuelles dans votre cluster (1 ou de 3 à 99). | **1** |

3. Ouvrez une console PowerShell, connectez-vous à Azure et sélectionnez l’abonnement dans lequel vous souhaitez déployer le cluster :

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```
4. Créez et chiffrez un mot de passe pour le certificat utilisé par Service Fabric.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```
5. Créez le cluster en exécutant la commande suivante :

   ```powershell
      New-AzureRmServiceFabricCluster
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >Le paramètre `-CertificateSubjectName` doit s’aligner sur le paramètre clusterName spécifié dans le fichier de paramètres, ainsi que sur le domaine lié à la région Azure que vous avez sélectionnée, par exemple : `clustername.eastus.cloudapp.azure.com`.
   
    Une fois la configuration terminée, cette commande génère des informations sur le cluster créé dans Azure, et copie le certificat dans le répertoire -CertificateOutputFolder.

6. Double-cliquez sur le certificat pour ouvrir l’Assistant Importation de certificat.

7. Utilisez les paramètres par défaut, mais veillez à cocher la case **Marquer cette clé comme exportable** à l’étape **Protection de clé privée**. Visual Studio doit exporter le certificat pendant la configuration d’Azure Container Registry pour l’authentification de cluster Service Fabric plus loin dans ce didacticiel.

8. Vous pouvez maintenant ouvrir Service Fabric Explorer dans un navigateur. L’URL est le point de terminaison de gestion (**ManagementEndpoint**) dans le résultat de la cmdlet PowerShell, par exemple, *https://mycluster.westeurope.cloudapp.azure.com:19080* 

>[!NOTE]
>Quand vous ouvrez Service Fabric Explorer, vous voyez une erreur de certificat, car vous utilisez un certificat auto-signé. Dans Edge, vous devez cliquer sur *Détails*, puis sur le lien *Atteindre la page web*. Dans Chrome, vous devez cliquer sur *Avancé*, puis sur le lien *proceed* (Continuer).

>[!NOTE]
>Si la création du cluster échoue, vous pouvez toujours réexécuter la commande et mettre ainsi à jour les ressources déjà déployées. Si un certificat a été créé dans le cadre du déploiement ayant échoué, un nouveau est généré. Pour résoudre les problèmes liés à la création du cluster, consultez l’article [Créer un cluster Service Fabric à l’aide d’Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="connect-to-the-secure-cluster"></a>Se connecter à un cluster sécurisé 
Connectez-vous au cluster à l’aide du module Service Fabric PowerShell installé avec le Kit de développement logiciel (SDK) Service Fabric.  Tout d’abord, installez le certificat dans le magasin personnel de l’utilisateur actuel sur votre ordinateur.  Exécutez la commande PowerShell suivante :

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Vous êtes maintenant prêt à vous connecter à votre cluster sécurisé.

Le module **Service Fabric** PowerShell fournit de nombreuses cmdlets pour la gestion des services, applications et clusters Service Fabric.  Pour vous connecter au cluster sécurisé, utilisez la cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Les détails du point de terminaison de connexion et de l’empreinte de certificat se trouvent dans la sortie d’une étape précédente. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Vérifiez que vous êtes connecté et que le cluster est sain à l’aide de la cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Un cluster est composé d’autres ressources Azure en plus de la ressource de cluster elle-même. Le plus simple pour supprimer le cluster et toutes les ressources qu’il consomme consiste à supprimer le groupe de ressources. 

Connectez-vous à Azure et sélectionnez l’ID d’abonnement pour lequel vous souhaitez supprimer le cluster.  Vous pouvez trouver votre ID d’abonnement en vous connectant au [portail Azure](http://portal.azure.com). Pour supprimer un groupe de ressources et toutes les ressources de cluster, utilisez la cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un cluster Service Fabric dans Azure
> * Sécuriser le cluster avec un certificat X.509
> * Se connecter à un cluster à l’aide de PowerShell
> * Supprimer un cluster

Ensuite, passez au didacticiel suivant pour apprendre à déployer une application existante.
> [!div class="nextstepaction"]
> [Déployer une application .NET existante avec Docker Compose](service-fabric-host-app-in-a-container.md)

