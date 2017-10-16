---
title: "Remplacement du comportement HTTP à l’aide du moteur de règles Azure CDN | Microsoft Docs"
description: "Le moteur de règles vous permet de personnaliser la manière dont Azure CDN gère les requêtes HTTP, telles que le blocage de la remise de certains types de contenu, la définition d’une stratégie de mise en cache et la modification des en-têtes HTTP."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: abfe283476206b181018d187675b47112dc5ad2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Remplacement du comportement HTTP à l’aide du moteur de règles Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Vue d'ensemble
Le moteur de règles vous permet de personnaliser comment sont gérées les requêtes HTTP, telles que le blocage de la remise de certains types de contenu, la définition d’une stratégie de mise en cache et la modification des en-têtes HTTP.  Ce didacticiel présente la création d’une règle qui modifie le comportement de mise en cache des ressources CDN.  Du contenu vidéo est aussi disponible dans la section «[Voir aussi](#see-also)».

   > [!TIP] 
   > Pour obtenir une référence à la syntaxe détaillée, consultez [Référence du moteur de règles](cdn-rules-engine-reference.md).
   > 


## <a name="tutorial"></a>Didacticiel
1. Dans le panneau de profil CDN, cliquez sur le bouton **Gérer** .
   
    ![Bouton de gestion du panneau de profil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Le portail de gestion CDN s'ouvre.
2. Cliquez sur l’onglet **HTTP volumineux**, puis sur **Moteur de règles**.
   
    Les options d'une nouvelle règle s'affichent.
   
    ![Options de nouvelle règle CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > L'ordre dans lequel plusieurs règles sont répertoriées affecte la façon dont elles sont gérées. Une règle ultérieure peut remplacer les actions spécifiées par une règle antérieure.
   > 
   > 
3. Entrez un nom dans la zone de texte **Nom / Description** .
4. Identifiez le type de requêtes auxquelles la règle s'applique.  Par défaut, la condition de correspondance **Toujours** est sélectionnée.  Pour ce didacticiel, vous allez utiliser **Toujours** , donc conservez cette option.
   
   ![Condition de correspondance CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!TIP]
   > Il existe de nombreux types de condition de correspondance disponibles dans la liste déroulante.  Cliquez sur l'icône d'information bleue à gauche de la condition de correspondance pour obtenir une explication détaillée de la condition sélectionnée.
   > 
   >  Pour obtenir la liste complète des expressions conditionnelles en détail, consultez [Expressions conditionnelles du moteur de règles](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Pour obtenir la liste complète des conditions de correspondance en détail, consultez [Conditions de correspondance du moteur de règles](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 
5. Cliquez sur le bouton **+** en regard de **Fonctionnalités** pour ajouter une fonctionnalité.  Dans la liste déroulante située à gauche, sélectionnez **Forcer l'âge maximal interne**.  Dans la zone de texte qui s'affiche, entrez **300**.  Conservez les valeurs par défaut restantes.
   
   ![Fonctionnalité CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Comme avec les conditions de correspondance, en cliquez sur l'icône d'information bleue à gauche de la nouvelle fonctionnalité pour obtenir plus d'informations sur cette fonctionnalité.  Dans le cas de **Forcer l’âge maximal interne**, nous remplaçons les en-têtes **Cache-Control** et **Expires** de la ressource, afin de contrôler quand le nœud de périmètre CDN actualise la ressource à partir de l’origine.  Notre exemple de 300 secondes signifie que le nœud de périmètre CDN met la ressource en cache pendant 5 minutes avant l'actualisation de la ressource à partir de son origine.
   > 
   > Pour obtenir la liste complète des fonctionnalités en détail, consultez [Informations sur les fonctionnalités du moteur de règles](cdn-rules-engine-reference-features.md).
   > 
   > 
6. Cliquez sur le bouton **Ajouter** pour enregistrer la nouvelle règle.  La nouvelle règle est en attente d'approbation. Une fois qu’elle a été approuvée, l’état passe de **XML en attente** à **XML actif**.
   
   > [!IMPORTANT]
   > Les modifications de règles peuvent prendre jusqu’à 90 minutes avant d’être propagées à travers le CDN.
   > 
   > 

## <a name="see-also"></a>Voir aussi
* [Vue d'ensemble d'Azure CDN](cdn-overview.md)
* [Référence du moteur de règles](cdn-rules-engine-reference.md)
* [Conditions de correspondance du moteur de règles](cdn-rules-engine-reference-match-conditions.md)
* [Expressions conditionnelles du moteur de règles](cdn-rules-engine-reference-conditional-expressions.md)
* [Fonctionnalités du moteur de règles](cdn-rules-engine-reference-features.md)
* [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-rules-engine.md)
* [Azure Friday : les nouvelles fonctionnalités Premium puissantes du CDN Azure](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vidéo)