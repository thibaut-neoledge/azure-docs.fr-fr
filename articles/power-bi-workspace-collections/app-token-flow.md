---
title: "Authentification et autorisation avec Collections d’espaces de travail Power BI | Microsoft Docs"
description: "Authentification et autorisation avec Collections d’espaces de travail Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: ae9627c6bb5e7bb099598acaa2eb29375c35593e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Authentification et autorisation avec Collections d’espaces de travail Power BI

Le service Collections d’espaces de travail Power BI utilise des **clés** et des **jetons d’application** pour l’authentification et l’autorisation. Il n’utilise pas l’authentification explicite des utilisateurs finaux. Dans ce modèle, votre application gère l’authentification et l’autorisation de vos utilisateurs finaux. Quand cela est nécessaire, votre application crée et envoie les jetons d’application à notre service pour lui indiquer d’afficher le rapport demandé. Avec cette conception, votre application peut gérer l’authentification et l’autorisation des utilisateurs sans passer par Azure Active Directory. Cette option est toutefois possible.

> [!IMPORTANT]
> Le service Collections d’espaces de travail Power BI est déprécié et disponible jusqu’en juin 2018 ou jusqu’à la date indiquée sur votre contrat. Nous vous conseillons de planifier votre migration vers Power BI Embedded pour éviter toute interruption dans votre application. Pour plus d’informations sur la façon de migrer vos données vers Power BI Embedded, consultez [Comment migrer le contenu d’une collection d’espaces de travail Power BI Embedded vers Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Deux méthodes d’authentification

**Clé** : vous pouvez utiliser des clés pour tous les appels d’API REST Collections d’espaces de travail Power BI. Vous pouvez accéder aux clés dans le **portail Azure Microsoft** en sélectionnant **Tous les paramètres** puis **Clés d’accès**. Traitez toujours votre clé comme s’il s’agissait d’un mot de passe. Ces clés ont des autorisations pour appeler toutes les API REST sur une collection d’espaces de travail donnée.

Pour utiliser une clé sur un appel REST, ajoutez l’en-tête d’autorisation suivant :

    Authorization: AppKey {your key}

**Jeton d’application** : les jetons d’application sont utilisés pour toutes les demandes d’incorporation. Ils sont conçus pour être exécutés côté client. Le jeton est limité à un rapport unique. Il est également recommandé de définir un délai d’expiration.

Les jetons d’application sont un JWT (JSON Web Token) signé par l’une de vos clés.

Votre jeton d’application peut contenir les revendications suivantes :

| Revendication | Description |
| --- | --- |
| **ver** |Version de jeton d’application. La version actuelle est 0.2.0. |
| **aud** |Destinataire du jeton. Pour Collections d’espaces de travail Power BI, utilisez : « https://analysis.windows.net/powerbi/api ». |
| **iss** |Chaîne indiquant l’application qui a émis le jeton. |
| **type** |Type de jeton d’application en cours de création. Le seul type pris en charge pour l’instant est **embed**. |
| **wcn** |Nom de collection d’espace de travail pour lequel le jeton est émis. |
| **wid** |ID d’espace de travail pour lequel le jeton est émis. |
| **rid** |ID de rapport pour lequel le jeton est émis. |
| **username** (facultatif) |Utilisé avec la fonction RLS, username est une chaîne qui peut aider à identifier l’utilisateur lors de l’application des règles RLS. |
| **roles** (facultatif) |Chaîne contenant les rôles à sélectionner lors de l’application des règles de sécurité au niveau des lignes. Si vous transmettez plusieurs rôles, ils doivent l’être en tant que table de chaînes. |
| **scp** (facultatif) |Chaîne contenant les étendues d’autorisation. Si vous transmettez plusieurs rôles, ils doivent l’être en tant que table de chaînes. |
| **exp** (facultatif) |Indique l’heure d’expiration du jeton. La valeur doit être transmise en tant qu’horodatages Unix. |
| **nbf** (facultatif) |Indique l’heure de début de validité du jeton. La valeur doit être transmise en tant qu’horodatages Unix. |

Voici un exemple de jeton d’application :

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Une fois décodé, il ressemble à ceci :

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Des méthodes disponibles dans les SDK facilitent la création des jetons d’application. Par exemple, pour .NET vous pouvez consulter la classe [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) et les méthodes [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_).

Pour le Kit de développement logiciel (SDK) .NET, vous pouvez vous référer à [Étendues](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Étendues

Si vous utilisez des jetons d’incorporation, vous souhaiterez peut-être limiter l’utilisation des ressources auxquelles vous donnez accès. Dans cette optique, vous pouvez générer un jeton avec des autorisations étendues.

Voici les étendues disponibles pour Collections d’espaces de travail Power BI.

|Scope|Description|
|---|---|
|Dataset.Read|Fournit l’autorisation de lire un jeu de données spécifié.|
|Dataset.Write|Fournit l’autorisation d’écrire sur un jeu de données spécifié.|
|Dataset.ReadWrite|Fournit l’autorisation de lire et d’écrire sur un jeu de données spécifié.|
|Report.Read|Fournit l’autorisation d’afficher un rapport spécifié.|
|Report.ReadWrite|Fournit l’autorisation d’afficher et de modifier un rapport spécifié.|
|Workspace.Report.Create|Fournit une autorisation pour créer un rapport dans l’espace de travail spécifié.|
|Workspace.Report.Copy|Fournit une autorisation pour cloner un rapport existant dans l’espace de travail spécifié.|

Vous pouvez fournir plusieurs étendues à l’aide d’un espace entre les étendues comme suit.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Revendications requises - Étendues**

scp: {scopesClaim} scopesClaim peut être une chaîne ou un tableau de chaînes répertoriant les permissions autorisées sur les ressources d’espace de travail (rapport, jeu de données, etc.)

Un jeton décodé, avec des étendues définies, ressemblerait à ceci :

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Opérations et étendues

|Opération|Ressource cible|Autorisations du jeton|
|---|---|---|
|Créer (en mémoire) un rapport basé sur un jeu de données.|Jeu de données|Dataset.Read|
|Créer (en mémoire) un rapport basé sur un jeu de données et enregistrez le rapport.|Jeu de données|* Dataset.Read<br>* Workspace.Report.Create|
|Afficher et explorer/modifier (en mémoire) un rapport existant. Report.Read implique Dataset.Read. Report.Read n’autorise pas l’enregistrement des modifications.|Rapport|Report.Read|
|Modifier et enregistrer un rapport existant.|Rapport|Report.ReadWrite|
|Enregistrer la copie d’un rapport (Enregistrer sous).|Rapport|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Voici comment fonctionne le flux :
1. Copiez les clés API dans votre application. Vous pouvez obtenir les clés à partir du **portail Azure**.
   
    ![Où trouver les clés d’API dans le portail Azure](media/get-started-sample/azure-portal.png)
1. Le jeton envoie une revendication et comporte un délai d'expiration.
   
    ![Flux de jeton d’application : le jeton déclare la revendication](media/get-started-sample/token-2.png)
1. Le jeton est signé à l’aide des clés d'accès API.
   
    ![Flux de jeton d’application : le jeton est signé](media/get-started-sample/token-3.png)
1. L’utilisateur demande à consulter un rapport.
   
    ![Flux de jeton d’application : l’utilisateur demande à voir un rapport](media/get-started-sample/token-4.png)
1. Le jeton est validé à l’aide d’une clé d'accès API.
   
   ![Flux de jeton d’application : le jeton est validé](media/get-started-sample/token-5.png)
1. Le service Collections d’espaces de travail Power BI envoie un rapport à l’utilisateur.
   
   ![Flux de jeton d’application : le service envoie le rapport à l’utilisateur](media/get-started-sample/token-6.png)

Une fois que **Collections d’espaces de travail Power BI** a envoyé un rapport à l’utilisateur, celui-ci peut afficher le rapport dans votre application personnalisée. Par exemple, si vous avez importé [l’exemple PBIX Analyse des données de vente](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l’exemple d’application a l’aspect suivant :

![Exemple de rapport incorporé dans l’application](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Voir aussi

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Bien démarrer avec les exemples Collections d’espaces de travail Microsoft Power BI](get-started-sample.md)  
[Scénarios courants pour Collections d’espaces de travail Microsoft Power BI](scenarios.md)  
[Bien démarrer avec Collections d’espaces de travail Microsoft Power BI](get-started.md)  
[Référentiel Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)

Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)