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
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: cristyg
ms.custom: mvc
ms.openlocfilehash: 29a8d5cf9a59a08dc63df1dbd601aa1f509cad95
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2017
---
# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>Créer un cluster sécurisé sur Azure à l’aide de PowerShell
Il s’agit de la première étape d’une série de didacticiels qui vous montre comment déplacer une application .NET sur le cloud à l’aide de clusters Service Fabric et de conteneurs. Dans les étapes suivantes, vous allez apprendre à créer un cluster Service Fabric (Windows ou Linux) s’exécutant dans Azure. Quand vous aurez terminé, vous disposerez d’un cluster sécurisé en cours d’exécution dans le cloud sur lequel vous pouvez déployer des applications.

## <a name="prerequisites"></a>Prérequis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez le [kit SDK Service Fabric](service-fabric-get-started.md)
- Installez le [module Azure PowerShell version 4.1 ou ultérieure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (si nécessaire, [installez Azure PowerShell](/powershell/azure/overview) ou [effectuez la mise à jour vers une version plus récente](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps))


# <a name="create-a-service-fabric-cluster"></a>Créer un cluster Service Fabric

Ce script crée un cluster Service Fabric en préversion à nœud unique. Le cluster est sécurisé par un certificat auto-signé, créé en même temps que le cluster et placé dans un coffre de clés. Les clusters à nœud unique ne peuvent pas être mis à l’échelle au-delà d’une machine virtuelle, et les clusters en préversion ne peuvent pas être mis à niveau vers une version plus récente.

Pour calculer le coût lié à l’exécution d’un cluster Service Fabric dans Azure, utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).
Pour plus d’informations sur la création de clusters Service Fabric, consultez l’article [Créer un cluster Service Fabric à l’aide d’Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="log-in-to-azure"></a>Connexion à Azure
Ouvrez une console PowerShell, connectez-vous à Azure et sélectionnez l’abonnement sur lequel vous souhaitez déployer le cluster :

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>Paramètres de cluster

   Ce script utilise les paramètres et concepts suivants. Personnalisez les paramètres selon vos besoins.

   | Paramètre       | Description | Valeur suggérée |
   | --------------- | ----------- | --------------- |
   | Lieu | Région Azure dans laquelle déployer le cluster. | *par exemple, westeurope, eastasia, eastus* |
   | Nom     | Nom du cluster que vous souhaitez créer. Le nom doit comprendre entre 4 et 23 caractères, et ne peut comporter que des lettres minuscules, des chiffres et des traits d’union. | *par exemple, bobs-sfpreviewcluster* |
   | ResourceGroupName   | Nom du groupe de ressources dans lequel créer le cluster. | *par exemple, myresourcegroup* |
   | VmSku  | Référence SKU de machine virtuelle à utiliser pour les nœuds. | *Toute référence SKU de machine virtuelle valide* |
   | Système d’exploitation  | Système d’exploitation de machine virtuelle à utiliser pour les nœuds. | *Tout système d’exploitation de machine virtuelle valide* |
   | KeyVaultName | Nom du nouveau Key Vault à associer au cluster. | *par exemple, mykeyvault* |
   | ClusterSize | Nombre de machines virtuelles dans votre cluster (1 ou de 3 à 99).| *Pour un cluster en préversion, spécifiez uniquement une machine virtuelle* |
   | CertificateSubjectName | Nom du sujet du certificat à créer. | *Suit le format <name>.<location>.cloudapp.azure.com* |

## <a name="default-parameter-values"></a>Valeurs de paramètres par défaut
Les paramètres de **machine virtuelle** sont facultatifs. Si vous ne les spécifiez pas, le nom d’utilisateur d’administrateur par défaut est « vmadmin » et PowerShell vous demandera un mot de passe de machine virtuelle avant que le cluster ne soit créé.

Les valeurs par défaut des **ports** sont les ports 80 et 8081. Vous pouvez spécifier des ports supplémentaires en suivant les instructions pour les [ports dans des clusters Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule).

Les **diagnostics** sont activés par défaut.

Le **service DNS** par défaut n’est pas activé.

Le **proxy inverse** n’est pas non plus activé par défaut.

## <a name="create-the-cluster-with-your-parameters"></a>Créer le cluster avec vos paramètres

Une fois que vous avez choisi les paramètres selon vos besoins, exécutez la commande suivante pour générer un cluster Service Fabric sécurisé et son certificat.

Vous pouvez modifier ce script pour inclure des paramètres supplémentaires. Pour plus d’informations sur les paramètres pour la création du cluster, consultez l’applet de commande [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md).

>[!NOTE]
>Avant d’exécuter cette commande, vous devez créer un dossier dans lequel placer le certificat de cluster.

```powershell

    # Certificate variables. This will create and encrypt a password to be used by Service Fabric.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

    # You must create the folder where you want to output the certificate on your machine before executing this step.
    $certfolder="c:\mycertificates\"

    # Variables for common values. Change the values to fit your needs.
    $clusterloc="WestUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=1

    # Create the Service Fabric cluster and its self-signed certificate. The OS specified here allows you use this cluster with any applications that are also leveraging containers.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

Le processus de création peut prendre plusieurs minutes. Une fois la configuration terminée, il génère des informations sur le cluster créé dans Azure. Il copie également le certificat de cluster dans le répertoire CertificateOutputFolder sur le chemin d’accès que vous avez spécifié pour ce paramètre.

Notez l’URL **ManagementEndpoint** de votre cluster. Elle peut être semblable à l’URL suivante : *https://mycluster.westeurope.cloudapp.azure.com:19080*

## <a name="import-the-certificate"></a>Importer le certificat

Quand le cluster est correctement créé, exécutez la commande suivante pour vérifier que vous pouvez utiliser le certificat auto-signé.

```powershell

    # Connect to the cluster using by installing the certificate into the Personal (My) store of the current user on your computer.
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
            -Password $certpwd
```

Cette commande installe le certificat sur l’utilisateur actuel de votre ordinateur.  Vous avez besoin de ce certificat pour accéder au Service Fabric Explorer et afficher l’intégrité de votre cluster.


## <a name="view-your-cluster-optional"></a>Afficher votre cluster (facultatif)

Une fois que vous disposez à la fois du cluster et du certificat importé, vous pouvez vous connecter au cluster et afficher son intégrité. Il existe plusieurs manières de se connecter, via Service Fabric Explorer ou PowerShell.

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
Vous pouvez afficher l’intégrité de votre cluster en ouvrant Service Fabric Explorer. Pour ce faire, accédez à l’URL **ManagementEndpoint** de votre cluster à l’aide d’un navigateur web et sélectionnez le certificat enregistré sur votre ordinateur.

>[!NOTE]
>Quand vous ouvrez Service Fabric Explorer, vous voyez une erreur de certificat, car vous utilisez un certificat auto-signé. Dans Edge, vous devez cliquer sur *Détails*, puis sur le lien *Atteindre la page web*. Dans Chrome, vous devez cliquer sur *Avancé*, puis sur le lien *proceed* (Continuer).

### <a name="powershell"></a>PowerShell

Le module **Service Fabric** PowerShell fournit de nombreuses applets de commande pour la gestion des services, applications et clusters Service Fabric.  Pour vous connecter au cluster sécurisé, utilisez l’applet de commande [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Les détails du point de terminaison de connexion et de l’empreinte de certificat se trouvent dans la sortie d’une étape précédente.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Vous pouvez également vérifier que vous êtes connecté et que le cluster est sain à l’aide de l’applet de commande [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à créer un cluster Service Fabric sécurisé dans Azure à l’aide de PowerShell

Ensuite, passez au didacticiel suivant pour apprendre à déployer une application existante.
> [!div class="nextstepaction"]
> [Déployer une application .NET existante avec Docker Compose](service-fabric-host-app-in-a-container.md)

---
