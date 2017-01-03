---
title: "Activité de connexion anormale"
description: "Un rapport qui inclut des connexions qui ont été identifiées comme « anormales » par nos algorithmes d&quot;apprentissage automatique (Machine Learning)."
services: active-directory
documentationcenter: 
author: SSalahAhmed
manager: gchander
editor: 
ms.assetid: a5a270a9-a0eb-4a99-b04c-ccde3829ffe4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2016
ms.author: saah;kenhoff
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 565321b6f3ad5988f383a701cb9d8bd5c9937795


---
# <a name="irregular-sign-in-activity"></a>Activité de connexion anormale
Les connexions irrégulières sont celles qui ont été identifiées par nos algorithmes d’apprentissage automatique, sur la base d'une condition de « déplacement impossible » associée à un emplacement et un périphérique de connexion anormaux. Cela peut signifier qu'un pirate est parvenu à se connecter à l'aide de ce compte.
Nous envoyons une notification par courrier électronique aux administrateurs globaux si nous trouvons au moins 10 événements de connexion anormaux dans un intervalle de moins de 30 jours. Veillez à inclure aad-alerts-noreply@mail.windowsazure.com dans votre liste d'expéditeurs approuvés.




<!--HONumber=Jan17_HO1-->


