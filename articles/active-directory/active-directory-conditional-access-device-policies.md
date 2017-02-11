---
title: "Stratégies d’accès conditionnel basées sur les appareils pour les services Office 365 | Microsoft Docs"
description: "Informations détaillées sur la façon dont les conditions basées sur les appareils contrôlent l’accès aux services Office 365. Alors que les travailleurs de l’information veulent accéder à des services Office 365 comme Exchange et SharePoint Online au travail ou à l’école à partir de leurs appareils personnels, leur administrateur informatique souhaite que l’accès soit sécurisé. Les administrateurs informatiques peuvent configurer des stratégies d’appareil d’accès conditionnel pour sécuriser les ressources d’entreprise, tout en autorisant les travailleurs de l’information sur des appareils compatibles à accéder aux services."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4f29eef42855a1dceefdca7715a607a81703f439


---
# <a name="conditional-access-device-policies-for-office-365-services"></a>Stratégies d’accès conditionnel basées sur les appareils pour les services Office 365
Le terme « accès conditionnel » est associé à de nombreuses conditions telles que l’utilisateur authentifié multifacteur, l’appareil authentifié, l’appareil compatible, etc. Dans cette rubrique, nous allons nous concentrer principalement sur les conditions basées sur les appareils pour contrôler l’accès aux services Office 365. Alors que les travailleurs de l’information veulent accéder à des services Office 365 comme Exchange et SharePoint Online au travail ou à l’école à partir de leurs appareils personnels, leur administrateur informatique souhaite que l’accès soit sécurisé. Les administrateurs informatiques peuvent configurer des stratégies d’appareil d’accès conditionnel pour sécuriser les ressources d’entreprise, tout en autorisant les travailleurs de l’information sur des appareils compatibles à accéder aux services. Les stratégies d’accès conditionnel à Office 365 peuvent être configurées à partir du portail d’accès conditionnel Microsoft Intune.

Azure Active Directory applique des stratégies d’accès conditionnel pour sécuriser l’accès aux services Office 365. Un administrateur de client peut créer une stratégie d’accès conditionnel qui empêche un utilisateur sur un appareil non compatible d’accéder à un service O365. L’utilisateur doit se conformer aux stratégies d’appareil de la société avant que l’accès au service ne puisse être accordé. L’administrateur peut également créer une stratégie qui impose aux utilisateurs d’inscrire simplement leurs appareils pour accéder à un service O365. Les stratégies peuvent être appliquées à tous les utilisateurs d’une organisation ou être limitées à quelques groupes cibles et améliorées au fil du temps pour inclure des groupes cibles supplémentaires.

Une condition préalable à l’application des stratégies d’appareil est que les utilisateurs inscrivent leurs appareils auprès du service Azure Active Directory Device Registration. Vous pouvez choisir d’activer l’authentification multifacteur pour l’inscription des appareils auprès du service Azure Active Directory Device Registration. L’authentification multifacteur est recommandée pour le service Azure Active Directory Device Registration. Quand l’authentification multifacteur est activée, les utilisateurs qui inscrivent leurs appareils auprès du service Azure Active Directory Device Registration sont invités à accepter une authentification de second facteur.

## <a name="how-does-conditional-access-policy-work"></a>Fonctionnement d’une stratégie d’accès conditionnel
Quand un utilisateur demande l’accès au service O365 à partir d’une plateforme d’appareil prise en charge, Azure Active Directory authentifie l’utilisateur et l’appareil à partir duquel l’utilisateur lance la demande et accorde l’accès au service uniquement quand l’utilisateur est conforme à la stratégie définie pour le service. Les utilisateurs qui n’ont pas inscrit leur appareil reçoivent des instructions de rattrapage sur la façon de s’inscrire et de devenir compatibles pour accéder aux services O365 d’entreprise. Il est demandé aux utilisateurs d’appareils iOS et Android d’inscrire ces appareils à l’aide de l’application de portail de la société. Quand un utilisateur inscrit son appareil, celui-ci est inscrit auprès d’Azure Active Directory, pour la compatibilité et la gestion des appareils. Les clients doivent utiliser le service Azure Active Directory Device Registration avec Microsoft Intune pour activer la gestion des appareils mobiles pour le service Office 365. L’inscription de l’appareil constitue une condition préalable à l’accès des utilisateurs aux services Office 365 quand les stratégies d’appareil sont appliquées.

Quand un utilisateur inscrit son appareil avec succès, l’appareil devient approuvé. Azure Active Directory offre l’authentification unique pour accéder aux applications de la société et applique une stratégie d’accès conditionnel pour accorder l’accès à un service non seulement au moment de la première demande d’accès de l’utilisateur, mais aussi à chaque demande de renouvellement de cet accès. L’utilisateur se voit refuser l’accès aux services quand des informations d’identification de connexion sont modifiées, l’appareil est perdu/volé ou la stratégie n’est pas respectée au moment de la demande de renouvellement.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement :
Le service Azure Active Directory Device Registration doit être utilisé pour l’inscription des appareils.

Quand des utilisateurs doivent être authentifiés en local, les services AD FS (Active Directory Federation Services), versions 1.0 et ultérieures, sont requis. Multi-Factor Authentication pour la jonction d’espace de travail échoue si le fournisseur d’identité ne prend pas en charge l’authentification multifacteur. Par exemple, AD FS 2.0 n’est pas compatible avec l’authentification multifacteur. L’administrateur de client doit s’assurer que les services AD FS locaux sont compatibles avec l’authentification multifacteur et qu’une méthode d’authentification multifacteur valide est activée avant d’activer l’authentification multifacteur sur le service Azure Active Directory Device Registration. Par exemple, AD FS sur Windows Server 2012 R2 dispose de fonctionnalités d’authentification multifacteur. Vous devez également activer une méthode d’authentification multifacteur valide supplémentaire sur le serveur AD FS avant d’activer l’authentification multifacteur sur le service Azure Active Directory Device Registration. Pour plus d’informations sur les méthodes d’authentification multifacteur prises en charge dans AD FS, consultez Configurer des méthodes d’authentification supplémentaires pour AD FS.

## <a name="frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ)
Q : quelle est la stratégie d’exclusion par défaut pour les plateformes d’appareils non prises en charge ?

R : à l’heure actuelle, les stratégies d’accès conditionnel sont appliquées de manière sélective aux utilisateurs sur des appareils iOS et Android. Les applications sur d’autres plateformes d’appareils ne sont pas, par défaut, affectées par la stratégie d’accès conditionnel pour appareils iOS et Android. L’administrateur de client peut, cependant, choisir de remplacer la stratégie globale pour interdire l’accès aux utilisateurs sur des plateformes non prises en charge.
La feuille de route indique d’étendre la stratégie d’accès conditionnel aux utilisateurs sur d’autres plateformes, y compris Windows.

Q : quand la stratégie d’accès conditionnel aux services Office 365 sera-t-elle étendue aux applications basées sur le navigateur (par exemple, OWA, SharePoint basé sur le navigateur) ?

R : à l’heure actuelle, l’accès conditionnel aux services Office 365 est limité aux applications riches sur l’appareil. La feuille de route indique d’étendre la stratégie d’accès conditionnel aux utilisateurs qui accèdent aux services depuis les navigateurs.




<!--HONumber=Nov16_HO3-->


