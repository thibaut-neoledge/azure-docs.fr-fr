
---
title: Création d’un cluster Service Fabric sécurisé à l’aide d’Azure Resource Manager | Microsoft Docs
description: Cet article décrit comment configurer un cluster Service Fabric sécurisé dans Azure à l’aide d’Azure Resource Manager, Azure Key Vault et Azure Active Directory (AAD) pour l’authentification des clients.
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: vturecek

---
# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Création d’un cluster Service Fabric dans Azure à l’aide d’un modèle Azure Resource Manager
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portail Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Ce guide vous mène pas à pas à travers les étapes de configuration d’un cluster Azure Service Fabric sécurisé dans Azure à l’aide d’Azure Resource Manager. Il vous permet de vous familiariser avec les procédures suivantes :

* configuration d’Azure Key Vault de manière à gérer des clés pour la sécurité des clusters et des applications ;
* création d’un cluster sécurisé dans Azure avec Azure Resource Manager ;
* authentification des utilisateurs avec Azure Active Directory (AAD) pour la gestion de clusters.

Un cluster sécurisé est un cluster qui empêche tout accès non autorisé à des opérations de gestion, notamment le déploiement, la mise à niveau et la suppression d’applications, de services et des données qu’ils contiennent. Un cluster non sécurisé est un cluster auquel toute personne peut se connecter à tout moment pour effectuer des opérations de gestion. Bien qu’il soit possible de créer un cluster non sécurisé, il est **vivement recommandé de créer un cluster sécurisé**. Un cluster non sécurisé **ne peut pas être sécurisé ultérieurement** , ce qui implique la création d’un nouveau cluster.

