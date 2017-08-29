---
title: "Découverte approfondie : réinitialisation du mot de passe libre-service Azure AD | Microsoft Docs"
description: "Découverte approfondie de la réinitialisation du mot de passe libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: 0fa05ee6a2df13845024e770a82f50ab7f75bafd
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Découverte approfondie de la réinitialisation de mot de passe libre-service dans Azure AD

Comment fonctionne la réinitialisation de mot de passe libre-service ? Que signifie cette option dans l’interface ? Poursuivez la lecture pour en savoir plus sur la réinitialisation du mot de passe libre-service Azure AD.

## <a name="how-does-the-password-reset-portal-work"></a>Fonctionnement du portail de réinitialisation du mot de passe

Lorsqu’un utilisateur accède au portail de réinitialisation du mot de passe, un flux de travail est lancé pour déterminer les éléments suivants :

   * Comment la page doit être localisée ?
   * Le compte d’utilisateur est-il valide ?
   * À quelle organisation l’utilisateur appartient-il ?
   * Où le mot de passe de l’utilisateur est-il géré ?
   * L’utilisateur a-t-il une licence pour utiliser la fonctionnalité ?


Lisez les étapes ci-dessous pour en savoir plus sur la logique sous-jacente à la page de réinitialisation de mot de passe.

