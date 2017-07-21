---
title: Entonnoirs Azure Application Insights
description: "Apprenez à utiliser les entonnoirs pour découvrir de quelle façon les clients interagissent avec votre application."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cfreeman
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 8d20995ebb63e8cf1e4d15c57628b80aaf9e036b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/19/2017

---

# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Découvrez comment les clients utilisent votre application avec les entonnoirs Application Insights

Comprendre l’expérience de vos utilisateurs est primordial pour votre entreprise. Si votre application implique plusieurs étapes, vous devrez savoir si la plupart des clients vont au bout du processus, ou s’ils arrêtent celui-ci à un moment donné. La progression via une série d’étapes dans une application web est appelée « entonnoir ». Vous pouvez utiliser les entonnoirs Application Insights pour obtenir des informations sur vos utilisateurs et suivre les taux de conversion étape par étape. 

## <a name="get-started-with-the-funnels-blade"></a>Prise en main du panneau Entonnoirs
La meilleure façon d’en savoir plus sur les entonnoirs est de prendre un exemple. Les illustrations suivantes montrent les étapes que les propriétaires d’entreprises de e-commerce devraient suivre pour apprendre de quelle façon leurs clients interagissent avec leur application web.  

### <a name="create-your-funnel"></a>Créer votre entonnoir
Avant de créer votre entonnoir, vous devez choisir la question à laquelle vous souhaitez répondre. Par exemple, vous souhaiterez peut-être connaître le nombre de clients qui cliquent sur une publicité lorsqu’ils consultent votre page d’accueil. Dans cet exemple, les propriétaires de la société Fabrikam Fiber souhaitent connaître le pourcentage de clients qui effectuent un achat après avoir ajouté des articles dans leur panier au cours du mois dernier.

Voici les étapes qu’ils suivent pour créer leur entonnoir.

1. Cliquez sur le bouton Nouveau du panneau Entonnoirs.
1. Sélectionnez l’intervalle de temps « Mois dernier » dans la liste déroulante **Intervalle de temps**. 
1. Sélectionnez l’événement **Product page** (Page du produit) dans la liste déroulante **Step 1** (Étape 1). 
1. Sélectionnez l’événement **Add to shopping cart** (Ajouter au panier) dans la liste déroulante **Step 2** (Étape 2).
1. Sélectionnez l’événement **Click purchase** (Cliquer sur Acheter) dans la liste déroulante **Step 3** (Étape 3).
1. Donnez un nom à l’entonnoir, puis cliquez sur **Enregistrer**.

L’illustration suivante montre les données générées par le panneau Entonnoirs. À partir d’ici, les propriétaires de Fabrikam peuvent voir que pendant la semaine passée, 22,7 % des clients ayant ajouté un article à leur panier ont effectué un achat. Ils peuvent également voir que 1 % des clients ont cliqué sur une publicité avant de consulter la page du produit, et que 20 % des clients se sont déconnectés après avoir effectué leur achat.


![Panneau Entonnoirs avec des données](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [l’analyse de l’utilisation](app-insights-usage-overview.md).
