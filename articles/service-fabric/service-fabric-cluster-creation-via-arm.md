---
title: "Créer un cluster Azure Service Fabric à partir d’un modèle | Microsoft Docs"
description: "Cet article explique comment configurer un cluster Service Fabric sécurisé dans Azure à l’aide d’Azure Resource Manager, Azure Key Vault et Azure Active Directory (Azure AD) pour l’authentification des clients."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 47152d05eb7e31e7fe1f35e33a10fe8e903e21e2
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Créer un cluster Service Fabric à l’aide d’Azure Resource Manager
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portail Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Ce guide vous montre pas à pas comment configurer un cluster Azure Service Fabric sécurisé dans Azure à l’aide d’Azure Resource Manager. Nous reconnaissons que cet article est un peu long. Cependant, à moins que vous soyez déjà parfaitement familiarisés avec le contenu, veillez à suivre attentivement chaque étape.

Ce guide couvre les procédures suivantes :

* Configuration d’un Azure Key Vault afin de charger des certificats pour la sécurité du cluster et des applications
* Création d’un cluster sécurisé dans Azure à l’aide d’Azure Resource Manager
* Authentification des utilisateurs à l’aide d’Azure Active Directory (Azure AD) pour la gestion du cluster

Un cluster sécurisé est un cluster qui empêche tout accès non autorisé aux opérations de gestion. Cela inclut le déploiement, la mise à niveau et la suppression des applications, des services et des données qu’ils contiennent. Un cluster non sécurisé est un cluster auquel toute personne peut se connecter à tout moment pour effectuer des opérations de gestion. Bien qu’il soit possible de créer un cluster non sécurisé, nous vous recommandons vivement de créer dès le départ un cluster sécurisé. En effet, un cluster non sécurisé ne peut pas être sécurisé ultérieurement, ce qui implique la création d’un nouveau cluster.

