---
title: "Cluster HPC Pack 2016 dans Azure | Microsoft Docs"
description: "Découvrez comment déployer un cluster HPC Pack 2016 dans Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/14/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 7e7dc6b6d58da556dfa07d5d21b3e70483d36ef9
ms.openlocfilehash: 688f3f0885606949a265300af215f416e8f94155


---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Déployer un cluster HPC Pack 2016 dans Azure

Suivez les étapes de cet article pour déployer un cluster [Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) dans les machines virtuelles Azure. HPC Pack est la solution HPC gratuite de Microsoft basée sur les technologies Microsoft Azure et Windows Server. Elle prend en charge un vaste éventail de charges de travail HPC.

Utilisez un des [modèles Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) pour déployer le cluster HPC Pack 2016. Vous avez plusieurs options de topologie de cluster avec différents nombres de nœuds principaux de cluster et vous pouvez utiliser des nœuds de calcul Linux ou Windows.

## <a name="prerequisites"></a>Composants requis

### <a name="pfx-certificate"></a>Certificat PFX

Un cluster Microsoft HPC Pack 2016 nécessite un certificat d’échange d’informations personnelles (PFX) pour sécuriser la communication entre les nœuds HPC. Le certificat doit répondre aux exigences suivantes :

* Il doit avoir une clé privée capable d’échanger des clés.
* L’utilisation de clés comprend la signature numérique et le chiffrage de clés.
* L’utilisation améliorée de la clé inclut l’authentification cliente et serveur.

Avant de déployer le cluster HPC, téléchargez le certificat vers un [coffre de clés Azure](../key-vault/index.md) en tant que secret et enregistrez les informations suivantes pour une utilisation pendant le déploiement : **nom du coffre**, **groupe de ressources du coffre**, **URL du certificat** et **empreinte numérique du certificat**.

Un exemple de script Powershell pour charger le certificat est fourni ci-dessous. Pour en savoir plus sur le téléchargement d’un certificat dans Azure Key Vault, voir [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Topologies prises en charge

Choisissez un des [modèles Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) pour déployer le cluster HPC Pack 2016. Voici les principales architectures des trois topologies de cluster prises en charge. Les topologies à haute disponibilité incluent plusieurs nœuds principaux de cluster.

1. Cluster haute disponibilité avec un domaine Active Directory

    ![Cluster haute disponibilité dans un domaine AD](./media/virtual-machines-windows-hpcpack-2016-cluster/haad.png)



2. Cluster haute disponibilité sans domaine Active Directory

    ![Cluster haute disponibilité sans domaine AD](./media/virtual-machines-windows-hpcpack-2016-cluster/hanoad.png)

3. Cluster avec un seul nœud principal

   ![Cluster avec un seul nœud principal](./media/virtual-machines-windows-hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Déployer un cluster

Pour créer le cluster, choisissez un modèle et cliquez sur **Déployer sur Azure**. Dans le [portail Azure](https://portal.azure.com), spécifiez les paramètres du modèle comme décrit dans les étapes suivantes. Chaque modèle crée toutes les ressources Azure requises pour l’infrastructure de cluster HPC. Les ressources incluent un réseau virtuel Azure, une adresse IP publique, un équilibreur de charge (uniquement pour un cluster haute disponibilité), des interfaces réseau, des groupes à haute disponibilité, des comptes de stockage et des machines virtuelles.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>Étape 1 : Sélectionner l’abonnement, l’emplacement et le groupe de ressources

L’**abonnement** et l’**emplacement** doivent être les mêmes que ceux que vous avez spécifiés lorsque vous avez téléchargé votre certificat PFX (voir Configuration requise). Nous vous recommandons de créer un **groupe de ressources** pour le déploiement.

### <a name="step-2-specify-the-parameter-settings"></a>Étape 2 : Spécifier les paramètres

Saisissez ou modifiez les valeurs des paramètres du modèle. Cliquez sur l’icône en regard de chaque paramètre pour obtenir de l’aide. Voir aussi les conseils sur les [tailles de machines virtuelles disponibles](virtual-machines-windows-sizes.md).

Spécifiez les valeurs que vous avez enregistrées dans les conditions préalables pour les paramètres suivants : **nom du coffre**, **groupe de ressources du coffre**, **URL du certificat** et **empreinte numérique du certificat**.

###<a name="step-3-review-legal-terms-and-create"></a>Étape 3. Consulter les termes et conditions et créer
Cliquez sur les **Mentions légales** pour les lire. Si vous les acceptez, cliquez sur **Acheter**, puis sur **Créer** pour commencer le déploiement.

## <a name="connect-to-the-cluster"></a>Connexion au cluster
1. Une fois que le cluster HPC Pack est déployé, accédez au [portail Azure](https://portal.azure.com). Cliquez sur **Groupes de ressources**, puis recherchez le groupe de ressources dans lequel le cluster a été déployé. Vous pouvez rechercher les machines virtuelles avec nœud principal.

    ![Nœuds principaux de cluster dans le portail](./media/virtual-machines-windows-hpcpack-2016-cluster/clusterhns.png)

2. Cliquez sur un nœud principal (dans un cluster haute disponibilité, cliquez sur l’un de ceux disponibles). Dans **Bases**, vous pouvez trouver l’adresse IP publique ou le nom DNS complet du cluster.

    ![Paramètres de connexion au cluster](./media/virtual-machines-windows-hpcpack-2016-cluster/clusterconnect.png)

3. Cliquez sur **Se connecter** pour vous connecter aux nœuds principaux à l’aide du Bureau à distance avec votre nom d’administrateur. Si le cluster que vous avez déployé est dans un domaine Active Directory, le nom d’utilisateur est au format <privateDomainName>\<adminUsername> (par exemple hpc.local\hpcadmin).

## <a name="next-steps"></a>Étapes suivantes
* Envoyez des travaux au cluster. Voir [Envoyer des travaux à un cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md) et [Gérer un cluster HPC Pack 2016 dans Azure avec Azure Active Directory](virtual-machines-windows-hpcpack-cluster-active-directory.md).




<!--HONumber=Nov16_HO3-->


