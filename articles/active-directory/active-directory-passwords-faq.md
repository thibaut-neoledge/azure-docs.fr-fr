---
title: "Forum aux questions : réinitialisation du mot de passe en libre-service Azure AD | Microsoft Docs"
description: "Forum aux questions concernant la réinitialisation du mot de passe en libre-service Azure AD"
services: active-directory
keywords: "Gestion des mots de passe Active Directory, gestion des mots de passe, réinitialisation de mot de passe en libre-service Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 00acd4090ed981ab2b05e955e93d1c689ea1a2e6
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---
# <a name="password-management-frequently-asked-questions"></a>Forum Aux Questions sur la gestion des mots de passe

Voici quelques questions fréquemment posées concernant toutes les tâches liées à la réinitialisation des mots de passe.

Si vous avez une question générale sur Azure AD et la réinitialisation du mot de passe en libre-service, vous ne trouverez pas de réponse ici, mais vous pouvez demander de l’aide à la communauté sur les [forums d’Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Les membres de la communauté comprennent les ingénieurs, les chefs de produit, MVP et autres professionnels de l’informatique.

Ce Forum Aux Questions est organisé de la manière suivante :

* [**Questions relatives à l’inscription à la réinitialisation du mot de passe**](#password-reset-registration)
* [**Questions relatives à la réinitialisation du mot de passe**](#password-reset)
* [**Questions relatives au changement du mot de passe**](#password-change)
* [**Questions relatives aux rapports de gestion des mots de passe**](#password-management-reports)
* [**Questions relatives à l’écriture différée du mot de passe**](#password-writeback)

## <a name="password-reset-registration"></a>Inscription à la réinitialisation de mot de passe
* **Q : Mes utilisateurs peuvent-ils inscrire leurs propres données de réinitialisation du mot de passe ?**

  > **R :** Oui, tant que la réinitialisation de mot de passe est activée et qu’ils disposent d’une licence, vos utilisateurs peuvent accéder au portail d’inscription à la réinitialisation de mot de passe à l’adresse http://aka.ms/ssprsetup pour inscrire leurs informations d’authentification. Les utilisateurs peuvent également s’inscrire via le volet d’accès à l’adresse http://myapps.microsoft.com, cliquer sur l’onglet du profil, puis sur l’option Réinitialiser mon mot de passe.
  >
  >
* **Q : Puis-je définir des données de réinitialisation de mot de passe pour le compte de mes utilisateurs ?**

  > **R :** Oui, vous pouvez le faire avec Azure AD Connect, PowerShell, le [portail Azure](https://portal.azure.com) ou le portail d’administration d’Office. Pour plus d’informations, consultez l’article [Données utilisées par la réinitialisation du mot de passe en libre-service Azure AD](active-directory-passwords-data.md).
  >
  >
* **Q : Puis-je synchroniser localement des données pour des questions de sécurité ?**

  > **R :** Ce n’est actuellement pas possible.
  >
  >
* **Q : Mes utilisateurs peuvent-ils inscrire des données pour que les autres utilisateurs ne puissent pas les voir ?**

  > **R :** Oui, lorsque les utilisateurs inscrivent des données à l’aide du portail d’inscription à la réinitialisation de mot de passe, les données sont enregistrées dans des champs d’authentification privés uniquement visibles par les administrateurs généraux et l’utilisateur.
    >
    > [!NOTE]
    > Si un **compte d’administrateur Azure** enregistre son numéro de téléphone d’authentification, il est également rempli dans le champ téléphone mobile et est visible.
    >
  >
  >
* **Q : Mes utilisateurs doivent-ils être inscrits avant de pouvoir utiliser la réinitialisation de mot de passe ?**

  > **R :** Non, si vous définissez suffisamment d’informations d’authentification en leur nom, les utilisateurs n’ont pas besoin de s’inscrire. La réinitialisation de mot de passe fonctionne tant que vous disposez de données correctement formatées stockées dans les champs appropriés de l’annuaire.
  >
  >
* **Q : Puis-je synchroniser ou définir les champs Téléphone d’authentification, Adresse électronique d’authentification ou Téléphone d’authentification secondaire pour le compte de mes utilisateurs ?**

  > **R :** Ce n’est actuellement pas possible.
  >
  >
* **Q : Comment le portail d’inscription sait-il quelles options proposer à mes utilisateurs ?**

  > **R :** Le portail d’inscription de réinitialisation de mot de passe affiche uniquement les options que vous avez activées pour vos utilisateurs. Ces options se trouvent sous la section Stratégie de réinitialisation de mot de passe utilisateur de l’onglet Configurer de votre annuaire. Par exemple, cela signifie que si vous n’activez pas les questions de sécurité, les utilisateurs ne sont pas en mesure de s’inscrire pour cette option.
  >
  >
* **Q : Quand un utilisateur est-il considéré comme inscrit ?**

  > **R :** Un utilisateur est considéré comme inscrit pour la réinitialisation de mot de passe en libre-service lorsqu’il a enregistré au moins le **Nombre de méthodes requis pour la réinitialisation** défini dans le [portail Azure](https://portal.azure.com).
  >
  >
## <a name="password-reset"></a>Réinitialisation de mot de passe
* **Q : Combien de temps dois-je attendre avant de recevoir un e-mail, un SMS ou un appel téléphonique de la réinitialisation du mot de passe ?**

  > **R :** Les messages électroniques, les SMS et les appels téléphoniques doivent arriver en moins d’une minute, la plupart du temps dans les 5 à 20 secondes suivant la réinitialisation.
    >Si vous ne recevez pas la notification dans ce laps de temps :
        > * Vérifiez votre dossier de courrier indésirable.
        > * Vérifiez que le numéro ou que l’e-mail contacté est celui que vous attendez.
        > * Vérifiez que les données d’authentification dans l’annuaire sont correctes.
                >     * Par exemple « +1 4255551234 » ou « user@contoso.com »
  >
  >
* **Q : Quelles langues sont prises en charge pour la réinitialisation de mot de passe ?**

  > **R :** L’interface utilisateur pour la réinitialisation du mot de passe, les SMS et les appels vocaux sont localisés dans les mêmes langues que celles prises en charge dans Office 365.
  >
  >
* **Q : Quelles parties de l’expérience de réinitialisation de mot de passe sont personnalisées quand je configure la personnalisation de l’organisation sous l’onglet Configurer de mon annuaire ?**

  > **R :** Le portail de réinitialisation de mot de passe affiche le logo de votre organisation et vous permet également de configurer le lien Contactez votre administrateur pour qu’il pointe vers une URL ou une adresse de messagerie personnalisée. Tout message électronique envoyé par la réinitialisation de mot de passe inclut le logo, les couleurs et le nom de votre organisation dans le corps du message électronique, ainsi que le nom personnalisé de l’émetteur.
  >
  >
* **Q : Comment puis-je informer mes utilisateurs des liens sur lesquels ils peuvent cliquer pour réinitialiser leurs mots de passe ?**

  > **R :** Vous pouvez diriger vos utilisateurs directement vers l’adresse https://passwordreset.microsoftonline.com ou leur demander de cliquer sur le lien **Vous ne parvenez pas à accéder à votre compte ?** qui figure sur n’importe quel écran de connexion à un compte professionnel ou scolaire. Vous pouvez également publier ces liens dans un endroit facilement accessible à vos utilisateurs.
  >
  >
* **Q : Puis-je utiliser cette page à partir d’un appareil mobile ?**

  > **R :** Oui, cette page fonctionne sur les appareils mobiles.
  >
  >
* **Q : Prenez-vous en charge le déverrouillage des comptes Active Directory locaux quand les utilisateurs réinitialisent leurs mots de passe ?**

  > **R :** oui, lorsqu’un utilisateur réinitialise son mot de passe et que l’écriture différée de mot de passe a été déployée à l’aide d’Azure AD Connect, ce compte d’utilisateur est déverrouillé automatiquement lorsqu’il réinitialise son mot de passe.
  >
  >
* **Q : Comment puis-je intégrer la réinitialisation de mot de passe directement dans l’expérience de connexion de Bureau de mes utilisateurs ?**

  > **R :**  Si vous vous êtes client Azure AD Premium, vous pouvez installer le Gestionnaire d’identité Microsoft sans coût supplémentaire et déployer la solution locale de réinitialisation de mot de passe pour répondre à cette exigence.
  >
  >
* **Q : Puis-je définir des questions de sécurité différentes pour différents paramètres régionaux ?**

  > **R :** Ce n’est actuellement pas possible.
  >
  >
* **Q : Combien de questions puis-je configurer pour l’option d’authentification Questions de sécurité ?**

  > **R :** Vous pouvez configurer jusqu’à 20 questions de sécurité personnalisées dans le [portail Azure](https://portal.azure.com).
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

  > **R :** N questions de sécurité sont sélectionnées au hasard à partir du nombre total de questions qu’un utilisateur a inscrit, N étant le **nombre de questions requises pour la réinitialisation**. Par exemple, si un utilisateur a inscrit 5 questions de sécurité, alors que seules 3 sont requises pour la réinitialisation, 3 de ces 5 questions sont sélectionnées au hasard et présentées au moment de la réinitialisation. Si l’utilisateur ne répond pas correctement aux questions, le processus de sélection se répète pour éviter de poser toujours les mêmes questions.
  >
  >
* **Q : Limitez-vous les tentatives de réinitialisation de mot de passe effectuées en un bref laps de temps ?**

  > **R :** Oui, il existe des fonctionnalités de sécurité intégrées à la réinitialisation de mot de passe pour empêcher toute utilisation malveillante. Les utilisateurs peuvent uniquement tenter 5 réinitialisations de mot de passe en une heure, après quoi leur compte est verrouillé pendant 24 heures. De même, ils peuvent uniquement essayer de valider un numéro de téléphone 5 fois en une heure, après quoi leur compte est verrouillé pendant 24 heures. Les utilisateurs peuvent également tenter une même méthode d’authentification seulement 5 fois en une heure, après quoi leur compte est verrouillé pendant 24 heures.
  >
  >
* **Q : Quelle est la durée de validité du code secret à usage unique pour les e-mails et les SMS ?**

  > **R :** La durée de vie de session pour la réinitialisation du mot de passe est de 105 minutes. À compter du début de l’opération de réinitialisation du mot de passe, l’utilisateur dispose de 105 minutes pour le réinitialiser. Le code secret à usage unique pour les messages électroniques et les SMS perd sa validité à l’issue de cette période.
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

  > **R :** Vous pouvez filtrer les rapports de gestion des mots de passe en cliquant sur la petite loupe qui se trouve à l’extrême droite des étiquettes de colonnes, près du sommet du rapport. Pour effectuer un filtrage plus précis, vous pouvez télécharger le rapport dans Excel et créer un tableau croisé dynamique.
  >
  >
* **Q : quel est le nombre maximal d’événements stockés dans les rapports de gestion de mot de passe ?**

  > **R :** Jusqu’à 75 000 événements de réinitialisation de mot de passe ou d’inscription de réinitialisation de mot de passe sont stockés dans les rapports de gestion de mot de passe, pour une sauvegarde d’une durée de 30 jours.  Nous travaillons à l’augmentation de ce nombre pour inclure davantage d’événements.
  >
  >
* **Q : Jusqu’à quand remontent les rapports de gestion des mots de passe ?**

  > **R :** Les rapports de gestion des mots de passe affichent les opérations qui ont eu lieu durant les 30 derniers jours. Pour le moment, si vous devez archiver ces données, vous pouvez télécharger les rapports régulièrement et les enregistrer dans un emplacement distinct.
  >
  >
* **Q : Existe-t-il une limite au nombre de lignes qui peuvent apparaître dans les rapports de gestion des mots de passe ?**

  > **R :** Oui, 75 000 lignes au maximum peuvent apparaître dans un rapport de gestion des mots de passe, qu’elles soient téléchargées ou affichées dans l’interface utilisateur.
  >
  >
* **Q : existe-t-il une API pour accéder aux données des rapports sur la réinitialisation de mot de passe ou sur l’inscription de réinitialisation de mot de passe ?**

  > **R :** Oui, consultez la documentation suivante pour savoir comment vous pouvez accéder au flux de données des rapports sur la réinitialisation de mot de passe.  [Découvrez comment accéder par programmation aux événements des rapports sur la réinitialisation de mot de passe](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Écriture différée du mot de passe
* **Q : Comment l’écriture différée du mot de passe fonctionne-t-elle en arrière-plan ?**

  > **R :** Consultez la rubrique [Fonctionnement de l’écriture différée du mot de passe](active-directory-passwords-writeback.md) pour obtenir une explication de ce qui se passe lorsque vous activez l’écriture différée du mot de passe et pour comprendre la manière dont les données circulent entre le système et votre environnement local.
  >
  >
* **Q : Combien de temps faut-il pour que l’écriture différée du mot de passe fonctionne ?  Y a-t-il un délai de synchronisation comme avec la synchronisation du hachage de mot de passe ?**

  > **R :** L’écriture différée du mot de passe est instantanée. Il s’agit d’un pipeline synchrone qui fonctionne différemment de la synchronisation du hachage de mot de passe. L’écriture différée du mot de passe permet aux utilisateurs d’obtenir des commentaires en temps réel quant à la réussite de la modification ou de la réinitialisation de leur mot de passe. L’écriture différée réussie d’un mot de passe dure en moyenne moins de 500 ms.
  >
  >
* **Q : Si mon compte local est désactivé, comment mon compte/accès cloud est-il affecté ?**

  > **R :** Si votre ID local est désactivé, votre ID/accès cloud sera également désactivé lors du prochain intervalle de synchronisation via AAD Connect, par défaut toutes les 30 minutes.
  >
  >
* **Q : Si mon compte local est contraint par une stratégie de mot de passe Active Directory local, la réinitialisation de mot de passe en libre-service respecte-t-elle cette stratégie lorsque je modifie le mot de passe ?**

  > **R :** Oui, la réinitialisation de mot de passe en libre-service s’appuie sur et respecte la stratégie de mot de passe AD locale, y compris la stratégie de mot de passe de domaine AD habituelle, ainsi que toute stratégie de mot de passe affinée pour un utilisateur donné.
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

  > **R :** Oui, l’écriture différée du mot de passe est sécurisée. Pour en savoir plus sur les quatre couches de sécurité implémentées par le service d’écriture différée du mot de passe, consultez [Modèle de sécurité de l’écriture différée du mot de passe](active-directory-passwords-writeback.md#password-writeback-security-model) dans l’article Fonctionnement de l’écriture différée du mot de passe.
  >
  >

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD

* [**Démarrage rapide** ](active-directory-passwords-getting-started.md) - soyez rapidement opérationnel avec la gestion de mots de passe en libre-service d’Azure AD 
* [**Licences** ](active-directory-passwords-licensing.md) - configurez vos licences Azure AD
* [**Données** ](active-directory-passwords-data.md) : comprenez les données requises et comment elles sont utilisées pour la gestion des mots de passe
* [**Déploiement** ](active-directory-passwords-best-practices.md) : planifiez et déployez la réinitialisation de mot de passe en libre-service sur vos utilisateurs grâce aux conseils figurant ici
* [**Personnalisation** ](active-directory-passwords-customize.md) : personnalisez l’apparence de l’interface de réinitialisation de mot de passe en libre-service de votre société.
* [**Rapports**](active-directory-passwords-reporting.md) : découvrez si, quand et où vos utilisateurs accèdent aux fonctionnalités de réinitialisation de mot de passe en libre-service
* [**Stratégie** ](active-directory-passwords-policy.md) : comprenez et définissez les stratégies de mot de passe d’Azure AD
* [**Écriture différée de mot de passe** ](active-directory-passwords-writeback.md) - fonctionnement de l’écriture différée de mot de passe avec votre annuaire local
* [**Présentation technique approfondie** ](active-directory-passwords-how-it-works.md) : découvrez ce qu’il se passe sous le capot pour comprendre le fonctionnement
* [**Résolution des problèmes** ](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre les problèmes courants que nous observons avec la réinitialisation de mot de passe en libre-service

