<properties
   pageTitle="Sécuriser un cluster Service Fabric avec des certificats | Microsoft Azure"
   description="Comment sécuriser un cluster Service Fabric à l’aide de certificats X.509."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Sécuriser un cluster Service Fabric sur Azure à l’aide de certificats

Un cluster Azure Service Fabric est une ressource que vous possédez. Pour éviter tout accès non autorisé à cette ressource, vous devez la sécuriser, notamment lorsque des charges de travail de production sont exécutées dessus. Pour configurer un cluster Service Fabric sécurisé à l’aide de certificats X.509, vous avez besoin au minimum d’un certificat de serveur X.509, que vous chargez vers le coffre de clés Azure et que vous utilisez lors de la création de clusters.

Cet article correspond à [l’étape 3: Configurer la sécurité](service-fabric-cluster-creation-via-portal.md#step-3--configure-security) du processus de création du cluster. Pour plus d’informations sur la façon dont Service Fabric utilise les certificats X.509, voir [Scénarios de sécurité des clusters](service-fabric-cluster-security.md).

La procédure comporte trois étapes distinctes :

1. Acquisition du certificat X.509.
2. Chargement du certificat vers le coffre de clés Azure.
3. Indication de l’emplacement et des informations du certificat au processus de création de clusters Service Fabric.

<a id="acquirecerts"></a>
## Étape 1 : Acquisition des certificats X.509

Vous devez utiliser un certificat X.509 signé par une [autorité de certification](https://en.wikipedia.org/wiki/Certificate_authority) pour sécuriser les clusters exécutant des charges de travail de production. Pour plus d’informations sur l’obtention de ces certificats, accédez à [Procédure : obtenir un certificat](http://msdn.microsoft.com/library/aa702761.aspx).

Pour les clusters que vous utilisez à des fins de test uniquement, vous pouvez choisir d’utiliser un certificat auto-signé. L’étape 2.5 ci-dessous explique comment procéder.

## Étape 2 : Chargement du certificat X.509 dans le coffre de clés

Comme il s’agit d’un processus complexe, nous avons chargé un module PowerShell vers un référentiel Git qui l’effectue à votre place.

### Étape 2.1
Assurez-vous qu’Azure PowerShell 1.0+ est installé sur votre machine. Si ce n’est déjà fait, suivez les étapes décrites dans [Installation et configuration d’Azure PowerShell.](../powershell-install-configure.md)

### Étape 2.2
Copiez le dossier *ServiceFabricRPHelpers* depuis ce [référentiel Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers) vers votre ordinateur.

### Étape 2.3
Ouvrez une fenêtre PowerShell et accédez au répertoire dans lequel vous avez téléchargé le module. Importez ensuite le module à l’aide de la commande suivante.

```powershell
Import-Module .\ServiceFabricRPHelpers.psm1
```

### Étape 2.4
Si vous utilisez un certificat que vous avez déjà acquis, suivez la procédure indiquée dans cette étape. Sinon, passez à étape 2.5, qui explique comment créer et déployer le certificat auto-signé dans le coffre de clés.

Vous pouvez utiliser un groupe de ressources et un coffre de clés existants pour stocker le certificat, ou vous pouvez créer un groupe de ressources et/ou un coffre de clés si ceux-ci n’existent pas déjà. Un coffre de clés existant doit être configuré pour prendre en charge le déploiement à l’aide de ce script.

```powershell
Login-AzureRmAccount

Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

Pour télécharger le certificat vers votre groupe de ressources et votre coffre de clés, exécutez le script suivant. Le groupe de ressources et le coffre de clés seront créés s’ils n’existent pas déjà.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
Voici un exemple de script rempli.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

À l’achèvement de l’exécution du script, vous obtenez un résultat similaire à celui qui suit, dont vous avez besoin pour l’étape 3 (Configurer un cluster sécurisé).

```
Certificate Thumbprint: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

SourceVault /Resource ID of the key vault :  /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea
```

Vous disposez maintenant des informations nécessaires pour configurer un cluster sécurisé. Passez à l’étape 3.

### Étape 2.5
Si vous *ne disposez pas* d’un certificat et que vous voulez créer un certificat auto-signé et le charger dans le coffre de clés, procédez comme suit. Les certificats auto-signés doivent uniquement être utilisés pour les clusters de test et non pour les clusters de produit.

Vous pouvez utiliser un groupe de ressources et un coffre de clés existants pour stocker le certificat, ou vous pouvez créer un groupe de ressources et/ou un coffre de clés si ceux-ci n’existent pas déjà. Un coffre de clés existant doit être configuré pour prendre en charge le déploiement à l’aide de ce script.

```powershell
Login-AzureRmAccount
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

Le script suivant créera un groupe de ressources et/ou un coffre de clés s’ils ne sont pas déjà présents, créera et téléchargera un certificat auto-signé dans le coffre de clés, et publiera le nouveau certificat sur *OutputPath*.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```
La chaîne *DnsName* spécifie un ou plusieurs noms DNS à placer dans l’extension d’autre nom de l’objet du certificat, lorsqu’un certificat à copier n’est pas spécifié dans le paramètre CloneCert. Le premier nom DNS est également enregistré en tant que nom de l’objet. Si aucun certificat de signature n’est spécifié, le premier nom DNS est également enregistré en tant que nom de l’émetteur. L’applet de commande *Invoke-AddCertToKeyVault* utilise [l’applet de commande New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633.aspx) pour créer le certificat auto-signé.

Voici un exemple de script rempli.

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

À l’achèvement de l’exécution du script, vous obtenez un résultat similaire à celui qui suit. Vous en avez besoin pour l’étape 3.

```
Certificate Thumbprint: 64881409F4D86498C88EEC3697310C15F8F1540F

SourceVault /Resource ID of the key vault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea
```

## Étape 3 : Configuration d’un cluster sécurisé

Une fois vos certificats téléchargés dans un coffre de clés Azure, vous pouvez créer un cluster sécurisé avec ces certificats. Cette étape correspond à [l’étape 3 : Configurer la sécurité](service-fabric-cluster-creation-via-portal.md#step-3--configure-security) du processus de création du cluster et explique comment configurer vos configurations de sécurité.

>[AZURE.NOTE]
Les certificats exigés sont spécifiés au niveau du type de nœud sous Configurations de sécurité. Vous devez spécifier ceci pour chaque type de nœud inclus dans votre cluster. Bien que ce document vous guide dans la réalisation de cette procédure à l’aide du portail, vous pouvez faire de même à l’aide d’un modèle Azure Resource Manager.

![Capture d’écran de Configurations de sécurité dans le portail Azure][SecurityConfigurations_01]

### Paramètres obligatoires

- **Mode de sécurité** Sélectionnez **Certificat X509 ** pour configurer un cluster sécurisé avec les certificats X.509.
- **Niveau de protection du cluster** Consultez ce [document sur les niveaux de protection](https://msdn.microsoft.com/library/aa347692.aspx) pour comprendre la signification de chacune de ces valeurs. Même si nous autorisons ici trois valeurs (EncryptAndSign, Sign et None), il est préférable de conserver la valeur par défaut EncryptAndSign sauf si vous savez ce que vous faites.
- **Coffre source** Ce paramètre désigne l’ID de ressource du coffre de clés. Il doit respecter le format suivant :

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **URL de certificat** Ce paramètre désigne l’URL de l’emplacement où le certificat a été chargé dans votre coffre de clés. Il doit respecter le format suivant :

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **Empreinte de certificat** Ce paramètre désigne l’empreinte du certificat, qui est disponible à l’URL que vous avez spécifiée plus haut.

### Paramètres facultatifs

 Vous pouvez éventuellement spécifier des certificats supplémentaires pour les ordinateurs clients que vous utilisez pour effectuer les opérations sur le cluster. Par défaut, l’empreinte que vous avez spécifiée dans les paramètres obligatoires est ajoutée à la liste des empreintes qui sont autorisées pour effectuer les opérations des clients.

**Client administrateur** : ces informations permettent de valider que le client qui se connecte au point de terminaison de gestion des clusters présente les informations d’identification appropriées pour effectuer l’administration et les actions en lecture seule sur le cluster. Vous pouvez spécifier plusieurs certificats de votre choix pour les opérations d’administration.

- **Autoriser par** Ce paramètre indique à Service Fabric si ce certificat doit être recherché en utilisant le nom de l’objet ou l’empreinte. Pour des raisons de sécurité, l’utilisation du nom de l’objet pour l’autorisation n’est pas recommandée, mais elle offre davantage de flexibilité.
- Le **nom de l’objet** est nécessaire uniquement si vous avez spécifié l’autorisation par nom d’objet.
- **Empreinte de l’émetteur** Ce paramètre fournit un niveau supplémentaire de vérification que le serveur peut effectuer quand un client présente ses informations d’identification sur le serveur.

**Client en lecture seule **: ces informations permettent de valider que le client qui se connecte au point de terminaison de gestion des clusters présente les informations d’identification appropriées pour effectuer les actions en lecture seule sur le cluster. Vous pouvez spécifier plusieurs certificats de votre choix pour les opérations en lecture seule.

- **Autoriser par** Ce paramètre indique à Service Fabric si ce certificat doit être recherché en utilisant le nom de l’objet ou l’empreinte. Pour des raisons de sécurité, l’utilisation du nom de l’objet pour l’autorisation n’est pas recommandée, mais elle offre davantage de flexibilité.
- Le **nom de l’objet** est nécessaire uniquement si vous avez spécifié l’autorisation par nom d’objet.
- **Empreinte de l’émetteur** Ce paramètre fournit un niveau supplémentaire de vérification que le serveur peut effectuer quand un client présente ses informations d’identification sur le serveur.

## Étapes suivantes
Après avoir configuré des certificats de sécurité sur votre cluster, reprenez le processus de création de cluster à [l’étape 4 : Terminer la création du cluster](service-fabric-cluster-creation-via-portal.md#step-4--complete-the-cluster-creation).

Après avoir créé le cluster, vous pouvez ultérieurement [mettre à jour un certificat](service-fabric-cluster-security-update-certs-azure.md).


<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0615_2016-->