---
title: "Qu’est-ce qu’une collection d’espaces de travail Power BI ?"
description: "Power BI Embedded vous permet d’intégrer des rapports Power BI dans vos applications web ou mobiles. Vous n’avez donc pas besoin de créer des solutions personnalisées."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 7df172895bb926f1715370b941964e2c29ab393d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-power-bi-workspace-collections"></a>Qu’est-ce qu’une collection d’espaces de travail Power BI ?

Les **collections d’espaces de travail Power BI** permettent d’intégrer des rapports Power BI dans vos applications web ou mobiles.

![Diagramme de l’application](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Les collections d’espaces de travail Power BI sont déconseillées et disponibles jusqu’en juin 2018, ou jusqu’à la date indiquée sur votre contrat. Nous vous conseillons de planifier votre migration vers Power BI Embedded pour éviter toute interruption dans votre application. Pour en savoir plus sur la migration de vos données vers Power BI Embedded, consultez la section relative à la [migration du contenu d’une collection d’espaces de travail Power BI vers Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Les collections d’espaces de travail Power BI sont un **service Azure** qui permet aux éditeurs de logiciels indépendants et aux développeurs d’applications de proposer des expériences de données Power BI au sein de leurs applications. En tant que développeur, vous avez créé des applications, et ces applications disposent de leurs propres utilisateurs et d’un ensemble distinct de fonctionnalités. Ces applications peuvent également contenir des éléments de données intégrés, tels que des graphiques et des rapports, que vous pouvez désormais proposer avec les collections d’espaces de travail Power BI. Vous n'avez pas besoin de compte Power BI pour utiliser votre application. Vous pouvez continuer à vous connecter à votre application comme avant, et afficher et interagir avec la création de rapports de Power BI sans nécessiter de licence supplémentaire.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Licences des collections d’espaces de travail Microsoft Power BI

Dans le modèle d’utilisation des **collections d’espaces de travail Microsoft Power BI**, la gestion des licences Power BI n’est pas la responsabilité de l’utilisateur.  Au lieu de cela, les **sessions** sont achetées par le développeur de l’application qui consomme les effets visuels et ils sont facturés à l’abonnement propriétaire de ces ressources. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Modèle de conception des collections d’espaces de travail Power BI

![Flux d’application lié aux collections d’espaces de travail](media/what-are-power-bi-workspace-collections/model.png)

Comme pour tout autre service dans Azure, les ressources des collections d’espaces de travail sont approvisionnées via les [API Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). Dans ce cas, la ressource approvisionnée correspond à une **collection d’espaces de travail Power BI**.

## <a name="workspace-collection"></a>Collection d’espaces de travail

Une **collection d’espaces de travail** est le conteneur Azure de plus haut niveau pour les ressources contenant 0 **espaces de travail**.  Une **collection** **d’espaces de travail** dispose de toutes les propriétés Azure standard, ainsi que des éléments suivants:

* **Clés d’accès** : clés utilisées lors de l’appel sécurisé aux API de Power BI (fonction décrite dans une section ultérieure).
* **Utilisateurs** : les utilisateurs Azure Active Directory (AAD) qui ont des droits d’administrateur pour gérer la collection d’espaces de travail Power BI via le portail Azure ou l’API Azure Resource Manager.
* **Région** : dans le cadre de l’approvisionnement d’une **collection d’espaces de travail**, vous pouvez sélectionner une région pour l’approvisionnement. Pour plus d’informations, consultez l’article [Régions Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Espace de travail

Un **espace de travail** est un conteneur de contenu Power BI, qui peut inclure des jeux de données et des rapports. Lors de sa création initiale, un **espace de travail** est vide. Vous allez créer du contenu avec Power BI Desktop et déployer par programmation le PBIX sur votre espace de travail avec les [API Power BI Import](https://msdn.microsoft.com/library/mt711504.aspx). Vous pouvez également créer votre jeu de données par programme, puis créer des rapports au sein de votre application au lieu d’utiliser Power BI Desktop.

## <a name="using-workspace-collections-and-workspaces"></a>Utiliser des collections d’espaces de travail et des espaces de travail

Les **collections d’espaces de travail** et les **espaces de travail** sont des conteneurs de contenu qui sont utilisés et organisés de la façon la plus adaptée à la conception de l’application que vous créez. Vous pouvez en organiser le contenu de plusieurs façons. Vous pouvez choisir de placer tout le contenu dans un seul espace de travail, puis d’utiliser des jetons d’application pour subdiviser le contenu entre vos clients. Vous pouvez également choisir de placer tous vos clients dans des espaces de travail distincts afin de les séparer. Ou, vous pouvez choisir d’organiser les utilisateurs par région, et non par client. Cette conception flexible vous permet de choisir la meilleure façon d’organiser le contenu.

## <a name="cached-datasets"></a>Jeux de données mis en cache

Il est possible d’utiliser des jeux de données mis en cache.  Cependant, vous ne pouvez pas actualiser les données mises en cache une fois qu’elles ont été chargées dans **les collections d’espaces de travail Microsoft Power BI**. Un jeu de données mis en cache signifie que vous avez importé les données dans Power BI Desktop au lieu d’utiliser DirectQuery.

## <a name="authentication-and-authorization-with-app-tokens"></a>Authentification et autorisation avec des jetons d’application

**Une collection d’espaces de travail Microsoft Power BI** s’appuie sur votre application pour l’autorisation et l’authentification requises des utilisateurs. Vos utilisateurs finaux ne doivent pas nécessairement être des clients Azure Active Directory (Azure AD).  Au lieu de cela, votre application donne aux **collections d’espaces de travail Microsoft Power BI** l’autorisation d’afficher un rapport Power BI en utilisant des **jetons d’authentification d’application (jetons d’application)**.  Ces **jetons d’application** sont créés en fonction des besoins lorsque votre application veut afficher un rapport.

![Diagramme représentant l’utilisation des jetons d’application](media/what-are-power-bi-workspace-collections/app-tokens.png)

Les **jetons d’authentification d’application (jetons d’application)** sont utilisés pour l’authentification auprès des **collections d’espaces de travail Microsoft Power BI**.  Il existe trois types de **jetons d’application**:

1. Jetons d’approvisionnement : utilisés lors de l’approvisionnement d’un nouvel **espace de travail** dans une **collection d’espaces de travail**
2. Jetons de développement : utilisés lors des appels directs aux **API REST de Power BI**
3. Jetons d’incorporation : utilisés lors d’appels servant à afficher un rapport dans l’iframe incorporé

Ces jetons sont utilisés pour les différentes phases de vos interactions avec les **collections d’espaces de travail Microsoft Power BI**.  Les jetons sont conçus de sorte que vous pouvez déléguer des autorisations de votre application à Power BI. Pour plus d’informations, consultez [Flux de jetons d’application](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Créer ou modifier des rapports dans l’application

Vous pouvez maintenant modifier des rapports existants ou en créer directement dans votre application sans avoir à utiliser Power BI Desktop. Cela requiert l’existence d’un jeu de données au sein de votre espace de travail.

## <a name="see-also"></a>Voir aussi

[Scénarios courants pour les collections d’espaces de travail Microsoft Power BI](scenarios.md)  
[Prise en main des collections d’espaces de travail Power BI](get-started.md)  
[Prise en main de l’exemple](get-started-sample.md)  
[Incorporer un rapport](embed-report.md)  
[Authentification et autorisation dans les collections d’espaces de travail Power BI](app-token-flow.md)  
[Exemple d’incorporation JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Référentiel Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Référentiel Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  

Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)
