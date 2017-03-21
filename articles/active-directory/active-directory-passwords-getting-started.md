---
title: "Prise en main : gestion des mots de passe Azure AD | Docs"
description: "Permettez à vos utilisateurs de réinitialiser eux-mêmes leurs mots de passe, de découvrir la configuration requise pour la réinitialisation du mot de passe et d’activer l’écriture différée pour gérer des mots de passe en local dans Active Directory."
services: active-directory
keywords: "Gestion des mots de passe Active Directory, gestion des mots de passe, réinitialiser le mot de passe Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 441caf3cc9a3b9074bd263f4a4c45763967fa580
ms.lasthandoff: 03/15/2017


---
# <a name="getting-started-with-password-management"></a>Prise en main de la gestion de mot de passe
> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password).
>
>

Pour permettre à vos utilisateurs de gérer eux-mêmes leurs mots de passe Azure Active Directory pour le cloud ou pour une utilisation en local, il suffit de suivre une procédure simple. Après avoir vérifié que vous disposez de la configuration requise, vous pouvez rapidement activer la modification et la réinitialisation des mots de passe pour toute votre organisation. Cet article présente les concepts suivants :

* [**Les meilleurs conseils de nos clients à lire avant de commencer**](#top-tips-from-our-customers-to-read-before-you-begin)
 * [**PRINCIPAL CONSEIL : NAVIGATION DANS LA DOCUMENTATION** - Utilisez notre table des matières et la fonction de recherche de votre navigateur pour trouver des réponses](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
 * [**Conseil 1 : GESTION DES LICENCES** -Assurez-vous que vous comprenez les accords de licence](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
 * [**Conseil 2 : TEST** - Effectuez des tests avec un utilisateur final, et non un administrateur, et pilotez un petit groupe d’utilisateurs](#tip-2-testing---test-with-a-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
 * [**Conseil 3 : DÉPLOIEMENT** -Préremplissez les données de vos utilisateurs afin qu’ils n’aient pas à s’inscrire](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
 * [**Conseil 4 : DÉPLOIEMENT** -Utilisez la réinitialisation de mot de passe pour éviter la transmission de mots de passe temporaires](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
 * [**Conseil 5 : ÉCRITURE DIFFÉRÉE** - Consultez le journal des événements d’application sur votre ordinateur AAD Connect pour résoudre les problèmes d’écriture différée de mot de passe](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
 * [**Conseil 6 : ÉCRITURE DIFFÉRÉE** - Assurez-vous que vous activez les autorisations appropriées, les règles de pare-feu et les paramètres de connexion pour l’écriture différée de mot de passe](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
 * [**Conseil 7 : CRÉATION DE RAPPORTS** - Identifiez qui inscrit ou réinitialise les mots de passe grâce aux journaux d’audit SSPR d’Azure AD](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
 * [**Conseil 8 : RÉSOLUTION DES PROBLÈMES** - Lisez notre guide de résolution des problèmes et le FAQ pour résoudre de nombreux problèmes](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
 * [**Conseil 9 : RÉSOLUTION DES PROBLÈMES** - Si vous avez besoin d’aide, indiquez suffisamment d’informations pour que nous puissions vous aider](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)
* [**Comment permettre aux utilisateurs de réinitialiser leur mot de passe Azure Active Directory**](#enable-users-to-reset-their-azure-ad-passwords)
 * [Configuration requise pour la réinitialisation du mot de passe libre-service](#prerequisites)
 * [Étape 1 : configuration de la stratégie de réinitialisation du mot de passe](#step-1-configure-password-reset-policy)
 * [Étape 2 : ajout de données de contact pour votre utilisateur de test](#step-2-add-contact-data-for-your-test-user)
 * [Étape 3 : réinitialisation de votre mot de passe en tant qu’utilisateur](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Comment permettre aux utilisateurs de réinitialiser leurs mots de passe Active Directory Azure pour une utilisation en local**](#enable-users-to-reset-or-change-their-ad-passwords)
 * [Configuration requise pour l’écriture différée de mot de passe](#writeback-prerequisites)
 * [Étape 1 : téléchargement de la dernière version d’Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
 * [Étape 2 : activation et vérification de l’écriture différée des mots de passe dans Azure AD Connect via l’interface utilisateur ou PowerShell](#step-2-enable-password-writeback-in-azure-ad-connect)
 * [Étape 3 : configuration de votre pare-feu](#step-3-configure-your-firewall)
 * [Étape 4 : configuration des autorisations appropriées](#step-4-set-up-the-appropriate-active-directory-permissions)
 * [Étape 5 : réinitialisation de votre mot de passe Active Directory en tant qu’utilisateur, puis vérification de son fonctionnement](#step-5-reset-your-ad-password-as-a-user)

## <a name="top-tips-from-our-customers-to-read-before-you-begin"></a>Les meilleurs conseils de nos clients à lire avant de commencer
Voici certains conseils qui se sont avérés utiles pour les clients ayant déployé la gestion des mots de passe dans leur organisation.

* [**PRINCIPAL CONSEIL : NAVIGATION DANS LA DOCUMENTATION** - Utilisez notre table des matières et la fonction de recherche de votre navigateur pour trouver des réponses](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
* [**Conseil 1 : GESTION DES LICENCES** -Assurez-vous que vous comprenez les accords de licence](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
* [**Conseil 2 : TEST** - Effectuez des tests avec un utilisateur final, et non un administrateur, et pilotez un petit groupe d’utilisateurs](#tip-2-testing---test-with-a-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
* [**Conseil 3 : DÉPLOIEMENT** -Préremplissez les données de vos utilisateurs afin qu’ils n’aient pas à s’inscrire](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
* [**Conseil 4 : DÉPLOIEMENT** -Utilisez la réinitialisation de mot de passe pour éviter la transmission de mots de passe temporaires](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
* [**Conseil 5 : ÉCRITURE DIFFÉRÉE** - Consultez le journal des événements d’application sur votre ordinateur AAD Connect pour résoudre les problèmes d’écriture différée de mot de passe](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
* [**Conseil 6 : ÉCRITURE DIFFÉRÉE** - Assurez-vous que vous activez les autorisations appropriées, les règles de pare-feu et les paramètres de connexion pour l’écriture différée de mot de passe](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
* [**Conseil 7 : CRÉATION DE RAPPORTS** - Identifiez qui inscrit ou réinitialise les mots de passe grâce aux journaux d’audit SSPR d’Azure AD](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
* [**Conseil 8 : RÉSOLUTION DES PROBLÈMES** - Lisez notre guide de résolution des problèmes et le FAQ pour résoudre de nombreux problèmes](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
* [**Conseil 9 : RÉSOLUTION DES PROBLÈMES** - Si vous avez besoin d’aide, indiquez suffisamment d’informations pour que nous puissions vous aider](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)

### <a name="top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers"></a>CONSEIL : NAVIGATION DANS LA DOCUMENTATION - Utilisez notre table des matières et la fonction de recherche de votre navigateur pour trouver des réponses
Dans notre documentation, nous nous sommes efforcés de fournir dans notre table des matières des liens rapides vers tous les contenus intéressants pour les administrateurs qui souhaitent en savoir plus. 

Consultez la table des matières ci-dessous : 
* [Réinitialisation de mot de passe Azure AD : Table des matières de la documentation](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)

### <a name="tip-1-licensing---make-sure-you-understand-the-licensing-requirements"></a>Conseil 1 : GESTION DES LICENCES -Assurez-vous que vous comprenez les accords de licence
Pour que la réinitialisation de mot de passe Azure AD fonctionne, vous devez disposer d’au moins une licence affectée dans votre organisation. Nous n’appliquons pas la gestion des licences par utilisateur dans l’expérience de réinitialisation de mot de passe en elle-même, toutefois, si vous utilisez la fonctionnalité sans disposer d’une licence affectée à un utilisateur, cela sera considéré comme une violation de votre contrat de licence Microsoft et vous devrez affecter des licences à ces utilisateurs.

Voici quelques documents qui peuvent vous aider à comprendre quelles licences sont requises pour la réinitialisation du mot de passe.
* [Informations générales sur la gestion des licences de réinitialisation de mot de passe]()
* [Informations sur la gestion des licences de réinitialisation de mot de passe par fonctionnalité]()
* [Scénarios pris en charge par l’écriture différée de mot de passe]()

### <a name="tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users"></a>Conseil 2 : TEST - Effectuez des tests avec un utilisateur final, et non un administrateur, et pilotez un petit groupe d’utilisateurs
Lors des tests avec un administrateur, nous appliquons la stratégie de réinitialisation de mot de passe administrateur définie ci-dessous.  Cela signifie que vous ne verrez PAS les résultats attendus de la stratégie que vous avez configurée pour vos utilisateurs finaux.

Les stratégies configurées dans l’expérience utilisateur d’administration s’appliquent uniquement aux utilisateurs finaux, et non aux administrateurs. Microsoft met en œuvre des stratégies de réinitialisation de mot de passe par défaut robustes pour les administrateurs. Celles-ci peuvent être différentes des stratégies que vous définissez pour vos utilisateurs finaux, afin de garantir la sécurité de votre organisation.

#### <a name="administrator-password-reset-policy"></a>Stratégie de réinitialisation de mot de passe administrateur
* **S’applique à** : n’importe quel rôle d’administrateur (administrateur général, administrateur de support technique, administrateur de mots de passe, etc.)
* **La stratégie à une entrée s’applique...**
 * ... pendant les 30 premiers jours après le démarrage d’un essai **OU**
 * ... lorsqu’un domaine personnel n’est pas présent **ET** qu’Azure AD Connect ne synchronise pas les identités
 * **_Requiert_** : la présence d’une valeur dans **l’une** des options suivantes : Adresse électronique d’authentification, Adresse de messagerie de secours, Téléphone d’authentification, Téléphone mobile ou Téléphone (bureau)
* **La stratégie à deux entrées s’applique...** 
 * ... à l’issue des 30 premiers jours de l’essai **OU**
 * ... lorsqu’un domaine personnel est présent **OU** 
 * ... si vous avez activé Azure AD Connect pour synchroniser les identités de votre environnement local
 * _**Requiert**_ : la présence d’une valeur dans **deux** des options suivantes : Adresse électronique d’authentification, Adresse de messagerie de secours, Téléphone d’authentification, Téléphone mobile ou Téléphone (bureau)

### <a name="tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register"></a>Conseil 3 : DÉPLOIEMENT -Préremplissez les données de vos utilisateurs afin qu’ils n’aient pas à s’inscrire
La plupart des gens ignorent qu’il n’est pas nécessaire que vos utilisateurs s’inscrivent à la réinitialisation de mot de passe pour pouvoir utiliser cette fonctionnalité.  En définissant les propriétés de téléphone ou de courrier électronique pour vos utilisateurs au préalable, vous pouvez immédiatement déployer la réinitialisation de mot de passe dans l’ensemble de l’organisation **sans que vos utilisateurs ne doivent effectuer quelque opération que ce soit !**

Pour savoir comment procéder à l’aide d’une API, de PowerShell ou d’Azure AD Connect, lisez la documentation ci-dessous :
* [Déploiement de la réinitialisation du mot de passe sans demander l’inscription des utilisateurs](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [Données utilisées par la réinitialisation de mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords"></a>Conseil 4 : DÉPLOIEMENT - Utilisez la réinitialisation de mot de passe pour éviter la transmission de mots de passe temporaires
Il s’agit d’un complément du conseil n° 3. Une fois vos utilisateurs configurés pour la réinitialisation de mot de passe, imaginez un scénario dans lequel un employé rejoint votre entreprise pour la première fois. Au lieu de lui communiquer le mot de passe temporaire, vous pouvez simplement lui demander d’accéder au [portail de réinitialisation de mot de passe Azure AD](https://passwordreset.microsoftonline.com) pour réinitialiser son mot de passe.

Si l’utilisateur fait usage d’un [appareil joint à un domaine Azure AD de Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy), il peut même effectuer l’opération directement dans l’écran de connexion de Windows 10, ce qui lui permet d’accéder à un tout nouveau PC sans aucune intervention de votre part !

Pour savoir comment procéder à l’aide d’une API, de PowerShell ou d’Azure AD Connect, lisez la documentation ci-dessous. Une fois ces données préremplies, invitez vos utilisateurs à réinitialiser leurs mots de passe. Ils peuvent alors accéder immédiatement à leurs comptes :
* [Déploiement de la réinitialisation du mot de passe sans demander l’inscription des utilisateurs](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [Données utilisées par la réinitialisation de mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback"></a>Conseil 5 : ÉCRITURE DIFFÉRÉE - Consultez le journal des événements d’application sur votre ordinateur AAD Connect pour résoudre les problèmes d’écriture différée de mot de passe
Le journal des événements de l’application de connexion Azure AD contient un ensemble complet d’informations de journalisation qui décrit une grande partie de ce qui se produit avec le service d’écriture différée de mot de passe, en temps réel. Pour accéder à ce journal, procédez comme suit :

1. Connectez-vous à votre ordinateur **Azure AD Connect**.
2. Ouvrez **l’observateur d’événements Windows** en cliquant sur **Démarrer** et en tapant **« Observateur d’événements »**.
3. Ouvrez le journal d’événements de **l’application**.
4. Recherchez des événements à partir des sources suivantes : **PasswordResetService** ou **ADSync** pour en savoir plus sur le problème qui se produit.

Pour obtenir une liste complète des événements qui peuvent apparaître dans ce journal, ainsi que quelques conseils de dépannage supplémentaires en matière d’écriture différée de mot de passe, consultez :
* [Résoudre les problèmes d’écriture différée du mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [Codes d’erreur du journal des événements d’écriture différée](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [Résoudre des problèmes de connectivité d’écriture différée du mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [Déploiement de l’écriture différée - Étape 3 : Configurer votre pare-feu](#step-3-configure-your-firewall)
* [Déploiement de l’écriture différée - Étape 4 : Configurer les autorisations appropriées](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback"></a>Conseil 6 : ÉCRITURE DIFFÉRÉE - Assurez-vous que vous activez les autorisations appropriées, les règles de pare-feu et les paramètres de connexion pour l’écriture différée de mot de passe
Pour que l’écriture différée fonctionne correctement, vous devez vérifier les points suivants :

1. Les **autorisations Active Directory** appropriées ont été définies pour les utilisateurs à l’aide de la fonctionnalité d’écriture différée de mot de passe de sorte qu’ils disposent de droits permettant de modifier leurs propres mots de passe et les indicateurs de déverrouillage de compte dans Active Directory
2. Les **ports de pare-feu** appropriés ont été ouverts pour permettre au service d’écriture différée de mot de passe de communiquer de manière sécurisée avec le monde extérieur à l’aide d’une connexion sortante
3. Les **exceptions de pare-feu** appropriées ont été définies pour les URL de service de réinitialisation de mot de passe de clé, comme Service Bus
4. Votre **proxy et votre pare-feu n’arrêtent pas les connexions sortantes inactives** (nous recommandons une durée d’au moins 10 minutes)

Pour obtenir la liste complète des conseils de dépannage et des recommandations spécifiques pour la configuration des autorisations et des règles de pare-feu pour l’écriture différée de mot de passe, consultez :
* [Résoudre les problèmes d’écriture différée du mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [Codes d’erreur du journal des événements d’écriture différée](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [Résoudre des problèmes de connectivité d’écriture différée du mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [Déploiement de l’écriture différée - Étape 3 : Configurer votre pare-feu](#step-3-configure-your-firewall)
* [Déploiement de l’écriture différée - Étape 4 : Configurer les autorisations appropriées](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs"></a>Conseil 7 : CRÉATION DE RAPPORTS - Identifiez qui inscrit ou réinitialise des mots de passe grâce aux journaux d’audit SSPR d’Azure AD 
Une fois que la réinitialisation de mot de passe est déployée et fonctionne, l’étape logique suivante consiste à observer son fonctionnement et à analyser les personnes devant toujours s’inscrire, les problèmes fréquents rencontrés par vos utilisateurs lors de la réinitialisation et votre retour sur investissement par rapport à la fonctionnalité.

Grâce aux journaux d’audit de réinitialisation de mot de passe Azure AD, vous pouvez effectuer cette opération et bien plus encore à partir du portail Azure, à partir de Power BI, à partir de l’API d’événements de rapports Azure AD ou à partir de PowerShell.  Pour en savoir plus sur l’utilisation de ces fonctions de génération de rapports, consultez :
* [Vue d’ensemble des rapports de gestion des mots de passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#overview-of-password-management-reports)
* [Comment visualiser les rapports de gestion des mots de passe dans le portail Azure](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-view-password-management-reports)
* [Types d’activités de gestion des mots de passe libre-service dans le portail Azure](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#self-service-password-management-activity-types-in-the-new-azure-portal)
* [Comment récupérer les événements de gestion des mots de passe à partir de l’API de rapports et d’événements Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
* [Comment télécharger rapidement les événements d’inscription de réinitialisation de mot de passe avec PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-download-password-reset-registration-events-quickly-with-powershell)

### <a name="tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues"></a>Conseil 8 : RÉSOLUTION DES PROBLÈMES - Lisez notre guide de résolution des problèmes et le FAQ pour résoudre de nombreux problèmes
Saviez-vous que la réinitialisation de mot de passe possède un ensemble complet de conseils de dépannage et un forum aux questions ? Si vous avez une question, il est probable que vous trouviez la réponse dans les liens ci-dessous.

En outre, vous pouvez également utiliser le panneau **« Support et dépannage »** dans le [portail Azure](https://portal.azure.com) pour accéder à un ensemble complet de contenus liés à la résolution des problèmes, directement à partir de l’expérience utilisateur d’administration de gestion des mots de passe disponible sous **Azure Active Directory** -> **Utilisateurs et groupes** -> **Réinitialisation de mot de passe** -> **Support et dépannage** dans le volet de navigation de gauche.

Liens vers des conseils et le FAQ relatifs à la résolution des problèmes de réinitialisation des mots de passe :
* [Résolution des problèmes de gestion des mots de passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot)
* [FAQ sur la gestion des mots de passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-faq)

### <a name="tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you"></a>Conseil 9 : RÉSOLUTION DES PROBLÈMES - Si vous avez besoin d’aide, indiquez suffisamment d’informations pour que nous puissions vous aider.
Si malgré toutes ces informations vous avez besoin d’aide pour résoudre un problème, n’hésitez pas à nous contacter. Vous pouvez ouvrir un cas de support ou faire appel à votre équipe de gestion de compte pour nous contacter directement. Nous sommes à votre écoute.

Cependant, avant de nous contacter, **veillez à rassembler toutes les informations indiquées ci-dessous** afin que nous puissions vous aider rapidement !
* [Informations à inclure lorsque vous avez besoin d’aide](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#information-to-include-when-you-need-help)

#### <a name="ways-to-provide-password-reset-feedback"></a>Méthodes de transmission de commentaires relatifs à la réinitialisation de mot de passe
* [Demandes ou résolutions de problèmes liées aux fonctionnalités - Publication sur les forums MSDN d’Azure AD](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)
* [Demandes ou résolutions de problèmes liées aux fonctionnalités - Publication sur StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
* [Demandes ou résolutions de problèmes liées aux fonctionnalités - Tweet @azuread!](https://twitter.com/azuread)
* [Demandes liées aux fonctionnalités uniquement - Laissez un message sur UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>Activation de la réinitialisation du mot de passe pour les utilisateurs Azure AD
Cette section explique comment activer la réinitialisation du mot de passe libre-service pour votre annuaire cloud AAD, en y inscrivant vos utilisateurs, puis comment effectuer un test de réinitialisation du mot de passe libre-service en tant qu’utilisateur.

* [Configuration requise pour la réinitialisation du mot de passe libre-service](#prerequisites)
* [Étape 1 : configuration de la stratégie de réinitialisation du mot de passe](#step-1-configure-password-reset-policy)
* [Étape 2 : ajout de données de contact pour votre utilisateur de test](#step-2-add-contact-data-for-your-test-user)
* [Étape 3 : réinitialisation de votre mot de passe en tant qu’utilisateur](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>Composants requis
Avant de pouvoir activer et utiliser la réinitialisation du mot de passe libre-service, vous devez effectuer la configuration requise suivante :

* Créez un client AAD. Pour plus d’informations, consultez la page [Prise en main d’Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)
* Obtenez un abonnement Azure. Pour plus d’informations, consultez la rubrique [Qu’est-ce qu’un client Azure AD ?](active-directory-administer.md#what-is-an-azure-ad-tenant).
* Associez votre client AAD à votre abonnement Azure. Pour plus d’informations, consultez la page [Comment sont associés les abonnements Azure et Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).
* Mettez à niveau vers Azure AD Premium, Basic ou utilisez une licence O365 payée. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

  > [!NOTE]
  > Pour activer la réinitialisation du mot de passe libre-service pour les utilisateurs du cloud, vous devez procéder à une mise à niveau vers Azure AD Premium ou Basic ou une licence O365 payée.  Pour activer la réinitialisation du mot de passe libre-service pour les utilisateurs locaux, vous devez procéder à une mise à niveau vers Azure AD Premium. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Ces informations incluent des instructions détaillées sur l'inscription à Azure AD Premium ou Basic, l’activation d’un plan de licence et d’un accès Azure AD, mais aussi l’attribution d’accès aux comptes administrateur et utilisateur.
  >
  >
* Créez au moins un compte d’administrateur et un compte d’utilisateur dans votre annuaire AAD.
* Attribuez une licence AAD Premium, Basic ou O365 aux comptes d’administrateur et d’utilisateur que vous avez créés.

### <a name="step-1-configure-password-reset-policy"></a>Étape 1 : configuration de la stratégie de réinitialisation du mot de passe
Pour configurer la stratégie de réinitialisation du mot de passe utilisateur, procédez comme suit :

1. Ouvrez le navigateur de votre choix et accédez au [Portail Azure Classic](https://manage.windowsazure.com).
2. Dans la barre de navigation à gauche du [portail Azure Classic](https://manage.windowsazure.com), recherchez l’**extension Active Directory**.

   ![Gestion des mots de passe dans Azure AD][001]
3. Sous l’onglet **Annuaire** , cliquez sur l’annuaire dans lequel vous souhaitez configurer la stratégie de réinitialisation du mot de passe utilisateur, par exemple, Wingtip Toys.

    ![][002]
4. Cliquez sur l'onglet **Configurer** .

   ![][003]

5. Sous l’onglet **Configurer**, faites défiler vers le bas jusqu’à la section **Stratégie de réinitialisation du mot de passe utilisateur**.  Dans cette section, vous pouvez configurer chaque aspect de la stratégie de réinitialisation du mot de passe pour un annuaire donné. *Si vous ne voyez pas l’onglet Configurer, vérifiez que vous êtes inscrit à Azure Active Directory Premium ou De base et que vous avez __affecté une licence__ au compte administrateur qui configure cette fonctionnalité.*  

   > [!NOTE]
   > **La stratégie qui vous avez définie s’applique uniquement aux utilisateurs finaux de votre organisation, et non aux administrateurs**. Pour des raisons de sécurité, Microsoft contrôle la stratégie de réinitialisation du mot de passe pour les administrateurs. La stratégie actuelle pour les administrateurs exige deux tests : Téléphone mobile et Adresse de messagerie.

   ![][004]
6. Pour configurer la stratégie de réinitialisation du mot de passe utilisateur, définissez le paramètre **Utilisateurs autorisés à réinitialiser leur mot de passe** sur **Oui**.  Cette action permet d’afficher plusieurs autres contrôles qui vous permettent de configurer le fonctionnement de cette fonctionnalité dans votre annuaire.  N’hésitez pas à personnaliser la réinitialisation du mot de passe selon vos besoins.  Si vous souhaitez en savoir plus sur le fonctionnement de chaque contrôle de la stratégie de réinitialisation du mot de passe, consultez la page [Personnalisation de la gestion de mot de passe Azure AD](active-directory-passwords-customize.md).

   ![][005]
7. Après avoir configuré la stratégie de réinitialisation du mot de passe comme vous le souhaitez pour votre client, cliquez sur le bouton **Enregistrer** situé en bas de l’écran.

   > [!NOTE]
   > Nous vous recommandons de mettre en place une stratégie de réinitialisation du mot de passe utilisateur à double vérification pour observer le comportement de cette fonctionnalité dans les situations les plus complexes.
   >
   >

   ![][006]

   > [!NOTE]
   > **La stratégie qui vous avez définie s’applique uniquement aux utilisateurs finaux de votre organisation, et non aux administrateurs**. Pour des raisons de sécurité, Microsoft contrôle la stratégie de réinitialisation du mot de passe pour les administrateurs. La stratégie actuelle pour les administrateurs exige deux tests : Téléphone mobile et Adresse de messagerie.
   >
   >

### <a name="step-2-add-contact-data-for-your-test-user"></a>Étape 2 : ajout de données de contact pour votre utilisateur de test
Vous avez plusieurs options pour spécifier les données que les utilisateurs de votre organisation doivent utiliser pour la réinitialisation du mot de passe.

* Modifiez les utilisateurs dans le [portail Azure Classic](https://manage.windowsazure.com) ou le [portail d’administration d’Office 365](https://portal.microsoftonline.com).
* Utilisez AAD Connect pour synchroniser les propriétés de l’utilisateur dans Azure AD à partir d’un domaine Active Directory local.
* Utilisez Windows PowerShell pour modifier les propriétés de l’utilisateur.
* Autorisez les utilisateurs à inscrire leurs propres données en les guidant vers le portail d’inscription à la page [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* Obligez les utilisateurs à s’inscrire à la fonction de réinitialisation de mot de passe lors de leur connexion au volet d’accès, à la page [http://account.activedirectory.windowsazure.com/applications/default.aspx](http://myapps.microsoft.com), en définissant l’option de configuration **Demander aux utilisateurs de s’inscrire lorsqu’ils se connectent au volet d’accès** sur **Oui**.

Si vous souhaitez en savoir plus sur les données utilisées par la réinitialisation du mot de passe ainsi que sur la configuration requise pour le formatage de ces données, consultez la rubrique [Données utilisées par la réinitialisation du mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>Pour ajouter des données de contact utilisateur via le portail d’inscription des utilisateurs
1. Pour utiliser le portail d’inscription à la réinitialisation du mot de passe, vous devez fournir aux utilisateurs de votre organisation un lien vers cette page ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) ou activer l’option pour obliger les utilisateurs à s’inscrire automatiquement.  Lorsqu’ils cliquent sur ce lien, ils sont invités à se connecter avec leur compte professionnel.  Après cela, la page suivante s’affiche :

   ![][007]
2. Sur cette page, les utilisateurs peuvent indiquer et vérifier la saisie de leur numéro de téléphone mobile, de leur adresse de messagerie secondaire ou de leurs questions de sécurité.  Voici comment se présente l’écran de vérification d’un numéro de téléphone mobile.

   ![][008]
3. Lorsqu’un utilisateur indique ces informations, la page est mise à jour pour indiquer que les informations sont valides (celles-ci sont masquées ci-dessous).  En cliquant sur les boutons **Terminer** ou **Annuler**, l’utilisateur peut accéder au volet d’accès.

   ![][009]
4. Une fois qu’un utilisateur a vérifié ces informations, son profil est mis à jour avec les données qu’il a fournies.  Dans cet exemple, le numéro **Téléphone professionnel** est indiqué manuellement, l’utilisateur peut alors l’utiliser en tant que méthode de contact pour la réinitialisation de son mot de passe.

   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>Étape 3 : réinitialisation de votre mot de passe Azure AD en tant qu’utilisateur
Maintenant que vous avez configuré une stratégie de réinitialisation utilisateur et indiqué les informations de contact de votre utilisateur, celui-ci peut effectuer une réinitialisation de mot de passe libre-service.

#### <a name="to-perform-a-self-service-password-reset"></a>Pour effectuer une réinitialisation de mot de passe libre-service
1. Si vous accédez à un site comme [**portal.microsoftonline.com**](http://portal.microsoftonline.com), un écran de connexion semblable à celui ci-dessous s’affiche.  Cliquez sur le lien **Votre compte n’est pas accessible ?** pour tester l’interface utilisateur de réinitialisation du mot de passe.

   ![][011]
2. Après avoir cliqué sur **Votre compte n’est pas accessible ?**, vous êtes redirigé vers une nouvelle page, qui vous demande de saisir l’**ID utilisateur** pour lequel vous souhaitez réinitialiser un mot de passe.  Saisissez votre **ID utilisateur** de test ici, passez le test CAPTCHA, puis cliquez sur **Suivant**.

   ![][012]
3. Comme l’utilisateur a renseigné les champs **Téléphone professionnel**, **Téléphone mobile** et **Autre adresse de messagerie**, vous pouvez constater qu’il a pu utiliser toutes ces options pour passer la première vérification.

   ![][013]
4. Dans ce cas, choisissez d’**appeler** le numéro de **téléphone professionnel** en premier.  Notez que lorsque vous sélectionnez une méthode basée sur le téléphone, les utilisateurs doivent **vérifier leur numéro de téléphone** avant de pouvoir réinitialiser leur mot de passe.  Cela permet d’empêcher les personnes malveillantes de contacter de façon indésirable les numéros de téléphone des utilisateurs de votre organisation.

   ![][014]
5. Lorsque l’utilisateur a confirmé son numéro de téléphone, le fait de cliquer sur Appeler entraîne l’affichage d’un compteur et fait sonner son téléphone.  Lorsque l’utilisateur décroche son téléphone, il peut entendre un message lui indiquant qu’il **doit appuyer sur la touche dièse (#)** pour vérifier son compte.  En appuyant sur cette touche, l’utilisateur valide automatiquement la première vérification, puis le système passe à la deuxième vérification.

   ![][015]
6. L’interface utilisateur est alors automatiquement mise à jour : le choix de vérification sélectionné est retiré de la liste.  Dans ce cas, étant donné que vous avez utilisé le champ **Téléphone professionnel** en premier, les options restantes pour la deuxième vérification sont uniquement **Téléphone mobile** et **Autre adresse de messagerie**.  Cliquez sur l’option **Envoyer un courrier électronique sur mon adresse de messagerie secondaire** .  Une fois ce choix effectué, le fait de cliquer sur Courrier électronique déclenche l’envoi d’un courrier électronique sur votre adresse de messagerie secondaire.

   ![][016]
7. Voici un exemple de courrier électronique affiché pour les utilisateurs (notez la marque du client) :

   ![][017]
8. Lorsque le courrier électronique arrive, la page est mise à jour, vous permettant de saisir dans la zone d’entrée le code de vérification contenu dans le courrier électronique, comme illustré ci-dessous.  Lorsque le code approprié est saisi, le bouton Suivant s’éclaire, permettant de passer la deuxième vérification.

   ![][018]
9. Une fois la configuration requise par la stratégie organisationnelle atteinte, vous pouvez choisir un nouveau mot de passe.  Le mot de passe est validé s’il répond aux exigences de mot de passe « fort » d’AAD (consultez la page [Stratégie de mot de passe dans Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)) : un indicateur de force apparaît pour signaler à l’utilisateur que le mot de passe entré répond à cette stratégie ou non.

   ![][019]
10. Lorsque vous avez fourni les mots de passe correspondant à la stratégie organisationnelle, votre mot de passe est réinitialisé et vous pouvez vous connecter immédiatement en l’utilisant.

    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>Activation de la réinitialisation ou de la modification du mot de passe pour les utilisateurs AD
Cette section présente la configuration de la réinitialisation d’un mot de passe pour l’écriture différée de mot de passe dans une version locale d’Active Directory.

* [Configuration requise pour l’écriture différée de mot de passe](#writeback-prerequisites)
* [Étape 1 : téléchargement de la dernière version d’Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
* [Étape 2 : activation et vérification de l’écriture différée des mots de passe dans Azure AD Connect via l’interface utilisateur ou PowerShell](#step-2-enable-password-writeback-in-azure-ad-connect)
* [Étape 3 : configuration de votre pare-feu](#step-3-configure-your-firewall)
* [Étape 4 : configuration des autorisations appropriées](#step-4-set-up-the-appropriate-active-directory-permissions)
* [Étape 5 : réinitialisation de votre mot de passe Active Directory en tant qu’utilisateur, puis vérification de son fonctionnement](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>Configuration requise pour l’écriture différée
Avant de pouvoir activer et utiliser l’écriture différée de mot de passe, vous devez respecter la configuration requise suivante :

* Vous avez un client Azure AD avec Azure AD Premium activé.  Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
* La réinitialisation du mot de passe a été configurée et activée dans votre client.  Pour plus d’informations, consultez la section [Activation de la réinitialisation du mot de passe pour les utilisateurs Azure AD](#enable-users-to-reset-their-azure-ad-passwords)
* Vous avez au moins un compte d’administrateur et un compte d’utilisateur de test avec une licence Azure AD Premium que vous pouvez utiliser pour tester cette fonctionnalité.  Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

  > [!NOTE]
  > Assurez-vous que le compte d’administrateur que vous utilisez pour activer l’écriture différée de mot de passe est un compte d’administrateur cloud (créé dans Azure AD) et non pas un compte fédéré (créé dans un AD local et synchronisé dans Azure AD).
  >
  >
* Vous avez un déploiement local d’AD pour une forêt unique ou des forêts multiples exécutant Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 ou Windows Server 2012 R2 dotés des derniers Service Packs.

  > [!NOTE]
  > Si vous exécutez une version antérieure de Windows Server 2008 ou 2008 R2, vous pouvez toujours utiliser cette fonctionnalité, mais vous devez [télécharger et installer la mise à jour KB 2386717](https://support.microsoft.com/kb/2386717) avant de pouvoir appliquer votre stratégie de mot de passe AD locale dans le cloud.
  >
  >
* Vous avez installé l’outil Azure AD Connect et vous avez préparé votre environnement AD pour la synchronisation dans le cloud.  Pour plus d’informations, consultez la page [Utilisation de votre infrastructure d’identité locale dans le cloud](connect/active-directory-aadconnect.md).

  > [!NOTE]
  > Avant de tester l’écriture différée du mot de passe, assurez-vous d’effectuer une importation complète et une synchronisation complète à partir d’Active Directory et Azure AD dans Azure AD Connect.
  >
  >
* Si vous utilisez Azure AD Sync ou Azure AD Connect, le port sortant **TCP 443** (voire, dans certains cas, les ports **TCP 9350-9354**) doit être ouvert.  Pour plus d’informations, consultez [Étape 3 : configuration de votre pare-feu](#step-3-configure-your-firewall) . L’utilisation de DirSync dans ce scénario n’est plus prise en charge.  Si vous utilisez encore DirSync, veuillez procéder à une mise à niveau vers la dernière version d’Azure AD Connect avant de déployer l’écriture différée du mot de passe.

  > [!NOTE]
  > Nous recommandons fortement à tous les utilisateurs des outils Azure AD Sync ou DirSync de procéder à une mise à niveau vers la dernière version d’Azure AD Connect pour s’assurer de bénéficier des meilleures conditions d’utilisation possibles et des fonctionnalités les plus récentes dès leur publication.
  >
  >

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>Étape 1 : téléchargement de la dernière version d’Azure AD Connect
L’écriture différée de mot de passe est intégrée à Azure AD Connect et à l’outil Azure AD Sync à partir de la version **1.0.0419.0911** .  L’écriture différée de mot de passe avec déverrouillage de compte automatique est intégrée à Azure AD Connect et à l’outil Azure AD Sync à partir de la version **1.0.0485.0222** . Si vous exécutez une version antérieure, veuillez procéder à la mise à niveau vers cette version ou une version ultérieure avant de continuer. [Cliquez ici pour télécharger la dernière version d’Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect).

#### <a name="to-check-the-version-of-azure-ad-sync"></a>Pour vérifier la version d’Azure AD Sync
1. Accédez à **%ProgramFiles%\Azure Active Directory Sync\**.
2. Recherchez l’exécutable **ConfigWizard.exe** .
3. Cliquez avec le bouton droit sur l’exécutable et sélectionnez l’option **Propriétés** dans le menu contextuel.
4. Cliquez sur l’onglet **Détails** .
5. Recherchez le champ **Version du fichier** .

   ![][021]

Si ce numéro de version est supérieur ou égal à **1.0.0419.0911**, ou que vous installez Azure AD Connect, vous pouvez passer à l’[étape 2 : activation et vérification de l’écriture différée de mot de passe dans Azure AD Connect via l’interface utilisateur ou PowerShell](#step-2-enable-password-writeback-in-azure-ad-connect).

> [!NOTE]
> Si vous installez l’outil Azure AD Connect pour la première fois, vous devez suivre quelques recommandations pour préparer votre environnement à la synchronisation d’annuaires.  Avant d’installer l’outil Azure AD Connect, vous devez activer la synchronisation d’annuaires dans le [portail d’administration d’Office 365](https://portal.microsoftonline.com) ou le [portail Azure Classic](https://manage.windowsazure.com).  Pour plus d'informations, consultez la rubrique [Gestion d’Azure AD Connect](active-directory-aadconnect-whats-next.md).
>
>

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>Étape 2 : activation de l’écriture différée de mot de passe dans Azure AD Connect
Lorsque vous avez téléchargé l’outil Azure AD Connect, vous pouvez activer l’écriture différée de mot de passe.  Il existe de méthodes pour le faire.  Vous pouvez activer l’écriture différée de mot de passe dans l’écran de fonctionnalités facultatives de l’Assistant Installation d’Azure AD Connect ou en utilisant Windows PowerShell.

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>Pour activer l’écriture différée de mot de passe dans l’Assistant Configuration
1. Sur votre **ordinateur de synchronisation d’annuaires**, ouvrez l’Assistant Configuration d’**Azure AD Connect**.
2. Cliquez pour passer les étapes jusqu’à atteindre l’écran de configuration **Fonctionnalités facultatives** .
3. Vérifiez l’option **Écriture différée de mot de passe** .

   ![][022]
4. Terminez l’Assistant : la dernière page résume les modifications et inclut la modification de configuration pour l’écriture différée de mot de passe.

> [!NOTE]
> Pour désactiver l’écriture différée de mot de passe, il vous suffit d’exécuter de nouveau cet Assistant et de désélectionner cette fonctionnalité, ou de définir le paramètre **Écriture différée de mot de passe sur le répertoire local** sur **Non** dans la section **Stratégie de réinitialisation du mot de passe utilisateur** de l’onglet **Configurer** de votre annuaire, dans le [portail Azure Classic](https://manage.windowsazure.com).  Pour plus d'informations sur la personnalisation de votre expérience de réinitialisation de mot de passe, consultez [Personnalisation de la gestion de mot de passe Azure AD](active-directory-passwords-customize.md).
>
>

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>Pour activer l’écriture différée de mot de passe en utilisant Windows PowerShell
1. Sur votre **ordinateur de synchronisation d’annuaires**, ouvrez une nouvelle **fenêtre Windows PowerShell avec élévation de privilèges**.
2. Si le module n’est pas déjà chargé, entrez la commande `import-module ADSync` pour charger les applets de commande Azure AD Connect dans votre session active.
3. Obtenez la liste des connecteurs Azure AD dans votre système en exécutant l’applet de commande `Get-ADSyncConnector` et en stockant les résultats dans `$aadConnectorName`, par exemple `$connectors = Get-ADSyncConnector|where-object {$\_.name -like "\*AAD"}`.
4. Obtenez l’état actuel de l’écriture différée pour le connecteur actuel en exécutant l’applet de commande suivante : `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`
5. Activez l’écriture différée de mot de passe en exécutant l’applet de commande : `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`

> [!NOTE]
> Si vous êtes invité à vous identifier, assurez-vous que le compte d’administrateur que vous indiquez pour AzureADCredential est un **compte d’administrateur cloud (créé dans Azure AD)**et non pas un compte fédéré (créé dans un AD local et synchronisé dans Azure AD).
>
> [!NOTE]
> Pour désactiver l’écriture différée de mot de passe via PowerShell, il vous suffit de répéter les instructions précédentes en transmettant `$false` à cette étape ou en définissant le paramètre **Écriture différée de mot de passe sur le répertoire local** sur **Non** dans la section **Stratégie de réinitialisation du mot de passe utilisateur** de l’onglet **Configurer** de votre annuaire, dans le [portail Azure Classic](https://manage.windowsazure.com).
>
>

#### <a name="verify-that-the-configuration-was-successful"></a>Vérification de la réussite de la configuration
Une fois la configuration terminée, un message indiquant la réussite de l’activation de l’écriture différée de mot de passe s’affiche dans la fenêtre Windows PowerShell ou dans l’interface utilisateur de configuration.

Vous pouvez également vérifier que le service a été correctement installé en ouvrant l’observateur d’événements, en accédant au journal d’événements de l’application et en recherchant l’événement **31005 - OnboardingEventSuccess** à partir de la source **PasswordResetService**.

  ![][023]

### <a name="step-3-configure-your-firewall"></a>Étape 3 : configuration de votre pare-feu
Une fois que vous avez activé l’écriture différée du mot de passe, vous devez vous assurer que la machine exécutant Azure AD Connect peut atteindre les services de cloud computing Microsoft pour recevoir les demandes d’écriture différée du mot de passe. Cette étape implique la mise à jour des règles de connexion dans vos appliances réseau (serveurs proxy, pare-feu, etc.) pour autoriser les connexions sortantes à certaines [URL appartenant à Microsoft et adresses IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US) sur des ports réseau spécifiques. Ces modifications peuvent varier selon la version de l’outil Azure AD Connect. Pour approfondir le contexte, vous pouvez lire les articles décrivant le [fonctionnement de l’écriture différée du mot de passe](active-directory-passwords-learn-more.md#how-password-writeback-works) et le [modèle de sécurité de l’écriture différée du mot de passe](active-directory-passwords-learn-more.md#password-writeback-security-model).

#### <a name="why-do-i-need-to-do-this"></a>De quels éléments dois-je disposer pour effectuer cette opération ?

Pour que l’écriture différée du mot de passe fonctionne correctement, la machine exécutant Azure AD Connect doit être en mesure d’établir des connexions HTTPS sortantes à **.servicebus.windows.net* et à une adresse IP spécifique utilisée par Azure, tel que défini dans la [liste des plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Pour la version **1.1.443.0** et les versions ultérieures de l’outil Azure AD Connect :

- La version la plus récente de l’outil Azure AD Connect a besoin d’un accès **HTTPS sortant** à :
    - *passwordreset.microsoftonline.com*
    - *servicebus.windows.net*

Pour les versions **1.0.8667.0** à **1.1.380.0** de l’outil Azure AD Connect :

- **Option 1 :** autorisez toutes les connexions HTTPS sortantes sur le port 443 (utilisant l’URL ou l’adresse IP).
    - Quand utiliser cette option :
        - Utilisez cette option si vous souhaitez mettre en œuvre la configuration la plus simple qui ne nécessite aucune mise à jour en cas de modification des plages d’adresses IP du centre de données Azure au fil du temps.
    - Étapes requises :
        - Autorisez toutes les connexions HTTPS sortantes sur le port 443 utilisant l’URL ou l’adresse IP.
<br><br>
- **Option 2 :** autorisez les connexions HTTPS sortantes à des plages d’adresses IP et URL spécifiques.
    - Quand utiliser cette option :
        - Utilisez cette option si vous travaillez dans un environnement réseau restreint ou que vous ne maîtrisez pas le processus d’autorisation des connexions sortantes.
        - Pour que l’écriture différée du mot de passe continue de fonctionner dans cette configuration, vous devrez vous assurer que vos appliances réseau sont mises à jour chaque semaine avec les dernières adresses IP de la liste des plages d’adresses IP du centre de données Microsoft Azure. Ces plages d’adresses IP sont disponibles sous la forme d’un fichier XML qui est mis à jour tous les mercredis (heure du Pacifique) et mis en œuvre le lundi suivant (heure du Pacifique).
    - Étapes requises :
        - Autorisez toutes les connexions HTTPS sortantes à *.servicebus.windows.net
        - Autorisez toutes les connexions HTTPS sortantes à toutes les adresses IP figurant dans la liste des plages d’adresses IP du centre de données Microsoft Azure et maintenez cette configuration à jour chaque semaine.

> [!NOTE]
> Si vous avez configuré l’écriture différée du mot de passe en suivant les instructions ci-dessus et que le journal des événements Azure AD Connect ne signale aucune erreur, mais que vous obtenez des erreurs de connectivité lors des tests, il est possible qu’une appliance réseau de votre environnement bloque les connexions HTTPS à des adresses IP. Par exemple, alors qu’une connexion à *https://*.servicebus.windows.net* est autorisée, une connexion à une adresse IP spécifique de cette plage peut être bloquée. Pour résoudre ce problème, vous devez soit configurer votre environnement réseau de façon à autoriser toutes les connexions HTTPS sortantes sur le port 443 à n’importe quelle URL ou adresse IP (Option 1 ci-dessus), soit travailler avec votre équipe de mise en réseau pour autoriser explicitement les connexions HTTPS à des adresses IP spécifiques (Option 2 ci-dessus).

**Pour les versions antérieures :**

- Autorisez les connexions TCP sortantes sur les ports 443, 9350-9354 et 5671
- Autorisez les connexions sortantes à *https://ssprsbprodncu-sb.accesscontrol.windows.net/*

> [!NOTE]
> Si vous utilisez une version d’Azure AD Connect antérieure à la version 1.0.8667.0, Microsoft vous recommande vivement de procéder à une mise à niveau vers la [dernière version d’Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594), qui intègre diverses améliorations en matière de mise en réseau de l’écriture différée afin de faciliter la configuration.

Une fois que les appliances réseau ont été configurées, redémarrez la machine exécutant l’outil Azure AD Connect.

#### <a name="idle-connections-on-azure-ad-connect-114430-and-up"></a>Connexion inactives sur Azure AD Connect (version 1.1.443.0 et ultérieure)
L’outil Azure AD Connect envoie des pings/appels keepalives périodiques aux points de terminaison ServiceBus pour garantir que les connexions restent actives. Si l’outil détecte la suppression d’un trop grand nombre de connexions, il augmente automatiquement la fréquence des pings adressés au point de terminaison. L’« intervalle de pings » le plus faible possible est de 1 ping toutes les 60 secondes. Toutefois, **nous recommandons vivement de configurer les proxys/pare-feux pour qu’ils autorisent les connexions inactives pendant au moins 2 à 3 minutes.** \*Pour les versions antérieures, nous suggérons 4 minutes ou plus.

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>Étape 4 : définition des autorisations Active Directory adéquates
Pour chaque forêt contenant des utilisateurs dont les mots de passe doivent être réinitialisés, si X correspond au compte spécifié pour cette forêt dans l’Assistant Configuration (durant l’installation d’origine), X doit avoir les droits étendus **Réinitialiser le mot de passe**, **Modifier le mot de passe** et **Autorisations en écriture** sur `lockoutTime`, et les droits **Autorisations en écriture** sur `pwdLastSet`, sur l’objet racine de chaque domaine dans cette forêt OU sur l’unité d’organisation utilisateur que vous souhaitez faire figurer dans la portée de SSPR.  Vous pouvez utiliser cette dernière option si vous souhaitez étendre vos autorisations de réinitialisation à un ensemble spécifique d’objets utilisateur dans le cas où cela serait impossible sur la racine du domaine. Le droit doit être marqué comme hérité par tous les objets utilisateur.  

Si vous ignorez de quel compte il s’agit exactement, ouvrez l’interface utilisateur de configuration d’Azure Active Directory Connect, puis cliquez sur l’option **Vérification de votre solution** .  Le compte auquel vous devez ajouter des autorisations est souligné en rouge dans la capture d’écran ci-dessous.

**<font color="red">Définissez cette autorisation pour chaque domaine dans chaque forêt de votre système, afin que l’écriture différée du mot de passe fonctionne correctement.</font>**

  ![][032]

  La définition de ces autorisations permet au compte de service de l’agent de gestion de chaque forêt de gérer les mots de passe pour les comptes d’utilisateur de cette forêt. Sans ces autorisations, même si l’écriture différée semblera configurée correctement, les utilisateurs rencontreront des erreurs en essayant de gérer leurs mots de passe locaux à partir du cloud. Voici des instructions détaillées expliquant comment procéder en utilisant le composant logiciel enfichable de gestion **Utilisateurs et ordinateurs Active Directory** :

> [!NOTE]
> La réplication de ces autorisations pour tous les objets peut durer jusqu’à une heure.
>
>

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>Pour configurer les autorisations appropriées pour l’écriture différée
1. Ouvrez **Utilisateurs et ordinateurs Active Directory** avec un compte ayant les autorisations d’administration de domaine appropriées.
2. Dans **Menu Affichage**, assurez-vous que l’option **Fonctionnalités avancées** est activée.
3. Dans le volet gauche, cliquez avec le bouton droit sur l’objet représentant la racine du domaine.
4. Cliquez sur l’onglet **Sécurité** .
5. Puis cliquez sur **Avancé**.

   ![][024]
6. Sous l’onglet **Autorisations**, cliquez sur **Ajouter**.

   ![][025]
7. Sélectionnez le compte auquel vous souhaitez octroyer des autorisations (il s’agit du même compte que celui qui a été spécifié lors de la configuration de la synchronisation de cette forêt).
8. Dans le menu déroulant situé en haut de la fenêtre, sélectionnez **Objets utilisateur descendants**.
9. Dans la boîte de dialogue **Entrée d’autorisation** qui s’affiche, cochez les cases **Réinitialiser le mot de passe**, **Modifier le mot de passe** et **Autorisations en écriture** sur `lockoutTime`, et **Autorisations en écriture** sur `pwdLastSet`.

   ![][026]
   ![][027]
   ![][028]
10. Puis, dans chaque boîte de dialogue qui s’affiche, cliquez sur **Appliquer/Ok** .

### <a name="step-5-reset-your-ad-password-as-a-user"></a>Étape 5 : réinitialisation de votre mot de passe AD en tant qu’utilisateur
Maintenant que l’écriture différée de mot de passe a été activée, vous pouvez tester son fonctionnement en réinitialisant le mot de passe d’un utilisateur dont le compte a été synchronisé dans votre client cloud.

#### <a name="to-verify-password-writeback-is-working-properly"></a>Pour vérifier le bon fonctionnement de l’écriture différée de mot de passe
1. Accédez à [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) ou dans n’importe quel écran de connexion d’ID d’organisation, cliquez sur le lien **Votre compte n’est pas accessible ?** .

   ![][029]
2. Une page s’affiche, vous demandant un ID utilisateur pour lequel réinitialiser le mot de passe. Entrez votre ID utilisateur de test et suivez les étapes du processus de réinitialisation de mot de passe.
3. Après avoir réinitialisé votre mot de passe, vous verrez un écran qui ressemble à ceci. Cela signifie que vous avez correctement réinitialisé votre mot de passe vos annuaires locaux/dans le cloud.

   ![][030]
4. Pour vérifier la réussite de l’opération ou diagnostiquer des erreurs, accédez à votre **ordinateur de synchronisation d’annuaires**, ouvrez l’**Observateur d’événements**, accédez au **journal des événements de l’application** et recherchez l’événement **31002 - PasswordResetSuccess** à partir de la source **PasswordResetService** pour votre utilisateur de test.

   ![][031]



## <a name="next-steps"></a>Étapes suivantes
Voici les liens vers toutes les pages de la documentation sur la réinitialisation de mot de passe Azure AD :

* **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password).
* [**Fonctionnement**](active-directory-passwords-how-it-works.md) : découvrez les six différents composants du service et la fonction de chacun d’eux.
* [**Personnalisation**](active-directory-passwords-customize.md) : découvrez comment personnaliser l’apparence et le comportement du service en fonction des besoins de votre organisation.
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) : découvrez comment déployer et gérer rapidement et efficacement les mots de passe de votre organisation.
* [**Obtention d’informations**](active-directory-passwords-get-insights.md) : découvrez nos fonctionnalités intégrées de création de rapports.
* [**Forum Aux Questions**](active-directory-passwords-faq.md) : obtenez des réponses aux questions fréquemment posées.
* [**Dépannage**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre rapidement les problèmes liés au service.
* [**En savoir plus**](active-directory-passwords-learn-more.md) : découvrez les détails techniques sur le fonctionnement du service.

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

