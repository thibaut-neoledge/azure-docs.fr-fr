---
title: Authentification et autorisation avec Power BI Embedded
description: Authentification et autorisation avec Power BI Embedded
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 93027f0f5467e0b21c47bbcbc84c67cdd50b26b8
ms.lasthandoff: 03/14/2017


---
# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Authentification et autorisation avec Power BI Embedded

Le service Power BI Embedded utilise des **clés** et des **jetons d’application** pour l’authentification et l’autorisation. Il n’utilise pas l’authentification explicite des utilisateurs finaux. Dans ce modèle, votre application gère l’authentification et l’autorisation de vos utilisateurs finaux. Quand cela est nécessaire, votre application crée et envoie les jetons d’application à notre service pour lui indiquer d’afficher le rendu du rapport demandé. Avec cette conception, votre application peut gérer l’authentification et l’autorisation des utilisateurs sans passer par Azure Active Directory. Cette option est toutefois possible.

## <a name="two-ways-to-authenticate"></a>Deux méthodes d’authentification

**Clé** : vous pouvez utiliser des clés pour tous les appels d’API REST Power BI Embedded. Les clés se trouvent sur le **portail Azure** en cliquant sur **Tous les paramètres**, puis sur **Clés d’accès**. Traitez toujours votre clé comme s’il s’agissait d’un mot de passe. Ces clés ont des autorisations pour appeler toutes les API REST sur une collection d’espaces de travail donnée.

Pour utiliser une clé sur un appel REST, ajoutez l’en-tête d’autorisation suivant :            

    Authorization: AppKey {your key}

**Jeton d’application** : les jetons d’application sont utilisés pour toutes les demandes d’incorporation. Ils sont conçus pour être exécutés côté client, afin qu’ils soient limités à un rapport unique. Il est également recommandé de définir un délai d’expiration.

Les jetons d’application sont un JWT (JSON Web Token) signé par l’une de vos clés.

Votre jeton d’application peut contenir les revendications suivantes :

| Revendication | Description |
| --- | --- |
| **ver** |Version de jeton d’application. La version actuelle est 0.2.0. |
| **aud** |Destinataire du jeton. Pour Power BI Embedded, utilisez : https://analysis.windows.net/powerbi/api. |
| **iss** |Chaîne indiquant l’application qui a émis le jeton. |
| **type** |Type de jeton d’application en cours de création. Le seul type pris en charge pour l’instant est **embed**. |
| **wcn** |Nom de collection d’espace de travail pour lequel le jeton est émis. |
| **wid** |ID d’espace de travail pour lequel le jeton est émis. |
| **rid** |ID de rapport pour lequel le jeton est émis. |
| **username** (facultatif) |Utilisé avec la fonction RLS, ceci est une chaîne qui peut aider à identifier l’utilisateur lors de l’application des règles RLS. |
| **roles** (facultatif) |Chaîne contenant les rôles à sélectionner lors de l’application des règles de sécurité au niveau des lignes. Si vous transmettez plusieurs rôles, ils doivent l’être en tant que table de chaînes. |
| **scp** (facultatif) |Chaîne contenant les étendues d’autorisation. Si vous transmettez plusieurs rôles, ils doivent l’être en tant que table de chaînes. |
| **exp** (facultatif) |Indique l’heure d’expiration du jeton. Les heures doivent être transmises en tant qu’horodatages Unix. |
| **nbf** (facultatif) |Indique l’heure de début de validité du jeton. Les heures doivent être transmises en tant qu’horodatages Unix. |

Un exemple de jeton d’application doit ressembler à ceci :

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Lorsqu’il est décodé, il doit ressembler à ceci :

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

Il existe des méthodes disponibles dans les Kits de développement logiciel (SDK)qui facilitent la création de jetons d’application. Par exemple, pour .NET vous pouvez consulter la classe [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) et les méthodes [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_).

Pour le Kit de développement logiciel (SDK) .NET, vous pouvez vous référer à [Étendues](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Étendues

Si vous utilisez des jetons d’incorporation, vous souhaiterez peut-être limiter l’utilisation des ressources auxquelles vous donnez accès. Pour cette raison, vous pouvez générer un jeton avec des étendues d’autorisation.

Voici les étendues disponibles pour Power BI Embedded.

|Étendue|Description|
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

Un jeton décodé, avec des étendues définies, ressemblerait à ceci :

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
|Créer (en mémoire) un rapport basé sur un jeu de données et enregistrez le rapport.|Jeu de données|*Dataset.Read<br>* Workspace.Report.Create|
|Afficher et explorer/modifier (en mémoire) un rapport existant. Report.Read implies Dataset.Read. Cela ne donne pas accès aux autorisations permettant d’enregistrer les modifications.|Rapport|Report.Read|
|Modifier et enregistrer un rapport existant.|Rapport|Report.ReadWrite|
|Enregistrer la copie d’un rapport (Enregistrer sous).|Rapport|*Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Voici comment fonctionne le flux :
1. Copiez les clés API dans votre application. Vous pouvez obtenir les clés à partir du **portail Azure**.
   
    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
2. Le jeton envoie une revendication et comporte un délai d'expiration.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-2.png)
3. Le jeton est signé à l’aide des clés d'accès API.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-3.png)
4. L’utilisateur demande à consulter un rapport.
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-4.png)
5. Le jeton est validé à l’aide d’une clé d'accès API.
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-5.png)
6. Power BI Embedded envoie un rapport à l'utilisateur.
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-6.png)

Après que **Power BI Embedded** envoie un rapport à l’utilisateur, ce dernier peut afficher le rapport dans votre application personnalisée. Par exemple, si vous avez importé l’ [exemple PBIX Analyse des données de vente](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l’exemple d’application a l’aspect suivant :

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Voir aussi

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Prise en main de l’exemple Microsoft Power BI Embedded Preview](power-bi-embedded-get-started-sample.md)  
[Scénarios Microsoft Power BI Embedded courants](power-bi-embedded-scenarios.md)  
[Prise en main de Microsoft Power BI Embedded](power-bi-embedded-get-started.md)  
[Dépôt Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)