La méthode de création de clusters sécurisés est la même pour les clusters Linux et Windows. Pour en savoir plus et accéder à des scripts d’assistance dédiés à la création de clusters Linux sécurisés, voir [Créer des clusters sécurisés sur Linux](#secure-linux-clusters).

## <a name="sign-in-to-your-azure-account"></a>Connexion à votre compte Azure
Ce guide utilise [Azure PowerShell][azure-powershell]. Lorsque vous démarrez une nouvelle session PowerShell, connectez-vous à votre compte Azure et sélectionnez votre abonnement avant d’exécuter des commandes Azure.

Connectez-vous à votre compte Azure :

```powershell
Login-AzureRmAccount
```

Sélectionnez votre abonnement :

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-a-key-vault"></a>Configurer un Key Vault
Cette section explique comment créer un Key Vault pour un cluster Service Fabric dans Azure et pour des applications Service Fabric. Pour obtenir un guide complet sur Azure Key Vault, reportez-vous à l’article [Prise en main de Key Vault][key-vault-get-started].

Service Fabric utilise des certificats X.509 pour sécuriser un cluster et fournir des fonctionnalités de sécurité d’applications. Key Vault sert à gérer des certificats pour des clusters Service Fabric dans Azure. Lorsqu’un cluster est déployé dans Azure, le fournisseur de ressources Azure chargé de la création des clusters Service Fabric extrait les certificats de Key Vault et les installe sur les machines virtuelles du cluster.

Le diagramme suivant illustre la relation entre Azure Key Vault, un cluster Service Fabric et le fournisseur de ressources Azure qui utilise des certificats stockés dans un Key Vault lorsqu’il crée un cluster :

![Diagramme de l’installation de certificats][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
La première étape consiste à créer un groupe de ressources dédié à votre Key Vault. Nous vous recommandons de placer ce Key Vault dans son propre groupe de ressources. Vous pouvez ainsi supprimer les groupes de ressources de calcul et de stockage, y compris le groupe de ressources contenant votre cluster Service Fabric, sans risquer de perdre vos clés et secrets. Le groupe de ressources qui contient votre Key Vault _doit se trouver dans la même région_ que le cluster qui l’utilise.

Si vous prévoyez de déployer des clusters dans plusieurs régions, nous vous conseillons de nommer le groupe de ressources et le Key Vault de manière à indiquer la région à laquelle ils appartiennent.  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
La sortie doit ressembler à ceci :

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

### <a name="create-a-key-vault-in-the-new-resource-group"></a>Créer un Key Vault dans le nouveau groupe de ressources
Le Key Vault _doit être activé pour le déploiement_ afin d’autoriser le fournisseur de ressources de calcul à obtenir des certificats à partir de ce Key Vault et à les installer sur les instances de machines virtuelles :

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

La sortie doit ressembler à ceci :

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```
<a id="existing-key-vault"></a>

## <a name="use-an-existing-key-vault"></a>Utiliser un Key Vault existant

Pour utiliser un Key Vault existant, vous _devez l’activer pour le déploiement_ afin d’autoriser le fournisseur de ressources de calcul à obtenir des certificats à partir de ce Key Vault et à les installer sur les nœuds de cluster :

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-your-key-vault"></a>Ajouter des certificats à votre Key Vault

Les certificats sont utilisés dans Service Fabric à des fins d’authentification et de chiffrement pour sécuriser les divers aspects d’un cluster et de ses applications. Pour plus d’informations sur l’utilisation de certificats dans Service Fabric, consultez la page [Scénarios de sécurité d’un cluster Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificat de cluster et de serveur (obligatoire)
Ce certificat est nécessaire pour sécuriser un cluster et empêcher un accès non autorisé à ce dernier. Il assure la sécurité du cluster de deux manières :

* Authentification du cluster : authentifie la communication nœud à nœud pour la fédération du cluster. Seuls les nœuds qui peuvent prouver leur identité avec ce certificat peuvent être ajoutés au cluster.
* Authentification du serveur : authentifie les points de terminaison de gestion du cluster sur un client de gestion, afin que le client de gestion sache qu’il communique avec le véritable cluster. Ce certificat fournit également un certificat SSL pour l’API de gestion HTTPS et Service Fabric Explorer par le biais de HTTPS.

Pour cela, le certificat doit répondre aux exigences suivantes :

* Le certificat doit contenir une clé privée.
* Le certificat doit être créé pour l’échange de clés, qui peut faire l’objet d’un export vers un fichier .pfx (Personal Information Exchange).
* Le nom de sujet du certificat doit correspondre au domaine utilisé pour accéder au cluster Service Fabric. Cela est nécessaire pour la fourniture d’un certificat SSL pour les points de terminaison de gestion HTTPS du cluster et pour Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d’une autorité de certification pour le domaine azure.com. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

### <a name="application-certificates-optional"></a>Certificats d’application (facultatif)
Un nombre quelconque de certificats supplémentaires peut être installé sur un cluster pour sécuriser une application. Avant de créer votre cluster, examinez les scénarios de sécurité d’application qui nécessitent l’installation d’un certificat sur les nœuds, notamment :

* Le chiffrement et déchiffrement de valeurs de configuration d’application.
* Le chiffrement des données sur les nœuds lors de la réplication.

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Mise en forme de certificats pour une utilisation par un fournisseur de ressources Azure
Vous pouvez ajouter et utiliser les fichiers de clé privée (.pfx) directement via votre Key Vault. Cependant, le fournisseur de ressources de calcul Azure exige que les clés soient stockées dans un format JSON (JavaScript Objet Notation) spécial. Ce format inclut le fichier .pfx sous la forme d’une chaîne encodée en base 64, ainsi que le mot de passe de la clé privée. Pour répondre à ces exigences, les clés doivent être placées dans une chaîne JSON, puis stockées en tant que « secrets » dans le Key Vault.

Un module PowerShell [disponible sur GitHub][service-fabric-rp-helpers] facilite ce processus. Pour utiliser ce module, procédez comme suit :

1. Téléchargez le contenu complet du référentiel dans un répertoire local.
2. Accédez au répertoire local.
2. Importez le module ServiceFabricRPHelpers dans votre fenêtre PowerShell :

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

La commande `Invoke-AddCertToKeyVault` dans ce module PowerShell met automatiquement en forme une clé privée de certificat dans une chaîne JSON et la charge dans le Key Vault. Utilisez cette commande pour ajouter le certificat de cluster et tous les certificats d’application supplémentaires dans le Key Vault. Répétez simplement cette étape pour tous les certificats supplémentaires à installer sur votre cluster.

#### <a name="uploading-an-existing-certificate"></a>Chargement d’un certificat existant

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

Si vous obtenez une erreur, comme celle illustrée ici, cela signifie généralement qu’il existe un conflit d’URL de ressource. Pour résoudre ce conflit, modifiez le nom du Key Vault.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Une fois le conflit résolu, la sortie doit ressembler à ceci :

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>Vous avez besoin des trois chaînes précédentes (CertificateThumbprint, SourceVault et CertificateURL) pour configurer un cluster Service Fabric sécurisé et obtenir les certificats d’application que vous utilisez peut-être pour la sécurité des applications. Si vous n’enregistrez pas ces chaînes, il peut s’avérer difficile de les récupérer ultérieurement en interrogeant le Key Vault.

<a id="add-self-signed-certificate-to-key-vault"></a>

#### <a name="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault"></a>Création d’un certificat auto-signé et chargement de ce certificat dans le Key Vault

Si vous avez déjà chargé vos certificats dans le Key Vault, ignorez cette étape. Cette étape concerne la génération d’un nouveau certificat auto-signé et son chargement dans votre Key Vault. Après avoir modifié les paramètres dans le script suivant et exécuté ce dernier, vous devriez être invité à fournir un mot de passe de certificat.  

```powershell

$ResourceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResourceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

Si vous obtenez une erreur, comme celle illustrée ici, cela signifie généralement qu’il existe un conflit d’URL de ressource. Pour résoudre ce conflit, modifiez le nom du Key Vault, le nom du groupe de ressources et ainsi de suite.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Une fois le conflit résolu, la sortie doit ressembler à ceci :

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>Vous avez besoin des trois chaînes précédentes (CertificateThumbprint, SourceVault et CertificateURL) pour configurer un cluster Service Fabric sécurisé et obtenir les certificats d’application que vous utilisez peut-être pour la sécurité des applications. Si vous n’enregistrez pas ces chaînes, il peut s’avérer difficile de les récupérer ultérieurement en interrogeant le Key Vault.

 À ce stade, vous devriez avoir les éléments suivants en place :

* Le groupe de ressources du Key Vault.
* Le Key Vault (appelé SourceVault dans la sorite PowerShell précédente) et son URL.
* Le certificat d’authentification du serveur de cluster et son URL dans le Key Vault.
* Les certificats d’application et leurs URL dans le Key Vault.


<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configuration d’Azure Active Directory pour l’authentification des clients

Azure AD permet aux organisation (appelées locataires) de gérer l’accès utilisateur aux applications. Ces dernières se composent d’applications avec une interface utilisateur de connexion web et d’applications avec une expérience client natif. Dans cet article, nous partons du principe que vous avez déjà créé un locataire. Si ce n’est pas le cas, commencez par lire [Obtention d’un client Azure Active Directory][active-directory-howto-tenant].

Un cluster Service Fabric offre différents points d’entrée pour leurs fonctionnalités de gestion, notamment les outils [Service Fabric Explorer][service-fabric-visualizing-your-cluster] et [Visual Studio][service-fabric-manage-application-in-visual-studio]. Par conséquent, vous allez créer deux applications Azure AD pour contrôler l’accès au cluster : une application web et une application native.

Pour simplifier certaines des étapes impliquées dans la configuration d’Azure AD avec un cluster Service Fabric, nous avons créé un ensemble de scripts Windows PowerShell.

> [!NOTE]
> Vous devez exécuter les étapes suivantes avant de créer le cluster. Étant donné que les scripts attendent des noms de cluster et des points de terminaison, ces valeurs doivent être des valeurs planifiées et non celles que vous avez déjà créées.

1. [Téléchargez les scripts][sf-aad-ps-script-download] sur votre ordinateur.
2. Cliquez avec le bouton sur le fichier zip, sélectionnez **Propriétés**, activez la case à cocher **Débloquer**, puis cliquez sur **Appliquer**.
3. Extrayez le fichier zip.
4. Exécutez `SetupApplications.ps1` et indiquez les valeurs de TenantId, ClusterName et WebApplicationReplyUrl en tant que paramètres. Par exemple :

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Vous pouvez trouver votre TenantId en exécutant la commande PowerShell `Get-AzureSubscription`. L’exécution de cette commande permet d’afficher le TenantId de chaque abonnement.

    Le paramètre ClusterName est utilisé pour préfixer les applications Azure AD créées par le script. Il ne doit pas forcément correspondre précisément au nom du cluster. Il sert uniquement à simplifier le mappage des artefacts Azure AD au cluster Service Fabric avec lequel ils sont utilisés.

    Le paramètre WebApplicationReplyUrl est le point de terminaison par défaut renvoyé par Azure AD aux utilisateurs une fois qu’ils se sont connectés. Définissez ce point de terminaison en tant que point de terminaison Service Fabric Explorer pour votre cluster, qui est par défaut :

    https://&lt;cluster_domain&gt;:19080/Explorer

    Vous êtes invité à vous connecter à un compte qui dispose de privilèges d’administration pour le locataire Azure AD. Une fois que vous vous êtes connecté, le script crée les applications web et native pour représenter votre cluster Service Fabric. Si vous examinez les applications du client dans le [Portail Azure Classic][azure-classic-portal], vous devez voir deux nouvelles entrées :

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

   Comme le script imprime le JSON exigé par le modèle Azure Resource Manager lorsque vous créez le cluster (dans la section suivante), il est judicieux de garder la fenêtre PowerShell ouverte.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Création d’un modèle Service Fabric Cluster Resource Manager
Dans cette section, les sorties des commandes PowerShell précédentes sont utilisées dans un modèle Resource Manager de cluster Service Fabric.

Des exemples de modèles Resource Manager sont disponibles dans [la galerie de modèles de démarrage rapide Azure sur GitHub][azure-quickstart-templates]. Ces modèles peuvent être utilisés comme point de départ pour votre modèle de cluster.

### <a name="create-the-resource-manager-template"></a>Créer le modèle Resource Manager
Ce guide utilise l’exemple de modèle [cluster sécurisé à 5 nœuds][service-fabric-secure-cluster-5-node-1-nodetype] et ses paramètres du modèle. Téléchargez `azuredeploy.json` et `azuredeploy.parameters.json` sur votre ordinateur et ouvrez les deux fichiers dans votre éditeur de texte préféré.

### <a name="add-certificates"></a>Ajout de certificats
Pour ajouter les certificats à un modèle Resource Manager de cluster, vous devez référencer le Key Vault qui contient les clés de certificat. Nous vous recommandons de placer les valeurs de Key Vault dans un fichier de paramètres de modèle Resource Manager. Le fichier de modèle Resource Manager sera ainsi réutilisable et exempt de valeurs propres à un déploiement.

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Ajouter tous les certificats à l’osProfile du groupe de machines virtuelles identiques
Chaque certificat qui est installé dans le cluster doit être configuré dans la section osProfile de la ressource des groupes identiques (Microsoft.Compute/virtualMachineScaleSets). Cela permet d’indiquer au fournisseur de ressources d’installer le certificat sur les machines virtuelles. Cette installation inclut le certificat de cluster et tous les certificats de sécurité d’application que vous projetez d’utiliser pour vos applications :

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurer le certificat de cluster Service Fabric
Le certificat d’authentification de cluster doit être configuré à la fois dans la ressource de cluster Service Fabric (Microsoft.ServiceFabric/clusters) et dans l’extension de Service Fabric pour les groupes de machines virtuelles identiques dans la ressource associée à ces derniers. Cette disposition permet au fournisseur de ressources de Service Fabric de configurer le certificat pour l’authentification du cluster et l’authentification du serveur pour les points de terminaison de gestion.

##### <a name="virtual-machine-scale-set-resource"></a>Ressource des groupes de machines virtuelles identiques :
```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Ressource Service Fabric :
```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-azure-ad-configuration"></a>Insérer la configuration Azure AD
La configuration Azure AD que vous avez créée précédemment peut être insérée directement dans votre modèle Resource Manager. Cependant, nous vous recommandons d’extraire d’abord les valeurs dans un fichier de paramètres pour que le modèle Resource Manager soit réutilisable et exempt de valeurs propres à un déploiement.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a "configure-arm" ></a>Configuration des paramètres de modèle Resource Manager
<!--- Loc Comment: It seems that <a "configure-arm" > must be replaced with <a name="configure-arm"></a> since the link seems not to be redirecting correctly --->
Enfin, utilisez les valeurs de sortie des commandes PowerShell Azure AD et du Key Vault pour renseigner le fichier de paramètres :

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
À ce stade, vous devriez avoir les éléments suivants en place :

* Groupe de ressources du Key Vault
  * Coffre de clés
  * Certificat d’authentification de serveur de clusters
  * Certificat de chiffrement de données
* Locataire Azure Active Directory
  * Application Azure AD pour la gestion basée sur le web et Service Fabric Explorer
  * Application Azure AD pour la gestion du client natif
  * Utilisateurs et rôles qui leur sont affectés
* Modèle Service Fabric Cluster Resource Manager
  * Certificats configurés par le biais du Key Vault
  * Configuration d’Azure Active Directory

Le diagramme suivant montre comment votre Key Vault et la configuration Azure AD s’appliquent à votre modèle Resource Manager.

![Mappage de dépendances de Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Création du cluster
Vous êtes maintenant prêt à créer le cluster en [déployant le modèle de ressource Azure][resource-group-template-deploy].

#### <a name="test-it"></a>Testez-le
Pour tester votre modèle Resource Manager avec un fichier de paramètres, utilisez la commande PowerShell suivante :

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Déployez-le
Si le test du modèle Resource Manager réussit, utilisez la commande PowerShell suivante pour déployer votre modèle Resource Manager avec un fichier de paramètres  :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Affecter des utilisateurs aux rôles
Une fois que vous avez créé les applications pour représenter votre cluster, affectez les utilisateurs aux rôles pris en charge par Service Fabric : lecture seule et administrateur. Vous pouvez affecter ces rôles à l’aide du [portail Azure Classic][azure-classic-portal].

1. Dans le portail Azure, accédez à votre locataire, puis sélectionnez **Applications**.
2. Sélectionnez l’application web, qui porte un nom similaire à `myTestCluster_Cluster`.
3. Cliquez sur l’onglet **Users** .
4. Sélectionnez un utilisateur à affecter, puis cliquez sur le bouton **Affecter** situé en bas de l’écran.

    ![Bouton Assign users to roles (Affecter des utilisateurs aux rôles)][assign-users-to-roles-button]
5. Sélectionnez le rôle à affecter à l’utilisateur.

    ![Boîte de dialogue Affecter des utilisateurs][assign-users-to-roles-dialog]

> [!NOTE]
> Pour plus d’informations sur les rôles dans Service Fabric, consultez [Contrôle d’accès en fonction du rôle pour les clients de Service Fabric](service-fabric-cluster-security-roles.md).
>
>

 <a name="secure-linux-clusters"></a>
 <!--- Loc Comment: It seems that letter S in cluster was missing, which caused the wrong redirection of the link --->

## <a name="create-secure-clusters-on-linux"></a>Créer des clusters sécurisés sur Linux
Pour faciliter le processus, nous avons mis à disposition un [script d’assistance](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Avant d’utiliser ce script d’assistance, assurez-vous que l’interface de ligne de commande Azure (CLI) est déjà installée, et qu’il se trouve dans votre chemin d’accès. Vérifiez que le script dispose des autorisations d’exécution en lançant la commande `chmod +x cert_helper.py` après l’avoir téléchargé. La première étape consiste à se connecter à votre compte Azure à l’aide de l’interface de ligne de commande avec la commande `azure login` . Une fois connecté à votre compte Azure, utilisez le script d’assistance avec votre certificat signé par une autorité de certification, comme l’illustre la commande suivante :

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

Le paramètre -ifile peut accepter un fichier .pfx ou .pem en entrée avec le type de certificat (pfx ou pem, ou ss s’il s’agit d’un certificat auto-signé).
Le paramètre -h permet d’afficher le texte d’aide.


La sortie de cette commande renvoie les trois chaînes suivantes :

* SourceVaultID, qui correspond à l’ID du nouvel élément KeyVault ResourceGroup créé pour vous ;
* CertificateUrl pour l’accès au certificat ;
* CertificateThumbprint, qui est utilisée pour l’authentification.

L’exemple suivant explique comment utiliser la commande :

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
L’exécution de la commande précédente génère les trois chaînes suivantes :

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

Le nom de sujet du certificat doit correspondre au domaine utilisé pour accéder au cluster Service Fabric. Cela est nécessaire pour la fourniture d’un certificat SSL pour les points de terminaison de gestion HTTPS du cluster et pour Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d’une autorité de certification pour le domaine `.cloudapp.azure.com`. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Ces noms de sujet sont les entrées dont vous avez besoin pour créer un cluster Service Fabric sécurisé (sans Azure AD), comme décrit dans la section [Configuration des paramètres de modèle Resource Manager](#configure-arm). Vous pouvez vous connecter au cluster sécurisé en suivant les instructions permettant d’[authentifier l’accès client à un cluster](service-fabric-connect-to-secure-cluster.md). Les clusters Linux ne prennent pas en charge l’authentification Azure AD. Vous pouvez affecter des rôles d’administrateur et de client, comme décrit dans la section [Affecter des utilisateurs aux rôles](#assign-roles). Lorsque vous spécifiez des rôles d’administrateur et de client pour un cluster Linux, vous devez fournir des empreintes de certificat à des fins d’authentification. Vous ne fournissez pas le nom du sujet, car aucune validation ou révocation de chaîne n’est effectuée.

Si vous souhaitez utiliser un certificat auto-signé à des fins de test, vous pouvez utiliser le même script pour en générer un. Vous pouvez ensuite charger le certificat dans votre Key Vault en fournissant l’indicateur `ss` au lieu de spécifier le chemin d’accès et le nom du certificat. Par exemple, consultez la commande suivante pour créer et télécharger un certificat auto-signé :

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
Cette commande renvoie les trois mêmes chaînes : SourceVault, CertificateUrl et CertificateThumbprint. Vous pouvez ensuite utiliser les chaînes pour créer un cluster Linux sécurisé et un emplacement pour le certificat auto-signé. Vous avez besoin du certificat auto-signé pour vous connecter au cluster. Vous pouvez vous connecter au cluster sécurisé en suivant les instructions permettant d’[authentifier l’accès client à un cluster](service-fabric-connect-to-secure-cluster.md).

Le nom de sujet du certificat doit correspondre au domaine utilisé pour accéder au cluster Service Fabric. Cela est nécessaire pour la fourniture d’un certificat SSL pour les points de terminaison de gestion HTTPS du cluster et pour Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d’une autorité de certification pour le domaine `.cloudapp.azure.com`. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Vous pouvez renseigner les paramètres fournis par le script d’assistance dans le portail, comme indiqué dans la section [Création d’un cluster dans le portail Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal).

## <a name="next-steps"></a>Étapes suivantes
À ce stade, vous avez un cluster sécurisé avec l’authentification de gestion fournie par Azure Active Directory. Ensuite, [connectez-vous à votre cluster](service-fabric-connect-to-secure-cluster.md) et découvrez comment [gérer les secrets d’application](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Résoudre les problèmes de configuration d’Azure Active Directory pour l’authentification des clients
Si vous rencontrez un problème pendant la configuration d’Azure AD pour l’authentification des clients, examinez les solutions potentielles présentées dans cette section.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer vous demande de sélectionner un certificat
#### <a name="problem"></a>Problème
Une fois que vous vous êtes connecté à Azure AD dans Service Fabric Explorer, le navigateur revient à la page d’accueil, mais un message vous invite à sélectionner un certificat.

![Boîte de dialogue SFX de sélection d’un certificat][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motif
Aucun rôle n’a été affecté à l’utilisateur dans l’application Azure AD en cluster. Par conséquent, l’authentification Azure AD échoue sur le cluster Service Fabric. Service Fabric Explorer revient à l’authentification par certificat.

#### <a name="solution"></a>Solution
Suivez les instructions de configuration d’Azure AD et affectez des rôles utilisateur. Nous vous recommandons également d’activer l’option Affectation de l’utilisateur requise pour accéder à l’application, comme le fait `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>La connexion avec PowerShell échoue avec un message d’erreur : « Les informations d’identification spécifiées ne sont pas valides »
#### <a name="problem"></a>Problème
Lorsque vous utilisez PowerShell pour vous connecter au cluster à l’aide du mode de sécurité « AzureActiveDirectory », une fois que vous vous êtes connecté à Azure AD, la connexion échoue avec un message d’erreur : « Les informations d’identification spécifiées ne sont pas valides. »

#### <a name="solution"></a>Solution
La solution est la même que pour le problème précédent.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Lorsque vous vous connectez, Service Fabric Explorer renvoie un message d’échec : « AADSTS50011 »
#### <a name="problem"></a>Problème
Lorsque vous essayez de vous connecter à Azure AD dans Service Fabric Explorer, la page renvoie un message d’échec : « AADSTS50011 : l’adresse de réponse &lt;url&gt; ne correspond pas aux adresses de réponse configurées pour l’application : &lt;guid&gt;. »

![L’adresse de réponse SFX ne correspond pas][sfx-reply-address-not-match]

#### <a name="reason"></a>Motif
L’application en cluster (web) représentant Service Fabric Explorer tente de s’authentifier auprès d’Azure AD. Dans le cadre de cette demande, elle fournit l’URL de redirection. Cette dernière ne figure cependant pas dans la liste **URL DE RÉPONSE** de l’application Azure AD.

#### <a name="solution"></a>Solution
Dans l’onglet **Configurer** de l’application en cluster (web), ajoutez l’URL de Service Fabric Explorer à la liste **URL de réponse** ou remplacez l’un des éléments de la liste. Lorsque vous avez terminé, enregistrez vos modifications.

![URL de réponse d’application web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Connecter le cluster à l’aide de l’authentification Azure AD via PowerShell
Pour connecter le cluster Service Fabric, utilisez l’exemple de commande PowerShell suivant :

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Pour en savoir plus sur l’applet de commande Connect-ServiceFabricCluster, consultez [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Puis-je utiliser le même locataire Azure AD pour plusieurs clusters ?
Oui. Cependant, n’oubliez pas d’ajouter l’URL de Service Fabric Explorer à votre application en cluster (web). Si vous ne le faites pas, Service Fabric Explorer ne fonctionnera pas.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Pourquoi dois-je disposer d’un certificat de serveur lorsqu’Azure AD est activé ?
FabricClient et FabricGateway effectuent une authentification mutuelle. Pendant l’authentification Azure AD, l’intégration Azure AD fournit une identité de client au serveur et le certificat de serveur est utilisé pour vérifier l’identité du serveur. Pour plus d’informations sur les certificats Service Fabric, consultez [Certificats X.509 et Service Fabric][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png


