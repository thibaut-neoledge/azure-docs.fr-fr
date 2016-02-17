<properties
   pageTitle="Sécuriser un cluster Service Fabric | Microsoft Azure"
   description="Sécurisation d’un cluster Service Fabric Quelles sont les options ?"
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
   ms.date="02/01/2016"
   ms.author="chackdan"/>

# Sécuriser un cluster Service Fabric

Un cluster Azure Service Fabric est une ressource que vous possédez. Pour éviter tout accès non autorisé à cette ressource, vous devez la sécuriser, notamment lorsque des charges de travail de production sont exécutées dessus. Cet article vous guide tout au long du processus de sécurisation d’un cluster Service Fabric.

##  Scénarios de sécurité du cluster

Service Fabric assure la sécurité dans les scénarios suivants :

1. **Sécurité nœud à nœud** : sécurise la communication entre les machines virtuelles et les ordinateurs du cluster. Cette sécurité garantit que seuls les ordinateurs qui sont autorisés à rejoindre le cluster peuvent participer à l’hébergement des applications et des services dans le cluster.

	![Diagramme de communication nœud à nœud][Node-to-Node]

2. **Sécurité client à nœud **: sécurise la communication entre un client Service Fabric et des nœuds individuels du cluster. Ce type de sécurité authentifie et sécurise les communications du client, ce qui garantit que seuls les utilisateurs autorisés peuvent accéder au cluster et aux applications déployées sur le cluster. Les clients sont identifiés de manière grâce à leurs informations d’identification de sécurité Windows ou à leurs informations d’identification de sécurité de certificat.

	![Diagramme de communication client à nœud][Client-to-Node]

	Pour la sécurité de type nœud à nœud ou client à nœud, vous pouvez utiliser la [sécurité de certificat](https://msdn.microsoft.com/library/ff649801.aspx) ou la [sécurité de Windows](https://msdn.microsoft.com/library/ff649396.aspx). Les choix de la sécurité nœud à nœud ou client à nœud sont indépendants les uns des autres, et peuvent être identiques ou différents.

	Azure Service Fabric utilise des certificats de serveur X509 que vous spécifiez dans le cadre des configurations du type de nœud, lorsque vous créez un cluster. La fin de cet article propose un rapide aperçu de ce que sont ces certificats et de la façon dont vous pouvez les acquérir ou les créer.

3. **Contrôle d’accès en fonction du rôle (RBAC)** : cette sécurité restreint les opérations d’administration effectuées sur le cluster à un ensemble particulier de certificats.


## Sécuriser un cluster Service Fabric à l’aide de certificats

Pour configurer un cluster Service Fabric sécurisé, vous devez disposer d’au moins un certificat de serveur x509. Vous le chargez ensuite sur le coffre de clés Azure et l’utilisez dans le processus de création de clusters.

La procédure comporte trois étapes distinctes :

1. Acquisition du certificat X.509.
2. Chargement du certificat vers le coffre de clés Azure.
3. Indication de l’emplacement et des informations du certificat au processus de création de clusters Service Fabric.

### Étape 1 : Acquisition des certificats X.509

1. Pour les clusters exécutant des charges de travail de production, vous devez utiliser un certificat x509 signé par une [autorité de certification (CA)](https://en.wikipedia.org/wiki/Certificate_authority) pour sécuriser le cluster. Pour des informations sur l’obtention de ces certificats, rendez-vous à l’adresse [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Pour les clusters que vous utilisez aux fins de test uniquement, vous pouvez choisir d’utiliser un certificat auto-signé. 


### Étape 2 : Chargement du certificat X.509 dans le coffre de clés

Comme il s’agit d’un processus complexe, nous avons chargé un module PowerShell vers un référentiel Git qui l’effectue à votre place.

**Étape 2.1** : copiez ce dossier sur votre ordinateur à partir de ce [référentiel Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Étape 2.2** : assurez-vous qu’Azure PS 1.0 (ou une version supérieure) est installé sur votre ordinateur. Si ce n’est déjà fait, nous vous recommandons vivement de suivre les étapes décrites dans [Installer et configurer Azure PowerShell](../powershell-install-configure.md).

**Étape 2.3** : ensuite, ouvrez une fenêtre PowerShell et importez ServiceFabricRPHelpers.psm (il s’agit du module que vous avez téléchargé à l’étape 2.1).

```
Remove-Module ServiceFabricRPHelpers
```

Copiez l’exemple suivant et modifiez le chemin d’accès à .psm1 pour qu’il corresponde à celui de votre ordinateur.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

**Étape 2.4** : si vous utilisez un certificat que vous avez déjà acquis, procédez comme suit, sinon, passez à l’étape 2.5. L’étape 2.5 indique comment créer le certificat auto-signé et le déployer sur le coffre de clés.

Connectez-vous à votre compte Azure. Si cette commande PowerShell échoue, vous devez vérifier si Azure PS est correctement installé.

```
Login-AzureRmAccount
```

Le script suivant crée un groupe de ressources et/ou un coffre s’ils n’existent pas déjà.

```
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
Voici un exemple de script rempli.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

À l’achèvement de l’exécution du script, vous obtenez un résultat similaire à celui qui suit. Notez ce résultat, car vous en aurez besoin à l’étape 3 (Configuration d’un cluster sécurisé).

1. **Empreinte de certificat** : 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A
2. **SourceVault** /ID de ressource de KeyVault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **URL de certificat** : URL vers l’emplacement du certificat dans le coffre de clés : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea 

Vous disposez maintenant des informations nécessaires pour configurer un cluster sécurisé. Passez à l’étape 3.

**Étape 2.5** : si vous *ne disposez pas* d’un certificat et que vous souhaitez créer un certificat auto-signé et le charger sur le coffre de clés, procédez comme suit.

Connectez-vous à votre compte Azure. Si cette commande PowerShell échoue, vous devez vérifier si Azure PS est correctement installé.

```
Login-AzureRmAccount
```

Le script suivant crée un groupe de ressources et/ou un coffre de clés s’ils n’existent pas déjà.

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```

Le chemin de sortie que vous avez donné au script contiendra le nouveau certificat auto-signé que le script a chargé sur le coffre de clés.

>[AZURE.NOTE] La chaîne Nom DNS spécifie un ou plusieurs noms DNS à placer dans l’extension d’autre nom de l’objet du certificat, lorsqu’un certificat à copier n’est pas spécifié dans le paramètre CloneCert. Le premier nom DNS est également enregistré en tant que nom de l’objet. Si aucun certificat de signature n’est spécifié, le premier nom DNS est également enregistré en tant que nom de l’émetteur.

Des informations supplémentaires concernant la création d’un certificat auto-signé sont disponibles à l’adresse [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx).

Voici un exemple de script rempli.

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

Puisqu’il s’agit d’un certificat auto-signé, vous devrez l’importer dans votre magasin d’ordinateurs autorisés avant de pouvoir l’utiliser pour vous connecter à un cluster sécurisé.

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

À l’achèvement de l’exécution du script, vous obtenez un résultat similaire à celui qui suit. Vous en avez besoin pour l’étape 3.

**Empreinte de certificat** : 64881409F4D86498C88EEC3697310C15F8F1540F **SourceVault** /ID de ressource du coffre de clés : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc **URL de certificat** /URL de l’emplacement du certificat dans le coffre de clés : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea

### Étape 3 : Configuration d’un cluster sécurisé

Suivez les étapes décrites dans le [processus de création de clusters Service Fabric](service-fabric-cluster-creation-via-portal.md) jusqu’à la section relative aux configurations de sécurité. Passez ensuite aux instructions indiquées ci-après pour configurer vos configurations de sécurité :

>[AZURE.NOTE] 
1\. Les certificats qui doivent être utilisés sont spécifiés au niveau du type de nœud sous les configurations de sécurité. 2. Vous devez spécifier ceci pour chaque type de nœud que vous avez dans votre cluster. 3. Bien que ce document vous guide sur la réalisation de cette procédure à l’aide du portail, vous pouvez faire la même chose à l’aide d’un modèle ARM.

![Capture d’écran de Configurations de sécurité dans le portail Azure][SecurityConfigurations_01]

Paramètres obligatoires :

- **Mode de sécurité.** Sélectionnez **Certificat X.509**. Ce paramètre indique à Service Fabric que vous souhaitez configurer un cluster sécurisé.
- **Niveau de protection du cluster.** Consultez ce [document sur les niveaux de protection](https://msdn.microsoft.com/library/aa347692.aspx) pour comprendre la signification de chacune de ces valeurs. Même si nous autorisons ici trois valeurs (EncryptAndSign, Sign et None), il est préférable de conserver la valeur par défaut EncryptAndSign sauf si vous savez ce que vous faites.
- **Coffre source.** Ce paramètre désigne l’ID de ressource du coffre de clés. Il doit respecter le format suivant :

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **URL de certificat.** Ce paramètre désigne l’URL de l’emplacement où le certificat a été chargé dans votre coffre de clés. Il doit respecter le format suivant :

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **Empreinte de certificat.** Ce paramètre désigne l’empreinte du certificat, qui est disponible à l’URL que vous avez spécifiée plus haut.

Paramètres facultatifs :

 - Vous pouvez éventuellement spécifier des certificats supplémentaires pour les ordinateurs clients que vous utilisez pour effectuer les opérations sur le cluster. Par défaut, l’empreinte que vous avez spécifiée dans les paramètres obligatoires est ajoutée à la liste des empreintes qui sont autorisées pour effectuer les opérations des clients.

Client administrateur : ces informations permettent de valider que le client qui se connecte au point de terminaison de gestion des clusters présente les informations d’identification appropriées pour effectuer l’administration et les actions en lecture seule sur le cluster. Vous pouvez spécifier plusieurs certificats de votre choix pour les opérations d’administration.

- **Autoriser par.** Ce paramètre indique à Service Fabric si ce certificat doit être recherché en utilisant le nom de l’objet ou l’empreinte. Pour des raisons de sécurité, l’utilisation du nom de l’objet pour l’autorisation n’est pas recommandée, mais elle offre davantage de flexibilité.
- **Nom de l’objet.** Ce paramètre est nécessaire uniquement si vous avez spécifié l’autorisation par nom d’objet.
- **Empreinte de l’émetteur**. Ce paramètre fournit un niveau supplémentaire de vérification que le serveur peut effectuer quand un client présente ses informations d’identification sur le serveur.



Client en lecture seule : ces informations permettent de valider que le client qui se connecte au point de terminaison de gestion des clusters présente les informations d’identification appropriées pour effectuer les actions en lecture seule sur le cluster. Vous pouvez spécifier plusieurs certificats de votre choix pour les opérations en lecture seule.

- **Autoriser par.** Ce paramètre indique à Service Fabric si ce certificat doit être recherché en utilisant le nom de l’objet ou l’empreinte. Pour des raisons de sécurité, l’utilisation du nom de l’objet pour l’autorisation n’est pas recommandée, mais elle offre davantage de flexibilité.
- **Nom de l’objet.** Ce paramètre est nécessaire uniquement si vous avez spécifié l’autorisation par nom d’objet.
- **Empreinte de l’émetteur.** Ce paramètre fournit un niveau supplémentaire de vérification que le serveur peut effectuer lorsqu’un client présente ses informations d’identification sur le serveur.

## Mettre à jour les certificats dans le cluster

Service Fabric vous permet de spécifier deux certificats, un principal et un secondaire. Celui que vous avez spécifié lors de la création est par défaut le certificat principal. Pour ajouter un autre certificat, vous devez déployer ce certificat sur les machines virtuelles du cluster. L’étape 2 (ci-dessus) de ce document décrit comment vous pouvez charger un nouveau certificat sur le coffre de clés. Pour ce faire, vous pouvez utiliser le même coffre de clés que pour le premier certificat. Pour plus d’informations, consultez [Déployer des certificats sur les machines virtuelles à partir de coffres de clés gérés par les clients](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

Une fois cette opération terminée, utilisez le portail ou Resource Manager pour indiquer à Service Fabric que vous disposez d’un certificat secondaire qui peut également être utilisé. Il vous suffit de disposer d’une empreinte.

**Voici comment ajouter un nouveau certificat** pour le cluster à utiliser : accédez au portail, puis à la ressource de cluster à laquelle vous souhaitez ajouter à ce certificat, cliquez sur le paramètre de certificat, entrez l’empreinte du certificat secondaire et appuyez sur Enregistrer. Un déploiement sera lancé et une fois qu’il aura réussi, vous pourrez utiliser le certificat principal ou le secondaire pour effectuer des opérations de gestion sur le cluster.

![Capture d’écran des empreintes de certificat dans le portail][SecurityConfigurations_02]

**Voici comment supprimer un ancien certificat** afin que le cluster ne l’utilise pas : accédez au portail, puis aux paramètres de sécurité de vos clusters, et supprimez le certificat concerné. Veillez à appuyer sur Enregistrer après avoir supprimé un certificat, afin de lancer un nouveau déploiement. Une fois que ce déploiement est terminé, le certificat supprimé ne peut plus être utilisé pour se connecter au cluster.

Remarque : pour un cluster sécurisé, vous avez toujours besoin d’au moins un certificat (primaire ou secondaire) déployé valide (non révoqué ou expiré), sinon vous ne pouvez pas accéder au cluster.


## 
Informations détaillées sur les genres de certificats utilisés par service Fabric.

## Certificats X.509

Les certificats numériques X.509 sont couramment utilisés pour authentifier les clients et serveurs et pour chiffrer et signer numériquement les messages. Pour plus d’informations sur ces certificats, consultez la page [Utilisation des certificats](http://msdn.microsoft.com/library/ms731899.aspx) dans MSDN Library.

>[AZURE.NOTE]
1\. Les certificats utilisés dans les clusters qui exécutent des charges de travail de production doivent être créés en utilisant un service de certificats Windows Server correctement configuré ou obtenus auprès d’une [autorité de certification (AC)](https://en.wikipedia.org/wiki/Certificate_authority) approuvée. 2. En production, n’utilisez jamais de certificats temporaires ou de test créés avec des outils comme MakeCert.exe. 3. Pour les clusters que vous utilisez à des fins de test uniquement, vous pouvez choisir d’utiliser un certificat auto-signé.

## Certificats de serveur et certificats clients

**Certificats X.509 de serveur**

La tâche principale des certificats de serveur est d’authentifier un serveur (nœud) pour les clients ou d’authentifier un serveur (nœud) pour un serveur (nœud). L’une des vérifications initiales lorsqu’un nœud ou un client authentifie un nœud consiste à vérifier la valeur du nom commun dans le champ Objet. Ce nom commun ou l’un des autres noms de l’objet des certificats doit figurer dans la liste des noms communs autorisés.

L’article suivant décrit comment générer des certificats avec d’autres noms de l’objet (SAN) : [Ajout d’un autre nom de l’objet à un certificat LDAP sécurisé](http://support.microsoft.com/kb/931351).

>[AZURE.NOTE] Le champ Objet peut contenir plusieurs valeurs, chacune étant précédée d’une initialisation pour indiquer le type de valeur. En règle générale, l’initialisation est « CN » pour « nom commun », par exemple, « CN = www.contoso.com ». Il est également possible que le champ Objet soit vide. Si le champ facultatif Autre nom de l’objet est renseigné, il doit contenir le nom commun du certificat et une entrée par autre nom de l’objet. Ils sont entrés sous forme de valeurs de nom DNS.

La valeur du champ Rôles prévus du certificat doit inclure une valeur appropriée, comme Authentification serveur ou Authentification client.

**Certificats clients**

Les certificats clients ne sont généralement pas émis par une autorité de certification tierce. Au lieu de cela, le magasin personnel de l’emplacement actuel de l’utilisateur contient généralement des certificats clients placés là par une autorité racine avec un rôle prévu d’authentification client. Le client peut utiliser un certificat lorsque l’authentification mutuelle est requise.

>[AZURE.NOTE] Toutes les opérations de gestion sur un cluster Service Fabric requièrent des certificats de serveur. Les certificats clients ne peuvent pas être utilisés pour la gestion.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [Processus de mise à niveau du cluster Service Fabric et attentes à votre égard](service-fabric-cluster-upgrade.md)
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md)
- [Sécurité des applications et Runas](service-fabric-application-runas-security.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!----HONumber=AcomDC_0204_2016-->