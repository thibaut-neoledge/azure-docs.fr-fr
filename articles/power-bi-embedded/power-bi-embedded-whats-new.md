---
title: "Nouveautés de Power BI Embedded"
description: "Obtenir les dernières informations sur les nouveautés de Power BI Embedded"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 07c53a5d6b1881a4c207a2aefed9fcede0fa069e
ms.lasthandoff: 03/14/2017

---
# <a name="whats-new-in-power-bi-embedded"></a>Nouveautés de Power BI Embedded

Les mises à jour de **Power BI Embedded** sont publiées régulièrement. Toutefois, comme certaines versions portent sur les fonctionnalités du service principal, chaque version ne contient pas nécessairement de nouvelles fonctionnalités orientées utilisateur. Nous décrirons les nouvelles fonctionnalités orientées utilisateur ici. Veillez à consulter cette page régulièrement.

## <a name="march-2017"></a>Mars 2017

<iframe width="640" height="360" src="https://www.youtube.com/embed/ibuN4DzCl5c?showinfo=0" frameborder="0" allowfullscreen></iframe>

**Fonctionnalités libre-service**

* [Créer un rapport](power-bi-embedded-create-report-from-dataset.md)
* [Enregistrer un rapport sous](power-bi-embedded-save-reports.md)
* Incorporer un rapport dans le nouveau mode Lecture/Édition/Création 
* [Basculer un rapport entre les modes Édition/Lecture](power-bi-embedded-toggle-mode.md)

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

* [Analyses avancées avec Power BI intégré et R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31st-2016"></a>31 août 2016
Inclus dans cette version :

* Tous les nouveaux Kits de développement logiciel (SDK) JavaScript qui prennent en charge [la navigation de pages et le filtrage avancés](power-bi-embedded-interact-with-reports.md).
* Power BI Embedded est maintenant pris en charge dans le centre de données central du Canada. Vérifiez [l’état du centre de données](https://azure.microsoft.com/status/).

## <a name="july-11th-2016"></a>11 juillet 2016
Inclus dans cette version :

* **Bonne nouvelle !** Le service Power BI Embedded n’est plus uniquement disponible en version préliminaire, mais a été mis à la disposition générale.  
* Toutes les API REST ont été déplacées de **/beta** vers **/v1.0**.
* Les Kits de développement logiciel (SDK) .NET et JavaScript ont été mis à jour pour **v1.0**.
* Les appels de l’API Power BI peuvent désormais être authentifiés directement à l’aide de clés API. Les jetons d’application sont uniquement nécessaires lors de l’incorporation. Par conséquent, les jetons d’approvisionnement et de développement sont déconseillés dans l’API v1.0, mais ils continueront à fonctionner dans la version bêta jusqu’au 30/12/2016. Pour plus d’informations, consultez [Authentification et autorisation avec Power BI Embedded](power-bi-embedded-app-token-flow.md).
* Prise en charge de la sécurité au niveau des lignes (RLS) pour les jetons d’application et les rapports incorporés. Pour plus d’informations, consultez [Sécurité au niveau des lignes avec Power BI Embedded](power-bi-embedded-rls.md).
* Exemple d’application mis à jour pour tous les appels d’API **v1.0** .
* Prise en charge de Power BI Embedded pour le kit de développement logiciel (SDK) Azure, PowerShell et l’interface de ligne de commande.
* Les utilisateurs peuvent exporter des données de visualisation vers un **.csv**.
* Power BI Embedded est désormais pris en charge dans les mêmes langues/paramètres régionaux que Microsoft Azure. Pour plus d’informations, consultez [Azure - Langues](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).


