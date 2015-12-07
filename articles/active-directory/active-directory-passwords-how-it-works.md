<properties 
	pageTitle="Fonctionnement de la gestion des mots de passe Azure AD | Microsoft Azure" 
	description="Découvrez les différents composants de la gestion des mots de passe Azure AD, y compris l’emplacement où les utilisateurs s’inscrivent, réinitialisent et modifient leurs mots de passe et celui où les administrateurs configurent, créent des rapports et activent la gestion des mots de passe Active Directory locaux." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="asteen"/>

# Fonctionnement de la gestion des mots de passe
La gestion des mots de passe dans Azure Active Directory se compose de plusieurs composants logiques qui sont décrits ci-dessous. Cliquez sur chaque lien pour en savoir plus sur le composant en question.

- [**Portail de configuration de la gestion des mots de passe**](#password-management-configuration-portal) : les administrateurs peuvent contrôler différents aspects de la gestion des mots de passe dans leurs clients en accédant à l’onglet Configurer de leur annuaire dans le [portail de gestion Azure](https://manage.windowsazure.com).
- [**Portail d’inscription des utilisateurs**](#user-registration-portal) : les utilisateurs peuvent s’inscrire à la réinitialisation de mot de passe via ce portail web.
- [**Portail de réinitialisation de mot de passe utilisateur**](#user-password-reset-portal) : les utilisateurs peuvent réinitialiser leur mot de passe à l’aide d’un certains nombre de vérifications selon la stratégie de réinitialisation de mot de passe contrôlée par l’administrateur.
- [**Portail de modification de mot de passe utilisateur**](#user-password-change-portal) : les utilisateurs peuvent modifier leur mot de passe à tout moment en entrant leur ancien mot de passe et en sélectionnant un nouveau mot de passe à l’aide de ce portail web.
- [**Rapports sur la gestion des mots de passe**](#password-management-reports) : les administrateurs peuvent afficher et analyser l’activité d’inscription et de réinitialisation de mot de passe dans leur client en accédant à la section « Rapports d’activité » de l’onglet « Rapports » de leur annuaire dans le [portail de gestion Azure](https://manage.windowsazure.com).
- [**Composant d’écriture différée de mot de passe de Azure AD Connect**](#password-writeback-component-of-azure-ad-connect) : les administrateurs peuvent éventuellement activer la fonctionnalité d’écriture différée de mot de passe lors de l’installation d’Azure AD Connect pour activer la gestion des mots de passe fédérés ou synchronisés par mot de passe à partir du cloud.

## Portail de configuration de la gestion des mots de passe
Vous pouvez configurer des stratégies de gestion des mots de passe pour un annuaire spécifique à l’aide du [portail de gestion Azure](https://manage.windowsazure.com) en accédant à la section **Stratégie de réinitialisation du mot de passe utilisateur** de l’onglet **Configurer** de l’annuaire. À partir de cette page de configuration, vous pouvez contrôler de nombreux aspects de la gestion des mots de passe dans votre organisation, notamment :

- Activation et désactivation de la réinitialisation de mot de passe pour tous les utilisateurs d’un annuaire
- Définition du nombre de vérifications (une ou deux) qu’un utilisateur doit réussir pour réinitialiser son mot de passe
- Définition des types spécifiques de vérifications que vous souhaitez activer pour les utilisateurs de votre organisation dans la liste ci-dessous :
 - Téléphone mobile (code de vérification par SMS ou appel vocal)
 - Téléphone professionnel (appel vocal)
 - Adresse de messagerie de secours (code de vérification par courrier électronique)
 - Questions de sécurité (authentification basée sur des informations)
- Définition du nombre de questions qu’un utilisateur doit indiquer pour utiliser la méthode d’authentification via des questions de sécurité (visible uniquement si les questions de sécurité sont activées)
- Définition du nombre de questions qu’un utilisateur doit fournir lors de la réinitialisation pour utiliser la méthode d’authentification via des questions de sécurité (visible uniquement si les questions de sécurité sont activées)
- Utilisation de questions de sécurité localisées préconçues, sur lesquelles peut s’appuyer un utilisateur pour s’inscrire à la réinitialisation de mot de passe (visible uniquement si les questions de sécurité sont activées)
- Définition de questions de sécurité personnalisées, sur lesquelles peut s’appuyer un utilisateur pour s’inscrire à la réinitialisation de mot de passe (visible uniquement si les questions de sécurité sont activées)
- Demander aux utilisateurs de s’inscrire à la réinitialisation de mot de passe lorsqu’ils accèdent au volet d’accès à l’application à la page [http://myapps.microsoft.com](http://myapps.microsoft.com)
- Demander aux utilisateurs de reconfirmer leurs données précédemment inscrites après un nombre configurable de jours (visible uniquement si l’inscription forcée est activée)
- Mise à disposition d’un courrier électronique de support technique personnalisé ou d’une URL qui sera affichée aux utilisateurs en cas de problème lors de la réinitialisation de leurs mots de passe
- Activation ou désactivation de la fonctionnalité d’écriture différée de mot de passe (lorsque celle-ci a été déployée à l’aide d’AAD Connect)
- Affichage de l’état de l’agent d’écriture différée de mot de passe (lorsque l’écriture différée a été déployée à l’aide d’AAD Connect)
- Activation des notifications par courrier électronique aux utilisateurs lorsque leur propre mot de passe a été réinitialisé (disponible dans la section **Notifications** du [portail de gestion Azure](https://manage.windowsazure.com))
- Activation des notifications par courrier électronique aux administrateurs lorsque d’autres administrateurs réinitialisent leur propre mot de passe (disponible dans la section **Notifications** du [portail de gestion Azure](https://manage.windowsazure.com))
- Personnalisation du portail et des courriers électroniques de réinitialisation de mot de passe avec le logo et le nom de votre organisation à l’aide de la fonctionnalité de personnalisation de la marque du client (disponible dans la section **Propriétés de l’annuaire** du [portail de gestion Azure](https://manage.windowsazure.com))

Pour en savoir plus sur la configuration de la gestion des mots de passe de votre organisation, consultez la page [Prise en main de la gestion des mots de passe Azure AD](active-directory-passwords-getting-started.md).

##Portail d’inscription des utilisateurs
Avant que les utilisateurs puissent faire appel à la réinitialisation de mot de passe, leurs comptes d’utilisateur cloud doivent être actualisés avec les données d’authentification correctes pour s’assurer qu’ils puissent répondre au nombre adéquat de vérifications définies par leur administrateur. Les administrateurs peuvent également définir ces informations d’authentification au nom de l’utilisateur à l’aide du portail web Azure ou Office, de DirSync/Azure AD Connect ou de Windows PowerShell.

Toutefois, si vous préférez que vos utilisateurs inscrivent leurs propres données, nous fournissons également une page web à laquelle les utilisateurs peuvent accéder pour fournir ces informations. Cette page permet aux utilisateurs de spécifier les informations d’authentification selon les stratégies de réinitialisation de mot de passe activées dans leur organisation. Une fois ces données vérifiées, elles sont stockées dans le compte d’utilisateur cloud à utiliser pour la récupération du compte ultérieurement. Voici à quoi ressemble le portail d’inscription :

  ![][001]

Pour plus d’informations, consultez les pages [Prise en main de la gestion des mots de passe Azure AD](active-directory-passwords-getting-started.md) et [Meilleures pratiques de gestion des mots de passe Azure AD](active-directory-passwords-best-practices.md).

##Portail de réinitialisation des mots de passe des utilisateurs
Une fois que vous avez activé la réinitialisation de mot de passe libre-service, configuré la stratégie de réinitialisation de mot de passe libre-service de votre organisation et vérifié que les utilisateurs disposent des données de contact appropriées dans l’annuaire, les utilisateurs de votre organisation sont en mesure de réinitialiser leurs mots de passe automatiquement à partir de n’importe quelle page web qui utilise un compte professionnel ou scolaire pour la connexion (tel que [portal.microsoftonline.com](https://portal.microsoftonline.com)). Sur ces pages, un lien **Vous ne parvenez pas à accéder à votre compte ?** s’affiche.

  ![][002]

Le fait de cliquer sur ce lien lance le portail de réinitialisation de mot de passe libre-service.

  ![][003]

Pour plus d’informations sur la façon dont les utilisateurs peuvent réinitialiser leur mot de passe, consultez la page [Prise en main de la gestion des mots de passe Azure AD](active-directory-passwords-getting-started.md).

##Portail de modification des mots de passe des utilisateurs
Si les utilisateurs souhaitent modifier leur mot de passe, ils peuvent le faire dans le portail de modification de mot de passe à tout moment. Ils peuvent accéder au portail de modification de mot de passe via la page Profil Panneau d’accès ou en cliquant sur le lien « Modifier le mot de passe » dans les applications Office 365. Lorsque son mot de passe expire, l’utilisateur est également invité à le modifier automatiquement lors de la connexion.

  ![][004]

Dans ces deux cas, si l’écriture différée de mot de passe a été activée et que l’utilisateur est fédéré ou synchronisé par mot de passe, les mots de passe modifiés sont mis à jour dans l’annuaire Active Directory local. Voici l’aspect du portail de modification de mot de passe :

  ![][005]

Pour plus d’informations sur la façon dont les utilisateurs peuvent changer leurs mots de passe Active Directory locaux, consultez la page [Prise en main de la gestion des mots de passe Azure AD](active-directory-passwords-getting-started.md).

##Rapports sur la gestion des mots de passe
En accédant à l’onglet **Rapports** et en consultant la section **Journaux d’activité**, deux rapports sur la gestion des mots de passe sont affichés : **Activité de réinitialisation de mot de passe** et **Activité d’inscription de réinitialisation de mot de passe**. Ils permettent d’obtenir une vue des utilisateurs inscrits à la réinitialisation de mot de passe dans votre organisation. Voici l’aspect de ces rapports dans le [portail de gestion Azure](https://manage.windowsazure.com) :

  ![][006]

Pour plus d’informations, consultez la rubrique [Obtention d’informations grâce aux rapports sur la gestion des mots de passe](active-directory-passwords-get-insights.md).

##Composant d’écriture différée de mot de passe Azure AD Connect
Si les mots de passe des utilisateurs de votre organisation sont issus de votre environnement local (via la fédération ou la synchronisation par mot de passe), vous pouvez installer la dernière version d’Azure AD Connect pour activer la mise à jour de ces mots de passe directement dans le cloud. Cela signifie que lorsque vos utilisateurs oublient ou souhaitent modifier leur mot de passe AD, ils peuvent le faire directement sur Internet. Voici où trouver l’écriture différée de mot de passe dans l’Assistant Installation Azure AD Connect :

  ![][007]

Pour plus d’informations sur Azure AD Connect, consultez la page [Prise en main d’Azure AD Connect](active-directory-aadconnect.md). Pour plus d’informations sur l’écriture différée de mot de passe, consultez la page [Prise en main de la gestion des mots de passe Azure AD](active-directory-passwords-getting-started.md).


<br/> <br/> <br/>

## Liens vers la documentation de réinitialisation du mot de passe
Voici les liens vers toutes les pages de la documentation sur la réinitialisation de mot de passe Azure AD :

* [**Réinitialiser votre mot de passe**](active-directory-passwords-update-your-own-password.md) : découvrez la procédure de réinitialisation ou de modification de votre mot de passe en tant qu'utilisateur du système.
* [**Prise en main**](active-directory-passwords-getting-started.md) : découvrez comment permettre à vos utilisateurs de réinitialiser et de modifier leurs mots de passe dans le cloud et localement.
* [**Personnalisation**](active-directory-passwords-customize.md) : découvrez comment personnaliser l’apparence et le comportement du service en fonction des besoins de votre organisation.
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) : découvrez comment déployer et gérer rapidement et efficacement les mots de passe de votre organisation.
* [**Obtention d’informations**](active-directory-passwords-get-insights.md) : découvrez nos fonctionnalités intégrées de création de rapports.
* [**FAQ**](active-directory-passwords-faq.md) : obtenez des réponses aux questions fréquemment posées.
* [**Dépannage**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre rapidement les problèmes liés au service.
* [**En savoir plus**](active-directory-passwords-learn-more.md) : découvrez les détails techniques sur le fonctionnement du service.



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

<!---HONumber=AcomDC_1125_2015-->