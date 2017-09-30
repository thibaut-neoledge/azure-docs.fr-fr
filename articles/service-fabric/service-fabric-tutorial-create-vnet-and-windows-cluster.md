---
title: "Créer un cluster Service Fabric dans Azure | Microsoft Docs"
description: "Découvrez comment créer un cluster Windows dans Azure à l’aide d’un modèle."
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
ms.date: 09/06/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5d56fd468998ee4b1253b47aa133812e0141062b
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-a-secure-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Déployer un cluster Windows Service Fabric sécurisé dans un réseau virtuel Azure
Ce didacticiel est la première partie d’une série d’étapes. Vous allez apprendre à créer un cluster Service Fabric (Windows) dans Azure et à le déployer dans un réseau virtuel (VNET) et un sous-réseau existant. Lorsque vous avez terminé, vous disposez d’un cluster en cours d’exécution dans le cloud sur lequel vous pouvez déployer des applications.  Pour créer un cluster Linux, consultez la page [Créer un cluster Linux sécurisé sur Azure à l’aide d’un modèle](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un réseau virtuel dans Azure à l’aide d’un modèle
> * Créer un coffre de clés et charger un certificat
> * Créer un cluster Service Fabric sécurisé dans Azure à l’aide d’un modèle
> * Sécuriser le cluster avec un certificat X.509
> * Se connecter à un cluster à l’aide de PowerShell
> * Supprimer un cluster

Cette série de didacticiels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * créer un cluster sécurisé sur Azure à l’aide d’un modèle ;
> * [déployer la Gestion des API avec Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez le [Kit de développement logiciel (SDK) Service Fabric et le module PowerShell](service-fabric-get-started.md).
- Installez le [module Azure PowerShell, version 4.1 ou ultérieure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Les procédures suivantes créent un cluster Service Fabric à cinq nœuds. Le cluster est sécurisé par un certificat auto-signé et placé dans un coffre de clés. 

Pour calculer le coût lié à l’exécution d’un cluster Service Fabric dans Azure, utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).
Pour plus d’informations sur la création de clusters Service Fabric, consultez l’article [Créer un cluster Service Fabric à l’aide d’Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Se connecter à Azure et sélectionner un abonnement
Ce guide utilise Azure PowerShell. Lorsque vous démarrez une nouvelle session PowerShell, connectez-vous à votre compte Azure et sélectionnez votre abonnement avant d’exécuter des commandes Azure.
 
Exécutez le script suivant pour vous connecter à votre compte Azure et sélectionner votre abonnement :

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un groupe de ressources pour votre déploiement, et donnez-lui un nom et un emplacement.

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroup -Name $ResourceGroupName -Location centralus
```

## <a name="deploy-the-network-topology"></a>Déploiement de la topologie du réseau
Maintenant, configurez la topologie du réseau sur lequel la Gestion des API et le cluster Service Fabric sont déployés. Le modèle Resource Manager [network.json][network-arm] est configuré pour créer un réseau virtuel (VNET) ainsi qu’un Groupe de sécurité réseau (NSG) et un sous-réseau pour Service Fabric et un NSG et un sous-réseau pour la Gestion des API. Pour en savoir plus sur les réseaux virtuels, les sous-réseaux et les Groupes de sécurité réseau, cliquez [ici](../virtual-network/virtual-networks-overview.md).

Le fichier de paramètres [network.parameters.json][network-parameters-arm] contient les noms des sous-réseaux et des Groupes de sécurité réseau sur lesquels Service Fabric et la Gestion des API sont déployés.  La Gestion des API est déployée dans le [didacticiel suivant](service-fabric-tutorial-deploy-api-management.md). Pour ce guide, il est inutile de changer les valeurs des paramètres. Les modèles Resource Manager Service Fabric les utilisent.  Si vous les modifiez ici, vous devrez faire de même dans les autres modèles Resource Manager utilisés dans ce didacticiel et le [didacticiel Déployer la Gestion des API](service-fabric-tutorial-deploy-api-management.md). 

Téléchargez le modèle Resource Manager et le fichier de paramètres suivants :
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Utilisez la commande PowerShell suivante pour déployer le modèle Resource Manager et les fichiers de paramètres nécessaires à la configuration du réseau :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="create-a-key-vault-and-upload-a-certificate"></a>Créer un coffre de clés et charger un certificat
À l’étape suivante, le modèle Resource Manager du cluster Service Fabric est configuré pour créer un cluster sécurisé par certificat. Le certificat est utilisé pour sécuriser la communication nœud à nœud de votre cluster et gérer l’accès utilisateur à votre cluster Service Fabric. La Gestion des API utilise également ce certificat afin d’accéder à Service Fabric Naming Service pour la détection des services. Cela implique de disposer d’un certificat dans Key Vault afin de préserver la sécurité du cluster.

Le script suivant crée un coffre de clés dans Azure, crée un certificat auto-signé et charge ce dernier dans le coffre de clés.  Si vous souhaitez utiliser un certificat existant, définissez **$CreateSelfSignedCertificate** sur « $false » et spécifiez l’emplacement dans **$ExistingPfxFilePath**.

```powershell
$VaultResourceGroupName = 'ryanwikeyvaultgroup'
$VaultName= 'ryanwikeyvault'
$Location = "westus"
$CertificateName = "ryanwicertificate1"
$Password = 'mypa$$word!'
$DnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$OutputPath = "C:\MyCertificates" # location where you want the .PFX to be stored
$CreateSelfSignedCertificate = $true
$ExistingPfxFilePath = 'C:\MyCertificates\ryanwicertificate1.pfx'

$ErrorActionPreference = 'Stop'

Write-Host "Switching context to SubscriptionId $SubscriptionId"
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null

# New-AzureRmResourceGroup is idempotent as long as the location matches
Write-Host "Ensuring ResourceGroup $VaultResourceGroupName in $Location"
New-AzureRmResourceGroup -Name $VaultResourceGroupName -Location $Location -Force | Out-Null
$resourceId = $null

try
{
    $existingKeyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName
    $resourceId = $existingKeyVault.ResourceId

    Write-Host "Using existing vault $VaultName in $($existingKeyVault.Location)"
}
catch
{
}

if(!$existingKeyVault)
{
    Write-Host "Creating new vault $VaultName in $location"
    $newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName -Location $Location -EnabledForDeployment
    $resourceId = $newKeyVault.ResourceId
}

if($CreateSelfSignedCertificate)
{
    $securePassword = ConvertTo-SecureString -String $password -AsPlainText -Force

    $NewPfxFilePath = Join-Path $OutputPath $($CertificateName+".pfx")

    Write-Host "Creating new self signed certificate at $NewPfxFilePath"
    
    ## Changes to PSPKI version 3.5.2 New-SelfSignedCertificate replaced by New-SelfSignedCertificateEx
    $PspkiVersion = (Get-Module PSPKI).Version
    if($PSPKIVersion.Major -ieq 3 -And $PspkiVersion.Minor -ieq 2 -And $PspkiVersion.Build -ieq 5) {
        New-SelfsignedCertificateEx -Subject "CN=$DnsName" -EKU "Server Authentication", "Client authentication" -KeyUsage "KeyEncipherment, DigitalSignature" -Path $NewPfxFilePath -Password $securePassword -Exportable
    }
    else {
        New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName $DnsName | Export-PfxCertificate -FilePath $NewPfxFilePath -Password $securePassword | Out-Null
    }

    $ExistingPfxFilePath = $NewPfxFilePath
}

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $Password

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $Password
   } | ConvertTo-Json

    $contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
    $content = [System.Convert]::ToBase64String($contentbytes)

    $secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

Write-Host "Writing secret to $CertificateName in vault $VaultName"
$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $CertificateName -SecretValue $secretValue

$output = @{};
$output.SourceVault = $resourceId;
$output.CertificateURL = $secret.Id;
$output.CertificateThumbprint = $cert.Thumbprint;

Write-Host "Source vault: " $output.SourceVault
Write-Host "Certificate URL: " $output.CertificateURL
Write-Host "Certificate Thumbprint: " $output.CertificateThumbprint
```

## <a name="deploy-the-service-fabric-cluster"></a>Déploiement du cluster Service Fabric
Une fois le déploiement des ressources réseau terminé et le certificat chargé dans un coffre de clés, il s’agit de déployer un cluster Service Fabric sur le réseau virtuel dans le sous-réseau et le Groupe de sécurité réseau désignés pour le cluster Service Fabric. Dans le cadre de ce didacticiel, le modèle Resource Manager Service Fabric est préconfiguré pour utiliser les noms du réseau virtuel, du sous-réseau et du Groupe de sécurité réseau que vous avez configurés au cours d’une étape précédente.

Téléchargez le modèle Resource Manager et le fichier de paramètres suivants :
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

Renseignez les paramètres vides dans le fichier `cluster.parameters.json` pour votre déploiement, y compris les [informations du coffre de clés](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault) pour votre certificat de cluster.

Utilisez la commande PowerShell suivante pour déployer le modèle Resource Manager et les fichiers de paramètres nécessaires à la création du cluster Service Fabric :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
```

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>Modifier le certificat et accéder à Service Fabric Explorer 

1. Double-cliquez sur le certificat pour ouvrir l’Assistant Importation de certificat.

2. Utilisez les paramètres par défaut, mais veillez à cocher la case **Marquer cette clé comme exportable** à l’étape **Protection de clé privée**. Visual Studio doit exporter le certificat pendant la configuration d’Azure Container Registry pour l’authentification de cluster Service Fabric plus loin dans ce didacticiel.

3. Vous pouvez maintenant ouvrir Service Fabric Explorer dans un navigateur. Pour ce faire, accédez à l’URL de **ManagementEndpoint** pour votre cluster à l’aide d’un navigateur web et sélectionnez le certificat qui a été enregistré sur votre ordinateur.

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

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Un cluster est composé d’autres ressources Azure en plus de la ressource de cluster elle-même. Le plus simple pour supprimer le cluster et toutes les ressources qu’il consomme consiste à supprimer le groupe de ressources.

Connectez-vous à Azure et sélectionnez l’ID d’abonnement pour lequel vous souhaitez supprimer le cluster.  Vous pouvez trouver votre ID d’abonnement en vous connectant au [portail Azure](http://portal.azure.com). Pour supprimer un groupe de ressources et toutes les ressources de cluster, utilisez la cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un réseau virtuel dans Azure à l’aide d’un modèle
> * Créer un coffre de clés et charger un certificat
> * Créer un cluster Service Fabric sécurisé dans Azure à l’aide d’un modèle
> * Sécuriser le cluster avec un certificat X.509
> * Se connecter à un cluster à l’aide de PowerShell
> * Supprimer un cluster

Ensuite, passez au didacticiel suivant pour apprendre à déployer une application existante.
> [!div class="nextstepaction"]
> [Déployer la Gestion des API](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

