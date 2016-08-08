<properties
	pageTitle="Réinitialisation de mot de passe Azure AD | Microsoft Azure"
	description="Description des fonctionnalités de gestion des mots de passe dans Azure AD, y compris la réinitialisation, la modification, les rapports de gestion et l’écriture différée vers votre annuaire Active Directory local."
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="asteen"/>


# Réinitialisation du mot de passe Azure AD pour les utilisateurs et les administrateurs

  >[AZURE.IMPORTANT] Êtes-vous ici parce que vous souhaitez réinitialiser votre mot de passe Azure ou O365 ? Dans ce cas, veuillez [Ignorer cette section](#users-how-to-manage-your-own-password).

Le libre-service a toujours été l’objectif principal des services informatiques du monde entier dans le but de réduire les coûts et de faciliter le travail. En effet, le marché est inondé de produits qui vous permettent de gérer vos groupes locaux, mots de passe ou profils utilisateur dans le cloud ou localement. Azure AD se différencie de ces offres en fournissant des fonctionnalités libre-service performantes et faciles à utiliser.

La **gestion des mots de passe Azure AD** est un ensemble de fonctionnalités qui permettent à vos utilisateurs de gérer les mots de passe à partir de n'importe quel appareil, à tout moment, en tout lieu, tout en respectant les stratégies de sécurité que vous définissez.

##UTILISATEURS : comment gérer votre mot de passe
Si vous êtes un utilisateur (pas un administrateur) dans une organisation qui utilise Office 365 ou des comptes Microsoft pour accéder aux ressources de travail, cliquez sur les liens ci-dessous pour savoir comment résoudre les problèmes courants avec votre mot de passe.

| Rubrique | |
| --------- | --------- |
| Je souhaite m'inscrire pour réinitialiser mon mot de passe | [Comment s'inscrire pour réinitialiser son mot de passe](active-directory-passwords-update-your-own-password.md#how-to-register-for-password-reset) |
| Je souhaite modifier mon mot de passe à partir d'O365 | [Comment modifier votre mot de passe à partir d'Office 365](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-o365) |
| Je souhaite modifier mon mot de passe à partir de myapps.microsoft.com | [Comment modifier votre mot de passe à partir du panneau d'accès](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-the-access-panel) |
| J'ai oublié mon mot de passe et je souhaite le réinitialiser | [Comment réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password) |
| Connexion impossible. Je souhaite déverrouiller mon compte. | [Comment déverrouiller votre compte local](active-directory-passwords-update-your-own-password.md#how-to-unlock-your-account) |
| Je veux vous aider à résoudre un problème suite à une réinitialisation de mot de passe ayant échoué. | [Problèmes courants et leurs solutions.](active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions) |

##ADMINISTRATEURS : découvrez comment commencer la réinitialisation d'un mot de passe Azure AD
Si vous êtes un administrateur qui souhaite activer la réinitialisation du mot de passe Azure AD, ou simplement en savoir plus sur elle, démarrez avec les liens ci-dessous pour accéder à ce qui vous intéresse.

| Rubrique | |
| --------- | --------- |
| Scénarios pris en charge | [Quelles sont les possibilités offertes par la réinitialisation de mot de passe Azure AD ?](#what-is-possible-with-azure-ad-password-reset) |
| Pourquoi l'utiliser ? | [Pourquoi utiliser la réinitialisation du mot de passe Azure AD ?](#why-use-azure-ad-password-reset) |
| Tarification et disponibilité | [Tarification et disponibilité](#pricing-and-availability) |
| Activer la réinitialisation du mot de passe | [Activer le mot de passe réinitialisé pour vos utilisateurs](#enable-password-reset-for-your-users) |
| Personnaliser le mode de fonctionnement | [Personnaliser le comportement de réinitialisation du mot de passe](#customize-password-reset-behavior) |
| Déployer à mes utilisateurs | [Configurer vos utilisateurs pour utiliser la réinitialisation du mot de passe](#configure-your-users-to-use-password-reset) |
| Afficher des rapports | [Afficher l'activité de réinitialisation du mot de passe avec des rapports intégrés](#view-password-reset-activity-with-integrated-reports) |
| Réinitialiser le mot de passe de l’utilisateur | [Gérer les mots de passe de vos utilisateurs](#manage-your-users-passwords) |
| Définir les stratégies de mot de passe de mon organisation | [Définir des stratégies de mot de passe](#set-password-policies) |
| Résoudre un problème | [Résoudre un problème](#troubleshoot-a-problem) |
| Forum Aux Questions | [Lire un Forum Aux Questions](#read-a-faq) |
| Détails techniques | [Comprendre les détails techniques](#understand-the-technical-details) |
| Nouvelles fonctionnalités | [Mises à jour de service récentes](#recent-service-updates) |
| Liens vers d'autres documents | [Liens vers la documentation de réinitialisation du mot de passe](#links-to-password-reset-documentation) |

### Quelles sont les possibilités offertes par la réinitialisation de mot de passe Azure AD ?
Voici quelques opérations que vous pouvez effectuer avec les fonctionnalités de gestion des mots de passe Azure AD.

- La **modification des mots de passe libre-service** permet aux utilisateurs finaux ou aux administrateurs de modifier leurs mots de passe expirés ou non expirés sans appeler un administrateur ou le support technique pour obtenir de l’aide.
- La **réinitialisation du mot de passe libre-service** permet aux utilisateurs finaux ou aux administrateurs de réinitialiser leurs mots de passe automatiquement sans appeler un administrateur ou le support technique pour obtenir de l’aide. La réinitialisation du mot de passe libre-service nécessite Azure AD Premium ou Basic. Pour plus d’informations, consultez la page Éditions d’Azure Active Directory.
- La **réinitialisation du mot de passe initiée par l’administrateur** permet à un administrateur de réinitialiser le mot de passe d’un utilisateur final ou d’un autre administrateur depuis le [portail de gestion Azure](https://manage.windowsazure.com).
- Les **rapports d'activité de gestion des mots de passe** donnent aux administrateurs un aperçu de l’activité d’inscription et de réinitialisation des mots de passe dans leur organisation.
- L’**écriture différée de mot de passe** permet la gestion des mots de passe locaux depuis le cloud afin que tous les scénarios mentionnés ci-dessus puissent être exécutés par ou pour des utilisateurs fédérés ou synchronisés par mot de passe. L'écriture différée de mot de passe nécessite Azure AD Premium. Pour plus d'informations, consultez la page Prise en main d’Azure AD Premium.

### Pourquoi utiliser la réinitialisation du mot de passe Azure AD ?
Voici quelques raisons pour lesquelles vous devez utiliser les fonctionnalités de gestion des mots de passe Azure AD.

- **Réduction des coûts** : la réinitialisation de mot de passe aidée par un support technique représente généralement 20 % des dépenses informatiques de l’organisation.
- **Amélioration des expériences utilisateur** : les utilisateurs ne veulent pas appeler le support technique et passer une heure au téléphone chaque fois qu'ils oublient leur mot de passe.
- **Réduction de la charge de travail du support technique** : la gestion des mots de passe est l’axe stratégique du support technique de la plupart des organisations.
- **Activation de la mobilité** : les utilisateurs peuvent réinitialiser leur mot de passe où qu’ils soient.

### Tarification et disponibilité
La réinitialisation du mot de passe Azure AD est disponible dans 3 niveaux, selon l'abonnement que vous avez :

- **Azure AD gratuit** - les administrateurs chargés uniquement du cloud peuvent réinitialiser leurs mots de passe.
- **Azure AD de base ou n'importe quel abonnement O365 payé** - les clients utilisant uniquement le cloud et les administrateurs chargés uniquement du cloud peuvent réinitialiser leurs mots de passe.
- **Azure AD Premium** - n'importe quel utilisateur ou administrateur, y compris les clients utilisant uniquement le cloud et les utilisateurs fédérés ou dont le mot de passe est synchronisé peuvent réinitialiser leurs mots de passe (nécessite [l'activation de l’écriture différée du mot de passe](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords))

Pour plus d'informations sur la tarification de base ou sur Azure AD Premium, visitez la page [Détails de tarification Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

##Activer le mot de passe réinitialisé pour vos utilisateurs
| Rubrique | |
| --------- | --------- |
| Comment activer la réinitialisation du mot de passe pour les utilisateurs du cloud ? | [Permettre aux utilisateurs de réinitialiser leurs mots de passe Active Directory Azure pour le cloud](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) |
| Comment activer la réinitialisation du mot de passe et modifier des utilisateurs locaux ? | [Permettre aux utilisateurs de réinitialiser leurs mots de passe Active Directory Azure locaux](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) |
| Comment appliquer la réinitialisation de mot de passe à un ensemble spécifique d'utilisateurs ? | [Restreindre la réinitialisation de mot de passe à certains utilisateurs](active-directory-passwords-customize.md#restrict-access-to-password-reset) |
| Comment tester la réinitialisation de mot de passe de cloud computing ? | [Réinitialiser votre mot de passe Azure AD en tant qu’utilisateur](active-directory-passwords-getting-started.md#step-3-reset-your-azure-ad-password-as-a-user) |
| Comment tester la réinitialisation de mot de passe local ? | [Réinitialiser votre mot de passe AD local en tant qu’utilisateur.](active-directory-passwords-getting-started.md#step-5-reset-your-ad-password-as-a-user) |
| Comment désactiver ultérieurement la réinitialisation du mot de passe ? | [Paramètre : utilisateurs autorisés à réinitialiser leur mot de passe](active-directory-passwords-customize.md#users-enabled-for-password-reset) |


##Personnaliser le comportement de réinitialisation du mot de passe
| Rubrique | |
| --------- | --------- |
| Comment modifier la liste des méthodes d'authentification prises en charge ? | [Paramètre : méthodes d'authentification accessibles aux utilisateurs](active-directory-passwords-customize.md#authentication-methods-available-to-users) |
| Comment modifier le nombre de méthodes d’authentification requis ? | [Paramètre : nombre de méthodes d’authentification requis](active-directory-passwords-customize.md#number-of-authentication-methods-required) |
| Comment configurer les questions de sécurité personnalisées ? | [Paramétrage : questions de sécurité personnalisées](active-directory-passwords-customize.md#custom-security-questions) |
| Comment définir des questions de sécurité localisées prédéfinies ? | [Paramètre : questions de sécurité basées sur des informations](active-directory-passwords-customize.md#knowledge-based-security-questions) |
| Comment modifier le nombre de questions de sécurité requis ? | [Paramètre : nombre de questions de sécurité pour l’enregistrement ou la réinitialisation](active-directory-passwords-customize.md#number-of-questions-required-to-register) |
| Comment personnaliser la façon dont un utilisateur contacte un administrateur ? | [Paramètre : personnaliser le lien « Contactez votre administrateur »](active-directory-passwords-customize.md#customize-the-contact-your-administrator-link) |
| Comment autoriser les utilisateurs à déverrouiller des comptes Active Directory sans réinitialiser un mot de passe ? | [Paramètre : autoriser les utilisateurs à déverrouiller leurs comptes AD sans réinitialiser un mot de passe](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password) |
| Comment activer les notifications de réinitialisation des mots de passe pour les utilisateurs ? | [Paramètre : avertir les utilisateurs quand leurs mots de passe ont été réinitialisés](active-directory-passwords-customize.md#notify-users-and-admins-when-their-own-password-has-been-reset) |
| Comment activer les notifications de réinitialisation de mot de passe pour les administrateurs ? | [Paramètre : avertir les autres administrateurs quand un administrateur réinitialise leur mot de passe](active-directory-passwords-customize.md#notify-admins-when-other-admins-reset-their-own-passwords) |
| Comment personnaliser l’apparence de la réinitialisation de mot de passe ? | [Paramètre : nom, marque et logo de la société](active-directory-passwords-customize.md#password-management-look-and-feel) |


##Configurer vos utilisateurs pour utiliser la réinitialisation de mot de passe
| Rubrique | |
| --------- | --------- |
| Comment savoir si un compte est configuré pour la réinitialisation du mot de passe ? | [Quels sont les critères de configuration d'un compte pour la réinitialisation du mot de passe ?](active-directory-passwords-best-practices.md#what-makes-an-account-configured) |
| Comment configurer mes utilisateurs pour la réinitialisation de mot de passe ? | [Manières de remplir les données d'authentification de réinitialisation de mot de passe pour vos utilisateurs](active-directory-passwords-best-practices.md#ways-to-populate-authentication-data) |
| Comment télécharger manuellement des données pour mes utilisateurs ? | [Télécharger vous-même les données de réinitialisation de mot de passe](active-directory-passwords-best-practices.md#uploading-data-yourself) |
| Comment utiliser PowerShell pour lire ou définir des données pour mes utilisateurs ? | [Comment accéder aux données de réinitialisation des mots de passe pour vos utilisateurs](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) |
| Comment synchroniser localement les données de réinitialisation de mot de passe ? | [Données utilisées par la réinitialisation de mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |
| Comment utiliser une campagne par courrier électronique pour amener mes utilisateurs à s’inscrire à la réinitialisation du mot de passe et à l’utiliser ? | [Déploiement de réinitialisation de mot de passe par courrier électronique](active-directory-passwords-best-practices.md#email-based-rollout) |
| Comment obliger mes utilisateurs à s’inscrire lorsqu’ils se connectent ? | [Inscription obligatoire lors de la réinitialisation de mot de passe](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) |
| Comment obliger mes utilisateurs à confirmer périodiquement qu'ils sont bien inscrits ? | [Paramètre : nombre de jours avant que les utilisateurs ne doivent confirmer leurs données d’authentification](active-directory-passwords-customize.md#number-of-days-before-users-must-confirm-their-contact-data) |
| Quelles sont les meilleures pratiques concernant la communication du mot de passe réinitialisé aux utilisateurs finaux ? | [Création de votre propre portail de mot de passe destiné à vos utilisateurs](active-directory-passwords-best-practices.md#creating-your-own-password-portal) |


##Afficher l'activité de réinitialisation du mot de passe avec des rapports intégrés
| Rubrique | |
| --------- | --------- |
| Où puis-je pour voir les rapports de réinitialisation de mots de passe ? | [Vue d’ensemble des rapports de gestion des mots de passe](active-directory-passwords-get-insights.md#overview-of-password-management-reports) |
| Où puis-je voir comment les utilisateurs utilisent un mot de passe réinitialisé dans mon organisation ? | [Visualiser l’activité de réinitialisation de mot de passe dans votre organisation](active-directory-passwords-get-insights.md#view-password-reset-activity) |
| Où puis-je voir combien d'utilisateurs s’inscrivent et à quoi ? | [Afficher l'activité d'inscription à la réinitialisation de mot de passe](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) |
| Comment obtenir des rapports de réinitialisation de mot de passe à partir d’une API ? | [Création d'une application Azure AD pour accéder à l'API de rapport](active-directory-reporting-api-getting-started.md#creating-an-azure-ad-application-to-access-the-api) |
| Quel type d’informations de rapport sur la réinitialisation de mot de passe fournit une API ? | [Événements de réinitialisation de mot de passe et d'enregistrement disponibles dans l'API de rapport](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview#SsprActivityEvent) |


##Gérer les mots de passe de vos utilisateurs
| Rubrique | |
| --------- | --------- |
| Comment réinitialiser le mot de passe d'un utilisateur à partir du portail de gestion O365 ? | [Réinitialiser le mot de passe d'un utilisateur dans Office 365](https://support.office.com/article/Reset-a-user-s-password-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C) |
| Comment réinitialiser le mot de passe d'un utilisateur à l'aide de PowerShell ? | [Réinitialiser le mot de passe d'un utilisateur avec Set-MsolUserPassword](https://msdn.microsoft.com/library/azure/dn194140.aspx) |


##Définir des stratégies de mot de passe
| Rubrique | |
| --------- | --------- |
| Comment définir la stratégie d'expiration du mot de passe d'une organisation à partir d'Office 365 ? | [Définir la stratégie d’expiration de mot de passe](https://support.office.com/article/Set-a-user-s-password-expiration-policy-0f54736f-eb22-414c-8273-498a0918678f) |
| Comment définir les mots de passe d'un utilisateur spécifique avec PowerShell pour qu'il n'expire jamais ? | [Définir le mot de passe d'un utilisateur individuel avec PowerShell pour qu'il n'expire jamais](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466) |
| Comment savoir si le mot de passe d'un utilisateur est défini avec PowerShell pour ne jamais expirer | [Vérifier l'état d'expiration du mot de passe d'un utilisateur individuel avec PowerShell](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466#__toc378845827) |


##Résoudre un problème
| Rubrique | |
| --------- | --------- |
| Quelles informations dois-je fournir au support si j'ai besoin d'aide ? | [Informations à inclure lorsque vous avez besoin d’aide](active-directory-passwords-troubleshoot.md#information-to-include-when-you-need-help) |
| Comment résoudre un problème de réinitialisation de mot de passe ? | [Résolution des problèmes du portail de réinitialisation du mot de passe](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-portal) |
| Comment puis-je résoudre un problème d'écriture différée du mot de passe ? | [Résoudre les problèmes d’écriture différée du mot de passe](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Comment résoudre un problème de connectivité d'écriture différée du mot de passe ? | [Résoudre des problèmes de connectivité d’écriture différée du mot de passe](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) |
| Comment résoudre un problème de configuration de réinitialisation du mot de passe ? | [Résoudre un problème de configuration de réinitialisation de mot de passe dans le portail de gestion Azure](active-directory-passwords-troubleshoot.md#troubleshoot-password-reset-configuration-in-the-azure-management-portal) |
| Comment résoudre un problème de rapports sur une réinitialisation de mot de passe ? | [Résolution des problèmes de rapports de gestion de mot de passe dans le portail de gestion Azure](active-directory-passwords-troubleshoot.md#troubleshoot-password-management-reports-in-the-azure-management-portal) |
| Comment résoudre un problème d'inscription à la réinitialisation de mot de passe ? | [Résolution des problèmes du portail d’inscription à la réinitialisation de mot de passe](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-registration-portal) |
| Codes d’erreur du journal des événements Écriture différée de mot de passe | [Codes d’erreur du journal des événements Écriture différée de mot de passe](active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes) |


##Lire un Forum Aux Questions
| Rubrique | |
| --------- | --------- |
| Je souhaite lire un Forum aux Questions relatif à l’inscription à la réinitialisation du mot de passe | [Forum aux Questions relatif à l’inscription à la réinitialisation du mot de passe](active-directory-passwords-faq.md#password-reset-registration) |
| Je souhaite lire un Forum aux Questions relatif à la réinitialisation du mot de passe | [ Forum aux Questions relatif à la réinitialisation de mot de passe](active-directory-passwords-faq.md#password-reset) |
| Je souhaite lire un Forum aux Questions relatif aux rapports de réinitialisation du mot de passe | [Forum aux Questions relatif aux rapports sur la gestion des mots de passe
](active-directory-passwords-faq.md#password-management-reports) |
| Je souhaite lire un Forum aux Questions relatif à l'écriture différée du mot de passe | [Forum aux Questions relatif à l'écriture différée du mot de passe](active-directory-passwords-faq.md#password-writeback) |


##Comprendre les détails techniques

| Rubrique | |
| --------- | --------- |
| Je souhaite en savoir plus sur l'écriture différée du mot de passe | [Vue d’ensemble de l’écriture différée de mot de passe](active-directory-passwords-learn-more.md#password-writeback-overview) |
| Je souhaite en savoir plus sur le fonctionnement de l'écriture différée du mot de passe | [Comment fonctionne l’écriture différée du mot de passe ?](active-directory-passwords-learn-more.md#how-password-writeback-works) |
| Je souhaite en savoir plus sur les scénarios sont pris en charge par l'écriture différée du mot de passe | [Scénarios pris en charge par l’écriture différée de mot de passe](active-directory-passwords-learn-more.md#scenarios-supported-for-password-writeback) |
| Je souhaite en savoir plus sur la sécurisation de l'écriture différée du mot de passe | [Modèle de sécurité de l’écriture différée de mot de passe](active-directory-passwords-learn-more.md#password-writeback-security-model) |
| Je souhaite en savoir plus sur le fonctionnement du portail de réinitialisation du mot de passe | [Fonctionnement du portail de réinitialisation de mot de passe](active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work) |
| Je souhaite en savoir plus sur les données utilisées par la réinitialisation de mot de passe | [Données utilisées par la réinitialisation de mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |

## Mises à jour de service récentes

####Application de l’inscription de réinitialisation de mot de passe lors de la connexion aux applications Office 365 - novembre 2015

- Maintenant, après avoir activé la fonctionnalité [inscription appliquée](active-directory-passwords-customize.md#require-users-to-register-when-signing-in), vos utilisateurs seront appelés à s'inscrire où qu'ils se trouvent, avec un compte professionnel ou scolaire. Cela augmente considérablement la vitesse à laquelle de nombreuses organisations peuvent s'intégrer à la réinitialisation de mot de passe. Grâce à cette nouvelle fonctionnalité, nous avons vu de grandes organisations s'intégrer en 2 semaines seulement !

####Prise en charge du déverrouillage des comptes Active Directory sans réinitialisation dé mot de passe - novembre 2015

- Déverrouiller uniquement (sans réinitialiser) est actuellement une des principales occupations du support technique. De fait, de nombreuses organisations consacrent jusqu'à 70 % de leur budget de réinitialisation de mot de passe à déverrouiller des comptes. Pour répondre à cette demande, la réinitialisation de mot de passe Azure AD vous permet maintenant d'activer une fonctionnalité qui permettra à vos utilisateurs de déverrouiller des comptes AD séparément de la réinitialisation de mot de passe. Découvrez comment l'activer ici : [Paramètre : autoriser les utilisateurs à déverrouiller leurs comptes AD sans réinitialiser un mot de passe](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password)

####Mises à jour de la convivialité de la page d’inscription - octobre 2015

- Désormais, lorsqu'un utilisateur dispose déjà de données inscrites, il lui suffit de cliquer sur « looks good » pour mettre à jour les données sans avoir à renvoyer l’e-mail ou à passer un nouveau coup de fil.

####Fiabilité améliorée de l'écriture différée du mot de passe - septembre 2015

- Depuis la version de septembre d’Azure AD Connect, l'agent d'écriture différée du mot de passe tente d’établir de façon plus agressive de nouvelles connexions et propose des fonctionnalités de basculement supplémentaires plus robustes.

####API pour la récupération des données des rapports sur la réinitialisation des mots de passe - août 2015

- Désormais, les données des rapports de réinitialisation de mot de passe peuvent être récupérées directement à partir des [rapports Azure AD et des API d'événements](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

####Prise en charge de la réinitialisation de mot de passe Azure AD pendant la jonction de domaine de cloud - août 2015

- À présent, chaque utilisateur de cloud peut réinitialiser son mot de passe directement depuis l’écran de connexion de Windows 10 lors de l’étape de jonction de domaine. Notez que cette fonctionnalité n’a pas encore été intégrée à l'écran de connexion Windows 10.

####Appliquer l’inscription de réinitialisation de mot de passe lors de la connexion à Azure et à des applications fédérées - juillet 2015

- En plus de forcer l'inscription lors de la connexion à myapps.microsoft.com, nous prenons maintenant en charge l'inscription lors des connexions au portail de gestion Azure et à n’importe laquelle de vos applications fédérées à authentification unique

####Prise en charge de la localisation de la question de sécurité - mai 2015

- À présent, vous avez la possibilité de sélectionner des questions de sécurité prédéfinies localisées en langage O365 intégral lors de la configuration des questions de sécurité pour la réinitialisation de mot de passe.

####Prise en charge du déverrouillage de compte lors de la réinitialisation de mot de passe - juin 2015

- Si vous utilisez l'écriture différée du mot de passe et que vous réinitialisez votre mot de passe lorsque votre compte est verrouillé, nous déverrouillerons automatiquement votre compte Active Directory !

####Inscription SSPR personnalisée - avril 2015

- La page d’inscription à la réinitialisation des mots de passe est désormais personnalisée avec le logo de votre entreprise.

####Questions de sécurité - mars 2015

- Les questions de sécurité sont désormais mises à la disponibilité générale.

####Déverrouillage de compte - Mars 2015

- Les utilisateurs peuvent désormais déverrouiller leurs comptes lors de la réinitialisation du mot de passe.

## Bientôt disponible

Vous trouverez ci-dessous certaines des remarquables fonctionnalités que nous développons actuellement.

**Prise en charge du rappel invitant les utilisateurs à mettre à jour leurs données enregistrées lors de la connexion** - en cours

- Nous pouvons aujourd’hui rappeler aux utilisateurs de mettre à jour leurs données enregistrées lors de l'accès à myapps.microsoft.com, et nous prévoyons d’étendre cette fonctionnalité à toutes les authentifications.

## Liens vers la documentation de réinitialisation du mot de passe
Voici les liens vers toutes les pages de la documentation sur la réinitialisation de mot de passe Azure AD :

* **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Fonctionnement**](active-directory-passwords-how-it-works.md) : découvrez les six différents composants du service et la fonction de chacun d’eux.
* [**Prise en main**](active-directory-passwords-getting-started.md) : découvrez comment permettre à vos utilisateurs de réinitialiser et de modifier leurs mots de passe dans le cloud et localement.
* [**Personnalisation**](active-directory-passwords-customize.md) : découvrez comment personnaliser l’apparence et le comportement du service en fonction des besoins de votre organisation.
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) : découvrez comment déployer et gérer rapidement et efficacement les mots de passe de votre organisation.
* [**Obtention d’informations**](active-directory-passwords-get-insights.md) : découvrez nos fonctionnalités intégrées de création de rapports.
* [**FAQ**](active-directory-passwords-faq.md) : obtenez des réponses aux questions fréquemment posées.
* [**Dépannage**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre rapidement les problèmes liés au service.
* [**En savoir plus**](active-directory-passwords-learn-more.md) : découvrez les détails techniques sur le fonctionnement du service.

<!---HONumber=AcomDC_0727_2016-->