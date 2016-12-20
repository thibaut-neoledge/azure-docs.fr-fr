---
title: "Gestion des mots de passe dans Azure Active Directory | Microsoft Docs"
description: "Comment gérer les mots de passe dans Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a7679724-0ed5-4973-92e2-bd1285a6ef93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: ba3690084439aac83c91a1b4cfb7171b74c814f8
ms.openlocfilehash: 0006818063724e50e2febb02e5ebaa0e2f688ea1


---
# <a name="manage-passwords-in-azure-active-directory"></a>Gestion des mots de passe dans Azure Active Directory
Si vous êtes un administrateur, vous pouvez réinitialiser le mot de passe d’un utilisateur dans Azure Active Directory (Azure AD) dans le portail Azure Classic. Cliquez sur le nom de votre répertoire et sur la page Utilisateurs, cliquez sur le nom de l’utilisateur et, en bas du portail, cliquez sur **Réinitialiser le mot de passe**.

Le reste de cette rubrique couvre l'ensemble complet des fonctionnalités de gestion de mot de passe pris en charge par Azure AD, notamment :

* **mot de passe libre-service** permet aux utilisateurs finaux ou aux administrateurs de modifier leurs mots de passe expirés ou non expirés sans appeler un administrateur ou le support technique pour obtenir un support.
* **mot de passe libre-service** permet aux utilisateurs finaux ou aux administrateurs de modifier leurs mots de passe expirés ou non expirés sans appeler un administrateur ou le support technique pour obtenir un support. La réinitialisation du mot de passe libre-service nécessite Azure AD Premium ou de base. Pour plus d’informations, consultez les [éditions d’Azure Active Directory](active-directory-editions.md).
* **réinitialisation du mot de passe initiée par l’administrateur** permet à un administrateur de réinitialiser le mot de passe d’un utilisateur final ou d’un autre administrateur depuis le portail Azure Classic.
* **rapports d'activité de gestion de mot de passe** donnent aux administrateurs un aperçu de l’activité de réinitialisation et d’enregistrement des mots de passe dans leur organisation.
* **L'écriture différée de mot de passe** permet la gestion des mots de passe locaux depuis le cloud afin que tous les scénarios mentionnés ci-dessus puissent être exécutés par ou pour des utilisateurs fédérés ou avec un mot de passe synchronisé. L'écriture différée de mot de passe nécessite Azure AD Premium. Pour plus d’informations, consultez [Prise en main d'Azure Active Directory Premium](active-directory-get-started-premium.md).

> [!NOTE]
> Azure AD Premium est disponible pour les clients chinois à l'aide de l'instance mondiale d'Azure AD. Actuellement, Azure AD Premium n’est pas pris en charge dans le service Microsoft Azure fonctionnant avec 21Vianet en Chine. Pour plus d’informations, contactez-nous sur le [forum Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).
>
>

Utilisez les liens suivants pour accéder à la documentation qui vous intéresse le plus :

* [Présentation : gestion de mot de passe dans Azure AD](active-directory-passwords-how-it-works.md)
* [Réinitialisation de mot de passe libre-service dans Azure AD : activation, configuration et test de la réinitialisation de mot de passe libre-service](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
* [Réinitialisation de mot de passe libre-service dans Azure AD : personnalisation de la réinitialisation de mot de passe pour répondre à vos besoins](active-directory-passwords-customize.md)
* [Réinitialisation de mot de passe libre-service dans Azure AD : déploiement et gestion des meilleures pratiques](active-directory-passwords-best-practices.md)
* [Rapports de gestion des mots de passe dans Azure AD : affichage de l'activité de gestion de mot de passe dans votre client](active-directory-passwords-get-insights.md)
* [Écriture différée de mot de passe :configuration d’Azure AD pour gérer les mots de passe locaux](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
* [Résolution des problèmes liés à la gestion des mots de passe Azure AD](active-directory-passwords-troubleshoot.md)
* [Forum aux questions sur la gestion des mots de passe Azure AD](active-directory-passwords-faq.md)

## <a name="whats-next"></a>Étapes suivantes
* [Administration d’Azure AD](active-directory-administer.md)
* [Création ou modification des utilisateurs dans Azure AD](active-directory-create-users.md)
* [Gestion des groupes dans Azure AD](active-directory-manage-groups.md)



<!--HONumber=Nov16_HO3-->


