---
title: "Connexions après plusieurs échecs"
description: "Un rapport indiquant les utilisateurs qui sont parvenus à se connecter, mais après plusieurs tentatives de connexion infructueuses."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: e4ec1a39-9c20-418f-8a75-6497d0117176
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e55e0145adbdb1f41a8b8753d5555f20e96bf161
ms.contentlocale: fr-fr
ms.lasthandoff: 11/17/2016

---
# Connexions après plusieurs échecs
<a id="sign-ins-after-multiple-failures" class="xliff"></a>
Ce rapport indique les utilisateurs qui sont parvenus à se connecter, mais après plusieurs tentatives de connexion infructueuses. Les causes possibles sont :

* L’utilisateur a oublié son mot de passe</li><li>Son mot de passe lui a été dérobé

Les résultats de ce rapport vous indiqueront le nombre de tentatives consécutives effectuées par l’utilisateur avant de parvenir à se connecter et l’horodatage associé à la première connexion réussie.

**Paramètres de rapport**: vous pouvez configurer le nombre minimal de tentatives de connexion infructueuses qui doivent être effectuées consécutivement avant d’apparaître dans le rapport. Lorsque vous modifiez ce paramètre, il est important de savoir que ces modifications ne seront pas appliquées aux échecs de connexion existants qui sont déjà mentionnés dans un rapport existant. Elles seront toutefois appliquées à toutes les connexions futures. Cependant, seuls les administrateurs titulaires d’une licence peuvent effectuer ces modifications.

![Connexions après plusieurs échecs](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)


