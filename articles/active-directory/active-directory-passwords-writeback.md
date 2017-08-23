---
title: "Réinitialisation de mot de passe en libre-service Azure AD avec écriture différée de mot de passe | Microsoft Docs"
description: "Utilisation d’Azure AD et Azure AD Connect pour l’écriture différée des mots de passe sur l’annuaire local"
services: active-directory
keywords: "Gestion des mots de passe Active Directory, gestion des mots de passe, réinitialisation de mot de passe en libre-service Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 7783d0fc30b4b7d098a89595771993b33fb0aa97
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---
# <a name="password-writeback-overview"></a>Vue d’ensemble de l’écriture différée de mot de passe

L’écriture différée de mot de passe vous permet de configurer Azure AD pour l’écriture différée des mots de passe vers l’environnement Active Directory local. Cela vous évite d’avoir à configurer et à gérer une solution de réinitialisation de mot de passe libre-service locale complexe en offrant à vos utilisateurs un moyen pratique via le cloud de réinitialiser leurs mots de passe locaux, où qu’ils se trouvent. L’écriture différée de mot de passe est un composant [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) qui peut être activé et utilisé par les abonnés actifs des [éditions Premium d’Azure Active Directory](active-directory-editions.md).

L’écriture différée des mots de passe fournit les fonctionnalités suivantes

