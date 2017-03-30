---
title: "En savoir plus : gestion des mots de passe Azure Active Directory | Microsoft Docs"
description: "Rubriques avancées sur la gestion des mots de passe Azure AD, y compris le fonctionnement de l’écriture différée du mot de passe, la sécurité de l’écriture différée du mot de passe, le fonctionnement du portail de réinitialisation de mot de passe et les données utilisées par la réinitialisation de mot de passe."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: d3be2912-76c8-40a0-9507-b7b1a7ce2f8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 0de0590c1cf5c71a7174fdcca84847b378aa40f8
ms.lasthandoff: 03/15/2017


---
# <a name="learn-more-about-password-management"></a>En savoir plus sur la gestion des mots de passe
> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password).
>
>

Si vous avez déjà déployé la gestion des mots de passe ou si vous souhaitez simplement en savoir plus sur les aspects techniques avant de procéder au déploiement, cette section offre une vue d’ensemble parfaite. Nous traitons les aspects suivants :

* [**Fonctionnement du portail de réinitialisation de mot de passe**](#how-does-the-password-reset-portal-work)
* [**Vue d’ensemble de l’écriture différée de mot de passe**](#password-writeback-overview)
 * [Fonctionnement de l’écriture différée de mot de passe](#how-password-writeback-works)
* [**Scénarios pris en charge par l’écriture différée du mot de passe**](#scenarios-supported-for-password-writeback)
 * [Prise en charge du client Azure AD Connect, Azure AD Sync et DirSync](#supported-clients)
 * [Licences requises pour l’écriture différée du mot de passe](#licenses-required-for-password-writeback)
 * [Modes d’authentification locale pris en charge pour l’écriture différée du mot de passe](#on-premises-authentication-modes-supported-for-password-writeback)
 * [Opérations de l’utilisateur et de l’administrateur prises en charge pour l’écriture différée du mot de passe](#user-and-admin-operations-supported-for-password-writeback)
 * [Opérations de l’utilisateur et de l’administrateur non prises en charge pour l’écriture différée du mot de passe](#user-and-admin-operations-not-supported-for-password-writeback)
* [**Modèle de sécurité de l’écriture différée du mot de passe**](#password-writeback-security-model)
 * [Informations détaillées sur le chiffrement de l’écriture différée du mot de passe](#password-writeback-encryption-details)
 * [Utilisation de la bande passante de l’écriture différée du mot de passe](#password-writeback-bandwidth-usage)
* [**Déploiement, gestion et accès aux données de réinitialisation des mots de passe pour vos utilisateurs**](#deploying-managing-and-accessing-password-reset-data-for-your-users)
 * [Données utilisées par la réinitialisation de mot de passe](#what-data-is-used-by-password-reset)
 * [Déploiement de la réinitialisation du mot de passe sans demander l’inscription de l’utilisateur final](#deploying-password-reset-without-requiring-end-user-registration)
 * [Que se passe-t-il lorsqu’un utilisateur s’inscrit pour la réinitialisation du mot de passe ?](#what-happens-when-a-user-registers)
 * [Comment accéder aux données de réinitialisation des mots de passe pour vos utilisateurs](#how-to-access-password-reset-data-for-your-users)
 * [Configuration de données de réinitialisation du mot de passe avec PowerShell](#setting-password-reset-data-with-powershell)
 * [Lecture de données de réinitialisation du mot de passe avec PowerShell](#reading-password-reset-data-with-powershell)
* [**Comment fonctionne la réinitialisation du mot de passe pour les utilisateurs B2B ?**](#how-does-password-reset-work-for-b2b-users)

## <a name="how-does-the-password-reset-portal-work"></a>Fonctionnement du portail de réinitialisation de mot de passe
Quand un utilisateur accède au portail de réinitialisation de mot de passe, un flux de travail est lancé pour déterminer si ce compte d’utilisateur est valide, l’organisation à laquelle cet utilisateur appartient, où est géré le mot de passe de cet utilisateur et si l’utilisateur dispose d’une licence pour utiliser la fonctionnalité.  Lisez les étapes ci-dessous pour en savoir plus sur la logique sous-jacente à la page de réinitialisation de mot de passe.

1. L’utilisateur clique sur le lien Vous ne parvenez pas à accéder à votre compte ? ou il accède directement à [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2. L’utilisateur entre un ID utilisateur et passe un test CAPTCHA.
3. Azure AD vérifie si l’utilisateur peut utiliser cette fonctionnalité en procédant comme suit :
   * Il vérifie que cette fonctionnalité est activée pour l’utilisateur et qu’il possède une licence Azure AD.
     * Si ce n’est pas le cas, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
   * Vérifie que l’utilisateur a défini les données de test appropriées sur son compte, conformément à la stratégie de l’administrateur.
     * Si la stratégie n’exige qu’un seul test, AAD vérifie que l’utilisateur a défini les données appropriées pour au moins un des tests activés par la stratégie de l’administrateur.
       * Si l’utilisateur n’est pas configuré, il est invité à contacter son administrateur pour réinitialiser son mot de passe.
     * Si la stratégie exige deux tests, AAD vérifie que l’utilisateur a défini les données appropriées pour au moins deux des tests activés par la stratégie de l’administrateur.
       * Si l’utilisateur n’est pas configuré, il est invité à contacter son administrateur pour réinitialiser son mot de passe.
   * Il vérifie si le mot de passe de l’utilisateur est géré localement (fédération ou synchronisation de hachage de mot de passe).
     * Si l’écriture différée est déployée et que le mot de passe est géré localement, l’utilisateur est autorisé à s’authentifier et à réinitialiser son mot de passe.
     * Si l’écriture différée n’est pas déployée et que le mot de passe est géré localement, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
4. S’il est établi que l’utilisateur est en mesure de réinitialiser son mot de passe, il reçoit alors des instructions pour mener à bien le processus de réinitialisation.

Pour en savoir plus sur le déploiement de l’écriture différée du mot de passe, consultez la page [Prise en main de la gestion des mots de passe Azure AD](active-directory-passwords-getting-started.md).

## <a name="password-writeback-overview"></a>Vue d’ensemble de l’écriture différée de mot de passe
L’écriture différée de mot de passe est un composant [Azure Active Directory Connect](connect/active-directory-aadconnect.md) qui peut être activé et utilisé par les abonnés actifs d’Azure Active Directory Premium. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

L’écriture différée de mot de passe vous permet de configurer votre client cloud pour l’écriture différée des mots de passe vers l’environnement Active Directory local.  Cela vous évite d’avoir à configurer et à gérer une solution de réinitialisation de mot de passe libre-service locale complexe en offrant à vos utilisateurs un moyen pratique via le cloud de réinitialiser leurs mots de passe locaux, où qu’ils se trouvent.  Voici certaines fonctionnalités importantes de l’écriture différée de mot de passe :

* **Retour d’informations immédiat.**  L’écriture différée de mot de passe est une opération synchrone.  Vos utilisateurs sont informés immédiatement si leur mot de passe ne remplit pas les critères des stratégies ou s’il n’a pas pu être réinitialisé ou modifié pour une raison quelconque.
* **Prise en charge de la réinitialisation des mots de passe pour les utilisateurs qui ont recours aux services AD FS ou à d’autres technologies de fédération.**  Avec l’écriture différée des mots de passe, tant que les comptes des utilisateurs fédérés sont synchronisés dans votre client Azure AD, ils sont en mesure de gérer leurs mots de passe AD locaux à partir du cloud.
* **Prise en charge de la réinitialisation des mots de passe pour les utilisateurs qui ont recours à la synchronisation de hachage de mot de passe.** Lorsque le service de réinitialisation des mots de passe détecte qu’un compte d’utilisateur synchronisé est activé pour la synchronisation de hachage de mot de passe, le mot de passe local de ce compte et le mot de passe du cloud sont tous deux réinitialisés simultanément.
* **Prise en charge de la modification des mots de passe à partir du volet d’accès et d’Office 365.**  Lorsque des utilisateurs fédérés ou disposant de la synchronisation du hachage de mot de passe modifient leurs mots de passe (ayant expiré ou non), nous réécrivons ces mots de passe dans votre environnement Active Directory local.
* **Prise en charge de l’écriture différée du mot de passe quand un administrateur les réinitialise à partir du** [**Portail de gestion Azure**](https://manage.windowsazure.com).  Chaque fois qu’un administrateur réinitialise le mot de passe d’un utilisateur dans le [portail de gestion Azure](https://manage.windowsazure.com), si cet utilisateur est fédéré ou dispose de la synchronisation de mot de passe, nous définissons également le mot de passe sélectionné par l’administrateur sur votre annuaire AD local.  Cette fonctionnalité n’est pas prise en charge actuellement dans le portail d’administration Office.
* **Application de vos stratégies de mot de passe AD locales.**  Lorsqu’un utilisateur réinitialise son mot de passe, nous vérifions qu’il est conforme à votre stratégie AD locale avant de le valider dans cet annuaire.  Cela comprend l’historique, la complexité, l’âge, les filtres de mot de passe et toute autre restriction de mot de passe que vous avez définie dans votre annuaire Active Directory local.
* **Aucune règle de pare-feu pour le trafic entrant requise.**  L’écriture différée des mots de passe utilise Azure Service Bus Relay comme canal de communication sous-jacent, ce qui signifie que vous n’avez pas à ouvrir de ports entrants sur votre pare-feu pour que cette fonctionnalité marche correctement. Seul le port 443 sortant doit être ouvert.
* **Aucune prise en charge pour les comptes d’utilisateur qui existent dans des groupes protégés dans votre annuaire Active Directory local.** Pour plus d’informations sur les groupes protégés, consultez la page [Comptes et groupes protégés dans Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Fonctionnement de l’écriture différée des mots de passe
L’écriture différée des mots de passe comporte trois composants principaux :

* Service cloud de réinitialisation de mot de passe (également intégré aux pages de modification de mot de passe d’Azure AD)
* Azure Service Bus Relay spécifique au client
* Point de terminaison de réinitialisation de mot de passe local

Ces trois composants interagissent comme décrit dans le schéma ci-dessous :

  ![][001]

Lorsqu’un utilisateur fédéré ou disposant de la synchronisation du hachage de mot de passe réinitialise ou modifie son mot de passe dans le cloud, les événements suivants ont lieu :

1. Nous vérifions le type de mot de passe de l’utilisateur.  Si nous constatons que le mot de passe est géré localement, nous nous assurons que le service d’écriture différée est en cours d’exécution.  Si c’est le cas, nous laissons l’utilisateur poursuivre. Dans le cas contraire, nous signalons à l’utilisateur que son mot de passe ne peut pas être réinitialisé ici.
2. Ensuite, l’utilisateur passe par les portails d’authentification appropriés et atteint l’écran de réinitialisation de mot de passe.
3. L’utilisateur sélectionne un nouveau mot de passe et le confirme.
4. Lorsqu’il clique sur Envoyer, nous chiffrons le mot de passe en texte brut avec une clé publique qui a été créée durant le processus d’installation de l’écriture différée.
5. Après avoir chiffré le mot de passe, nous l’incluons dans une charge utile qui est envoyée via un canal HTTPS à votre relais Service Bus spécifique au client (que nous configurons également pour vous lors du processus d’installation de l’écriture différée).  Ce relais est protégé par un mot de passe généré de manière aléatoire, connu uniquement de votre installation locale.
6. Une fois que le message a atteint Service Bus, le point de terminaison de réinitialisation de mot de passe sort de veille automatiquement et découvre qu’une demande de réinitialisation est en attente.
7. Le service recherche alors l’utilisateur en question à l’aide de l’attribut d’ancrage de cloud.  Pour que cette recherche réussisse, l’objet utilisateur doit exister dans l’espace de connecteur Active Directory et il doit être lié à l’objet MV correspondant et à l’objet de connecteur AAD correspondant. Pour finir, pour que la synchronisation trouve ce compte d’utilisateur, le lien de l’objet de connecteur Active Directory à MV doit avoir la règle de synchronisation `Microsoft.InfromADUserAccountEnabled.xxx` .  Celle-ci est nécessaire, car lorsque l’appel provient du cloud, le moteur de synchronisation utilise l’attribut cloudAnchor pour rechercher l’objet d’espace de connecteur AAD, puis il suit le lien vers l’objet MV, puis le lien vers l’objet Active Directory. Comme il peut y avoir plusieurs objets Active Directory (plusieurs forêts) pour le même utilisateur, le moteur de synchronisation s’appuie sur le lien `Microsoft.InfromADUserAccountEnabled.xxx` pour choisir celui qui convient. Remarque : en raison de cette logique, vous devez connecter Azure AD Connect au contrôleur de domaine principal pour que l’écriture différée de mot de passe fonctionne.  Si vous avez besoin de le faire, vous pouvez configurer Azure AD Connect pour utiliser un émulateur de contrôleur de domaine principal en cliquant avec le bouton droit sur les **propriétés** du connecteur de synchronisation Active Directory, puis en sélectionnant **configurer des partitions d’annuaire**. À partir de là, recherchez la section **domain controller connection settings** (paramètres de connexion de contrôleur de domaine) et cochez la case intitulée **only use preferred domain controllers** (utiliser uniquement les contrôleurs de domaine préférés). Remarque : si le contrôleur de domaine préféré n’est pas un émulateur de contrôleur de domaine principal, Azure AD Connect cherche toujours à atteindre le contrôleur de domaine principal pour l’écriture différée du mot de passe.
8. Une fois le compte d’utilisateur trouvé, nous tentons de réinitialiser le mot de passe directement dans la forêt Active Directory appropriée.
9. Si l’opération de définition du mot de passe réussit, nous signalons à l’utilisateur que son mot de passe a été modifié et qu’il peut continuer.
10. Si l’opération de définition du mot de passe échoue, nous renvoyons l’erreur à l’utilisateur et le laissons réessayer.  L’opération peut échouer parce que le service est arrêté, parce que le mot de passe qu’il a sélectionné ne remplit pas les critères des stratégies de l’organisation, parce que nous n’avons pas trouvé l’utilisateur dans l’annuaire Active Directory local ou pour différentes autres raisons.  Nous avons un message spécifique pour la plupart de ces cas de figure et nous indiquons à l’utilisateur ce qu’il peut faire pour résoudre le problème.

## <a name="scenarios-supported-for-password-writeback"></a>Scénarios pris en charge par l’écriture différée de mot de passe
La section ci-dessous décrit les scénarios pris en charge pour les différentes versions de nos fonctions de synchronisation.  En général, nous vous recommandons vivement d’utiliser la fonctionnalité de mise à jour automatique d’Azure AD Connect ou d’installer la dernière version d’[Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect) si vous souhaitez utiliser l’écriture différée du mot de passe.

* [**Prise en charge des clients Azure AD Connect, Azure AD Sync et DirSync**](#supported-clients)
* [**Licences requises pour l’écriture différée du mot de passe**](#licenses-required-for-password-writeback)
* [**Modes d’authentification locale pris en charge pour l’écriture différée du mot de passe**](#on-premises-authentication-modes-supported-for-password-writeback)
* [**Opérations de l’utilisateur et de l’administrateur prises en charge pour l’écriture différée du mot de passe**](#user-and-admin-operations-supported-for-password-writeback)
* [**Opérations de l’utilisateur et de l’administrateur non prises en charge pour l’écriture différée du mot de passe**](#user-and-admin-operations-not-supported-for-password-writeback)

### <a name="supported-clients"></a>Clients pris en charge
Nous vous recommandons vivement d’utiliser la fonctionnalité de mise à jour automatique d’Azure AD Connect ou d’installer la dernière version d’[Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect) si vous souhaitez utiliser l’écriture différée du mot de passe.

* **DirSync (versions supérieures à 1.0.6862)** - _NON PRIS EN CHARGE_ : prend en charge uniquement les fonctionnalité d’écriture différée de base et n’est plus pris en charge par le groupe de produits 
* **Azure AD Sync** - _OBSOLÈTE_ : prend en charge uniquement les fonctionnalités d’écriture différée mais n’intègre pas les fonctionnalités de déverrouillage de compte, la journalisation détaillée et les améliorations de la fiabilité d’Azure AD Connect. C’est pourquoi nous recommandons **vivement** d’effectuer la mise à niveau.
* **Azure AD Connect** - _PRISE EN CHARGE COMPLÈTE_ : prend en charge toutes les fonctionnalités d’écriture différée. Effectuez la mise à niveau vers la dernière version pour bénéficier des nouvelles fonctionnalités optimisées ainsi que d’une stabilité et d’une fiabilité optimales

### <a name="licenses-required-for-password-writeback"></a>Licences requises pour l’écriture différée du mot de passe
Pour utiliser l’écriture différée du mot de passe, vous devez avoir attribué une des licences suivantes dans votre locataire.

* **Azure AD Premium P1** : aucune limitation à l’utilisation de l’écriture différée du mot de passe
* **Azure AD Premium P2** : aucune limitation à l’utilisation de l’écriture différée du mot de passe
* **Enterprise Moblity Suite** : aucune limitation à l’utilisation de l’écriture différée du mot de passe
* **Enterprise Cloud Suite** : aucune limitation à l’utilisation de l’écriture différée du mot de passe

Vous ne pouvez pas utiliser l’écriture différée du mot de passe avec un plan de licence Office 365, qu’il s’agisse de la version d’essai ou payante. Pour utiliser cette fonctionnalité, vous devez effectuer une mise à niveau vers l’un des plans ci-dessus. 

Nous n’avons pas l’intention d’activer l’écriture différée du mot de passe pour toutes les références Office 365.

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>Modes d’authentification locale pris en charge pour l’écriture différée de mot de passe
L’écriture différée du mot de passe fonctionne pour les types de mots de passe utilisateur suivants :

* **Utilisateurs cloud uniquement** : l’écriture différée du mot de passe ne s’applique pas dans cette situation, faute de mot de passe local
* **Utilisateurs dont le mode passe est synchronisé** : écriture différée du mot de passe prise en charge
* **Utilisateurs fédérés** : écriture différée du mot de passe prise en charge
* **Utilisateurs à authentification directe** : écriture différée du mot de passe prise en charge

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>Opérations de l’utilisateur et de l’administrateur prises en charge pour l’écriture différée du mot de passe
Les mots de passe sont écrits en différé dans les situations suivantes :

* **Opérations de l’utilisateur final prises en charge**
 * Toute modification volontaire en libre-service du mot de passe de l’utilisateur final
 * Toute modification forcée en libre-service du mot de passe de l’utilisateur final (par exemple expiration du mot de passe)
 * Toute réinitialisation du mot de passe en libre-service de l’utilisateur final émanant du [portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com)
* **Opérations de l’administrateur final prises en charge**
 * Toute modification volontaire en libre-service du mot de passe de l’administrateur
 * Toute modification forcée en libre-service du mot de passe de l’administrateur (par exemple expiration du mot de passe)
 * Toute réinitialisation du mot de passe en libre-service de l’administrateur émanant du [portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com)
 * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis le [portail de gestion Azure Classic](https://manage.windowsazure.com)
 * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis le [portail Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>Opérations de l’utilisateur et de l’administrateur non prises en charge pour l’écriture différée du mot de passe
Les mots de passe ne sont pas écrits en différé dans les situations suivantes :

* **Opérations de l’utilisateur final non prises en charge**
 * Tout utilisateur réinitialisant son mot de passe à l’aide de PowerShell (version 1 ou version 2) ou l’API Azure AD Graph
* **Opérations de l’administrateur non prises en charge**
 * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis le [portail de gestion Office](https://portal.office.com)
 * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis PowerShell (versions 1 et 2) ou l’API Azure AD Graph
 
Nous travaillons à supprimer ces limitations mais nous n’avons pas encore de calendrier de publication des améliorations.

## <a name="password-writeback-security-model"></a>Modèle de sécurité de l’écriture différée de mot de passe
L’écriture différée des mots de passe est un service hautement sécurisé et robuste.  Pour garantir la protection de vos informations, nous activons un modèle de sécurité à quatre niveaux, décrit ci-dessous.

* **Relais Service Bus spécifique du client** : lorsque vous configurez le service, nous définissons un relais Service Bus spécifique au client et protégé par un mot de passe fort généré de manière aléatoire, auquel Microsoft n’a jamais accès.
* **Clé de chiffrement de mot de passe forte et verrouillée** : une fois le relais Service Bus créé, nous créons une paire de clés asymétriques forte qui nous permet de chiffrer le mot de passe lorsqu’il arrive sur le réseau.  Cette clé réside uniquement dans le magasin de secrets de votre entreprise dans le cloud, qui est fortement verrouillé et audité, comme n’importe quel mot de passe de l’annuaire.
* **Sécurité de couche de transport (TLS) standard** : lorsqu’une opération de réinitialisation ou de modification de mot de passe a lieu dans le cloud, nous prenons le mot de passe et nous le chiffrons avec votre clé publique.  Ensuite, nous insérons cela dans un message HTTPS envoyé à votre relais Service Bus via un canal chiffré à l’aide de certificats SSL Microsoft.  Une fois ce message arrivé dans Service Bus, votre agent local sort de veille, s’authentifie auprès de Service Bus à l’aide du mot de passe fort généré précédemment, récupère le message chiffré, le déchiffre à l’aide de la clé privée que nous avons générée et essaie ensuite de définir le mot de passe via l’API SetPassword AD DS.  C’est cette étape qui nous permet d’appliquer votre stratégie de mot de passe Active Directory local (complexité, âge, historique, filtres, etc.) dans le cloud.
* **Stratégies d’expiration de message** : pour finir, si pour une raison quelconque le message reste dans Service Bus car votre service local est arrêté, le message est supprimé après quelques minutes, afin d’accroître encore davantage la sécurité.

### <a name="password-writeback-encryption-details"></a>Informations détaillées sur le chiffrement de l’écriture différée de mot de passe
Voici la description des étapes de chiffrement de la procédure de réinitialisation du mot de passe exécutée par un utilisateur avant que celle-ci parvienne dans votre environnement local, et ce, afin d’assurer une fiabilité et une sécurité de service maximales.

* **Étape 1 - Chiffrement du mot de passe avec la clé RSA de 2 048 bits** : lorsqu’un utilisateur envoie un mot de passe en vue de l’écriture différée au niveau local, celui-ci est préalablement chiffré avec une clé RSA de 2 048 bits. 

* **Étape 2 - Chiffrement de niveau package avec AES-GCM** : la méthode AES-GCM permet de chiffrer le package en totalité (mot de passe et métadonnées requises). Cela empêche toute personne ayant un accès direct au canal ServiceBus sous-jacent de visualiser/compromettre le contenu. 

* **Étape 3 - Toutes les communications passent par une connexion TLS / SSL** : en outre, toutes les communications avec ServiceBus transitent par un canal SSL/TLS. Cela protège le contenu contre les tiers non autorisés.

* **Étape 4 - Renouvellement automatique des clés tous les 6 mois** - Enfin, tous les 6 mois de façon automatique ou chaque fois que l’écriture différée du mot de passe est désactivée/réactivée sur Azure AD Connect, nous renouvelons toutes ces clés afin d’assurer la sécurité et la protection maximales du service.

### <a name="password-writeback-bandwidth-usage"></a>Utilisation de la bande passante de l’écriture différée du mot de passe

L’écriture différée du mot de passe est un service de bande passante très basse qui renvoie des demandes à l’agent local uniquement dans les circonstances suivantes :

1. Deux messages envoyés lors de l’activation ou de la désactivation de la fonction via Azure AD Connect.
2. Un message est envoyé une fois toutes les 5 minutes comme une pulsation du service tant que le service est en cours d’exécution.
3. Deux messages sont envoyés chaque fois qu’un nouveau mot de passe est envoyé : un message en tant que demande pour effectuer l’opération, et un autre message qui contient le résultat de l’opération. Ces messages sont envoyés dans les circonstances suivantes.
4. Chaque fois qu’un nouveau mot de passe est soumis pendant une réinitialisation de mot de passe libre-service par l’utilisateur.
5. Chaque fois qu’un nouveau mot de passe est soumis pendant une opération de modification de mot de passe par l’utilisateur.
6. Chaque fois qu’un nouveau mot de passe est soumis au cours d’une réinitialisation de mot de passe utilisateur initiée par un administrateur (depuis les portails d’administration Azure uniquement)

#### <a name="message-size-and-bandwidth-considerations"></a>Considérations relatives à la taille des messages et à la bande passante

La taille de chacun des messages décrits ci-dessus est généralement inférieure à 1 Ko, ce qui signifie que, même sous des charges extrêmes, le service de l’écriture différée du mot de passe lui-même utilise au maximum quelques kilobits par seconde de bande passante. Étant donné que chaque message est envoyé en temps réel, uniquement lorsqu’une opération de mise à jour du mot de passe le requiert, et étant donné que le message est très petit, l’utilisation de la bande passante de la fonctionnalité d’écriture différée est effectivement trop petite pour produire un impact mesurable réel.

## <a name="deploying-managing-and-accessing-password-reset-data-for-your-users"></a>Déploiement, gestion et accès aux données de réinitialisation des mots de passe pour vos utilisateurs
Vous pouvez gérer et consulter les données de réinitialisation du mot de passe pour vos utilisateurs en vous appuyant sur Azure AD Connect, PowerShell, Graph ou nos expériences d’inscription.  Vous pouvez même déployer la réinitialisation du mot de passe dans toute votre organisation sans que les utilisateurs doivent s’inscrire en utilisant les options décrites ci-dessous.

  * [Données utilisées par la réinitialisation de mot de passe](#what-data-is-used-by-password-reset)
  * [Déploiement de la réinitialisation du mot de passe sans demander l’inscription de l’utilisateur final](#deploying-password-reset-without-requiring-end-user-registration)
  * [Que se passe-t-il lorsqu’un utilisateur s’inscrit pour la réinitialisation du mot de passe ?](#what-happens-when-a-user-registers)
  * [Comment accéder aux données de réinitialisation des mots de passe pour vos utilisateurs](#how-to-access-password-reset-data-for-your-users)
  * [Configuration de données de réinitialisation du mot de passe avec PowerShell](#setting-password-reset-data-with-powershell)
  * [Lecture de données de réinitialisation du mot de passe avec PowerShell](#reading-password-reset-data-with-powershell)

### <a name="what-data-is-used-by-password-reset"></a>Données utilisées par la réinitialisation de mot de passe
Le tableau suivant indique où et comment ces données sont utilisées lors de la réinitialisation de mot de passe. Il a pour but de vous aider à choisir les options d’authentification les plus appropriées pour votre organisation. Ce tableau recense également les formats à respecter si vous fournissez des données provenant de chemins d’entrée qui ne valident pas ces données pour le compte d’utilisateurs.

> [!NOTE]
> Le téléphone professionnel n’apparaît pas dans le portail d’inscription, car les utilisateurs ne sont pas actuellement en mesure de modifier cette propriété dans l’annuaire. Seuls les administrateurs peuvent définir cette valeur.
>
>

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nom de la méthode de contact</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Élément de données Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Élément de données Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Utilisé/définissable où ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Format requis</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Téléphone de bureau</p>
            </td>
            <td>
              <p>telephoneNumber</p>
              <p>Cette propriété peut être synchronisée avec l’attribut PhoneNumber dans Azure Active Directory et immédiatement utilisée pour la réinitialisation du mot de passe SANS qu’un utilisateur doive s’inscrire préalablement.</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>Par exemple, Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Utilisés dans :</p>
              <p>Portail de réinitialisation du mot de passe</p>
              <p>Peut être défini à partir de :</p>
              <p>PhoneNumber peut être défini à partir de PowerShell, de DirSync, du portail de gestion Azure et du portail d’administration Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (par exemple, + 1 1234567890)</p>
              <ul>
                <li class="unordered">
Doit contenir l’indicatif du pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit contenir l’indicatif régional (le cas échéant)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit contenir le symbole + devant l’indicatif du pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit contenir un espace entre l’indicatif du pays et le reste du numéro<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Les extensions ne sont pas prises en charge. Si des extensions sont spécifiées, elles seront supprimées avant la répartition de l’appel téléphonique.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Téléphone mobile</p>
            </td>
            <td>
              <p>Mobile</p>
              <p>Cette propriété peut être synchronisée avec l’attribut MobilePhone dans Azure Active Directory et immédiatement utilisée pour la réinitialisation du mot de passe SANS qu’un utilisateur doive s’inscrire préalablement.</p>
              <p>Pour l’instant, il n’est pas possible de synchroniser cette propriété avec AuthenticationPhone.</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OU</p>
              <p>MobilePhone</p>
              <p>(AuthenticationPhone est utilisé si des données sont présentes. Sinon, le champ Téléphone portable est utilisé).</p>
              <p>Par exemple, Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Utilisés dans :</p>
              <p>Portail de réinitialisation du mot de passe</p>
              <p>Portail d’inscription</p>
              <p>Peut être défini à partir de : </p>
              <p>AuthenticationPhone peut être défini à partir du portail d’inscription de réinitialisation de mot de passe ou du portail d’inscription MFA.</p>
              <p>MobilePhone peut être défini à partir de PowerShell, d’Azure AD Connect, du portail de gestion Azure et du portail d’administration Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (par exemple, + 1 1234567890)</p>
              <ul>
                <li class="unordered">
Doit contenir l’indicatif du pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit contenir l’indicatif régional (le cas échéant)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit contenir le symbole + devant l’indicatif du pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit contenir un espace entre l’indicatif du pays et le reste du numéro<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Les extensions ne sont pas prises en charge. Si des extensions sont spécifiées, elles seront ignorées lors de la répartition de l’appel téléphonique.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Autre adresse de messagerie</p>
            </td>
            <td>
              <p>Non disponible</p>
              <p>Pour l’instant, il n’est pas possible de synchroniser les valeurs d’Active Directory avec la propriété AuthenticationEmail ou AlternateEmailAddresses[0]. </p>
              <p>Vous pouvez utiliser PowerShell pour définir AlternateEmailAddresses[0]. Les instructions de cette procédure figurent immédiatement après ce tableau.</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OU</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(Authentication Email est utilisé si des données sont présentes. Sinon, le champ Adresse de messagerie de secours est utilisé).</p>
              <p>Remarque : le champ Adresse de messagerie de secours est spécifié comme un tableau de chaînes dans l’annuaire.  Nous utilisons la première entrée de ce tableau.</p>
              <p>Par exemple, Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses « email@live.com »</p>
            </td>
            <td>
              <p>Utilisés dans :</p>
              <p>Portail de réinitialisation du mot de passe</p>
              <p>Portail d’inscription</p>
              <p>Peut être défini à partir de : </p>
              <p>AuthenticationEmail peut être défini à partir du portail d’inscription de réinitialisation de mot de passe ou du portail d’inscription MFA.</p>
              <p>AlternateEmail peut être défini à partir de PowerShell, du portail de gestion Azure et du portail d’administration Office</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> ou 甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
Les adresses de messagerie doivent respecter la mise en forme standard.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Les adresses de messagerie Unicode sont prises en charge.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Questions et réponses de sécurité</p>
            </td>
            <td>
              <p>Non disponible</p>
              <p>Pour l’instant, il n’est pas possible de synchroniser les questions ou les réponses de sécurité à partir d’Active Directory.</p>
            </td>
            <td>
              <p>Non disponible pour une modification directe dans l’annuaire.</p>
              <p>Ne peut être défini que durant le processus d’inscription de l’utilisateur final à la réinitialisation du mot de passe.</p>
            </td>
            <td>
              <p>Utilisés dans :</p>
              <p>Portail de réinitialisation du mot de passe</p>
              <p>Portail d’inscription </p>
              <p>Peut être défini à partir de : </p>
              <p>La seule façon de définir des questions de sécurité est de passer par le portail de gestion Azure.</p>
              <p>La seule façon de définir des réponses aux questions de sécurité pour un utilisateur donné est de passer par le portail d’inscription.</p>
            </td>
            <td>
              <p>Les questions de sécurité doivent comporter au maximum 200 caractères et au minimum 3 caractères.</p>
              <p>Les réponses doivent comporter au maximum 40 caractères et au minimum 3 caractères.</p>
            </td>
          </tr>
        </tbody></table>


### <a name="deploying-password-reset-without-requiring-end-user-registration"></a>Déploiement de la réinitialisation du mot de passe sans demander l’inscription de l’utilisateur final
Si vous souhaitez déployer la réinitialisation du mot de passe sans obliger vos utilisateurs à s’inscrire pour pouvoir effectuer cette procédure, cela est possible en suivant l’une des deux options ci-après. Ceci peut être un bon moyen d’inciter un grand nombre d'utilisateurs à utiliser la réinitialisation de mot de passe libre-service (SSPR), tout en leur permettant de valider ces informations à travers le processus d'inscription.

La plupart de nos clients grands comptes recourent à ce moyen pour la prise en main rapide de la réinitialisation du mot de passe.

#### <a name="synchronize-phone-numbers-with-azure-ad-connect"></a>Synchroniser les numéros de téléphone avec Azure AD Connect
Si vous synchronisez les données de l’un des deux champs ou des deux champs ci-dessous, celles-ci sont utilisables immédiatement pour la réinitialisation du mot de passe sans que les utilisateurs doivent s’inscrire préalablement :

* Téléphone mobile
* Téléphone de bureau

Pour en savoir plus sur les propriétés à mettre à jour localement, consultez la section [Données utilisées par la réinitialisation du mot de passe](#what-data-is-used-by-password-reset) et reportez-vous aux champs mentionnés plus haut.  

Vérifiez que les numéros de téléphone sont au format « +1 1234567890 » pour qu’ils fonctionnent parfaitement avec notre système.

#### <a name="set-phone-numbers-or-emails-with-powershell"></a>Définir les numéros de téléphone et les adresses de messagerie avec PowerShell
Si vous définissez un ou plusieurs de ces champs, ils sont immédiatement utilisables pour la réinitialisation du mot de passe sans que les utilisateurs doivent s’inscrire préalablement :

* Téléphone mobile
* Téléphone de bureau
* Autre adresse de messagerie

Pour en savoir plus sur la définition de ces propriétés à l’aide de PowerShell, consultez la section [Définition des données de réinitialisation du mot de passe avec PowerShell](#setting-password-reset-data-with-powershell).

Vérifiez que les numéros de téléphone sont au format « +1 1234567890 » pour qu’ils fonctionnent parfaitement avec notre système.

### <a name="what-happens-when-a-user-registers"></a>Que se passe-t-il lorsqu'un utilisateur s'inscrit ?
Lorsqu'un utilisateur s'inscrit, la page d'inscription définira **toujours** les champs suivants :

* Téléphone d’authentification
* E-mail d’authentification
* Questions et réponses de sécurité

Si vous avez fourni une valeur pour **Téléphone mobile** ou **Autre adresse de messagerie**, les utilisateurs peuvent immédiatement les utiliser pour réinitialiser leur mot de passe, même s’ils ne se sont pas inscrits au service.  Les utilisateurs verront ces valeurs lorsqu’ils s’inscriront pour la première fois et ils auront la possibilité de les modifier.  Cependant, une fois inscrits, ces valeurs seront respectivement conservées dans les champs **Téléphone d’authentification** et **E-mail d’authentification**.

### <a name="how-to-access-password-reset-data-for-your-users"></a>Comment accéder aux données de réinitialisation des mots de passe pour vos utilisateurs
#### <a name="data-settable-via-synchronization"></a>Données définissables via la synchronisation
Les champs suivants peuvent être synchronisés en local :

* Téléphone mobile
* Téléphone de bureau

#### <a name="data-settable-with-azure-ad-powershell--azure-ad-graph"></a>Données définissables avec Azure AD PowerShell et Azure AD Graph
Les champs suivants peuvent être définis à l’aide d’Azure AD PowerShell et de l’API Azure AD Graph :

* Autre adresse de messagerie
* Téléphone mobile
* Téléphone de bureau

#### <a name="data-settable-with-registration-ui-only"></a>Données définissables avec l’interface utilisateur d’inscription uniquement
Les champs suivants sont uniquement accessibles au moyen de l’interface utilisateur d’inscription SSPR (https://aka.ms/ssprsetup) :

* Questions et réponses de sécurité

#### <a name="data-readable-with-azure-ad-powershell--azure-ad-graph"></a>Données lisibles avec Azure AD PowerShell et Azure AD Graph
Les champs suivants sont accessibles avec Azure AD PowerShell et l’API Azure AD Graph :

* Autre adresse de messagerie
* Téléphone mobile
* Téléphone de bureau
* Téléphone d’authentification
* E-mail d’authentification

### <a name="setting-password-reset-data-with-powershell"></a>Configuration de données de réinitialisation de mot de passe avec PowerShell
Vous pouvez définir des valeurs pour les champs suivants à l’aide d’Azure AD PowerShell.

* Autre adresse de messagerie
* Téléphone mobile
* Téléphone de bureau

**_PowerShell V1_**

Pour commencer, vous devez d'abord [télécharger et installer le module Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Une fois installé, vous pouvez suivre les étapes ci-dessous pour configurer chaque champ.

**_PowerShell V2_**

Pour commencer, vous devez d’abord [télécharger et installer le module Azure AD V2 PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). Une fois installé, vous pouvez suivre les étapes ci-dessous pour configurer chaque champ.

Pour installer rapidement des versions récentes de PowerShell compatibles avec Install-Module, exécutez ces commandes (la première ligne vérifie simplement si le produit est déjà installé) :

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="alternate-email---how-to-set-alternate-email-with-powershell"></a>Autre adresse de messagerie - Comment définir l’autre adresse de messagerie avec PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
```

#### <a name="mobile-phone---how-to-set-mobile-phone-with-powershell"></a>Téléphone mobile - Comment définir le téléphone mobile avec PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 1234567890"
```

#### <a name="office-phone---how-to-set-office-phone-with-powershell"></a>Téléphone de bureau - Comment définir le téléphone de bureau avec PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"
```

### <a name="reading-password-reset-data-with-powershell"></a>Lecture de données de réinitialisation de mot de passe avec PowerShell
Vous pouvez lire des valeurs pour les champs suivants à l’aide d’Azure AD PowerShell.

* Autre adresse de messagerie
* Téléphone mobile
* Téléphone de bureau
* Téléphone d’authentification
* E-mail d’authentification

#### <a name="alternate-email---how-to-read-alternate-email-with-powershell"></a>Autre adresse de messagerie - Comment lire l’autre adresse de messagerie avec PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select otherMails
```

#### <a name="mobile-phone---how-to-read-mobile-phone-with-powershell"></a>Téléphone mobile - Comment lire le téléphone mobile avec PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select Mobile
```

#### <a name="office-phone---how-to-read-office-phone-with-powershell"></a>Téléphone de bureau - Comment lire le téléphone de bureau avec PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber
```

#### <a name="authentication-phone---how-to-read-authentication-phone-with-powershell"></a>Téléphone d’authentification - Comment lire le téléphone d’authentification avec PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```

#### <a name="authentication-email---how-to-read-authentication-email-with-powershell"></a>E-mail d’authentification - Comment lire un e-mail d’authentification avec PowerShell
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```
## <a name="how-does-password-reset-work-for-b2b-users"></a>Comment fonctionne la réinitialisation du mot de passe pour les utilisateurs B2B ?
La réinitialisation et la modification du mot de passe sont totalement compatibles avec toutes les configurations B2B.  Lisez ce qui suit pour en savoir plus sur les trois cas B2B explicites pris en charge par la réinitialisation du mot de passe.

1. **Utilisateurs d’une organisation partenaire disposant d’un locataire Azure AD** : si l’organisation avec laquelle vous avez un partenariat dispose d’un locataire Azure AD, nous **respectons les stratégies de réinitialisation du mot de passe activées dans ce locataire**. Pour que la réinitialisation du mot de passe fonctionne, l’organisation partenaire doit simplement s’assurer qu’Azure AD SSPR est activé (ce qui est gratuit pour les clients O365) et peut être activé en suivant la procédure de notre guide [Prise en main de la gestion de mot de passe](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
2. **Utilisateurs qui se sont inscrits dans le cadre de l’[inscription en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup)** : si l’organisation dont vous êtes partenaire a utilisé la fonctionnalité d’[inscription en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup) pour accéder à un locataire, nous l’autorisons à réinitialiser le mot de passe en indiquant l’adresse de messagerie qu’elle a utilisée pour l’inscription.
3. **Utilisateurs B2B** : tous les utilisateurs B2B créés à l’aide des nouvelles [fonctionnalités B2B d’Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) peuvent réinitialiser leurs mots de passe en indiquant l’adresse de messagerie utilisée pour l’inscription.
 
Pour tester ces méthodes avec l’un des utilisateurs partenaires, consultez la page http://passwordreset.microsoftonline.com.  Tant que ces utilisateurs disposent d’une autre adresse de messagerie électronique ou d’une adresse électronique d’authentification, la réinitialisation du mot de passe fonctionne comme prévu.  La vue d’ensemble [Données utilisées par la réinitialisation de mot de passe](https://azure.microsoft.com/en-us/documentation/articles/active-directory-passwords-learn-more/#what-data-is-used-by-password-reset) fournit plus d’informations sur les données utilisées par sspr.

## <a name="next-steps"></a>Étapes suivantes
Voici les liens vers toutes les pages de la documentation sur la réinitialisation de mot de passe Azure AD :

* **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password).
* [**Fonctionnement**](active-directory-passwords-how-it-works.md) : découvrez les six différents composants du service et la fonction de chacun d’eux
* [**Prise en main**](active-directory-passwords-getting-started.md) : découvrez comment permettre à vos utilisateurs de réinitialiser et de modifier leurs mots de passe dans le cloud et localement
* [**Personnalisation**](active-directory-passwords-customize.md) : découvrez comment personnaliser l’apparence et le comportement du service en fonction des besoins de votre organisation.
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) : découvrez comment déployer et gérer rapidement et efficacement les mots de passe de votre organisation.
* [**Obtention d’informations**](active-directory-passwords-get-insights.md) : découvrez nos fonctionnalités intégrées de création de rapports.
* [**Forum Aux Questions**](active-directory-passwords-faq.md) : obtenez des réponses aux questions fréquemment posées.
* [**Dépannage**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre rapidement les problèmes liés au service.

[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"

