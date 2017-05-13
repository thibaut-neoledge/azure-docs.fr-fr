---
title: "Sécurisation de l’accès aux données dans DocumentDB | Microsoft Docs"
description: "Découvrez les concepts de contrôle d&quot;accès dans DocumentDB, dont les clés principales, les clés en lecture seule, les utilisateurs et les autorisations."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 7ab474747c74295a5dba9a6f3ad32000a7551a9c
ms.contentlocale: fr-fr
ms.lasthandoff: 04/28/2017


---
# <a name="securing-access-to-documentdb-data"></a>Sécurisation de l'accès aux données DocumentDB
Cet article fournit une vue d’ensemble de la sécurisation de l’accès aux données stockées dans [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

DocumentDB utilise deux types de clés pour authentifier les utilisateurs et fournir un accès à ses données et à ses ressources. 

|Type de clé|Ressources|
|---|---|
|[Clés principales](#master-keys) |Utilisées pour les ressources d’administration : comptes de base de données, bases de données, utilisateurs et autorisations|
|[Jetons de ressource](#resource-tokens)|Utilisés pour les ressources de l’application : collections, documents, pièces jointes, procédures stockées, déclencheurs et fonctions définies par l’utilisateur|

<a id="master-keys"></a>

## <a name="master-keys"></a>Clés principales 

Les clés principales fournissent un accès à toutes les ressources d’administration du compte de base de données. Clés principales :  
- Fournissent un accès aux comptes, aux bases de données, aux utilisateurs et aux autorisations. 
- Ne peuvent pas être utilisées pour fournir un accès précis aux collections et aux documents.
- Sont créées lors de la création d’un compte.
- Peuvent être régénérées à tout moment.

Chaque compte comporte deux clés principales : une clé primaire et une clé secondaire. L’objectif de ces paires de clés est de pouvoir régénérer ou restaurer des clés tout en fournissant un accès permanent à votre compte et à vos données. 

Outre les deux clés principales du compte DocumentDB, il existe deux clés en lecture seule. Ces clés en lecture seule autorisent uniquement les opérations de lecture sur le compte. Les clés en lecture seule ne permettent pas de lire les ressources d’autorisation.

Les clés principales primaire, secondaire, en lecture seule et en lecture-écriture peuvent être récupérées et régénérées à l’aide du portail Azure. Pour connaître la procédure, consultez [Affichage, copie et régénération des clés d’accès](documentdb-manage-account.md#a-idkeysaview-copy-and-regenerate-access-keys).

![Contrôle d’accès (IAM) dans le portail Azure - Démonstration de la sécurité de la base de données NoSQL](./media/documentdb-secure-access-to-data/nosql-database-security-master-key-portal.png)

Le processus de rotation de votre clé principale est simple. Accédez au portail Azure pour récupérer votre clé secondaire, puis remplacez votre clé primaire par votre clé secondaire dans votre application. Enfin, faites pivoter la clé primaire dans le portail Azure.

![Rotation de clé principale dans le portail Azure - Démonstration de la sécurité de la base de données NoSQL](./media/documentdb-secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Exemple de code pour utiliser une clé principale

L’exemple de code suivant indique comment utiliser une clé principale et un point de terminaison de compte DocumentDB pour instancier un DocumentClient et créer une base de données. 

```csharp
//Read the DocumentDB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the NOSQL (DocumentDB) account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Jetons de ressource

Les jetons de ressource fournissent un accès aux ressources d’application au sein d’une base de données. Jetons de ressource :
- Fournissent un accès à des collections, clés de partition, documents, pièces jointes, procédures stockées, déclencheurs et fonctions définies par l’utilisateur spécifiques.
- Sont créés lorsqu’un [utilisateur](#users) dispose des [autorisations](#permissions) sur une ressource spécifique.
- Sont recréés lorsqu’une ressource d’autorisation est exécutée par un appel POST, GET ou PUT.
- Utilisent un jeton de ressource de hachage créé spécifiquement pour l’utilisateur, la ressource et les autorisations.
- Sont liés à une période de validité personnalisable. La durée de validité par défaut est d’une heure. La durée de vie du jeton peut cependant être définie de manière explicite (cinq heures maximum).
- Offrent une alternative sûre pour céder la clé principale. 
- Permettent aux clients de lire, d’écrire et de supprimer des ressources dans le compte DocumentDB en fonction des autorisations qui leur ont été accordées.

Vous pouvez utiliser un jeton de ressource (en créant des utilisateurs et des autorisations DocumentDB) lorsque vous voulez fournir un accès aux ressources dans votre compte DocumentDB à un client qui ne peut pas être approuvé avec la clé principale.  

Les jetons de ressource DocumentDB offrent une alternative sûre qui permet aux clients de lire, d’écrire et de supprimer des ressources dans votre compte DocumentDB en fonction des autorisations que vous avez octroyées, sans avoir besoin d’une clé principale ou en lecture seule.

Voici un modèle de conception standard dans le cadre duquel des jetons de ressource peuvent être demandés, générés et fournis aux clients :

1. Un service de niveau intermédiaire est configuré pour servir une application mobile pour partager les photos de l'utilisateur. 
2. Le service de niveau intermédiaire dispose de la clé principale du compte DocumentDB.
3. L’application photo est installée sur les appareils mobiles des utilisateurs finaux. 
4. Lors de la connexion, l'application photo établit l'identité de l'utilisateur avec le service de niveau intermédiaire. Ce mécanisme d'identification dépend totalement de l'application.
5. Une fois l'identité établie, le service de niveau intermédiaire demande des autorisations en fonction de l'identité.
6. Le service de niveau intermédiaire renvoie un jeton de ressource à l'application du téléphone.
7. Cette dernière peut continuer à utiliser le jeton de ressource pour accéder directement aux ressources DocumentDB avec les autorisations définies et pendant l'intervalle autorisé. 
8. À expiration du jeton de ressource, les demandes suivantes reçoivent une exception non autorisée 401.  L'application du téléphone établit alors de nouveau l'identité de l'utilisateur et demande un nouveau jeton de ressource.

    ![Flux de travail des jetons de ressource DocumentDB](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

La gestion et la génération des jetons de ressource est prise en charge par les bibliothèques clientes natives DocumentDB. Toutefois, si vous utilisez REST, vous devez créer les en-têtes de demande/d’authentification. Pour plus d’informations sur la création d’en-têtes d’authentification pour REST, consultez [Access Control on DocumentDB Resources](https://docs.microsoft.com/en-us/rest/api/documentdb/access-control-on-documentdb-resources) (Contrôle d’accès aux ressources DocumentDB) ou le [code source de nos Kits de développement logiciel (SDK)](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Pour obtenir un exemple de service de niveau intermédiaire utilisé pour générer ou répartir les jetons de ressource, consultez [l’application ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Utilisateurs
Les utilisateurs DocumentDB sont associés à une base de données DocumentDB.  Chaque base de données peut contenir zéro utilisateur DocumentDB ou plus.  L’exemple de code suivant indique comment créer une ressource utilisateur DocumentDB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Chaque utilisateur DocumentDB dispose d’une propriété PermissionsLink qui peut être utilisée pour récupérer la liste des [autorisations](#permissions) associées à l’utilisateur.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Autorisations
Une ressource d'autorisation DocumentDB est associée à un utilisateur DocumentDB.  Chaque utilisateur peut contenir zéro autorisation DocumentDB ou plus.  Une ressource d'autorisation donne accès à un jeton de sécurité dont l'utilisateur a besoin lorsqu'il tente d'accéder à une ressource d'application spécifique.
Il existe deux niveaux d’accès disponibles qui peuvent être fournis par une ressource d’autorisation :

* Tout : L’utilisateur dispose de toutes les autorisations sur la ressource.
* Lecture : L’utilisateur peut uniquement lire le contenu de la ressource, mais il ne peut pas procéder à des opérations d’écriture, de mise à jour ou de suppression au niveau de la ressource.

> [!NOTE]
> Pour exécuter les procédures stockées DocumentDB, l'utilisateur doit disposer de toutes les autorisations sur la collection dans laquelle la procédure stockée sera exécutée.
> 
> 

### <a name="code-sample-to-create-permission"></a>Exemple de code pour créer une autorisation

L’exemple de code suivant indique comment créer une ressource d’autorisation, lire le jeton de ressource de la ressource d’autorisation et associer les autorisations à [l’utilisateur](#users) créé ci-dessus.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = documentCollection.SelfLink,
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Si vous avez spécifié une clé de partition pour votre collection, l’autorisation pour les ressources de collection, de document et de pièce jointe doit inclure l’élément ResourcePartitionKey en plus de l’élément ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Exemple de code pour les autorisations de lecture de l’utilisateur

Pour obtenir facilement toutes les ressources d’autorisation associées à un utilisateur spécifique, DocumentDB met à disposition un flux d’autorisations pour chaque objet utilisateur.  L'extrait de code suivant indique comment récupérer l'autorisation associée à l'utilisateur créé ci-dessus, créer une liste d'autorisations et instancier un nouveau DocumentClient pour l'utilisateur.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la sécurité de la base de données DocumentDB, consultez [DocumentDB: NoSQL database security](documentdb-nosql-database-security.md) (DocumentDB : sécurité de la base de données NoSQL).
* Pour en savoir plus sur la gestion des clés principales et en lecture seule, consultez [Gestion d’un compte DocumentDB](documentdb-manage-account.md#a-idkeysaview-copy-and-regenerate-access-keys).
* Pour savoir comment créer des jetons d’autorisation DocumentDB, consultez [Access Control on DocumentDB Resources](https://docs.microsoft.com/en-us/rest/api/documentdb/access-control-on-documentdb-resources) (Contrôle d’accès aux ressources DocumentDB).