* **Retour d’informations immédiat** : l’écriture différée de mot de passe est une opération synchrone. Vos utilisateurs sont informés immédiatement si leur mot de passe ne remplit pas les critères des stratégies ou s’il n’a pas pu être réinitialisé ou modifié pour une raison quelconque.
* **Prend en charge la réinitialisation des mots de passe des utilisateurs se servant d’AD FS ou autres technologies de fédération** : avec l’écriture différée de mot de passe, tant que les comptes utilisateur fédérés sont synchronisés dans votre client Azure AD, ils sont en mesure de gérer leurs mots de passe AD locaux à partir du cloud.
* **Prise en charge de la réinitialisation des mots de passe pour les utilisateurs qui ont recours à la [synchronisation de hachage de mot de passe](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)** : lorsque le service de réinitialisation des mots de passe détecte qu’un compte d’utilisateur synchronisé est activé pour la synchronisation de hachage de mot de passe, le mot de passe local de ce compte et le mot de passe du cloud sont tous deux réinitialisés simultanément.
* **Prend en charge la modification des mots de passe à partir du panneau d’accès et Office 365** : lorsque des utilisateurs fédérés ou synchronisés par mot de passe viennent à modifier leurs mots de passe ayant expiré ou non, nous réécrivons ces mots de passe dans votre environnement AD local.
* **Prend en charge l’écriture différée des mots de passe lorsqu’un administrateur les réinitialise depuis le portail Azure** : chaque fois qu’un administrateur réinitialise le mot de passe d’un utilisateur dans le [portail Azure](https://portal.azure.com), si cet utilisateur est fédéré ou dispose de la synchronisation de mot de passe, nous définissons également le mot de passe sélectionné par l’administrateur sur votre annuaire AD local. Cette fonctionnalité n’est pas prise en charge actuellement dans le portail d’administration Office.
* **Applique vos stratégies de mot de passe AD locales** : lorsqu’un utilisateur réinitialise son mot de passe, nous nous assurons qu’il répond à votre stratégie AD locale avant de la valider dans cet annuaire. Cela comprend l’historique, la complexité, l’âge, les filtres de mot de passe et toute autre restriction de mot de passe que vous avez définie dans votre annuaire Active Directory local.
* **Aucune règle de pare-feu pour le trafic entrant requise** : l’écriture différée des mots de passe utilise Azure Service Bus Relay comme canal de communication sous-jacent, ce qui signifie que vous n’avez pas à ouvrir de ports entrants sur votre pare-feu pour que cette fonctionnalité marche correctement. Seul le port 443 sortant doit être ouvert.
* **Aucune prise en charge pour les comptes d’utilisateur qui existent dans des groupes protégés dans votre annuaire Active Directory local.** : pour plus d’informations sur les groupes protégés, consultez la page [Comptes et groupes protégés dans Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Fonctionnement de l’écriture différée des mots de passe

Lorsqu’un utilisateur fédéré ou disposant de la synchronisation du hachage de mot de passe réinitialise ou modifie son mot de passe dans le cloud, les événements suivants ont lieu :

1. Nous vérifions le type de mot de passe de l’utilisateur.
    * Si nous constatons que le mot de passe est géré localement
        * Nous vérifions que le service d’écriture différée fonctionne et est en cours d’exécution. Si c’est le cas, nous laissons l’utilisateur poursuivre
        * Si le service d’écriture différée n’est pas disponible, nous indiquons à l’utilisateur que son mot de passe ne peut pas être réinitialisé maintenant
2. Ensuite, l’utilisateur passe par les portails d’authentification appropriés et atteint l’écran de réinitialisation de mot de passe.
3. L’utilisateur sélectionne un nouveau mot de passe et le confirme.
4. Lorsqu’il clique sur Envoyer, nous chiffrons le mot de passe en texte brut avec une clé publique qui a été créée durant le processus d’installation de l’écriture différée.
5. Après avoir chiffré le mot de passe, nous l’incluons dans une charge utile qui est envoyée via un canal HTTPS à votre relais Service Bus spécifique au client (que nous configurons également pour vous lors du processus d’installation de l’écriture différée). Ce relais est protégé par un mot de passe généré de manière aléatoire, connu uniquement de votre installation locale.
6. Une fois que le message a atteint Service Bus, le point de terminaison de réinitialisation de mot de passe sort de veille automatiquement et découvre qu’une demande de réinitialisation est en attente.
7. Le service recherche alors l’utilisateur en question à l’aide de l’attribut d’ancrage de cloud. Pour que cette recherche aboutisse :

    * L’objet utilisateur doit exister dans l’espace de connecteur AD
    * L’objet utilisateur doit être lié à l’objet MV correspondant
    * L’objet utilisateur doit être lié à l’objet de connecteur AAD correspondant.
    * Le lien de l’objet de connecteur AD à MV doit avoir la règle de synchronisation `Microsoft.InfromADUserAccountEnabled.xxx` sur le lien. <br> <br>
    Celle-ci est nécessaire, car lorsque l’appel provient du cloud, le moteur de synchronisation utilise l’attribut cloudAnchor pour rechercher l’objet d’espace de connecteur AAD, puis il suit le lien vers l’objet MV, puis le lien vers l’objet AD. Comme il peut y avoir plusieurs objets Active Directory (plusieurs forêts) pour le même utilisateur, le moteur de synchronisation s’appuie sur le lien `Microsoft.InfromADUserAccountEnabled.xxx` pour choisir celui qui convient.

    > [!Note]
    > Conformément à cette logique, Azure AD Connect doit être en mesure de communiquer avec l’émulateur PDC pour que l’écriture différée de mot de passe fonctionne. Si vous avez besoin de l’activer manuellement, vous pouvez connecter Azure AD Connect à l’émulateur PDC en cliquant avec le bouton droit sur les **propriétés** du connecteur de synchronisation Active Directory, puis en sélectionnant **configurer des partitions d’annuaire**. À partir de là, recherchez la section **domain controller connection settings** (paramètres de connexion de contrôleur de domaine) et cochez la case intitulée **only use preferred domain controllers** (utiliser uniquement les contrôleurs de domaine préférés). Même si le contrôleur de domaine préféré n’est pas un émulateur de contrôleur de domaine principal, Azure AD Connect cherche à atteindre le contrôleur de domaine principal pour l’écriture différée du mot de passe.

8. Une fois le compte d’utilisateur trouvé, nous tentons de réinitialiser le mot de passe directement dans la forêt Active Directory appropriée.
9. Si l’opération de définition du mot de passe réussit, nous signalons à l’utilisateur que son mot de passe a été modifié.
    > [!NOTE]
    > Lorsque le mot de passe de l’utilisateur est synchronisé sur Azure AD à l’aide de la synchronisation de mot de passe, il se peut que la stratégie de mot de passe locale soit plus faible que la stratégie de mot de passe cloud. Dans ce cas, nous appliquons toujours la stratégie locale, quelle qu’elle soit, et nous autorisons la synchronisation du hachage de mot de passe à synchroniser le hachage de ce mot de passe. Cela garantit que votre stratégie locale est appliquée dans le cloud, que vous utilisiez ou non la synchronisation ou la fédération de mot de passe pour fournir une authentification unique.

10. Si l’opération de définition du mot de passe échoue, nous renvoyons l’erreur à l’utilisateur et le laissons réessayer.
    * L’opération peut échouer pour les raisons suivantes
        * Le service est arrêté
        * Le mot de passe sélectionné ne répondait pas aux stratégies de l’organisation
        * Nous n’avons pas trouvé l’utilisateur dans l’annuaire AD local

    Nous avons un message spécifique pour la plupart de ces cas de figure et nous indiquons à l’utilisateur ce qu’il peut faire pour résoudre le problème.

## <a name="configuring-password-writeback"></a>Configuration de l’écriture différée du mot de passe

Nous vous recommandons d’utiliser la fonctionnalité de mise à jour automatique [d’Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) si vous souhaitez utiliser l’écriture différée de mot de passe.

DirSync et Azure AD Sync ne sont plus pris en charge pour l’activation de l’écriture différée du mot de passe. L’article [Mettre à niveau depuis DirSync et Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md) contient des informations supplémentaires pour faciliter votre transition.

Les étapes suivantes partent du principe que vous avez déjà configuré Azure AD Connect dans votre environnement à l’aide des paramètres [Express](./connect/active-directory-aadconnect-get-started-express.md) ou [Personnalisé](./connect/active-directory-aadconnect-get-started-custom.md).

1. Pour configurer et activer le journal de l’écriture différée du mot de passe sur votre serveur Azure AD Connect et démarrer l’Assistant de configuration **Azure AD Connect**.
2. Dans l’écran d’accueil, cliquez sur **Configurer**.
3. Dans l’écran autres Tâches supplémentaires, cliquez sur **Personnaliser les options de synchronisation**, puis choisissez **Suivant**.
4. Dans l’écran de connexion à Azure AD, entrez des informations d’identification d’Administrateur général et choisissez **Suivant**.
5. Dans les écrans Connexion de vos annuaires et Filtrage domaine et unité organisationnelle, vous pouvez choisir **Suivant**.
6. Dans l’écran des fonctionnalités facultatives, cochez la case en regard de **Écriture différée du mot de passe**, puis cliquez sur **Suivant**.
   ![Activation de l’écriture différée du mot de passe dans Azure AD Connect][Writeback]
7. Dans l’écran Prêt à configurer, cliquez sur **Configurer** et attendez la fin du processus.
8. Lorsque Configuration terminée s’affiche, vous pouvez cliquer sur **Quitter**

## <a name="licensing-requirements-for-password-writeback"></a>Conditions de licence pour l’écriture différée de mot de passe

Pour plus d’informations concernant les licences, consultez la rubrique [Licences requises pour l’écriture différée du mot de passe](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) ou les sites suivants

* [Site de tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Secure Productive Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

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
  * Toute modification forcée en libre-service du mot de passe de l’utilisateur final (par exemple, expiration du mot de passe)
  * Toute réinitialisation du mot de passe en libre-service de l’utilisateur final émanant du [portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com)
* **Opérations de l’administrateur final prises en charge**
  * Toute modification volontaire en libre-service du mot de passe de l’administrateur
  * Toute modification forcée en libre-service du mot de passe de l’administrateur (par exemple, expiration du mot de passe)
  * Toute réinitialisation du mot de passe en libre-service de l’administrateur émanant du [portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com)
  * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis le [portail Azure Classic](https://manage.windowsazure.com)
  * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis le [portail Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>Opérations de l’utilisateur et de l’administrateur non prises en charge pour l’écriture différée du mot de passe

Les mots de passe ne sont **pas** écrits en différé dans les situations suivantes :

* **Opérations de l’utilisateur final non prises en charge**
  * Tout utilisateur final réinitialisant son mot de passe à l’aide de PowerShell (version 1 ou version 2) ou l’API Azure AD Graph
* **Opérations de l’administrateur non prises en charge**
  * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis le [portail de gestion Office](https://portal.office.com)
  * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis PowerShell (versions 1 et 2) ou l’API Azure AD Graph

Nous travaillons à supprimer ces limitations mais nous n’avons pas encore de calendrier de publication des améliorations.

## <a name="password-writeback-security-model"></a>Modèle de sécurité de l’écriture différée de mot de passe

L’écriture différée des mots de passe est un service hautement sécurisé.  Pour garantir la protection de vos informations, nous activons un modèle de sécurité à quatre niveaux, décrit ci-dessous.

* **Relais de Service Bus propre au client**
  * Lorsque vous configurez le service, nous définissons un relais Service Bus spécifique au client et protégé par un mot de passe fort généré de manière aléatoire, auquel Microsoft n’a jamais accès.
* **Clé de chiffrement de mot de passe forte et verrouillée**
  * Une fois le relais Service Bus créé, nous créons une paire de clés asymétriques forte qui nous permet de chiffrer le mot de passe lorsqu’il arrive sur le réseau. Cette clé réside uniquement dans le magasin de secrets de votre entreprise dans le cloud, qui est fortement verrouillé et audité, comme n’importe quel mot de passe de l’annuaire.
* **TLS standard du secteur**
  1. Lorsqu’une opération de réinitialisation ou de modification de mot de passe a lieu dans le cloud, nous prenons le mot de passe et nous le chiffrons avec votre clé publique.
  2. Ensuite, nous insérons cela dans un message HTTPS envoyé à votre relais Service Bus via un canal chiffré à l’aide de certificats SSL Microsoft.
  3. Une fois le message arrivé dans Service Bus, votre agent local sort de veille et s’authentifie sur Service Bus en utilisant le mot de passe fort qui a été généré précédemment.
  4. L’agent local récupère le message chiffré et le déchiffre à l’aide de la clé privée que nous avons générée.
  5. L’agent local tente ensuite de définir le mot de passe via l’API SetPassword AD DS.
     * C’est cette étape qui nous permet d’appliquer votre stratégie de mot de passe AD local (complexité, âge, historique, filtres, etc.) dans le cloud.
* **Stratégies d’expiration de message** 
  * Si le message reste dans Service Bus car votre service local est arrêté, il expire et est supprimé après quelques minutes pour augmenter encore davantage la sécurité.

### <a name="password-writeback-encryption-details"></a>Informations détaillées sur le chiffrement de l’écriture différée de mot de passe

Sont décrites ci-dessous les étapes de chiffrement de la procédure de réinitialisation du mot de passe exécutée par un utilisateur avant que celle-ci parvienne dans votre environnement local, et ce, afin d’assurer une fiabilité et une sécurité de service maximales.

* **Étape 1 - Chiffrement du mot de passe avec la clé RSA de 2 048 bits** : lorsqu’un utilisateur envoie un mot de passe en vue de l’écriture différée au niveau local, celui-ci est préalablement chiffré avec une clé RSA de 2 048 bits.
* **Étape 2 - Chiffrement de niveau package avec AES-GCM** : la méthode AES-GCM permet de chiffrer le package en totalité (mot de passe et métadonnées requises). Cela empêche toute personne ayant un accès direct au canal ServiceBus sous-jacent de visualiser/compromettre le contenu.
* **Étape 3 - Toutes les communications passent par une connexion TLS / SSL** : toutes les communications avec ServiceBus transitent par un canal SSL/TLS. Cela protège le contenu contre les tiers non autorisés.
* **Renouvellement automatique des clés tous les 6 mois** : tous les 6 mois de façon automatique ou chaque fois que l’écriture différée du mot de passe est désactivée/réactivée sur Azure AD Connect, nous renouvelons toutes ces clés afin d’assurer la sécurité et la protection maximales du service.

### <a name="password-writeback-bandwidth-usage"></a>Utilisation de la bande passante de l’écriture différée du mot de passe

L’écriture différée du mot de passe est un service de bande passante très basse qui renvoie des demandes à l’agent local uniquement dans les circonstances suivantes :

1. Deux messages envoyés lors de l’activation ou de la désactivation de la fonction via Azure AD Connect.
2. Un message est envoyé une fois toutes les cinq minutes comme une pulsation du service tant que le service est en cours d’exécution.
3. Deux messages sont envoyés chaque fois qu’un nouveau mot de passe est envoyé
    * Premier message, en tant que requête pour effectuer l’opération
    * Deuxième message, qui contient le résultat de l’opération et est envoyé dans les circonstances suivantes :
        * Chaque fois qu’un nouveau mot de passe est soumis pendant une réinitialisation de mot de passe libre-service par l’utilisateur.
        * Chaque fois qu’un nouveau mot de passe est soumis pendant une opération de modification de mot de passe par l’utilisateur.
        * Chaque fois qu’un nouveau mot de passe est soumis au cours d’une réinitialisation de mot de passe utilisateur initiée par un administrateur (depuis les portails d’administration Azure uniquement).

#### <a name="message-size-and-bandwidth-considerations"></a>Considérations relatives à la taille des messages et à la bande passante

La taille de chacun des messages décrits ci-dessus est généralement inférieure à 1 ko, ce qui signifie que, même sous des charges extrêmes, le service de l’écriture différée du mot de passe lui-même utilise quelques kilobits par seconde de bande passante. Étant donné que chaque message est envoyé en temps réel, uniquement lorsqu’une opération de mise à jour du mot de passe le requiert, et étant donné que le message est très petit, l’utilisation de la bande passante de la fonctionnalité d’écriture différée est effectivement trop petite pour produire un impact mesurable réel.

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [**Démarrage rapide**](active-directory-passwords-getting-started.md) : soyez rapidement opérationnel avec la gestion des mots de passe en libre-service Azure AD. 
* [**Licences**](active-directory-passwords-licensing.md) : configurez vos licences Azure AD.
* [**Données**](active-directory-passwords-data.md) : comprenez mieux les données requises et leur utilisation dans la gestion des mots de passe.
* [**Déploiement**](active-directory-passwords-best-practices.md) : planifiez et déployez la réinitialisation de mot de passe en libre-service pour vos utilisateurs grâce aux conseils figurant ici.
* [**Personnalisation**](active-directory-passwords-customize.md) : personnalisez l’apparence de l’interface de réinitialisation de mot de passe en libre-service de votre société.
* [**Stratégie**](active-directory-passwords-policy.md) : comprenez mieux et définissez les stratégies de mot de passe d’Azure AD.
* [**Rapports**](active-directory-passwords-reporting.md) : découvrez si, quand et où vos utilisateurs accèdent aux fonctionnalités de réinitialisation de mot de passe en libre-service.
* [**Présentation technique approfondie**](active-directory-passwords-how-it-works.md) : découvrez ce qui se passe sous le capot pour mieux comprendre le fonctionnement.
* [**Forum Aux Questions (FAQ)**](active-directory-passwords-faq.md) : Comment ? Pourquoi ? Quoi ? Où ? Qui ? Quand ? - Les réponses aux questions que vous vouliez poser depuis toujours.
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec la réinitialisation de mot de passe en libre-service.

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Activation de l’écriture différée du mot de passe dans Azure AD Connect"

