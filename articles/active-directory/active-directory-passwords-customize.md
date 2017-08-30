---
title: "Personnalisation : réinitialisation de mot de passe en libre-service d’Azure AD | Microsoft Docs"
description: "Options de personnalisation pour la réinitialisation de mot de passe libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 94d1d4c243bede354ae3deba7fbf5da0652567cb
ms.openlocfilehash: ee58d0c6703d7e6688ba9959a7f75c3b52a9411c
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>Personnaliser les fonctionnalités d’Azure AD pour la réinitialisation de mot en passe libre-service

Les professionnels de l’informatique qui souhaitent déployer la réinitialisation de mot de passe libre-service peuvent personnaliser l’expérience en fonction de leurs utilisateurs.

## <a name="customize-the-contact-your-administrator-link"></a>Personnaliser le lien « Contactez votre administrateur »

Même si la réinitialisation n’est pas activée, les utilisateurs peuvent toujours voir un lien « contactez votre administrateur » portail de réinitialisation de mot de passe.  Cliquer sur ce lien envoie un e-mail à vos administrateurs pour demander une assistance lors de la modification du mot de passe de l’utilisateur. Ce message électronique est envoyé aux destinataires suivants dans cet ordre :

1. Si le rôle **Administrateur de mot de passe** est affecté, les administrateurs détenteurs de ce rôle sont avertis
2. Si aucun administrateur de mot de passe n’est affecté, les administrateurs disposant du rôle **Administrateur d’utilisateurs** sont avertis
3. Si aucun des rôles précédents n’a été affecté, les **Administrateurs globaux** sont avertis

Dans tous les cas, un maximum de 100 destinataires sont informés.

