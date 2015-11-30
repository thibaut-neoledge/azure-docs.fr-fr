<properties
   pageTitle="Comment sécuriser un cluster Service Fabric | Microsoft Azure"
   description="Comment sécuriser un cluster Service Fabric. Quelles sont les options ?"
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

# Comment sécuriser le cluster Service Fabric à l’aide de certificats.

Pour configurer un cluster Service Fabric sécurisé, vous devez disposer d’au moins un serveur / certificat x509. Vous le chargez ensuite sur le coffre de clés Azure et l’utilisez dans le processus de création de clusters présenté dans [Processus de création de clusters Service Fabric](service-fabric-cluster-creation-via-portal.md).

Il existe trois étapes distinctes :

1. Acquérir le certificat x509.
2. Charger le certificat sur le coffre de clés Azure.
3. Fournir l’emplacement et les informations du certificat au processus de création de clusters Service Fabric.

Avant de trop entrer dans les détails, voyons quelques notions de base sur les scénarios.

##  Quels scénarios sont couverts ?

Service Fabric assure la sécurité dans les scénarios suivants :

1. Sécurisation d’un cluster pour la communication nœud à nœud.
2. Sécurisation d’un client Service Fabric communicant avec un nœud particulier dans le cluster.
3. Contrôle d’accès en fonction du rôle (RBAC) : possibilité de limiter les opérations d’administration dans les opérations en lecture seule sur le cluster à un ensemble de certificats.   

Service Fabric utilise des certificats de serveur X509 que vous spécifiez dans le cadre des configurations du type de nœud, lorsque vous créez un cluster. Pour un aperçu rapide de ce que sont ces certificats et de la manière dont vous pouvez les acquérir et les créer, rendez-vous au bas de cette page.

 
## Acquérir les certificats x509