Les concepts appliqués sont les mêmes pour la création de clusters sécurisés, qu’il s’agisse de clusters Linux ou Windows. Pour en savoir plus et pour accéder à des scripts d’assistance dédiés à la création de clusters Linux sécurisés, voir [Créer des clusters sécurisés sur Linux](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>Connexion à Azure
Ce guide utilise [Azure PowerShell][azure-powershell]. Lorsque vous démarrez une nouvelle session PowerShell, connectez-vous à votre compte Azure et sélectionnez votre abonnement avant d’exécuter des commandes Azure.

Connectez-vous à votre compte Azure :

```powershell
Login-AzureRmAccount
```

Sélectionnez votre abonnement :

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Configuration d’Azure Key Vault
Cette section vous présente la création d’un Key Vault pour un cluster Service Fabric dans Azure ainsi que pour des applications Service Fabric. Pour obtenir un guide complet sur Key Vault, consultez la documentation relative à la [prise en main d’Azure Key Vault][key-vault-get-started].

Service Fabric utilise des certificats X.509 pour sécuriser un cluster et fournir des fonctionnalités de sécurité d’applications. Azure Key Vault permet de gérer des certificats pour des clusters Service Fabric dans Azure. Lorsqu’un cluster est déployé dans Azure, le fournisseur de ressources Azure chargé de la création des clusters Service Fabric extrait les certificats de Key Vault et les installe sur les machines virtuelles du cluster.

Le diagramme suivant illustre la relation entre Key Vault, un cluster Service Fabric et le fournisseur de ressources Azure qui utilise des certificats stockés dans Key Vault lorsqu’il crée un cluster :

![Installation de certificats][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Création d’un groupe de ressources
La première étape consiste à créer un groupe de ressources dédié au Key Vault. Nous vous recommandons de placer ce Key Vault dans son propre groupe de ressources. Cela vous permet de supprimer les groupes de ressources de calcul et de stockage, y compris le groupe de ressources incluant votre cluster Service Fabric, sans risquer de perdre vos clés et secrets. Le groupe de ressources qui contient votre coffre de clés doit se trouver dans la même région que le cluster qui l’utilise.

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Création d'un coffre de clés
Créez un coffre de clés dans le nouveau groupe de ressources. Le coffre de clés **doit être activé pour le déploiement** afin d’autoriser le fournisseur de ressources Service Fabric à obtenir des certificats à partir de ce coffre et à les installer sur les nœuds de cluster :

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
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

Si vous avez un coffre de clés existant, vous pouvez l’activer pour le déploiement à l’aide de l’interface de ligne de commande :

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-key-vault"></a>Ajout de certificats à Key Vault
Les certificats sont utilisés dans Service Fabric à des fins d’authentification et de chiffrement pour sécuriser les divers aspects d’un cluster et de ses applications. Pour plus d’informations sur l’utilisation de certificats dans Service Fabric, consultez l’article [Scénarios de sécurité d’un cluster Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificat de cluster et de serveur (obligatoire)
Ce certificat est nécessaire pour sécuriser un cluster et empêcher un accès non autorisé à ce dernier. Il assure la sécurité du cluster de différentes manières :

* **Authentification du cluster :** authentifie la communication nœud à nœud pour la fédération du cluster. Seuls les nœuds qui peuvent prouver leur identité avec ce certificat peuvent être ajoutés au cluster.
* **Authentification du serveur :** authentifie les points de terminaison de gestion du cluster sur un client de gestion, afin que le client de gestion sache qu’il communique avec le véritable cluster. Ce certificat fournit également SSL pour l’API de gestion HTTPS et Service Fabric Explorer par le biais de HTTPS.

Pour cela, le certificat doit répondre aux exigences suivantes :

* Le certificat doit contenir une clé privée.
* Le certificat doit être créé pour l'échange de clés et pouvoir faire l'objet d'un export au format Personal Information Exchange (.pfx).
* Le nom d'objet du certificat doit correspondre au domaine servant à accéder au cluster Service Fabric. Cela est nécessaire pour la fourniture de SSL pour les points de terminaison de gestion HTTPS du cluster et Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d'une autorité de certification pour le domaine `.cloudapp.azure.com` . Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom d’objet du certificat doit correspondre au nom de domaine personnalisé que vous utilisez pour votre cluster.

### <a name="application-certificates-optional"></a>Certificats d’application (facultatif)
Un nombre quelconque de certificats supplémentaires peut être installé sur un cluster pour sécuriser une application. Avant de créer votre cluster, examinez les scénarios de sécurité d’application qui nécessitent l’installation d’un certificat sur les nœuds, notamment :

* Le chiffrement et déchiffrement de valeurs de configuration d’applications.
* Le chiffrement des données sur les nœuds lors de la réplication. 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Mise en forme de certificats pour une utilisation par un fournisseur de ressources Azure
Des fichiers de clé privée (.pfx) peuvent être ajoutés et utilisés directement par le biais de Key Vault. Toutefois, le fournisseur de ressources Azure nécessite que les clés soient stockées dans un format JSON spécial qui inclut le fichier .pfx en tant que chaîne encodée en base 64 et le mot de passe de clé privée. Pour répondre à ces exigences, les clés doivent être placées dans une chaîne JSON, puis stockées en tant que *secrets* dans Key Vault.

Pour faciliter ce processus, un module PowerShell est [disponible sur GitHub][service-fabric-rp-helpers]. Procédez comme suit pour utiliser le module :

1. Téléchargez le contenu complet du référentiel dans un répertoire local. 
2. Importez le module dans la fenêtre PowerShell :
   
   ```powershell
   PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
   ```

La commande `Invoke-AddCertToKeyVault` dans ce module PowerShell met automatiquement en forme une clé privée de certificat dans une chaîne JSON et la charge vers Key Vault. Il permet d’ajouter le certificat de cluster et tous les certificats d’application supplémentaires à Key Vault. Répétez simplement cette étape pour tous les certificats supplémentaires à installer sur votre cluster.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Les chaînes précédentes constituent les conditions préalables d’un Key Vault pour la configuration d’un modèle Service Fabric Cluster Resource Manager qui installe des certificats pour l’authentification de nœud, l’authentification et la sécurité des points de terminaison d’administration et les fonctionnalités de sécurité d’application supplémentaires qui utilisent des certificats X.509. À ce stade, vous disposez à présent la configuration suivante dans Azure :

* Groupe de ressources Key Vault
  * Key Vault
    * Certificat d’authentification de serveur de clusters
    * Certificats d’application

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configuration d’Azure Active Directory pour l’authentification des clients
AAD permet aux organisations (appelées locataires) de gérer l’accès utilisateur aux applications qui sont réparties entre les applications avec une interface utilisateur de connexion web et les applications avec une expérience client natif. Dans ce document, nous partons du principe que vous avez déjà créé un locataire. Si ce n’est pas le cas, commencez par lire [Obtention d’un client Azure Active Directory][active-directory-howto-tenant].

Un cluster Service Fabric offre différents points d’entrée pour leurs fonctionnalités de gestion, notamment les outils [Service Fabric Explorer][service-fabric-visualizing-your-cluster] et [Visual Studio][service-fabric-manage-application-in-visual-studio]. Par conséquent, vous allez créer deux applications AAD pour contrôler l’accès au cluster : une application web et une application native.

Pour simplifier certaines des étapes impliquées dans la configuration d’AAD avec un cluster Service Fabric, nous avons créé un ensemble de scripts Windows PowerShell.

> [!NOTE]
> Vous devez effectuer ces étapes *avant* de créer le cluster. Ainsi, dans les situations où les scripts attendent des noms de cluster et des points de terminaison, ces valeurs doivent être les valeurs planifiées et non celles que vous avez déjà créées.
> 
> 

1. [Téléchargez les scripts][sf-aad-ps-script-download] sur votre ordinateur.
2. Cliquez avec le bouton droit sur le fichier zip, choisissez **Propriétés**, puis cochez la case **Débloquer** et appliquez l’action correspondante.
3. Extrayez le fichier zip.
4. Exécutez `SetupApplications.ps1`, en indiquant TenantId, ClusterName et WebApplicationReplyUrl en tant que paramètres. Par exemple :
   
    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```
   
    Vous pouvez trouver votre **TenantId** en examinant l’URL pour le locataire dans le Portail Azure Classic. Le GUID incorporé dans cette URL est le TenantId. Par exemple :
   
    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users
   
    Le paramètre **ClusterName** est utilisé pour préfixer les applications AAD créées par le script. Il n’a pas à correspondre exactement au nom de cluster proprement dit, car il est uniquement destiné à faciliter le mappage des artefacts AAD au cluster Service Fabric avec lequel ils sont utilisés.
   
    Le paramètre **WebApplicationReplyUrl** est le point de terminaison par défaut renvoyé par AAD aux utilisateurs une fois le processus de connexion terminé. La valeur de ce paramètre doit être le point de terminaison Service Fabric Explorer pour votre cluster, qui est par défaut :
   
    https://&lt;cluster_domain&gt;:19080/Explorer
   
    Vous serez invité à vous connecter à un compte qui dispose de privilèges d’administration pour le locataire AAD. Une fois cette opération effectuée, le script effectue la création des applications web et native pour représenter votre cluster Service Fabric. Si vous examinez les applications du locataire dans le [portail Azure Classic][azure-classic-portal], vous devez voir deux nouvelles entrées :
   
   * *ClusterName*\_Cluster
   * *ClusterName*\_Client
     
     Comme le script imprime le JSON exigé par le modèle Azure Resource Manager lorsque vous créez le cluster (dans la section suivante), gardez la fenêtre PowerShell ouverte.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Création d’un modèle Service Fabric Cluster Resource Manager
Dans cette section, la sortie des commandes PowerShell précédentes est utilisée dans un modèle Service Fabric Cluster Resource Manager.

Des exemples de modèles Resource Manager sont disponibles dans la [galerie de modèles de démarrage rapide Azure sur GitHub][azure-quickstart-templates]. Ces modèles peuvent être utilisés comme point de départ pour votre modèle de cluster. 

### <a name="create-the-resource-manager-template"></a>Créer le modèle Resource Manager
Ce guide utilise l’exemple de modèle de [cluster sécurisé à 5 nœuds][service-fabric-secure-cluster-5-node-1-nodetype-wad] et ses paramètres. Téléchargez `azuredeploy.json` et `azuredeploy.parameters.json` sur votre ordinateur et ouvrez les deux fichiers dans votre éditeur de texte préféré.

### <a name="add-certificates"></a>Ajout de certificats
Les certificats sont ajoutés à un modèle Resource Manager de cluster en référençant le coffre de clés qui contient les clés de certificat. Il est recommandé de placer ces valeurs de Key Vault dans un fichier de paramètres de modèle Resource Manager pour que le fichier de modèle Resource Manager puisse être réutilisable et sans valeur spécifique à un déploiement.

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>Ajouter de tous les certificats à l’osProfile de VMSS
Chaque certificat qui doit être installé dans le cluster doit être configuré dans la section osProfile de la ressource VMSS (Microsoft.Compute/virtualMachineScaleSets). Cela indique au fournisseur de ressources d’installer le certificat sur les machines virtuelles. Y sont notamment inclus le certificat de cluster et tous les certificats de sécurité d’application que vous projetez d’utiliser pour vos applications :

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

#### <a name="configure-service-fabric-cluster-certificate"></a>Configuration d’un certificat de cluster Service Fabric
Le certificat d’authentification de cluster doit également être configuré dans la ressource de cluster Service Fabric (Microsoft.ServiceFabric/clusters) et l’extension de Service Fabric pour VMSS dans la ressource VMSS. Cela permet au fournisseur de ressources de Service Fabric de le configurer pour l’authentification du cluster et l’authentification du serveur pour les points de terminaison de gestion.

##### <a name="vmss-resource"></a>Ressource VMSS :
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

### <a name="insert-aad-config"></a>Insertion de la configuration AAD
La configuration AAD créée précédemment peut être insérée directement dans votre modèle Resource Manager. Toutefois, il est recommandé d’extraire les valeurs dans des paramètres (tout d’abord dans un fichier de paramètres) pour que le modèle Resource Manager puisse être réutilisable et sans valeur spécifique à un déploiement.

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

### <a name="a-configurearm-aconfigure-resource-manager-template-parameters"></a><a "configure-arm" ></a>Configuration des paramètres de modèle Resource Manager
Enfin, utilisez les valeurs de sortie du coffre de clés et les commandes PowerShell AAD pour renseigner le fichier de paramètres :

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
À ce stade, vous disposez de la configuration suivante :

* Groupe de ressources Key Vault
  * Key Vault
  * Certificat d’authentification de serveur de clusters
  * Certificat de chiffrement de données
* Locataire Azure Active Directory 
  * Application AAD pour la gestion basée sur le web et de Service Fabric Explorer
  * Application AAD pour la gestion du client natif
  * Utilisateurs avec des rôles affectés 
* Modèle Service Fabric Cluster Resource Manager
  * Certificats configurés par le biais de Key Vault
  * Configuration d’Azure Active Directory 

Le diagramme suivant montre comment Azure Key Vault et la configuration AAD s’appliquent à votre modèle Resource Manager.

![Mappage de dépendances de Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Création du cluster
Vous êtes maintenant prêt à créer le cluster à l’aide du [déploiement ARM][resource-group-template-deploy].

#### <a name="test-it"></a>Testez-le
Pour tester votre modèle Resource Manager avec un fichier de paramètres, utilisez la commande PowerShell suivante :

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Déployez-le
Si le test du modèle Resource Manager réussit, utilisez la commande PowerShell suivante pour déployer votre modèle Resource Manager avec un fichier de paramètres  :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Affecter des utilisateurs aux rôles
Une fois que vous avez créé les applications pour représenter votre cluster, vous devez affecter les utilisateurs aux rôles pris en charge par Service Fabric : en lecture seule et administrateur. Pour ce faire, utilisez le [portail Azure Classic][azure-classic-portal].

1. Accédez à votre locataire et choisissez Applications.
2. Choisissez l’application web, qui porte un nom similaire à `myTestCluster_Cluster`.
3. Cliquez sur l’onglet Utilisateurs.
4. Choisissez un utilisateur à affecter, puis cliquez sur le bouton **Affecter** situé en bas de l’écran.
   
    ![Bouton Assign users to roles (Affecter des utilisateurs aux rôles)][assign-users-to-roles-button]
5. Sélectionnez le rôle à affecter à l’utilisateur.
   
    ![Affecter des utilisateurs aux rôles][assign-users-to-roles-dialog]

> [!NOTE]
> Pour plus d’informations sur les rôles dans Service Fabric, consultez [Contrôle d’accès en fonction du rôle pour les clients de Service Fabric](service-fabric-cluster-security-roles.md).
> 
> 

 <a name="secure-linux-cluster"></a> 

## <a name="create-secure-clusters-on-linux"></a>Créer des clusters sécurisés sur Linux
Pour faciliter le processus, un script d’assistance est mis à votre disposition [ici](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Pour l’utilisation de ce script d’assistance, nous partons du principe que vous avez déjà installé l’interface de ligne de commande Azure, et qu’elle se trouve sur votre chemin d’accès. Vérifiez que le script dispose des autorisations d’exécution en lançant la commande `chmod +x cert_helper.py` après l’avoir téléchargé. La première étape consiste à se connecter à votre compte Azure à l’aide de l’interface de ligne de commande avec la commande `azure login` . Une fois connecté à votre compte Azure, utilisez l’assistance associée à votre certificat signé par une autorité de certification, comme l’indique la commande suivante :

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

La sortie de cette commande renvoie les trois chaînes suivantes : 

1. la valeur SourceVaultID, qui correspond à l’ID du nouvel élément KeyVault ResourceGroup créé pour vous ; 
2. une valeur CertificateUrl pour l’accès au certificat ;
3. une valeur CertificateThumbprint, qui est utilisée pour l’authentification.

L’exemple suivant explique comment utiliser la commande :

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
L’exécution de la commande précédente génère les trois chaînes suivantes :

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 Le nom d'objet du certificat doit correspondre au domaine servant à accéder au cluster Service Fabric. Cela est nécessaire pour la fourniture de SSL pour les points de terminaison de gestion HTTPS du cluster et Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d'une autorité de certification pour le domaine `.cloudapp.azure.com` . Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d'une autorité de certification, le nom d'objet du certificat doit correspondre au nom de domaine personnalisé que vous utilisez pour votre cluster.

Voici les entrées requises pour la création d’un cluster Service Fabric sécurisé (sans AAD) comme décrit dans la section [Configuration des paramètres de modèle Resource Manager](#configure-arm). Vous pouvez vous connecter au cluster sécurisé en suivant les instructions permettant d’ [authentifier l’accès client à un cluster](service-fabric-connect-to-secure-cluster.md). Les clusters Linux en version préliminaire ne prennent pas en charge l’authentification AAD. Vous pouvez attribuer des rôles d’administrateur et de client comme décrit dans la section [Affecter des utilisateurs aux rôles](#assign-roles). Lorsque vous indiquez les rôles d’administrateur et de client associés à un cluster Linux en version préliminaire, vous devez fournir des empreintes de certificat à des fins d’authentification (et non un nom d’objet, car aucune révocation ou validation de chaîne n’est effectuée dans la version préliminaire).

Si vous souhaitez utiliser un certificat auto-signé à des fins de test, vous pouvez utiliser le même script pour générer un certificat auto-signé et le charger dans le KeyVault, en fournissant l’indicateur `ss` au lieu du chemin d’accès et du nom du certificat. Par exemple, consultez la commande suivante pour créer et télécharger un certificat auto-signé :

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Cette commande renvoie les trois mêmes chaînes : SourceVault, CertificateUrl et CertificateThumbprint, qui est utilisée pour créer un cluster Linux sécurisé, ainsi que l’emplacement hébergeant le certificat auto-signé. Vous aurez besoin du certificat auto-signé pour vous connecter au cluster.  Vous pouvez vous connecter au cluster sécurisé en suivant les instructions permettant d’ [authentifier l’accès client à un cluster](service-fabric-connect-to-secure-cluster.md). Le nom d'objet du certificat doit correspondre au domaine servant à accéder au cluster Service Fabric. Cela est nécessaire pour la fourniture de SSL pour les points de terminaison de gestion HTTPS du cluster et Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d'une autorité de certification pour le domaine `.cloudapp.azure.com` . Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d'une autorité de certification, le nom d'objet du certificat doit correspondre au nom de domaine personnalisé que vous utilisez pour votre cluster.

Les paramètres fournis par le script d’assistance peuvent être insérés directement dans le portail, comme indiqué dans la section [Création d’un cluster dans le portail Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-portal).

## <a name="next-steps"></a>Étapes suivantes
À ce stade, vous avez un cluster sécurisé avec l’authentification de gestion fournie par Azure Active Directory. Ensuite, [connectez-vous à votre cluster](service-fabric-connect-to-secure-cluster.md) et découvrez comment [gérer les secrets d’application](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Résoudre les problèmes de configuration d’Azure Active Directory pour l’authentification des clients
Si vous rencontrez un problème lors de la configuration d’Azure Active Directory pour l’authentification des clients, étudiez les suggestions suivantes.

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Service Fabric Explorer demande de sélectionner un certificat
#### <a name="problem"></a>Problème
Une fois la connexion correctement établie sur la page de connexion AAD dans Service Fabric Explorer, le navigateur revient à la page d’accueil, mais vous invite à sélectionner un certificat.

![Boîte de dialogue SFX de sélection d’un certificat][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motif
Aucun rôle n’a été affecté à l’utilisateur dans l’application AAD en cluster. L’authentification AAD échoue sur le cluster Service Fabric. Service Fabric Explorer revient à l’authentification par certificat.

#### <a name="solution"></a>Solution
Suivez les instructions de configuration d’AAD et attribuez des rôles utilisateur. Nous recommandons d’activer l’option « AFFECTATION DE L’UTILISATEUR REQUISE POUR ACCÉDER À L’APPLICATION », comme sur `SetupApplications.ps1`.

### <a name="connect-with-powershell-fails-with-error-the-specified-credentials-are-invalid"></a>La connexion avec PowerShell échoue avec une erreur indiquant que les informations d’identification spécifiées ne sont pas valides
#### <a name="problem"></a>Problème
Lorsque vous utilisez PowerShell pour vous connecter au cluster à l’aide du mode de sécurité « AzureActiveDirectory », une fois la connexion établie sur la page de connexion AAD, la connexion échoue avec une erreur indiquant que les informations d’identification spécifiées ne sont pas valides.

#### <a name="solution"></a>Solution
Identique à ce qui précède.

### <a name="service-fabric-explorer-signing-in-return-failure-aadsts50011"></a>Échec de la connexion à Service Fabric Explorer : AADSTS50011
#### <a name="problem"></a>Problème
Une fois la connexion établie sur la page de connexion AAD de Service Fabric Explorer, la page renvoie un message d’échec de connexion (AADSTS50011) : l’adresse de réponse &lt;url&gt; ne correspond pas aux adresses de réponse configurées pour l’application : &lt;guid&gt;. 

![L’adresse de réponse SFX ne correspond pas][sfx-reply-address-not-match]

#### <a name="reason"></a>Motif
L’application cluster (web) représentant Service Fabric Explorer tente de s’authentifier auprès d’AAD. Dans le cadre de la demande, elle fournit l’URL de redirection. Cette dernière ne figure cependant pas dans la liste de « REPLY URL » de l’application AAD.

#### <a name="solution"></a>Solution
Ajoutez l’URL de Service Fabric Explorer à la liste « REPLY URL » dans l’onglet Configurer de l’application cluster (web) ou remplacez l’un des éléments de la liste. Enregistrez.

![URL de réponse d’application web][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters"></a>Puis-je utiliser le même client AAD pour plusieurs clusters ?
#### <a name="answer"></a>Réponse
Oui. Mais n’oubliez pas d’ajouter l’URL de Service Fabric Explorer à votre application cluster (web). Dans le cas contraire, Service Fabric Explorer ne fonctionnera pas.

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled"></a>Pourquoi dois-je disposer d’un certificat de serveur lorsqu’AAD est activé ?
#### <a name="answer"></a>Réponse
FabricClient et FabricGateway effectuent une authentification mutuelle. En cas d’authentification AAD, l’intégration AAD fournit l’identité du client au serveur et le certificat de serveur est utilisé pour vérifier l’identité du serveur. Pour plus d’informations sur le fonctionnement du certificat dans Service Fabric, consultez [Certificats X.509 et Service Fabric][x509-certificates-and-service-fabric]

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
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
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


<!--HONumber=Oct16_HO2--->


