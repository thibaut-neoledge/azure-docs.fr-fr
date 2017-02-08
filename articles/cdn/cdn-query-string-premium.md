---
title: "Contrôle de CDN Azure Premium sur le comportement de mise en cache des demandes Verizon avec des chaînes de requête | Microsoft Docs"
description: "La mise en cache des chaînes de requête CDN Azure contrôle la manière dont les fichiers doivent être mis en cache lorsqu’ils contiennent des chaînes de requête."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 20efef4e60da6a0ab07eab22ff18e7ae9ab12a30


---
# <a name="controlling-caching-behavior-of-cdn-requests-with-query-strings---premium"></a>Contrôle du comportement de mise en cache des demandes CDN avec des chaînes de requête - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [CDN Azure Premium fourni par Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
La mise en cache des chaînes de requête contrôle la manière dont les fichiers doivent être mis en cache lorsqu'ils contiennent des chaînes de requête.

> [!IMPORTANT]
> Les produits CDN Standard et Premium proposent les mêmes fonctionnalités de mise en cache des chaînes de requête, mais l’interface utilisateur est différente.  Ce document décrit l’interface du **CDN Azure Premium fourni par Verizon**.  Pour la mise en cache des chaînes de requête avec le **CDN Azure Standard fourni par Akamai** et le **CDN Azure Standard fourni par Verizon**, consultez [Contrôle du comportement de mise en cache des demandes CDN avec des chaînes de requête](cdn-query-string.md).
> 
> 

Trois modes sont disponibles :

* **standard-cache** : il s’agit du mode par défaut.  Le nœud de périmètre CDN transmet la chaîne de requête du demandeur vers l’origine de la première demande et met en cache l’élément multimédia.  Toutes les demandes ultérieures concernant cet élément multimédia traitées à partir du nœud de périmètre ignorent la chaîne de requête jusqu’à l’arrivée à expiration de l’élément multimédia mis en cache.
* **no-cache** : dans ce mode, les demandes avec des chaînes de requête ne sont pas mises en cache au niveau du nœud de périmètre CDN.  Le nœud de périmètre récupère l’élément multimédia directement à partir de l’origine et le transmet au demandeur avec chaque demande.
* **unique-cache** : ce mode traite chaque demande avec une chaîne de requête comme élément multimédia unique avec son propre cache.  Par exemple, la réponse depuis l’origine d’une demande pour *foo.ashx?q=bar* est mise en cache au niveau du nœud de périmètre et renvoyée pour les caches suivants avec la même chaîne de requête.  Une demande pour *foo.ashx?q=somethingelse* est mise en cache comme un élément multimédia distinct avec sa propre durée de vie.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Modification des paramètres de mise en cache des chaînes de requête pour les profils CDN Premium
1. Dans le panneau de profil CDN, cliquez sur le bouton **Gérer** .
   
    ![Bouton de gestion du panneau de profil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Le portail de gestion CDN s'ouvre.
2. Pointez sur l’onglet **HTTP volumineux**, puis pointez sur le menu volant **Paramètres de cache**.  Cliquez sur **Mise en cache des chaînes de requête**.
   
    Les options de mise en cache des chaînes de requête s'affichent.
   
    ![Options de mise en cache des chaînes de requête CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Une fois vos sélections effectuées, cliquez sur le bouton **Mettre à jour** .

> [!IMPORTANT]
> La modification des paramètres peut ne pas être visible immédiatement, car la propagation de l’inscription dans le CDN prend un certain temps.  Pour les profils du <b>CDN Azure fourni par Verizon</b>, la propagation s’effectue généralement dans un délai de 90 minutes, mais elle peut prendre plus de temps dans certains cas.
> 
> 




<!--HONumber=Nov16_HO3-->


