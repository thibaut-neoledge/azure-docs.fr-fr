---
title: "Approvisionnement d’applications avec filtres d’étendue | Microsoft Docs"
description: "Découvrez comment utiliser des filtres d’étendue pour empêcher les objets dans les applications qui prennent en charge l’approvisionnement automatisé des utilisateurs d’être approvisionnés si un objet n’est pas conforme à vos besoins."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: c06c089fb08c19b55246122201c378917a560e14
ms.openlocfilehash: 3e4458f70afce9ebd9477b00afc39b6e84e49319
ms.contentlocale: fr-fr
ms.lasthandoff: 03/01/2017


---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Approvisionnement d’applications basé sur les attributs avec filtres d’étendue
L’objectif de cette section est d’expliquer comment utiliser des filtres d’étendue pour définir des règles basées sur des attributs qui déterminent quels utilisateurs sont approvisionnés pour l’application.

## <a name="clauses-and-scope-groups"></a>Clauses et groupes d’étendue
![Filtre d’étendue][1] 

Les filtres d’étendue sont définis par un ou plusieurs **groupes d’étendue**, chacun contenant une ou plusieurs **clauses**. Pour afficher les clauses d’un groupe d’étendue particulier, développez-le en cliquant sur la flèche située à gauche du nom du groupe.

Une **clause** détermine quels utilisateurs sont autorisés à traverser le filtre d’étendue en évaluant les attributs de chaque utilisateur. Par exemple, l’une de vos clauses peut exiger que l’attribut « état » d’un utilisateur soit égal à New York, afin que seuls les utilisateurs de New York soient approvisionnés dans l’application.

![Nom du groupe d’étendue][2] 

Chaque **groupe d’étendue** commence par une **clause** obligatoire, comme illustré dans la capture d’écran ci-dessus. Cette clause stipule simplement que l’utilisateur doit être préalablement affecté à l’application avant son évaluation par vos filtres d’étendue. Cette clause ne peut pas être supprimée ou modifiée.

Vous pouvez ajouter de nouvelles clauses ou de nouveaux groupes d’étendue en appuyant sur le bouton approprié. Vous pouvez attribuer un nom à chaque groupe d’étendue en modifiant sa propriété **Nom du groupe d’étendue** .

## <a name="how-scoping-filters-are-evaluated"></a>Évaluation des filtres d’étendue
Lors de l’approvisionnement, nous testons chaque utilisateur affecté par rapport à vos filtres d’étendue pour déterminer si cet utilisateur mérite l’accès à l’application. On peut considérer chaque clause comme un test qui doit être réussi pour que l’utilisateur ne soit pas filtré et exclu. 

Si vous avez défini plusieurs groupes d’étendue, chaque utilisateur doit satisfaire à au moins l’un d’eux pour accéder à l’application. Dans chaque groupe d’étendue, toutefois, l’utilisateur doit satisfaire à chaque clause pour satisfaire à ce groupe d’étendue spécifique. 

En d’autres termes, on peut considérer les groupes d’étendue comme étant liés par une opération logique OU et les clauses au sein de ces groupes comme étant liées par une opération logique ET. Par exemple, considérez le filtre d’étendue ci-dessous :

![Nom du groupe d’étendue][3]  

D’après ce filtre d’étendue, les utilisateurs doivent satisfaire aux critères suivants pour être approvisionnés :

1. Ils doivent être affectés à l’application.
2. Ils doivent travailler dans le service Ingénierie
3. Ils doivent travailler à San Francisco ou au Canada.

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](active-directory-saas-customizing-attribute-mappings.md)
* [Écriture d’expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Notifications d’approvisionnement de comptes](active-directory-saas-account-provisioning-notifications.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./media/active-directory-saas-scoping-filters/ic782813.png