1. Pour les clusters exécutant des charges de travail de production, vous devez utiliser un certificat x509 signé par une [autorité de certification (CA)](https://en.wikipedia.org/wiki/Certificate_authority) pour sécuriser le cluster. Pour des informations sur l’obtention de ces certificats, rendez-vous à l’adresse [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Pour les clusters que vous utilisez aux fins de test uniquement, vous pouvez choisir d’utiliser un certificat auto-signé.


## Création d’un certificat auto-signé à des fins de test

Les informations concernant la création d’un certificat auto-signé personnel sont disponibles à l’adresse [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx)
    
Voici le PS que j’utilise pour créer mes certificats de test. Veillez à lire le document ci-dessus pour vous assurer qu’il répond à vos besoins. ```
$password = Read-Host -AsSecureString 
``` ```
New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName ChackdanTestCertificate | Export-PfxCertificate -FilePath E:\MyCertificates\ChackdanTestCertificate.pfx -Password $password
```

**Remarque** Le nom de DNS <String> spécifie un ou plusieurs noms DNS à placer dans l’extension d’autre nom de l’objet du certificat, lorsqu’un certificat à copier n’est pas spécifié via le paramètre CloneCert. Le premier nom DNS est également enregistré en tant que nom de l’objet. Si aucun certificat de signature n’est spécifié, le premier nom DNS est également enregistré en tant que nom de l’émetteur.

## Chargement du certificat x509 sur le coffre de clés

Des instructions de chargement d’un certificat sur le coffre de clés sont disponibles à l’adresse suivante [lien vers la documentation du coffre de clés](https://azure.microsoft.com/documentation/articles/key-vault-get-started/).

Veillez à noter l’URL du coffre source, l’URL de certificat et l’empreinte de certificat. Vous en aurez besoin lors de la configuration du cluster Service Fabric sécurisé. Les données dont vous avez besoin ressembleront à ce qui suit :



1. **ID de ressource de l’URL KeyVault/Source Vault** : /subscriptions/6c653126-e4ba-42cd-a1dd-f7bf96af7a47/resourceGroups/chackdan-keyvault/providers/Microsoft.KeyVault/vaults/chackdan-kmstest
2. **URL vers l’emplacement du certificat dans le coffre de clés** : https://chackdan-kmstest.vault.azure.net:443/secrets/MyCert/dcf17bdbb86b42ad864e8e827c268431 
3. **Empreinte de certificat** : 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A



##Configuration d’un cluster sécurisé 

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

Service Fabric vous permet de spécifier deux certificats, un principal et un secondaire. Celui que vous avez spécifié lors de la création est par défaut le certificat principal. Pour ajouter un autre certificat, vous devez déployer ce certificat sur les machines virtuelles du cluster. Consultez le document [Déployer des certificats sur les machines virtuelles à partir de coffres de clés gérés par les clients](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) pour connaître la procédure.

Une fois cette opération réussie, accédez au portail ou utilisez ARM pour indiquer à Service Fabric que vous disposez d’un certificat secondaire qui peut également être utilisé. Il vous suffit de disposer d’une empreinte.

Dans le portail, accédez à la ressource de cluster à laquelle vous souhaitez ajouter à ce certificat, cliquez sur le paramètre de certificat et entrez l’empreinte du certificat secondaire. Appuyez sur Enregistrer. Un déploiement sera lancé et une fois qu’il aura réussi, vous pourrez utiliser le certificat principal ou le secondaire pour effectuer des opérations de gestion sur le cluster.

![SecurityConfigurations\_02][SecurityConfigurations_02]

Si vous souhaitez supprimer l’un des certificats, vous pouvez le faire. Veillez à appuyer sur Enregistrer après avoir supprimé un certificat, afin de lancer un nouveau déploiement. Une fois que ce déploiement est terminé, le certificat supprimé ne peut plus être utilisé pour se connecter au cluster. Pour un cluster sécurisé, vous aurez toujours besoin d’au moins un certificat déployé valide (non révoqué ou expiré), sinon vous ne pourrez pas accéder au cluster.

Il existe un événement de diagnostic qui vous permet de savoir si les certificats sont proches de l’expiration.



## Que sont les certificats X509 ?

Les certificats numériques X509 sont couramment utilisés pour authentifier des clients et des serveurs, chiffrer et signer numériquement les messages. Pour plus d’informations sur ces certificats, rendez-vous à l’adresse [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx)

**Remarque :**

1. Les certificats utilisés dans les clusters exécutant des charges de travail de production devraient être créés en utilisant un service de certificats Windows Server correctement configuré, obtenus via une [autorité de certification (CA)](https://en.wikipedia.org/wiki/Certificate_authority) approuvée.
2. N’utilisez jamais de certificats temporaires ou de tests créés avec des outils comme MakeCert.exe en production.
3. Pour les clusters que vous utilisez aux fins de test uniquement, vous pouvez choisir d’utiliser un certificat auto-signé. 


##Quels sont les certificats de serveur et les certificats clients ?

**Certificats de serveur /X 509**

La tâche principale des certificats de serveur est l’authentification du serveur (nœud) pour les clients, ou du serveur (nœud) pour un serveur (nœud). L’une des vérifications initiales à effectuer lorsqu’un client ou un nœud authentifie un nœud consiste à comparer la valeur du nom commun dans le champ Objet pour s’assurer qu’il est présent dans la liste des noms communs autorisées qui a été configurée. Ce nom commun ou l’un des autres noms de l’objet des certificats doit figurer dans la liste des noms communs autorisés.

L’article suivant décrit comment générer des certificats avec d’autres noms de l’objet (SAN). [http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351)
 
**Remarque :** le champ Objet peut contenir plusieurs valeurs, chacune précédée d’une initialisation pour indiquer la valeur. En règle générale, l’initialisation est « CN » pour « nom commun » (common name), par exemple, « CN = www.contoso.com ». Il est également possible que le champ Objet soit vide. Notez également le champ Autre nom de l’objet. S’il est renseigné, il doit contenir le nom commun du certificat et une entrée par Autre nom de l’objet. Ils sont entrés sous forme de valeurs de nom DNS.

Notez également que la valeur du champ Rôles prévus du certificat doit inclure une valeur appropriée, comme Authentification serveur ou Authentification client.

**Certificats clients**

Les certificats clients ne sont généralement pas émis par une autorité de certification tierce. Au lieu de cela, le magasin personnel de l’emplacement actuel de l’utilisateur contient généralement des certificats placés là par une autorité racine avec un rôle prévu d’authentification client. Le client peut utiliser un certificat lorsque l’authentification mutuelle est requise. Toutes les opérations de gestion sur le cluster Service Fabric requièrent des certificats de serveur. Les certificats clients ne doivent pas être utilisés.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [Processus de mise à niveau du cluster Service Fabric et attentes à votre égard](service-fabric-cluster-upgrade.md)
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Présentation du modèle d’état de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png

<!---HONumber=Nov15_HO4-->