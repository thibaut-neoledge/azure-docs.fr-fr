---
title: "Expressions conditionnelles du moteur de règles Azure CDN | Microsoft Docs"
description: "Documentation de référence sur les fonctionnalités et conditions de correspondance du moteur de règles Azure CDN."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Expressions conditionnelles du moteur de règles Azure CDN
Cette rubrique répertorie les descriptions détaillées des expressions conditionnelles pour le [moteur de règles](cdn-rules-engine.md) Azure Content Delivery Network (CDN).

La première partie d’une règle est l’expression conditionnelle.

Expression conditionnelle | Description
-----------------------|-------------
IF | Une expression IF fait toujours partie de la première instruction dans une règle. Comme toutes les autres expressions conditionnelles, cette instruction IF doit être associée à une correspondance. Si aucune expression conditionnelle supplémentaire n’est définie, cette correspondance détermine le critère qui doit être remplie avant qu’un ensemble de fonctionnalités puisse être appliqué à une requête.
AND IF | Une expression AND IF ne peut être ajoutée qu’après les types d’expressions conditionnelles suivants : IF, AND IF. Elle indique qu’il existe une autre condition qui doit être remplie pour l’instruction IF initiale.
ELSE IF| Une expression ELSE IF spécifie une autre condition qui doit être remplie avant la mise en place d’un ensemble de fonctionnalités spécifiques à cette instruction ELSE IF. La présence d’une instruction ELSE IF indique la fin de l’instruction précédente. La seule expression conditionnelle pouvant être placée après une instruction ELSE IF est une autre instruction ELSE IF. Cela signifie qu’une instruction IF ELSE peut uniquement servir à spécifier une seule condition supplémentaire qui doit être respectée.

**Exemple** : ![condition de correspondance CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Une règle ultérieure peut remplacer les actions spécifiées par une règle antérieure. Exemple : une règle de fourre-tout sécurise toutes les requêtes par le biais de l’authentification basée sur un jeton. Une autre règle peut être créée directement en dessous pour générer une exception pour certains types de requêtes.

### <a name="next-steps"></a>Étapes suivantes
* [Vue d'ensemble d'Azure CDN](cdn-overview.md)
* [Référence du moteur de règles](cdn-rules-engine-reference.md)
* [Conditions de correspondance du moteur de règles](cdn-rules-engine-reference-match-conditions.md)
* [Fonctionnalités du moteur de règles](cdn-rules-engine-reference-features.md)
* [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-rules-engine.md)
