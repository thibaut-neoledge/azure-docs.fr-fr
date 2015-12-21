<properties
   pageTitle="Comment sécuriser un cluster Service Fabric | Microsoft Azure"
   description="Comment sécuriser un cluster Service. Quelles sont les options ?"
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
   ms.date="11/10/2015"
   ms.author="chackdan"/>

# Sécurisation d’un cluster Service Fabric

Un cluster Service Fabric est une ressource que vous possédez. Afin d’éviter tout accès non autorisé à la ressource, vous devez la sécuriser, surtout lorsque des charges de travail de production sont exécutées dessus. Ce document vous guide à travers la procédure permettant de le faire.

##  Scénarios de sécurité du cluster à envisager

Service Fabric assure la sécurité dans les scénarios suivants :

1. **Sécurité nœud à nœud** ou sécurisation d’un cluster pour la communication nœud à nœud. Sécurise la communication entre les machines virtuelles/ordinateurs dans le cluster. Cela garantit que seuls les ordinateurs qui sont autorisés à rejoindre le cluster peuvent participer à l’hébergement des applications et services dans le cluster.

	![Nœud à nœud][Node-to-Node]

2. **Sécurité Client à nœud** ou sécurisation d’un client Service Fabric communicant avec un nœud particulier dans le cluster. Authentifie et sécurise les communications client, ce qui garantit que seuls les utilisateurs autorisés sont en mesure d’accéder au cluster et aux applications déployées sur le cluster Windows Fabric. Les clients sont identifiés de manière grâce à leurs informations d’identification de sécurité Windows ou à leurs informations d’identification de sécurité de certificat.

	![Client à nœud][Client-to-Node]

	Pour chaque type de scénario de communication (Nœud à nœud ou Client à nœud), Service Fabric fournit un support en utilisant la [sécurité de certificat](https://msdn.microsoft.com/library/ff649801.aspx) ou la [sécurité Windows](https://msdn.microsoft.com/library/ff649396.aspx). Les choix pour la sécurité Nœud à nœud ou Client à nœud sont indépendants les uns des autres et peut être identiques ou différents pour chaque scénario.

	Dans Azure, Service Fabric utilise des certificats de serveur X509 que vous spécifiez dans le cadre des configurations du type de nœud, lorsque vous créez un cluster. Pour un aperçu rapide de ce que sont ces certificats et de la manière dont vous pouvez les acquérir et les créer, rendez-vous au bas de cette page.

3. **Contrôle d’accès en fonction du rôle (RBAC)** : possibilité de limiter les opérations d’administration dans les opérations en lecture seule sur le cluster à un ensemble de certificats.

4. **Comptes de service et RunAs** : Service Fabric lui-même s’exécute comme un processus de service Windows (Fabric.exe) et le compte de sécurité dans lequel s’exécute le processus Fabric.exe est configurable. Les comptes de processus dans lesquels Fabric.exe s’exécute sur chaque nœud dans le cluster peuvent être sécurisés, ainsi que les processus hôte de service qui sont activés pour chaque service. Consultez le document [Sécurité des applications et Runas](service-fabric-application-runas-security.md) pour plus d’informations.
  

## Comment sécuriser le cluster Service Fabric à l’aide de certificats.

Pour configurer un cluster Service Fabric sécurisé, vous devez disposer d’au moins un serveur / certificat x509. Vous le chargez ensuite sur le coffre de clés Azure et l’utilisez dans le processus de création de clusters.

Il existe trois étapes distinctes :

1. Acquérir le certificat x509.
2. Charger le certificat sur le coffre de clés Azure.
3. Fournir l’emplacement et les informations du certificat au processus de création de clusters Service Fabric.

 
## Étape 1 : Acquérir les certificats x509

1. Pour les clusters exécutant des charges de travail de production, vous devez utiliser un certificat x509 signé par une [autorité de certification (CA)](https://en.wikipedia.org/wiki/Certificate_authority) pour sécuriser le cluster. Pour des informations sur l’obtention de ces certificats, rendez-vous à l’adresse [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Pour les clusters que vous utilisez aux fins de test uniquement, vous pouvez choisir d’utiliser un certificat auto-signé. L’étape 2.5 présentera la procédure correspondante.


## Étape 2 : Chargement du certificat x509 sur le coffre de clés

Il s’agit d’un processus complexe, par conséquent nous avons chargé un module PowerShell sur un référentiel Git qui l’effectue pour vous.

**Étape 2.1** : copiez ce dossier sur votre ordinateur à partir de ce [référentiel Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Étape 2.2** : assurez-vous que le Kit de développement logiciel (SDK) Azure 1.0 (ou une version supérieure) est installé sur votre ordinateur.

**Étape 2.3**: ouvrez une fenêtre PowerShell et importez ServiceFabricRPHelpers.psm.

```
Remove-Module ServiceFabricRPHelpers
```

Copiez les éléments suivants et remplacez le chemin d’accès vers .psm1 par celui de votre ordinateur. Voici un exemple ```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```
  

**Étape 2.4** : si vous utilisez un certificat que vous avez déjà acquis, suivez la procédure, sinon passez à l’étape 2.5.


Connectez-vous à votre compte Azure.

```
Login-AzureRmAccount
```

Le script crée un groupe de ressources et/ou un coffre s’ils n’existent pas déjà.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file> 
```
Voici un exemple de script rempli. ```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ") -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx 
```

Une fois le script réussi, vous obtenez un résultat similaire à celui ci-dessous. Vous en avez besoin pour l’étape 3.

1. **Empreinte de certificat** : 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A
2. **SourceVault** /ID de ressource de KeyVault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **URL de certificat** : URL vers l’emplacement du certificat dans le coffre de clés : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea 

Vous disposez des informations nécessaires pour configurer un cluster sécurisé. Passez à l’étape 3.


**Étape 2.5** : si vous souhaitez créer un certificat auto-signé et le charger dans le coffre de clés.

Connectez-vous à votre compte Azure.

```
Login-AzureRmAccount
```

Le script crée un groupe de ressources et/ou un coffre s’ils n’existent pas déjà.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file> 
```
Le chemin de sortie que vous avez donné au script contiendra le nouveau certificat auto-signé que nous avons chargé dans le coffre de clés.


**Remarque** Le nom DNS <String> spécifie un ou plusieurs noms DNS à placer dans l’extension d’autre nom de l’objet du certificat, lorsqu’un certificat à copier n’est pas spécifié via le paramètre CloneCert. Le premier nom DNS est également enregistré en tant que nom de l’objet. Si aucun certificat de signature n’est spécifié, le premier nom DNS est également enregistré en tant que nom de l’émetteur.

Des informations supplémentaires générales concernant la création d’un certificat auto-signé personnel sont disponibles à l’adresse [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx).

Voici un exemple de script rempli. ```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ") -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Puisqu’il s’agit d’un certificat auto-signé, vous devrez l’importer dans votre magasin d’ordinateurs autorisés avant de pouvoir l’utiliser ce certificat pour vous connecter à un cluster sécurisé. ```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
``` ```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

Une fois le script réussi, vous obtenez un résultat similaire à celui ci-dessous. Vous en avez besoin pour l’étape 3.

1. **Empreinte de certificat** : 64881409F4D86498C88EEC3697310C15F8F1540F
2. **SourceVault** /ID de ressource de KeyVault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **URL de certificat** : URL vers l’emplacement du certificat dans le coffre de clés : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea 

##Étape 3 : Configuration d’un cluster sécurisé 

Suivez les étapes décrites dans le document [Processus de création de clusters Service Fabric](service-fabric-cluster-creation-via-portal.md), jusqu’à atteindre les configurations de sécurité. Voici comment définir les configurations de sécurité.

Les certificats qui doivent être utilisés sont spécifiés au niveau du type de nœud sous les configurations de sécurité. Vous devez spécifier ceci pour chaque type de nœud que vous avez dans votre cluster. Bien que ce document vous guide sur la réalisation de cette procédure à l’aide du portail, vous pouvez faire la même chose à l’aide d’un modèle ARM.

![SecurityConfigurations\_01][SecurityConfigurations_01]

Paramètres obligatoires

- Le **mode Sécurité** veille à ce que vous sélectionniez le certificat x509. Il indique à Service Fabric que vous comptez configurer un cluster sécurisé. 
- Le **niveau de protection du cluster** se rapporte à ce [document de niveau de protection](https://msdn.microsoft.com/library/aa347692.aspx) pour comprendre ce que signifie chacune de ces valeurs. Trois valeurs sont autorisées ici : EncryptAndSign, Sign, None. Il est préférable de conserver la valeur par défaut EncryptAndSign, sauf si vous savez ce que vous faites.
- Le **coffre-fort source** fait référence à l’ID de ressource du coffre de clés, et doit être au format de ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```

- L’**URL de certificat** fait référence à l’URL de l’emplacement dans votre coffre de clés où le certificat a été chargé, et doit être au format de ```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```
- L’**empreinte de certificat** fait référence à l’empreinte du certificat, qui est disponible à l’URL que vous avez spécifiée précédemment.

Paramètres facultatifs : vous pouvez éventuellement spécifier des certificats supplémentaires des machines clientes que vous utilisez pour effectuer des opérations sur le cluster. Par défaut, l’empreinte que vous avez spécifiée dans les paramètres obligatoires est ajoutée à la liste autorisée des empreintes qui sont autorisées pour les opérations des clients.

Client administrateur : ces informations sont utilisées pour valider que le client qui se connecte au point de terminaison de la gestion de clusters présente en effet les informations d’identification appropriées pour effectuer l’administration et les actions en lecture seule sur le cluster. Vous pouvez spécifier plusieurs certificats que vous souhaitez autoriser pour les opérations d’administration.


- **Autoriser par** indique à Service Fabric si ce certificat doit être recherché en utilisant le nom de l’objet ou par empreinte. Pour des raisons de sécurité, l’utilisation du nom de l’objet pour l’autorisation n’est pas recommandée. Elle offre cependant davantage de flexibilité.


- Le **nom de l’objet** est nécessaire uniquement si vous avez spécifié l’autorisation par nom d’objet.
- **Empreinte de l’émetteur** : autorise un niveau supplémentaire de vérification que le serveur peut effectuer lorsqu’un client présente ses informations d’identification sur le serveur.

Client en lecture seule : ces informations sont utilisées pour valider que le client qui se connecte au point de terminaison de la gestion de clusters présente en effet les informations d’identification appropriées pour effectuer les actions en lecture seule sur le cluster. Vous pouvez spécifier plusieurs certificats que vous souhaitez autoriser pour les opérations en lecture seule.


- **Autoriser par** indique à Service Fabric si ce certificat doit être recherché en utilisant le nom de l’objet ou par empreinte. Pour des raisons de sécurité, l’utilisation du nom de l’objet pour l’autorisation n’est pas recommandée. Elle offre cependant davantage de flexibilité.

- Le **nom de l’objet** est nécessaire uniquement si vous avez spécifié l’autorisation par nom d’objet.
- **Empreinte de l’émetteur** : autorise un niveau supplémentaire de vérification que le serveur peut effectuer lorsqu’un client présente ses informations d’identification sur le serveur.


## Comment mettre à jour les certificats dans le cluster

Service Fabric vous permet de spécifier deux certificats, un principal et un secondaire. Celui que vous avez spécifié lors de la création est par défaut le certificat principal. Pour ajouter un autre certificat, vous devez déployer ce certificat sur les machines virtuelles du cluster. L’étape 2 ci-dessus décrit comment vous pouvez charger un nouveau certificat dans le coffre de clés. Pour ce faire, vous pouvez utiliser le même coffre de clés que pour le premier certificat.

Consultez [Déployer des certificats sur les machines virtuelles à partir de coffres de clés gérés par les clients](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) pour connaître la procédure.

Une fois cette opération réussie, accédez au portail ou utilisez ARM pour indiquer à Service Fabric que vous disposez d’un certificat secondaire qui peut également être utilisé. Il vous suffit de disposer d’une empreinte.

Voici la procédure : dans le portail, accédez à la ressource de cluster à laquelle vous souhaitez ajouter à ce certificat, cliquez sur le paramètre de certificat et entrez l’empreinte du certificat secondaire. Appuyez sur Enregistrer. Un déploiement sera lancé et une fois qu’il aura réussi, vous pourrez utiliser le certificat principal ou le secondaire pour effectuer des opérations de gestion sur le cluster.

![SecurityConfigurations\_02][SecurityConfigurations_02]

Si vous souhaitez supprimer l’un des certificats, vous pouvez le faire. Veillez à appuyer sur Enregistrer après avoir supprimé un certificat, afin de lancer un nouveau déploiement. Une fois que ce déploiement est terminé, le certificat supprimé ne peut plus être utilisé pour se connecter au cluster. Pour un cluster sécurisé, vous aurez toujours besoin d’au moins un certificat déployé valide (non révoqué ou expiré), sinon vous ne pourrez pas accéder au cluster.

Il existe un événement de diagnostic qui vous permet de savoir si les certificats sont proches de l’expiration.



## Que sont les certificats X509 ?

Les certificats numériques X509 sont couramment utilisés pour authentifier des clients et des serveurs, chiffrer et signer numériquement les messages. Pour plus d’informations sur ces certificats, rendez-vous à l’adresse [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx).

**Remarque :**

1. Certificats utilisés dans les clusters exécutant des charges de travail de production - Ceux-ci devraient être créés en utilisant un service de certificats Windows Server correctement configuré ou obtenus via une [autorité de certification (CA)](https://en.wikipedia.org/wiki/Certificate_authority) approuvée.
2. N’utilisez jamais de certificats temporaires ou de tests créés avec des outils comme MakeCert.exe en production.
3. Pour les clusters que vous utilisez aux fins de test uniquement, vous pouvez choisir d’utiliser un certificat auto-signé. 


## Quels sont les certificats de serveur et les certificats clients ?

**Certificats de serveur /X 509**

La tâche principale des certificats de serveur est l’authentification du serveur (nœud) pour les clients, ou du serveur (nœud) pour un serveur (nœud). L’une des vérifications initiales à effectuer lorsqu’un client ou un nœud authentifie un nœud consiste à comparer la valeur du nom commun dans le champ Objet pour s’assurer qu’il est présent dans la liste des noms communs autorisées qui a été configurée. Ce nom commun ou l’un des autres noms de l’objet des certificats doit figurer dans la liste des noms communs autorisés.

L’article suivant décrit comment générer des certificats avec d’autres noms de l’objet (SAN). [http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351).
 
**Remarque :** le champ Objet peut contenir plusieurs valeurs, chacune précédée d’une initialisation pour indiquer la valeur. En règle générale, l’initialisation est « CN » pour « nom commun » (common name), par exemple, « CN = www.contoso.com ». Il est également possible que le champ Objet soit vide. Notez également le champ Autre nom de l’objet. S’il est renseigné, il doit contenir le nom commun du certificat et une entrée par Autre nom de l’objet. Ils sont entrés sous forme de valeurs de nom DNS.

Notez également que la valeur du champ Rôles prévus du certificat doit inclure une valeur appropriée, comme Authentification serveur ou Authentification client.

**Certificats clients**

Les certificats clients ne sont généralement pas émis par une autorité de certification tierce. Au lieu de cela, le magasin personnel de l’emplacement actuel de l’utilisateur contient généralement des certificats placés là par une autorité racine avec un rôle prévu d’authentification client. Le client peut utiliser un certificat lorsque l’authentification mutuelle est requise. Toutes les opérations de gestion sur le cluster Service Fabric requièrent des certificats de serveur. Les certificats clients ne doivent pas être utilisés.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [Processus de mise à niveau du cluster Service Fabric et attentes à votre égard](service-fabric-cluster-upgrade.md)
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_1210_2015-->