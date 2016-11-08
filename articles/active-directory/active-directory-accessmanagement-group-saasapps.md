
---
title: Utilisation d’un groupe pour gérer l’accès aux applications SaaS| Microsoft Docs
description: Comment utiliser les groupes dans Azure Active Directory Premium ou Basic pour attribuer l’accès à des applications SaaS intégrées à Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: curtand

---
# Utilisation d’un groupe pour gérer l’accès aux applications SaaS
En utilisant Azure Active Directory (Azure AD) avec une licence Azure AD Premium ou Azure AD Basic, vous pouvez utiliser les groupes pour attribuer l’accès à une application SaaS intégrée à Azure AD. Par exemple, si vous souhaitez attribuer l’accès à différentes applications SaaS au service marketing, vous pouvez créer un groupe comportant les utilisateurs du service marketing, puis allouer ce groupe aux cinq applications SaaS dont il a besoin. De cette manière, vous pouvez gagner du temps en gérant de manière centralisée l’adhésion du service marketing. Ensuite, les utilisateurs sont affectés à l’application lorsqu’ils sont ajoutés en tant que membres du groupe marketing. Leurs attributions sont retirées de l’application lors de leur suppression du groupe marketing.

Cette fonctionnalité peut être utilisée avec des centaines d’applications pouvant être ajoutées à partir de la galerie d’applications Azure AD.

**Pour attribuer à un groupe l’accès à une application SaaS**

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **Active Directory** dans la barre de navigation à gauche.
2. Sélectionnez l’onglet **Répertoire**, puis ouvrez le répertoire dans lequel vous souhaitez attribuer l’accès à un groupe à une application SaaS.
3. Sélectionnez l’onglet **Applications**. Sélectionnez une application ajoutée à partir de la galerie d’applications, puis cliquez sur l’onglet **Utilisateurs et groupes**.
4. Sous l’onglet **Utilisateurs et groupes**, dans le champ **Commencer par**, saisissez le nom du groupe auquel vous souhaitez attribuer l’accès, puis cliquez sur la coche dans le coin supérieur droit. Il vous suffit de saisir le début du nom d’un groupe.
5. Sélectionnez le groupe, puis cliquez sur le bouton **Autoriser l’accès**. Sélectionnez **Oui** lorsque vous voyez le message de confirmation. Les appartenances à des groupes imbriquées ne sont pas prises en charge pour l’affectation basée sur le groupe à des applications à ce stade.
6. Vous pouvez également voir quels utilisateurs sont affectés à l’application, soit directement ou en étudiant l’adhésion au sein d’un groupe. Pour ce faire, modifiez la définition de la **liste déroulante Affichage de Groupe** sur **Tous les utilisateurs**. La liste affiche les utilisateurs du répertoire et indique s’ils sont affectés ou non à l’application. Elle indique également si les utilisateurs sont affectés directement à l’application (type d’affectation direct) ou par le biais de l’adhésion à un groupe (type d’affectation hérité).

> [!NOTE]
> L’onglet Utilisateurs et groupes ne s’affiche qu’une fois que vous avez activé Azure AD Premium ou Azure AD Basic.
> 
> 

## Articles connexes
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0817_2016-->