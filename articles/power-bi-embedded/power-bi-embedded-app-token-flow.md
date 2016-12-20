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
ms.date: 10/04/2016
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2b860b5815a0dd35138c685eb90490a8e2c53d5e


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
| **exp** (facultatif) |Indique l’heure d’expiration du jeton. Les heures doivent être transmises en tant qu’horodatages Unix. |
| **nbf** (facultatif) |Indique l’heure de début de validité du jeton. Les heures doivent être transmises en tant qu’horodatages Unix. |

Un exemple de jeton d’application doit ressembler à ceci :

![](media\\power-bi-embedded-app-token-flow\\power-bi-embedded-app-token-flow-sample-coded.png)

Lorsqu’il est décodé, il doit ressembler à ceci :

![](media\\power-bi-embedded-app-token-flow\\power-bi-embedded-app-token-flow-sample-decoded.png)

## <a name="heres-how-the-flow-works"></a>Voici comment fonctionne le flux :
1. Copiez les clés API dans votre application. Vous pouvez obtenir les clés à partir du **portail Azure**.
   
    ![](media\\powerbi-embedded-get-started-sample\\azure-portal.png)
2. Le jeton envoie une revendication et comporte un délai d'expiration.
   
    ![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-token-2.png)
3. Le jeton est signé à l’aide des clés d'accès API.
   
    ![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-token-3.png)
4. L’utilisateur demande à consulter un rapport.
   
    ![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-token-4.png)
5. Le jeton est validé à l’aide d’une clé d'accès API.
   
   ![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-token-5.png)
6. Power BI Embedded envoie un rapport à l'utilisateur.
   
   ![](media\\powerbi-embedded-get-started-sample\\power-bi-embedded-token-6.png)

Après que **Power BI Embedded** envoie un rapport à l’utilisateur, ce dernier peut afficher le rapport dans votre application personnalisée. Par exemple, si vous avez importé l’ [exemple PBIX Analyse des données de vente](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l’exemple d’application a l’aspect suivant :

![](media\\powerbi-embedded-get-started-sample\\sample-web-app.png)

## <a name="see-also"></a>Voir aussi
* [Prise en main de l’exemple Microsoft Power BI Embedded Preview](power-bi-embedded-get-started-sample.md)
* [Scénarios Microsoft Power BI Embedded courants](power-bi-embedded-scenarios.md)
* [Prise en main de Microsoft Power BI Embedded](power-bi-embedded-get-started.md)




<!--HONumber=Nov16_HO3-->


