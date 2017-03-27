---
title: "FAQ : gestion des mots de passe Azure AD | Microsoft Docs"
description: "Forum aux questions (FAQ) concernant la gestion des mots de passe dans Azure AD, y compris la réinitialisation des mots de passe, l’inscription, les rapports et l’écriture différée vers Active Directory en local."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 3a157d27-a410-4371-bcbf-8312941ae9d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: cdf233c2b141f4c73abcabc199b5fc583f14962c
ms.lasthandoff: 03/10/2017


---
# <a name="password-management-frequently-asked-questions"></a>Forum Aux Questions sur la gestion des mots de passe
> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password).
>
>

Voici quelques questions fréquemment posées concernant toutes les tâches liées à la gestion des mots de passe.

Si vous vous posez une question ou si vous avez besoin d’aide pour un problème particulier, consultez les questions et réponses ci-dessous, vous y trouverez peut-être les explications que vous cherchez.  Si ce n’est pas le cas, ne vous inquiétez pas ! N’hésitez pas à poser les questions auxquelles vous n’avez pas trouvé de réponses dans ce [Forum Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) et nous vous répondrons dès que possible.

Ce Forum Aux Questions est organisé de la manière suivante :

* [**Questions relatives à l’inscription à la réinitialisation du mot de passe**](#password-reset-registration)
* [**Questions relatives à la réinitialisation du mot de passe**](#password-reset)
* [**Questions relatives au changement du mot de passe**](#password-change)
* [**Questions relatives aux rapports de gestion des mots de passe**](#password-management-reports)
* [**Questions relatives à l’écriture différée du mot de passe**](#password-writeback)

## <a name="password-reset-registration"></a>Inscription à la réinitialisation de mot de passe
* **Q : Mes utilisateurs peuvent-ils inscrire leurs propres données de réinitialisation du mot de passe ?**

  > **R :** Oui, tant que la réinitialisation de mot de passe est activée et qu’ils disposent d’une licence, vos utilisateurs peuvent accéder au portail d’inscription à la réinitialisation de mot de passe à l’adresse http://aka.ms/ssprsetup pour inscrire leurs informations d’authentification à utiliser avec la réinitialisation du mot de passe. Les utilisateurs peuvent également s’inscrire via le volet d’accès à l’adresse http://myapps.microsoft.com, cliquer sur l’onglet du profil, puis sur l’option Réinitialiser mon mot de passe. Consultez la rubrique Configuration des utilisateurs pour la gestion des mots de passe pour en savoir plus sur la configuration de vos utilisateurs pour la réinitialisation de mot de passe.
  >
  >
* **Q : Puis-je définir des données de réinitialisation de mot de passe pour le compte de mes utilisateurs ?**

  > **R :** Oui, vous pouvez le faire avec la synchronisation d’annuaire ou PowerShell, ou via le [portail de gestion Azure](https://manage.windowsazure.com) ou le portail d’administration Office. Pour en savoir plus sur cette fonctionnalité, consultez le billet de blog Amélioration de la confidentialité pour les numéros de téléphones associés à Azure AD MFA et à la réinitialisation de mot de passe et consultez la page Découvrez comment les données sont utilisées par la réinitialisation des mots de passe.
  >
  >
* **Q : Puis-je synchroniser localement des données pour des questions de sécurité ?**

  > **R :** Non, ce n’est actuellement pas possible, mais nous envisageons de proposer cette fonctionnalité.
  >
  >
* **Q : Mes utilisateurs peuvent-ils inscrire des données pour que les autres utilisateurs ne puissent pas les voir ?**

  > **R :** Oui, lorsque les utilisateurs inscrivent des données à l’aide du portail d’inscription à la réinitialisation de mot de passe, les données sont enregistrées dans des champs d’authentification privés uniquement visibles par les administrateurs généraux et l’utilisateur lui-même. Pour en savoir plus sur cette fonctionnalité, consultez le billet de blog Amélioration de la confidentialité pour les numéros de téléphones associés à Azure AD MFA et à la réinitialisation de mot de passe et consultez la page Découvrez comment les données sont utilisées par la réinitialisation des mots de passe.
  >
  >
* **Q : Mes utilisateurs doivent-ils être inscrits avant de pouvoir utiliser la réinitialisation de mot de passe ?**

  > **R :** Non, si vous définissez suffisamment d’informations d’authentification en leur nom, les utilisateurs n’auront pas besoin de s’inscrire. La réinitialisation de mot de passe fonctionne parfaitement tant que vous disposez de données correctement formatées stockées dans les champs appropriés de l’annuaire. Pour en savoir plus, consultez la page Découvrez comment les données sont utilisées par la réinitialisation des mots de passe.
  >
  >
* **Q : Puis-je synchroniser ou définir les champs Téléphone d’authentification, Adresse électronique d’authentification ou Téléphone d’authentification secondaire pour le compte de mes utilisateurs ?**

  > **R :** À l’heure actuelle, non, mais nous envisageons l’activation de cette fonctionnalité.
  >
  >
* **Q : Comment le portail d’inscription sait-il quelles options proposer à mes utilisateurs ?**

  > **R :** Le portail d’inscription à la réinitialisation du mot de passe affiche uniquement les options que vous avez activées pour vos utilisateurs dans la section Stratégie de réinitialisation du mot de passe utilisateur de l’onglet Configurer de votre annuaire. Cela signifie que si vous n’activez pas, par exemple, les questions de sécurité, alors les utilisateurs ne sont pas en mesure de s’inscrire pour cette option.
  >
  >
* **Q : Quand un utilisateur est-il considéré comme inscrit ?**

  > **R :** Un utilisateur est considéré comme inscrit lorsqu’il a au moins N éléments d’information d’authentification définis, N étant le nombre de méthodes d’authentification requises que vous avez défini dans le [portail de gestion Azure](https://manage.windowsazure.com). Pour en savoir plus, consultez la page Personnalisation de la stratégie de réinitialisation de mot de passe utilisateur.
  >
  >

## <a name="password-reset"></a>Réinitialisation de mot de passe
* **Q : Combien de temps dois-je attendre avant de recevoir un e-mail, un SMS ou un appel téléphonique de la réinitialisation du mot de passe ?**

  > **R :** Les messages électroniques, les SMS et les appels téléphoniques doivent arriver en moins d’une minute, la plupart du temps dans les 5 à 20 secondes suivant la réinitialisation. Si vous ne recevez pas la notification dans ce laps de temps, consultez votre dossier de courrier indésirable, vérifiez que le numéro/l’adresse de messagerie utilisés sont corrects et que les données d’authentification dans l’annuaire sont correctement formatées. Pour en savoir plus sur le formatage des numéros de téléphone et des adresses de messagerie à utiliser avec la réinitialisation du mot de passe, consultez la page Découvrez comment les données sont utilisées par la réinitialisation des mots de passe.
  >
  >
* **Q : Quelles langues sont prises en charge pour la réinitialisation de mot de passe ?**

  > **R :** L’interface utilisateur pour la réinitialisation du mot de passe, les SMS et les appels vocaux sont localisés dans les 40 langues prises en charge dans Office 365, à savoir : arabe, bulgare, chinois simplifié, chinois traditionnel, croate, tchèque, danois, néerlandais, anglais, estonien, finnois, français, allemand, grec, hébreu, hindi, hongrois, indonésien, italien, japonais, kazakh, coréen, letton, lituanien, malais (Malaisie), norvégien (Bokmål), polonais, portugais (Brésil), portugais (Portugal), roumain, russe, serbe (Latin), slovaque, slovène, espagnol, suédois, thaï, turc, ukrainien et vietnamien.
  >
  >
* **Q : Quelles parties de l’expérience de réinitialisation de mot de passe sont personnalisées quand je configure la personnalisation de l’organisation sous l’onglet Configurer de mon annuaire ?**

  > **R :** Le portail de réinitialisation de mot de passe affiche le logo de votre organisation et vous permet également de configurer le lien Contactez votre administrateur pour qu’il pointe vers une URL ou une adresse de messagerie personnalisée. Tout message électronique envoyé par la réinitialisation de mot de passe inclut le logo, les couleurs (rouge, dans cet exemple) et le nom de votre organisation dans le corps du message électronique, ainsi que le nom personnalisé de l’émetteur. Examinez un exemple avec tous les éléments personnalisés ci-dessous. Pour en savoir plus, lisez la page Personnalisation de l’apparence de la réinitialisation de mot de passe.
  >
  >

  ![][001]
* **Q : Comment puis-je informer mes utilisateurs des liens sur lesquels ils peuvent cliquer pour réinitialiser leurs mots de passe ?**

  > **R :** Vous pouvez diriger vos utilisateurs directement vers l’adresse https://passwordreset.microsoftonline.com ou leur demander de cliquer sur le lien « Vous ne parvenez pas à accéder à votre compte ? » qui figure sur n’importe quel écran de connexion à un compte professionnel ou scolaire. Libre à vous de publier ces liens (ou de créer une URL de redirection vers ces liens) à un emplacement facilement accessible à vos utilisateurs.
  >
  >
* **Q : Puis-je utiliser cette page à partir d’un appareil mobile ?**

  > **R :** Oui, cette page fonctionne sur les appareils mobiles.
  >
  >
* **Q : Prenez-vous en charge le déverrouillage des comptes Active Directory locaux quand les utilisateurs réinitialisent leurs mots de passe ?**

  > **R :** Oui, lorsqu’un utilisateur réinitialise son mot de passe et que l’écriture différée du mot de passe a été déployée avec toutes les versions d’Azure AD Connect, ou les versions d’Azure AD Sync 1.0.0485.0222 ou version ultérieure, le compte de cet utilisateur est automatiquement déverrouillé.
  >
  >
* **Q : Comment puis-je intégrer la réinitialisation de mot de passe directement dans l’expérience de connexion de Bureau de mes utilisateurs ?**

  > **R :** Ce n’est actuellement pas possible. Toutefois, si vous avez absolument besoin de cette fonctionnalité et que vous êtes client Azure AD Premium, vous pouvez installer le Gestionnaire d’identité Microsoft sans coût supplémentaire et déployer la solution locale de réinitialisation de mot de passe qui s’y trouve.
  >
  >
* **Q : Puis-je définir des questions de sécurité différentes pour différents paramètres régionaux ?**

  > **R :** Non, ce n’est actuellement pas possible, mais nous envisageons de proposer cette fonctionnalité.
  >
  >
* **Q : Combien de questions puis-je configurer pour l’option d’authentification Questions de sécurité ?**

  > **R :** Vous pouvez configurer jusqu’à 20 questions de sécurité personnalisées dans le [portail de gestion Azure](https://manage.windowsazure.com).
  >
  >
* **Q : Quelle doit être la longueur des questions de sécurité ?**

  > **R :** Les questions de sécurité peuvent comprendre entre 3 et 200 caractères.
  >
  >
* **Q : Quelle doit être la longueur des réponses aux questions de sécurité ?**

  > **R :** Les réponses doivent comprendre entre 3 et 40 caractères.
  >
  >
* **Q : Les réponses dupliquées aux questions de sécurité sont-elles rejetées ?**

  > **R :** Oui, nous rejetons les réponses dupliquées aux questions de sécurité.
  >
  >
* **Q : Un utilisateur peut-il inscrire plusieurs fois la même question de sécurité ?**

  > **R :** Non, une fois qu’un utilisateur a inscrit une question particulière, il ne peut plus inscrire cette même question une deuxième fois.
  >
  >
* **Q : Est-il possible de définir un nombre minimal de questions de sécurité pour l’inscription et la réinitialisation ?**

  > **R :** Oui, vous pouvez définir une limite pour l’inscription et une autre pour la réinitialisation. Vous pouvez poser entre 3 et 5 questions de sécurité pour l’inscription et entre 3 et 5 questions pour la réinitialisation.
  >
  >
* **Q : Si un utilisateur a inscrit plus de questions que le nombre maximal requis pour la réinitialisation, comment les questions de sécurité sont-elles sélectionnées lors de la réinitialisation ?**

  > **R :** N questions de sécurité sont sélectionnées au hasard à partir du nombre total de questions qu’un utilisateur a inscrit, N étant le nombre minimal de questions requises pour la réinitialisation du mot de passe. Par exemple, si un utilisateur a inscrit 5 questions de sécurité, alors que seules 3 sont requises pour la réinitialisation, 3 de ces 5 questions sont sélectionnées au hasard et présentées à l’utilisateur au moment de la réinitialisation. Si l’utilisateur ne répond pas correctement aux questions, le processus de sélection se répète pour éviter de poser toujours les mêmes questions.
  >
  >
* **Q : Limitez-vous les tentatives de réinitialisation de mot de passe effectuées en un bref laps de temps ?**

  > **R :** Oui, il existe plusieurs fonctionnalités de sécurité intégrées à la réinitialisation de mot de passe. Les utilisateurs peuvent uniquement tenter 5 réinitialisations de mot de passe en une heure, après quoi leur compte est verrouillé pendant 24 heures. De même, ils peuvent uniquement essayer de valider un numéro de téléphone 5 fois en une heure, après quoi leur compte est verrouillé pendant 24 heures. Les utilisateurs peuvent également tenter une même méthode d’authentification seulement 5 fois en une heure, après quoi leur compte est verrouillé pendant 24 heures.
  >
  >
* **Q : Quelle est la durée de validité du code secret à usage unique pour les e-mails et les SMS ?**

  > **R :** La durée de vie de session pour la réinitialisation du mot de passe est de 105 minutes. Cela signifie qu’à compter du début de l’opération de réinitialisation du mot de passe, l’utilisateur dispose de 105 minutes pour le réinitialiser. Le code secret à usage unique pour les messages électroniques et les SMS perd sa validité à l’issue de cette période.
  >
  >

## <a name="password-change"></a>Modification de mot de passe
* **Q : Où mes utilisateurs doivent-ils aller pour modifier leurs mots de passe ?**

  > **R :** Les utilisateurs peuvent modifier leurs mots de passe partout où ils voient leur image ou leur icône de profil (par exemple, en haut à droite de leur interface [Office 365](https://portal.office.com) ou [Panneau d’accès](https://myapps.microsoft.com). Les utilisateurs peuvent modifier leurs mots de passe à partir de la [page du profil Panneau d’accès](https://account.activedirectory.windowsazure.com/r#/profile). Ils peuvent également être invités à modifier automatiquement leurs mots de passe sur l’écran de connexion Azure AD si leurs mots de passe ont expiré. Enfin, les utilisateurs peuvent accéder directement au [portail de changement de mot de passe Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) s’ils souhaitent modifier leur mot de passe.
  >
  >
* **Q : Mes utilisateurs peuvent-ils être informés de l’expiration de leur mot de passe local dans le portail Office ?**

  > **R :** Cela est actuellement possible si vous utilisez AD FS en suivant les instructions fournies ici : [Configurer AD FS pour envoyer des revendications d’expiration de mot de passe](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396). Si vous utilisez la synchronisation de hachage de mot de passe, ces notifications ne sont pas prises en charge actuellement. Comme nous ne synchronisons pas les stratégies de mot de passe en local, nous ne sommes pas en mesure de publier des notifications d’expiration dans les expériences cloud. Dans les deux cas, il est également possible de [notifier les utilisateurs dont les mots de passe sont sur le point d’expirer à l’aide de PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
  
## <a name="password-management-reports"></a>Rapports sur la gestion des mots de passe
* **Q : Combien de temps faut-il pour que les données soient affichées dans les rapports de gestion des mots de passe ?**

  > **R :** Les données doivent apparaître dans les rapports de gestion des mots de passe dans les cinq à dix minutes. Dans certains cas, il faut attendre une heure avant qu’elles n’apparaissent.
  >
  >
* **Q : Comment faire pour filtrer les rapports de gestion des mots de passe ?**

  > **R :** Vous pouvez filtrer les rapports de gestion des mots de passe en cliquant sur la petite loupe qui se trouve à l’extrême droite des étiquettes de colonnes, dans la partie supérieure du rapport (voir la capture d’écran). Pour effectuer un filtrage plus précis, vous pouvez télécharger le rapport dans Excel et créer un tableau croisé dynamique.
  >
  >

  ![][002]
* **Q : quel est le nombre maximal d’événements stockés dans les rapports de gestion de mot de passe ?**

  > **R :** Jusqu’à 75 000 événements de réinitialisation de mot de passe ou d’inscription de réinitialisation de mot de passe sont stockés dans les rapports de gestion de mot de passe, pour une sauvegarde d’une durée de 30 jours.  Nous travaillons à l’augmentation de ce nombre pour inclure davantage d’événements.
  >
  >
* **Q : Jusqu’à quand remontent les rapports de gestion des mots de passe ?**

  > **R :** Les rapports de gestion des mots de passe affichent les opérations qui ont eu lieu durant les 30 derniers jours. Nous étudions actuellement un moyen de prolonger cette période. Pour le moment, si vous devez archiver ces données, vous pouvez télécharger les rapports régulièrement et les enregistrer dans un emplacement distinct.
  >
  >
* **Q : Existe-t-il une limite au nombre de lignes qui peuvent apparaître dans les rapports de gestion des mots de passe ?**

  > **R :** Oui, 75 000 lignes au maximum peuvent apparaître dans un rapport de gestion des mots de passe, qu’elles soient téléchargées ou affichées dans l’interface utilisateur. Nous étudions actuellement un moyen d’augmenter cette limite.
  >
  >
* **Q : existe-t-il une API pour accéder aux données des rapports sur la réinitialisation de mot de passe ou sur l’inscription de réinitialisation de mot de passe ?**

  > **R :** Oui, consultez la documentation suivante pour savoir comment vous pouvez accéder au flux de données des rapports sur la réinitialisation de mot de passe.  [Découvrez comment accéder par programmation aux événements des rapports sur la réinitialisation de mot de passe](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Écriture différée du mot de passe
* **Q : Comment l’écriture différée du mot de passe fonctionne-t-elle en arrière-plan ?**

  > **R :** Consultez la rubrique [Fonctionnement de l’écriture différée du mot de passe](active-directory-passwords-learn-more.md#how-password-writeback-works) pour obtenir une explication détaillée de ce qui se passe lorsque vous activez l’écriture différée du mot de passe et pour comprendre la manière dont les données circulent entre le système et votre environnement local. Consultez la section [Modèle de sécurité de l’écriture différée du mot de passe](active-directory-passwords-learn-more.md#password-writeback-security-model) de l’article Fonctionnement de l’écriture différée du mot de passe pour découvrir comment nous veillons à ce que l’écriture différée du mot de passe soit un service hautement sécurisé.
  >
  >
* **Q : Combien de temps faut-il pour que l’écriture différée du mot de passe fonctionne ?  Y a-t-il un délai de synchronisation comme avec la synchronisation du hachage de mot de passe ?**

  > **R :** L’écriture différée du mot de passe est instantanée. Il s’agit d’un pipeline synchrone qui fonctionne différemment de la synchronisation du hachage de mot de passe. L’écriture différée du mot de passe permet aux utilisateurs d’obtenir des commentaires en temps réel quant à la réussite de la modification ou de la réinitialisation de leur mot de passe. L’écriture différée réussie d’un mot de passe dure en moyenne moins de 500 ms.
  >
  >
* **Q : Pour quels types de compte l’écriture différée du mot de passe fonctionne-t-elle ?**

  > **R :** L’écriture différée du mot de passe fonctionne pour les utilisateurs fédérés et ceux disposant de la synchronisation du hachage de mot de passe.
  >
  >
* **Q : L’écriture différée du mot de passe applique-t-elle les stratégies de mot de passe de mon domaine ?**

  > **R :** Oui, l’écriture différée du mot de passe applique l’âge du mot de passe, l’historique, la complexité, les filtres et toute autre restriction que vous pouvez mettre en place sur les mots de passe dans votre domaine local.
  >
  >
* **Q : L’écriture différée du mot de passe est-elle sécurisée ?  Comment puis-je être sûr de ne pas être piraté ?**

  > **R :** Oui, l’écriture différée du mot de passe est extrêmement sécurisée. Pour en savoir plus sur les 4 couches de sécurité implémentées par le service d’écriture différée du mot de passe, consultez la section [Modèle de sécurité de l’écriture différée du mot de passe](active-directory-passwords-learn-more.md#password-writeback-security-model) de l’article Fonctionnement de l’écriture différée du mot de passe.
  >
  >

## <a name="next-steps"></a>Étapes suivantes
Voici les liens vers toutes les pages de la documentation sur la réinitialisation de mot de passe Azure AD :

* **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password).
* [**Fonctionnement**](active-directory-passwords-how-it-works.md) : découvrez les six différents composants du service et la fonction de chacun d’eux
* [**Prise en main**](active-directory-passwords-getting-started.md) : découvrez comment permettre à vos utilisateurs de réinitialiser et de modifier leurs mots de passe dans le cloud et localement
* [**Personnalisation**](active-directory-passwords-customize.md) : découvrez comment personnaliser l’apparence et le comportement du service en fonction des besoins de votre organisation.
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) : découvrez comment déployer et gérer rapidement et efficacement les mots de passe de votre organisation.
* [**Obtention d’informations**](active-directory-passwords-get-insights.md) : découvrez nos fonctionnalités intégrées de création de rapports.
* [**Dépannage**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre rapidement les problèmes liés au service.
* [**En savoir plus**](active-directory-passwords-learn-more.md) : découvrez les détails techniques sur le fonctionnement du service.

[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"

