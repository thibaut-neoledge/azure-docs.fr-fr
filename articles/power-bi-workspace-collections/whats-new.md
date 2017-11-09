---
title: "Nouveautés dans les collections d’espaces de travail Power BI"
description: "Obtenir les dernières informations sur les nouveautés des collections d’espaces de travail Power BI"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: a2faf610ca50acdb54353ade7c7a4ecabd314347
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Nouveautés dans les collections d’espaces de travail Power BI

Des mises à jour des **collections d’espaces de travail Power BI** sont publiées régulièrement. Toutefois, comme certaines versions portent sur les fonctionnalités du service principal, chaque version ne contient pas nécessairement de nouvelles fonctionnalités orientées utilisateur. Nous décrivons les nouvelles fonctionnalités orientées utilisateur dans cet article.

> [!IMPORTANT]
> Les collections d’espaces de travail Power BI sont déconseillées et disponibles jusqu’en juin 2018 ou jusqu’à la date indiquée sur votre contrat. Nous vous conseillons de planifier votre migration vers Power BI Embedded pour éviter toute interruption dans votre application. Pour plus d’informations sur la migration de vos données vers Power BI Embedded, consultez l’article [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migration du contenu de collections d’espaces de travail Power BI vers Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>Mars 2017

**Fonctionnalités libre-service**

* [Créer un rapport](create-report-from-dataset.md)
* [Enregistrer un rapport sous](save-reports.md)
* Incorporer un rapport dans le nouveau mode Lecture/Édition/Création 
* [Basculer un rapport entre les modes Édition/Lecture](toggle-mode.md)

**Connectivité des données avec les API REST**

* [Créer un jeu de données](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Effectuer une transmission des données de type push 

**API de gestion**

* Cloner un rapport et un jeu de données
* Lier un rapport à un autre jeu de données

**Exemples**

* [Exemple mis à jour d’incorporation de rapport JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>Décembre 2016

* [Nouvel exemple JavaScript intégré](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Octobre 2016

* [Analytique avancée avec les collections d’espaces de travail Power BI et R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>31 août 2016
Inclus dans cette version :

* Tous les nouveaux Kits de développement logiciel (SDK) JavaScript qui prennent en charge [la navigation de pages et le filtrage avancés](interact-with-reports.md).
* Les collections d’espaces de travail Power BI sont désormais prises en charge dans le centre de données central du Canada. Vérifiez [l’état du centre de données](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11 juillet 2016
Inclus dans cette version :

* **Bonne nouvelle !** Le service des collections d’espaces de travail Power BI n’est plus en préversion, mais mis à la disposition générale.  
* Toutes les API REST ont été déplacées de **/beta** vers **/v1.0**.
* Les Kits de développement logiciel (SDK) .NET et JavaScript ont été mis à jour pour **v1.0**.
* Les appels de l’API Power BI peuvent désormais être authentifiés directement à l’aide de clés API. Les jetons d’application sont uniquement nécessaires lors de l’incorporation. Par conséquent, les jetons d’approvisionnement et de développement sont déconseillés dans les API v1.0, mais ils continueront à fonctionner dans la version bêta jusqu’au 30 décembre 2016. Pour plus d’informations, consultez l’article [Authentification et autorisation avec Collections d’espaces de travail Power BI](app-token-flow.md).
* Prise en charge de la sécurité au niveau des lignes (RLS) pour les jetons d’application et les rapports incorporés. Pour plus d’informations, consultez l’article [Sécurité au niveau des lignes avec Collections d’espaces de travail Power BI](row-level-security.md).
* Exemple d’application mis à jour pour tous les appels d’API **v1.0** .
* Prise en charge des collections d’espaces de travail Power BI pour le Kit de développement logiciel (SDK) Azure, PowerShell et l’interface de ligne de commande.
* Les utilisateurs peuvent exporter des données de visualisation vers un **.csv**.
* Les collections d’espaces de travail Power BI sont désormais prises en charge dans les mêmes langues/paramètres régionaux que Microsoft Azure. Pour plus d’informations, consultez [Azure - Langues](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