Pour en savoir plus sur les différents rôles d’administrateur et sur la façon de les affecter, consultez le document [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Désactiver les e-mails Contactez votre administrateur

Si votre organisation ne souhaite pas avertir les administrateurs au sujet des demandes de réinitialisation de mot de passe, la configuration suivante peut être activée.

* Activez la réinitialisation du mot de passe en libre-service pour tous les utilisateurs finaux. Cette option se trouve sous **Réinitialisation de mot de passe > Propriétés**.
    * Si vous ne voulez pas que les utilisateurs réinitialisent leurs propres mots de passe, vous pouvez étendre l’accès à un groupe vide, mais **nous ne recommandons pas cette option**.
* Personnalisez le lien du support technique pour fournir une URL web ou un lien mailto: que les utilisateurs peuvent utiliser pour obtenir une assistance. Cette option se trouve sous **Réinitialisation de mot de passe > Personnalisation > Adresse e-mail ou URL du support technique**.

## <a name="customize-adfs-sign-in-page-for-sspr"></a>Personnaliser la page de connexion AD FS pour SSPR

Les administrateurs des services AD FS peut ajouter un lien à leur page de connexion à l’aide des instructions figurant dans l’article [Ajouter la description de la page de connexion](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

L’exécution de la commande suivante sur votre serveur AD FS ajoute un lien vers la page de connexion AD FS permettant aux utilisateurs d’accéder directement au flux de travail de réinitialisation de mot de passe libre-service.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>Personnaliser l’apparence du panneau d’accès et de connexion

Lorsque vos utilisateurs accèdent à la page de connexion, vous pouvez personnaliser le logo qui s’affiche, ainsi que l’image de la page de connexion en fonction de l’identité de votre société.

Ces graphiques s’affichent dans les circonstances suivantes :

* Lorsqu’un utilisateur saisit son nom d’utilisateur
* Lorsque l’utilisateur accède à une URL personnalisée
    * Lors de la transmission du paramètre « whr » à la page de réinitialisation de mot de passe, par exemple « https://login.microsoftonline.com/?whr=contoso.com »
    * Lors de la transmission du paramètre « username » à la page de réinitialisation de mot de passe, par exemple « https://login.microsoftonline.com/?username=admin@contoso.com »

### <a name="graphics-details"></a>Détails des éléments visuels

Les paramètres suivants vous permettent de modifier les caractéristiques visuelles de la page de connexion et se trouvent sous **Azure Active Directory**, **Personnalisation de la société**, **Modifier la personnalisation de la société**

* L’image de la page de connexion doit être un fichier PNG ou JPG de 1420 x 1200 pixels et ne dépassant pas 500 ko. Nous recommandons une taille d’environ 200 ko pour de meilleurs résultats.
* La couleur d’arrière-plan de la page de connexion est utilisée sur des connexions à latence élevée et doit être au format hexadécimal RVB.
* L’image de la bannière doit être un fichier PNG ou JPG de 60 x 280 pixels ne dépassant pas 10 Ko.
* Le logo carré (thème normal et foncé) doit être un fichier PNG ou JPG de 240 x 240 (redimensionnable) ne dépassant pas 10 Ko.

### <a name="sign-in-text-options"></a>Options de texte de connexion

Les paramètres suivants permettent d’ajouter du texte à la page de connexion propre à votre organisation. Ces paramètres se trouvent sous **Azure Active Directory**, **Personnalisation de la société**, **Modifier la personnalisation de la société**

* **Indice de nom d’utilisateur** remplace le texte d’exemple de someone@example.com avec un nom plus approprié pour vos utilisateurs, il est recommandé de laisser la valeur par défaut lors de la prise en charge d’utilisateurs internes et externes
* Le **Texte de la page de connexion** fait un maximum de 256 caractères. Ce texte apparaît partout où vos utilisateurs se connectent, et dans l’interface Azure AD Join sur Windows 10. Utilisez ce texte pour les conditions d’utilisation, les instructions et conseils pour vos utilisateurs. **N’importe qui peut voir votre page de connexion, aussi ne fournissez pas d’informations sensibles ici.**

### <a name="keep-me-signed-in-disabled"></a>Désactiver le maintien de la connexion

L’option « Désactiver le maintien de la connexion » permet aux utilisateurs de rester connectés quand ils ferment et rouvrent la fenêtre du navigateur. Cette option n’affecte pas la durée de vie de session. Ce paramètre se trouve sous **Azure Active Directory > Personnalisation de la société > Modifier la personnalisation de la société**.

Certaines fonctionnalités de SharePoint Online et Office 2010 dépendent de la capacité des utilisateurs à sélectionner cette case à cocher. Si vous masquez cette option, les utilisateurs peuvent obtenir des invites de connexion supplémentaires et inattendues.

### <a name="directory-name"></a>Nom de l’annuaire

Vous pouvez modifier l’attribut de nom sous **Azure Active Directory > Propriétés** pour indiquer un nom d’organisation convivial dans les communications automatisées et sur le portail. Cette option est essentiellement visible sous la forme d’e-mails automatiques dans les formes qui suivent

* Nom convivial dans l’e-mail « Microsoft pour le compte de la démonstration CONTOSO »
* Ligne d’objet dans l’e-mail « Code de vérification d’e-mail pour le compte de démonstration CONTOSO »

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [**Démarrage rapide**](active-directory-passwords-getting-started.md) : soyez rapidement opérationnel avec la gestion des mots de passe en libre-service Azure AD. 
* [**Licences**](active-directory-passwords-licensing.md) : configurez vos licences Azure AD.
* [**Données**](active-directory-passwords-data.md) : comprenez mieux les données requises et leur utilisation dans la gestion des mots de passe.
* [**Déploiement**](active-directory-passwords-best-practices.md) : planifiez et déployez la réinitialisation du mot de passe libre-service sur vos utilisateurs grâce aux conseils figurant ici.
* [**Stratégie**](active-directory-passwords-policy.md) : comprenez et définissez les stratégies de mot de passe d’Azure AD.
* [**Écriture différée du mot de passe**](active-directory-passwords-writeback.md) : fonctionnement de l’écriture différée du mot de passe avec votre répertoire local.
* [**Rapports**](active-directory-passwords-reporting.md) : découvrez si, quand et où vos utilisateurs accèdent aux fonctionnalités de réinitialisation de mot de passe en libre-service.
* [**Présentation technique approfondie**](active-directory-passwords-how-it-works.md) : découvrez ce qui se passe sous le capot pour mieux comprendre le fonctionnement.
* [**Forum Aux Questions (FAQ)**](active-directory-passwords-faq.md) : Comment ? Pourquoi ? Quoi ? Où ? Qui ? Quand ? - Les réponses aux questions que vous vouliez poser depuis toujours.
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec la réinitialisation de mot de passe en libre-service.