1. L’utilisateur clique sur le lien Vous ne parvenez pas à accéder à votre compte ? ou il accède directement à [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2. Selon les paramètres régionaux du navigateur, l’expérience est proposée dans la langue appropriée. L’expérience de réinitialisation du mot de passe est localisée dans les mêmes langues que celles prises en charge par Office 365.
3. L’utilisateur entre un ID utilisateur et passe un test CAPTCHA.
4. Azure AD vérifie si l’utilisateur peut utiliser cette fonctionnalité en procédant comme suit :
   * Il vérifie que cette fonctionnalité est activée pour l’utilisateur et qu’il possède une licence Azure AD.
     * Si ce n’est pas le cas, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
   * Vérifie que l’utilisateur a défini les données de test appropriées sur son compte, conformément à la stratégie de l’administrateur.
     * Si la stratégie n’exige qu’un seul test, AAD vérifie que l’utilisateur a défini les données appropriées pour au moins un des tests activés par la stratégie de l’administrateur.
       * Si l’utilisateur n’est pas configuré, il est invité à contacter son administrateur pour réinitialiser son mot de passe.
     * Si la stratégie exige deux tests, AAD vérifie que l’utilisateur a défini les données appropriées pour au moins deux des tests activés par la stratégie de l’administrateur.
       * Si l’utilisateur n’est pas configuré, il est invité à contacter son administrateur pour réinitialiser son mot de passe.
   * Vérifie si le mot de passe de l’utilisateur est géré localement (fédération ou synchronisation de hachage de mot de passe).
     * Si l’écriture différée est déployée et que le mot de passe est géré localement, l’utilisateur est autorisé à s’authentifier et à réinitialiser son mot de passe.
     * Si l’écriture différée n’est pas déployée et que le mot de passe est géré localement, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
5. S’il est établi que l’utilisateur est en mesure de réinitialiser son mot de passe, il reçoit alors des instructions pour mener à bien le processus de réinitialisation.

## <a name="authentication-methods"></a>Méthodes d’authentification

Si la réinitialisation de mot de passe en libre-service est activée, vous devez sélectionner au moins l’une des options suivantes pour les méthodes d’authentification. Nous vous recommandons vivement de choisir au moins deux méthodes d’authentification pour offrir davantage de flexibilité à vos utilisateurs.

* Email
* Téléphone mobile
* Téléphone de bureau
* Questions de sécurité

### <a name="what-fields-are-used-in-the-directory-for-authentication-data"></a>Champs utilisés dans le répertoire pour l’authentification des données

* Téléphone professionnel correspond au numéro de téléphone professionnel.
    * Les utilisateurs ne peuvent pas définir ce champ eux-mêmes, il doit être défini par un administrateur.
* Téléphone mobile correspond à Téléphone d’authentification (non visible publiquement) ou Téléphone mobile (visible publiquement).
    * Le service commence par rechercher le numéro de téléphone d’authentification, puis revient au numéro de téléphone mobile s’il n’y a pas de numéro de téléphone d’authentification.
* Autre adresse de messagerie correspond à E-mail d’authentification (non visible publiquement) ou Autre adresse de messagerie.
    * Le service commence par rechercher l’e-mail d’authentification, puis revient à l’autre adresse de messagerie.

Par défaut, seuls les attributs cloud Téléphone professionnel et Téléphone mobile sont synchronisés dans votre répertoire cloud à partir de votre répertoire local pour les données d’authentification.

Les utilisateurs peuvent uniquement réinitialiser leur mot de passe s’ils ont des données présentes dans les méthodes d’authentification que l’administrateur a activées et exige.

Si les utilisateurs ne souhaitent pas que leur numéro de téléphone mobile soit visible dans le répertoire, mais veulent quand même l’utiliser pour la réinitialisation du mot de passe, les administrateurs ne doivent pas le renseigner dans le répertoire et l’utilisateur doit ensuite indiquer son attribut **Téléphone d’authentification** via le [portail d’inscription de réinitialisation du mot de passe](http://aka.ms/ssprsetup). Les administrateurs peuvent consulter ces informations dans le profil de l’utilisateur, mais elles ne sont pas publiées ailleurs. Si un compte d’administrateur Azure inscrit son numéro de téléphone d’authentification, il est également renseigné dans le champ Téléphone mobile et est visible.

### <a name="number-of-authentication-methods-required"></a>Nombre de méthodes d’authentification requises

Cette option détermine le nombre minimal de méthodes d’authentification disponibles que l’utilisateur doit appliquer pour réinitialiser ou débloquer son mot de passe. Elle peut être définie sur 1 ou 2.

Les utilisateurs peuvent choisir de fournir plusieurs méthodes d’authentification si elles sont activées par l’administrateur.

Si un utilisateur n’a pas le nombre minimal requis de méthodes inscrites, une page d’erreur s’affiche et lui indique de contacter un administrateur pour réinitialiser son mot de passe.

### <a name="how-secure-are-my-security-questions"></a>Sécuriser mes questions de sécurité

Si vous utilisez des questions de sécurité, nous vous recommandons de les combiner à une autre méthode, car elles peuvent être moins sécurisées que d’autres méthodes étant donné que certaines personnes peuvent connaître les réponses aux questions d’autres utilisateurs.

> [!NOTE] 
> Les questions de sécurité sont stockées de façon privée et sécurisée dans un objet utilisateur du répertoire et ne peuvent être posées qu’aux utilisateurs lors de l’inscription. L’administrateur ne peut pas lire ni modifier les questions ou les réponses des utilisateurs.
>

### <a name="security-question-localization"></a>Localisation de la question de sécurité

Toutes les questions prédéfinies ci-après sont localisées dans l’ensemble des langues Office 365 en fonction des paramètres régionaux du navigateur de l’utilisateur.

* Dans quelle ville avez-vous rencontré votre premier(ère) époux/épouse ou compagnon/compagne ?
* Dans quelle ville vos parents se sont-ils rencontrés ?
* Dans quelle ville vit votre frère ou sœur le/la plus proche ?
* Dans quelle ville votre père est-il né ?
* Dans quelle ville était votre premier travail ?
* Dans quelle ville votre mère est-elle née ?
* Dans quelle ville étiez-vous pour le Nouvel an de l’année 2000 ?
* Quel était le nom de votre professeur préféré au lycée ?
* Quel est le nom d’un établissement d’enseignement supérieur auquel vous avez postulé mais auquel vous n’êtes finalement pas allé ?
* Quel est le nom de l’endroit où vous avez organisé la réception de votre premier mariage ?
* Quel est le deuxième prénom de votre père ?
* Quel est votre aliment préféré ?
* Quels sont les nom et prénom de votre grand-mère maternelle ?
* Quel est le deuxième prénom de votre mère ?
* Quel est le mois et l’année de naissance de votre frère/sœur aîné(e) ? (par exemple, novembre 1985)
* Quel est le deuxième prénom de votre frère/sœur aîné(e) ?
* Quels sont les nom et prénom de votre grand-père paternel ?
* Quel est le deuxième prénom de votre frère/sœur cadet(te) ?
* Où avez-vous effectué votre dernière année de l’école primaire ?
* Quels étaient les nom et prénom de votre meilleur ami quand vous étiez enfant ?
* Quels étaient les nom et prénom de votre premier(ère) petit(e) ami(e) ?
* Quel était le nom de votre instituteur préféré à l’école primaire ?
* Quels étaient la marque et le modèle de votre première voiture ou moto ?
* Quel était le nom de votre toute première école ?
* Quel était le nom de l’hôpital ou de la clinique où vous êtes né ?
* Quel était le nom de la rue de la première maison de votre enfance ?
* Quel était le nom de votre héros d’enfance ?
* Quel était le nom de votre peluche préférée ?
* Quel était le nom de votre premier animal domestique ?
* Quel était votre surnom quand vous étiez enfant ?
* Quel était votre sport préféré au lycée ?
* Quel a été votre premier travail ?
* Quels étaient les quatre derniers chiffres de votre numéro de téléphone quand vous étiez enfant ?
* Quand vous étiez petit, qu’auriez-vous voulu être ?
* Quelle est la personne la plus célèbre que vous ayez jamais rencontrée ?

### <a name="custom-security-questions"></a>Questions de sécurité personnalisées

Les questions de sécurité personnalisées ne sont pas localisées pour tous les paramètres régionaux. Toutes les questions personnalisées s’affichent dans la même langue que celle dans laquelle elles sont saisies dans l’interface utilisateur d’administration, même si les paramètres régionaux du navigateur de l’utilisateur sont différents. Si vous avez besoin de questions localisées, utilisez les questions prédéfinies.

La longueur maximale d’une question de sécurité personnalisée est de 200 caractères.

### <a name="security-question-requirements"></a>Conditions relatives aux questions de sécurité

* La limite minimale du nombre de caractères par réponse est de 3 caractères.
* La limite maximale du nombre de caractères par réponse est de 40 caractères.
* L’utilisateur ne peut pas répondre plusieurs fois à la même question.
* L’utilisateur ne peut pas fournir la même réponse à plusieurs questions.
* N’importe quel jeu de caractères peut être utilisé pour définir les questions et les réponses, y compris les caractères Unicode.
* Le nombre de questions défini doit être supérieur ou égal au nombre de questions requis pour l’inscription.

## <a name="registration"></a>Inscription

### <a name="require-users-to-register-when-signing-in"></a>Obliger les utilisateurs à s’inscrire durant la connexion ?

L’activation de cette option oblige un utilisateur pouvant demander la réinitialisation du mot de passe à s’inscrire à la réinitialisation du mot de passe s’il se connecte à des applications, comme celles qui suivent, à l’aide d’Azure AD :

* Office 365
* Portail Azure
* Volet d'accès
* Applications fédérées
* Applications personnalisées utilisant Azure AD

La désactivation de cette fonctionnalité permettra toujours aux utilisateurs d’inscrire manuellement leurs informations de contact en se rendant sur [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) ou en cliquant sur le lien **Réinitialiser mon mot de passe** sous l’onglet Profil dans le volet d’accès.

> [!NOTE]
> Les utilisateurs peuvent fermer le portail d’inscription de réinitialisation du mot de passe en cliquant sur Annuler ou en fermant la fenêtre, mais celui-ci s’affichera tout de même à chaque fois, tant qu’ils n’auront pas effectué l’inscription.
>

### <a name="number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification

Cette option détermine la période de temps entre la configuration et la reconfirmation des informations d’authentification, et est uniquement disponible si vous activez l’option **Obliger les utilisateurs à s’inscrire durant la connexion ?**.

Les valeurs valides sont comprises entre 0 et 730 jours. En définissant la valeur sur 0, vos utilisateurs ne seront jamais invités à reconfirmer leurs informations d’authentification

## <a name="notifications"></a>Notifications

### <a name="notify-users-on-password-resets"></a>Notifier les utilisateurs lors des réinitialisations de mot de passe ?

Si cette option est définie sur Oui, l’utilisateur qui réinitialise son mot de passe reçoit un e-mail l’avertissant que son mot de passe a été modifié via le portail de réinitialisation du mot de passe libre-service à son adresse de messagerie principale ou à son autre adresse de messagerie sur fichier dans Azure AD. Personne n’est informé de cet événement de réinitialisation.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notifier tous les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe ?

Si cette option est définie sur Oui, **tous les administrateurs** reçoivent un e-mail sur leur adresse de messagerie principale sur fichier dans Azure AD pour les informer qu’un autre administrateur a changé son mot de passe à l’aide de la réinitialisation du mot de passe libre-service.

Exemple : quatre administrateurs font partie d’un environnement. L’administrateur A réinitialise son mot de passe à l’aide de la réinitialisation du mot de passe libre-service. Les administrateurs B, C et D reçoivent un e-mail les informant de cet évènement.

## <a name="on-premises-integration"></a>Intégration locale

Si vous avez installé, configuré et activé Azure AD Connect, vous disposerez d’options supplémentaires pour les intégrations locales.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Écriture différée des mots de passe dans votre répertoire local

Contrôle si l’écriture différée du mot de passe est activée ou non pour ce répertoire. Si elle l’est, indique l’état du service d’écriture différée local. Cela peut être utile si vous voulez désactiver temporairement l’écriture différée du mot de passe sans reconfigurer Azure AD Connect.

* Si le commutateur est défini sur Oui, l’écriture différée est activée et les utilisateurs fédérés et synchronisés par hachage du mot de passe pourront réinitialiser leur mot de passe.
* Si le commutateur est défini sur Non, l’écriture différée est désactivée et les utilisateurs fédérés et synchronisés par hachage du mot de passe ne pourront pas réinitialiser leur mot de passe.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Autoriser les utilisateurs à déverrouiller les comptes sans réinitialiser leur mot de passe

Indique si les utilisateurs qui visitent le portail de réinitialisation de mot de passe doivent ou non avoir la possibilité de déverrouiller leurs comptes Active Directory locaux sans réinitialiser leur mot de passe. Par défaut, Azure AD déverrouille toujours les comptes sans procéder à la réinitialisation de mot de passe. Ce paramétrage permet donc de différencier ces deux opérations. 

* Si la valeur est « Oui », les utilisateurs auront la possibilité de réinitialiser leur mot de passe et de déverrouiller le compte ou de déverrouiller celui-ci sans réinitialiser le mot de passe.
* Si la valeur est « Non », les utilisateurs devront réinitialiser leur mot de passe lorsqu’ils déverrouilleront leur compte.

## <a name="network-requirements"></a>Configuration requise pour le réseau

### <a name="firewall-rules"></a>Règles de pare-feu

[URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

Pour Azure AD Connect version 1.1.443.0 et versions ultérieures, vous avez besoin d’un accès HTTPS sortant à ce qui suit :
* passwordreset.microsoftonline.com
* servicebus.windows.net

Pour un accès plus granulaire, vous trouverez [ici](https://www.microsoft.com/download/details.aspx?id=41653) la liste mise à jour des plages d’adresses IP du centre de données Microsoft Azure, actualisée tous les mercredis et appliquée le lundi qui suit.

### <a name="idle-connection-timeouts"></a>Délais d’expiration de connexion inactive

L’outil Azure AD Connect envoie des pings/appels keepalives périodiques aux points de terminaison ServiceBus pour garantir que les connexions restent actives. Si l’outil détecte la suppression d’un trop grand nombre de connexions, il augmente automatiquement la fréquence des pings adressés au point de terminaison. L’« intervalle de pings » le plus faible possible est de 1 ping toutes les 60 secondes. Toutefois, nous recommandons vivement de configurer les proxys/pare-feux pour qu’ils autorisent les connexions inactives pendant au moins 2 à 3 minutes. *Pour les versions antérieures, nous suggérons 4 minutes ou plus.

## <a name="active-directory-permissions"></a>Autorisations Active Directory

Le compte spécifié dans l’utilitaire Azure AD Connect doit avoir des autorisations de réinitialisation de mot de passe, de modification de mot de passe, d’écriture sur lockoutTime et des autorisations d’écriture sur pwdLastSet, des droits étendus sur l’objet racine de **chaque domaine** de cette forêt **OU** sur les unités d’organisation utilisateur que vous souhaitez intégrer à la réinitialisation du mot de passe libre-service.

Si vous ignorez de quel compte il s’agit exactement, ouvrez l’interface utilisateur de configuration d’Azure Active Directory Connect, puis cliquez sur l’option Afficher la configuration actuelle. Le compte auquel vous devez ajouter l’autorisation est répertorié sous « Annuaires synchronisés ».

La définition de ces autorisations permet au compte de service de l’agent de gestion de chaque forêt de gérer les mots de passe pour les comptes d’utilisateur de cette forêt. **Sans ces autorisations, même si l’écriture différée semble configurée correctement, les utilisateurs rencontreront des erreurs en essayant de gérer leurs mots de passe locaux à partir du cloud.**

> [!NOTE]
> La réplication de ces autorisations pour tous les objets peut durer plusieurs heures.
>

Pour configurer les autorisations appropriées pour que l’écriture différée du mot de passe ait lieu :

1. Ouvrez Utilisateurs et ordinateurs Active Directory avec un compte ayant les autorisations d’administration de domaine appropriées.
2. Dans le menu Affichage, assurez-vous que l’option Fonctionnalités avancées est activée.
3. Dans le volet gauche, cliquez avec le bouton droit sur l’objet représentant la racine du domaine et sélectionnez Propriétés.
    * Cliquez sur l’onglet Sécurité.
    * Puis, cliquez sur Avancé.
4. Dans l’onglet Autorisations, cliquez sur Ajouter.
5. Sélectionnez le compte auquel les autorisations sont appliquées (à partir de la configuration Azure AD Connect).
6. Dans le menu déroulant S’applique à, sélectionnez Objets utilisateur descendants.
7. Sous Autorisations, activez les cases correspondant aux éléments suivants :
    * Ne pas faire expirer le mot de passe
    * Réinitialiser le mot de passe
    * Modifier le mot de passe
    * Écrire lockoutTime
    * Écrire pwdLastSet
8. Cliquez sur Appliquer/OK pour appliquer et fermer les boîtes de dialogue ouvertes.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Comment fonctionne la réinitialisation du mot de passe pour les utilisateurs B2B ?
La réinitialisation et la modification du mot de passe sont totalement compatibles avec toutes les configurations B2B.  Lisez ce qui suit pour en savoir plus sur les trois cas B2B explicites pris en charge par la réinitialisation du mot de passe.

1. **Utilisateurs d’une organisation partenaire disposant d’un locataire Azure AD** : si l’organisation avec laquelle vous avez un partenariat dispose d’un locataire Azure AD, nous **respectons les stratégies de réinitialisation du mot de passe activées dans ce locataire**. Pour que la réinitialisation du mot de passe fonctionne, l’organisation partenaire doit simplement s’assurer qu’Azure AD SSPR est activé (ce qui est gratuit pour les clients O365) et peut être activé en suivant la procédure de notre guide [Prise en main de la gestion de mot de passe](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
2. **Utilisateurs qui se sont inscrits dans le cadre de [l’inscription libre-service](active-directory-self-service-signup.md)** : si l’organisation avec laquelle vous avez un partenariat a utilisé la fonctionnalité [d’inscription en libre-service](active-directory-self-service-signup.md) pour accéder à un locataire, nous l’autorisons à réinitialiser le mot de passe en indiquant l’adresse de messagerie qu’elle a utilisée pour l’inscription.
3. **Utilisateurs B2B** : tous les utilisateurs B2B créés à l’aide des nouvelles [fonctionnalités B2B d’Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md) peuvent réinitialiser leur mot de passe en indiquant l’adresse de messagerie utilisée pour l’inscription.

Pour tester ces méthodes avec l’un des utilisateurs partenaires, consultez la page http://passwordreset.microsoftonline.com. Tant que ces utilisateurs disposent d’une autre adresse de messagerie ou d’un e-mail d’authentification, la réinitialisation du mot de passe fonctionne comme prévu.

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [**Démarrage rapide**](active-directory-passwords-getting-started.md) : soyez rapidement opérationnel avec la gestion des mots de passe en libre-service Azure AD. 
* [**Licences**](active-directory-passwords-licensing.md) : configurez vos licences Azure AD.
* [**Données**](active-directory-passwords-data.md) : comprenez mieux les données requises et leur utilisation dans la gestion des mots de passe.
* [**Déploiement**](active-directory-passwords-best-practices.md) : planifiez et déployez la réinitialisation du mot de passe libre-service sur vos utilisateurs grâce aux conseils figurant ici.
* [**Stratégie**](active-directory-passwords-policy.md) : comprenez et définissez les stratégies de mot de passe d’Azure AD.
* [**Écriture différée du mot de passe**](active-directory-passwords-writeback.md) : fonctionnement de l’écriture différée du mot de passe avec votre répertoire local.
* [**Personnalisation**](active-directory-passwords-customize.md) : personnalisez l’apparence de l’interface de réinitialisation du mot de passe libre-service de votre société.
* [**Rapports**](active-directory-passwords-reporting.md) : découvrez si, quand et où vos utilisateurs accèdent aux fonctionnalités de réinitialisation du mot de passe libre-service.
* [**Forum Aux Questions (FAQ)**](active-directory-passwords-faq.md) - Comment ? Pourquoi ? Quoi ? Où ? Qui ? Quand ? - Les réponses aux questions que vous vouliez poser depuis toujours.
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec la réinitialisation de mot de passe en libre-service.


