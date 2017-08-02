---
title: "Déploiement des stratégies recommandées pour sécuriser la messagerie | Microsoft Docs"
description: "Décrit les recommandations et les concepts de base de Microsoft concernant le déploiement de stratégies et de configurations de messagerie sécurisées."
author: jeffgilb
manager: femila
editor: jsnow
ms.service: guidance
ms.topic: article
ms.date: 07/12/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
pnp.series.title: Best Practices
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: a5eb77c68c48e9cca2805fa64075279560179e14
ms.contentlocale: fr-fr
ms.lasthandoff: 07/15/2017

---

# <a name="deploy-recommended-secure-email-policies"></a>Déployer les stratégies recommandées pour sécuriser la messagerie

Cette section décrit la procédure de déploiement des stratégies recommandées dans un environnement nouvellement approvisionné. La configuration de ces stratégies dans un environnement lab distinct vous permet de comprendre et d’évaluer les stratégies recommandées avant d’échelonner le déploiement de vos environnements de pré-production et de production. Votre environnement nouvellement approvisionné peut être un environnement cloud uniquement ou hybride.  

Pour déployer correctement les stratégies recommandées, vous devez effectuer certaines opérations dans le Portail Azure afin de remplir les conditions préalables indiquées précédemment. Plus précisément, vous devez :
* Configurer les réseaux nommés pour garantir qu’Azure Identity Protection peut générer correctement un indice de risque
* Exiger que tous les utilisateurs s’inscrivent pour l’authentification multifacteur (MFA)
* Configurer la synchronisation de mot de passe et la réinitialisation de mot de passe en libre-service pour permettre aux utilisateurs de réinitialiser eux-mêmes les mots de passe

Vous pouvez cibler des stratégies à la fois Azure Active Directory et Intune vers des groupes d’utilisateurs spécifiques. Nous vous suggérons d’échelonner le déploiement des stratégies définies précédemment. Cette approche vous permet de valider de façon incrémentielle les performances des stratégies et de vos équipes de support par rapport à une stratégie spécifique.

## <a name="baseline-conditional-access-policy"></a>Stratégie d’accès conditionnel de référence

Pour créer une stratégie d’accès conditionnel, connectez-vous au Portail Microsoft Azure avec vos informations d’identification d’administrateur. Puis accédez à **Azure Active Directory > Sécurité > Accès conditionnel**. 

Vous pouvez ajouter une nouvelle stratégie (+Ajouter) comme illustré dans la capture d’écran suivante :

![Stratégie d’autorité de confiance de référence](./media/secure-email/baseline-ca-policy.png)

Les tableaux ci-après décrivent les paramètres appropriés nécessaires pour exprimer les stratégies requises selon chaque niveau de protection.

### <a name="medium-and-above-risk-requires-mfa"></a>Exiger l’authentification multifacteur pour un niveau de risque moyen ou plus élevé

Le tableau ci-après décrit les paramètres de stratégie d’accès conditionnel à implémenter pour cette stratégie.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Affectations**|Utilisateurs et groupes|Inclure|Sélectionner des utilisateurs et des groupes - Sélectionner un groupe de sécurité spécifique contenant les utilisateurs ciblés|Commencer par un groupe de sécurité incluant des utilisateurs pilotes|
|||Exclure|Groupe de sécurité d’exception ; comptes de service (identités d’application)|Appartenance modifiée à titre temporaire selon les besoins|
||Applications cloud|Inclure|Sélectionner les applications - Sélectionner Office 365 Exchange Online||
||Conditions|Configuré|Oui|Configurer en fonction de l’environnement et des besoins|
||Risque à la connexion|Niveau de risque|Élevé, moyen|Cocher les deux valeurs|
|**Contrôles d’accès**|Grant (Autoriser)|Accorder l’accès|true|Sélectionné|
|||Exiger une authentification multifacteur|true|Vérification|
|||Exiger un appareil conforme|False||
|||Exiger un appareil joint au domaine|False||
|||Demander tous les contrôles sélectionnés|true|Sélectionné|
|**Activer la stratégie**|||Il en va|Déploie une stratégie d’accès conditionnel|

### <a name="require-a-compliant-or-domain-joined-device"></a>Exiger un appareil conforme ou joint au domaine

Pour créer une stratégie d’accès conditionnel Intune pour Exchange Online, connectez-vous au [Portail de gestion Microsoft (http://manage.microsoft.com)](http://manage.microsoft.com/) avec vos informations d’identification d’administrateur, puis accédez à **Stratégie > Accès conditionnel > Stratégie Exchange Online**.

![Stratégie Exchange Online](./media/secure-email/exchange-online-policy.png)

Vous devez définir une stratégie d’accès conditionnel spécifiquement pour Exchange Online dans le Portail de gestion Intune afin d’exiger un appareil conforme ou joint au domaine.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Accès aux applications**|Outlook et les autres applications qui utilisent une authentification récente|Toutes les plateformes|true|Sélectionné|
|||Windows doit répondre à l’exigence suivante|L’appareil doit être joint au domaine ou conforme|Sélectionné (liste)|
|||Plateforme sélectionnée|False||
||Outlook Web Access (OWA)|Bloquer les appareils non conformes sur les mêmes plateformes qu’Outlook|true|Vérification|
||Applications Exchange ActiveSync qui utilisent une authentification de base|Bloquer les appareils non conformes sur les plateformes prises en charge par Microsoft Intune|true|Vérification|
|||Bloquer tous les autres appareils sur les plateformes qui ne sont pas prises en charge par Microsoft Intune|true|Vérification|
|**Déploiement de stratégie**|Groupes cibles|Sélectionner les groupes Active Directory à cibler avec cette stratégie|||
|||Tous les utilisateurs|False||
|||Groupes de sécurité sélectionnés|true|Sélectionné|
|||Modifier|Sélectionner un groupe de sécurité spécifique contenant les utilisateurs ciblés||
||Groupes exempts|Sélectionner les groupes de sécurité Active Directory à exempter de cette stratégie (remplace les membres de la liste Groupes ciblés)|||
|||Aucun utilisateur exempt|true|Sélectionné|
|||Groupes de sécurité sélectionnés|False|||

### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>Accès conditionnel de gestion des applications mobiles pour Exchange Online

Vous devez définir une stratégie d’accès conditionnel spécifiquement destinée à Exchange Online dans le Portail de gestion Intune afin de gérer les applications mobiles.

Pour gérer les applications mobiles, connectez-vous au Portail Microsoft Azure avec vos informations d’identification d’administrateur, puis accédez à **Protection d’application Intune > Paramètres > Accès conditionnel > Exchange Online**.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Accès des applications**|Applications autorisées|Activer l’accès aux applications|Autoriser les applications qui prennent en charge les stratégies d’application Intune|Sélectionné (liste) - Cette sélection résulte en une liste de combinaisons d’applications/plateformes prises en charge par les stratégies d’application Intune|
|**Accès utilisateur**|Applications autorisées|Groupes d’utilisateurs restreints|Ajouter des utilisateurs et des groupes - Sélectionner un groupe de sécurité spécifique contenant les utilisateurs ciblés|Commencer par un groupe de sécurité incluant des utilisateurs pilotes|
|||Groupes d’utilisateurs exemptés|Groupes de sécurité d’exception|||

#### <a name="apply-to"></a>S’applique à

Une fois votre projet pilote achevé, ces stratégies doivent être appliquées à tous les utilisateurs de votre organisation.

## <a name="sensitive-conditional-access-policy"></a>Stratégie d’accès conditionnel sensible

### <a name="low-and-above-risk-requires-mfa"></a>Exiger l’authentification multifacteur pour un niveau de risque faible ou plus élevé
Le tableau ci-après décrit les paramètres de stratégie d’accès conditionnel à implémenter pour les stratégies de risque faible ou plus élevé.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Affectations**|Utilisateurs et groupes|Inclure|Sélectionner des utilisateurs et des groupes - Sélectionner un groupe de sécurité spécifique contenant les utilisateurs ciblés|Commencer par un groupe de sécurité incluant des utilisateurs pilotes|
|||Exclure|Groupe de sécurité d’exception ; comptes de service (identités d’application)|Appartenance modifiée à titre temporaire selon les besoins|
||Applications cloud|Inclure|Sélectionner les applications - Sélectionner Office 365 Exchange Online||
||Conditions|Configuré|Oui|Configurer en fonction de l’environnement et des besoins|
||Risque à la connexion|Configuré|Oui|Configurer en fonction de l’environnement et des besoins|
|||Niveau de risque|Faible, moyen, élevé|Cocher les trois valeurs|
|**Contrôles d’accès**|Grant (Autoriser)|Accorder l’accès|true|Sélectionné|
|||Exiger une authentification multifacteur|true|Vérification|
|||Exiger un appareil conforme|False||
|||Exiger un appareil joint au domaine|False||
|||Demander tous les contrôles sélectionnés|true|Sélectionné|
|**Activer la stratégie**|||Il en va|Déploie une stratégie d’accès conditionnel|

### <a name="require-a-compliant-or-domain-joined-device"></a>Exiger un appareil conforme ou joint au domaine
(Voir les instructions de la stratégie de référence)

### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>Accès conditionnel de gestion des applications mobiles pour Exchange Online

(Voir les instructions de la stratégie de référence)

#### <a name="apply-to"></a>S’applique à

Une fois le projet pilote achevé, ces stratégies doivent être appliquées aux utilisateurs de votre organisation qui nécessitent un accès aux e-mails considérés comme étant sensibles.

## <a name="highly-regulated-conditional-access-policy"></a>Stratégie d’accès conditionnel hautement réglementée
### <a name="mfa-required"></a>MFA obligatoire

Le tableau ci-après décrit les paramètres de stratégie d’accès conditionnel à implémenter pour la stratégie hautement réglementée.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Affectations**|Utilisateurs et groupes|Inclure|Sélectionner des utilisateurs et des groupes - Sélectionner un groupe de sécurité spécifique contenant les utilisateurs ciblés|Commencer par un groupe de sécurité incluant des utilisateurs pilotes|
|||Exclure|Groupe de sécurité d’exception ; comptes de service (identités d’application)|Appartenance modifiée à titre temporaire selon les besoins|
||Applications cloud|Inclure|Sélectionner les applications - Sélectionner Office 365 Exchange Online||
|**Contrôles d’accès**|Grant (Autoriser)|Accorder l’accès|true|Sélectionné|
|||Exiger une authentification multifacteur|true|Vérification|
|||Exiger un appareil conforme|False|Vérification|
|||Exiger un appareil joint au domaine|False||
|||Demander tous les contrôles sélectionnés|true|Sélectionné|
|**Activer la stratégie**|||Il en va|Déploie une stratégie d’accès conditionnel|

### <a name="require-a-compliant-or-domain-joined-device"></a>Exiger un appareil conforme ou joint au domaine
(Voir les instructions de la stratégie de référence)
### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>Accès conditionnel de gestion des applications mobiles pour Exchange Online
(Voir les instructions de la stratégie de référence)
#### <a name="apply-to"></a>S’applique à
Une fois le projet pilote achevé, ces stratégies doivent être appliquées aux utilisateurs de votre organisation qui nécessitent un accès aux e-mails considérés comme étant hautement réglementés.

## <a name="user-risk-policy"></a>Stratégie de risque d’utilisateur
### <a name="high-risk-users-must-change-password"></a>Les utilisateurs à haut risque doivent changer de mot de passe
Pour que tous les comptes d’utilisateurs à haut risque compromis soient contraints d’effectuer un changement de mot de passe lorsqu’ils se connectent, vous devez appliquer la stratégie suivante. 

Connectez-vous au [Portail Microsoft Azure (http://portal.azure.com)](http://portal.azure.com/) avec vos informations d’identification d’administrateur, puis accédez à **Azure AD Identity Protection > Stratégie d’utilisateur à risque**.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Affectations**|Utilisateurs|Inclure|Tous les utilisateurs|Sélectionné|
|||Exclure|Aucun||
||Conditions|Risque de l’utilisateur|Élevé|Sélectionné|
|**Contrôles**|Access|Autoriser l’accès|true|Sélectionné|
||Access|Nécessite une modification du mot de passe|true|Vérification|
|**Révision**|N/A|N/A|N/A|N/A|
|**Appliquer la stratégie**|||Il en va|Commence à appliquer la stratégie|

## <a name="additional-configurations"></a>Configurations supplémentaires
Outre les stratégies ci-dessus, vous devez configurer les paramètres de gestion des applications et des appareils mobiles ci-après décrits dans cette section. 

### <a name="intune-mobile-application-management"></a>Gestion des applications mobiles Intune 

Pour vous assurer que la messagerie est protégée par les recommandations de stratégies indiquées précédemment pour chaque niveau de sécurité et de protection des données, vous devez créer des stratégies de protection des applications Intune à partir du Portail Azure.

Pour créer une stratégie de protection des applications, connectez-vous au Portail Microsoft Azure avec vos informations d’identification d’administrateur, puis accédez à **Protection d’application Intune > Paramètres > Stratégie d’application**.

Ajoutez une nouvelle stratégie (+Ajouter) comme illustré dans la capture d’écran suivante :

![Gestion des applications mobiles Intune](./media/secure-email/intune-mobile-app-mgmt.png)

>[!NOTE]
>Il existe de légères différences entre les options de stratégie de protection d’applications iOS et celles d’Android. La stratégie ci-dessous est propre à Android.
>

Les tableaux ci-après décrivent en détail les paramètres appropriés nécessaires pour exprimer les stratégies requises selon chaque niveau de protection.
| Le tableau suivant décrit les paramètres de stratégie de protection d’application Intune recommandés.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Généralités**|Email|Nom|Stratégie de messagerie sécurisée pour Android|Entrer un nom de stratégie|
|||Description||Entrer un texte décrivant la stratégie|
|||Plateforme|Android|Il existe de légères différences entre les options de stratégie de protection d’applications iOS et celles d’Android ; cette stratégie est propre à Android|
|**Applications**|Applications|Applications|Outlook|Sélectionné (liste)|
|**Paramètres**|Réadressage des données|Empêcher la sauvegarde Android|Oui|Sur iOS, ce paramètre appellera iTunes et iCloud|
||||Autoriser l’application à transférer des données vers d’autres applications|Applications gérées par la stratégie||
|||Autoriser l’application à recevoir des données d’autres applications|Applications gérées par la stratégie||
|||Interdire « Enregistrer sous »|Oui||
|||Restreindre les opérations couper, copier et coller avec d’autres applications|Applications gérées par la stratégie||
|||Limiter le contenu web à afficher dans Managed Browser|Non||
|||Chiffrer les données de l’application|Oui|Sur iOS, sélectionner l’option : Quand l’appareil est verrouillé|
|||Désactiver la synchronisation des contacts|Non||
||Access|Exiger un code confidentiel d’accès|Oui||
|||Nombre de tentatives avant réinitialisation du code confidentiel|3||
|||Autoriser un code confidentiel simple|Non||
|||Longueur du code confidentiel|6||
|||Autoriser les empreintes digitales à la place du code confidentiel|Oui||
|||Exiger des informations d’identification d’entreprise pour l’accès|Non||
|||Bloquer l’exécution des applications gérées sur les appareils jailbreakés ou rootés|Oui||
|||Revérifier les exigences d’accès après (minutes)|30||
|||Période de grâce hors connexion|720||
|||Intervalle hors connexion (jours) avant la réinitialisation des données d’application|90||
|||Bloquer la capture d’écran et l’Assistant Android|Non|Sur iOS, cette option n’est pas disponible|

Lorsque vous avez terminé, pensez à cliquer sur « Créer ». Répétez les étapes ci-dessus et remplacez la plateforme sélectionnée (liste déroulante) par iOS. Cette opération crée deux stratégies d’application. Par conséquent, une fois que vous avez créé la stratégie, attribuez-lui des groupes, puis déployez la stratégie.

### <a name="intune-mobile-device-management"></a>Gestion des appareils mobiles Intune
Vous créez les stratégies de configuration et de conformité ci-après en vous connectant au [Portail de gestion Microsoft (http://manage.microsoft.com)](https://manage.microsoft.com/) avec vos informations d’identification d’administrateur.

#### <a name="ios-email-profile"></a>Profil de messagerie iOS
Dans le [Portail de gestion Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), créez les stratégies de configuration ci-après à l’emplacement **Stratégie > Stratégies de configuration > Ajouter > iOS > Profil d’e-mail (iOS 8.0 et ultérieur)**.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Profil de messagerie**|Exchange Active Sync|Hôte|Outlook.office365.com||
|||Nom du compte|SecureEmailAccount|Choix d’administrateur|
|||Nom d’utilisateur|Nom d’utilisateur principal|Sélectionné - Liste déroulante|
|||Adresse de messagerie|Adresse SMTP principale|Sélectionné - Liste déroulante|
|||Méthode d’authentification|Nom d’utilisateur et mot de passe|Sélectionné - Liste déroulante|
|||Utiliser S/MIME|False||
||Paramètres de synchronisation|Nombre de jours de courrier électronique à synchroniser|Deux semaines|Sélectionné - Liste déroulante|
|||Utiliser SSL|true|Vérification|
|||Autoriser le déplacement des messages vers d’autres comptes e-mail|False||
|||Autoriser l’envoi de messages à partir d’applications tierces|true||
|||Synchroniser les adresses de messagerie récemment utilisées|true|Vérification|

#### <a name="ios-app-sharing-profile"></a>Profil de partage d’application iOS
Dans le [Portail de gestion Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), créez les stratégies de configuration ci-après à l’emplacement **Stratégie > Stratégies de configuration > Ajouter > iOS > Configuration générale (iOS 8.0 et ultérieur)**.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Sécurité**|Tout|Tout|Non configuré||
|**Cloud**|Tout|Tout|Non configuré||
|**Applications**|Browser|Tout|Non configuré||
||Applications|Autoriser l’installation d’applications|Non configuré||
|||Exiger un mot de passe pour accéder à la boutique d’applications|Non configuré||
|||Autoriser les achats dans l’application|Non configuré||
|||Autoriser les documents gérés dans d’autres applications non gérées (iOS 8.0 et ultérieur)|Non|Sélectionné - Liste déroulante|
|||Autoriser les documents non gérés dans d’autres applications gérées|Non configuré||
|||Autoriser la vidéoconférence|Non configuré||
|||Autoriser l’utilisateur à faire confiance aux nouveaux créateurs d’applications d’entreprise|Non configuré||
||Jeux|Tout|Non configuré||
||Contenu multimédia|Tout|Non configuré|||

#### <a name="android-email-profile"></a>Profil de messagerie Android
Dans le [Portail de gestion Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), créez les stratégies de configuration ci-après à l’emplacement **Stratégie > Stratégies de configuration > Ajouter > iOS > Profil de messagerie (Samsung KNOX Standard 4.0 et versions ultérieures)**.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Profil de messagerie**|Exchange Active Sync|Hôte| Outlook.office365.com|
|||Nom du compte|SecureEmailAccount|Choix d’administrateur|
|||Nom d’utilisateur|Nom d’utilisateur principal|Sélectionné - Liste déroulante|
|||Adresse de messagerie|Adresse SMTP principale|Sélectionné - Liste déroulante|
|||Méthode d’authentification|Nom d’utilisateur et mot de passe|Sélectionné - Liste déroulante|
|||Utiliser S/MIME|False||
||Paramètres de synchronisation|Nombre de jours de courrier électronique à synchroniser|Deux semaines|Sélectionné - Liste déroulante|
|||Planification de la synchronisation|Non configuré|Sélectionné - Liste déroulante|
|||Utiliser SSL|true|Vérification|
|||Type de contenu à synchroniser|||
|||Email|true|Cocher (verrouillé)|
|||Contacts|true|Vérification|
|||Calendrier|true|Vérification|
|||Tâches|true|Vérification|
|||Remarques|true|Vérification|

#### <a name="android-for-work-email-profile"></a>Profil de messagerie Android for Work
Dans le [Portail de gestion Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), créez les stratégies de configuration ci-après à l’emplacement **Stratégie > Stratégies de configuration > Ajouter > iOS > Profil d’e-mail (Android for Work - Gmail)**.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Profil de messagerie**|Exchange Active Sync|Hôte| Outlook.office365.com|
|||Nom du compte|SecureEmailAccount|Choix d’administrateur|
|||Nom d’utilisateur|Nom d’utilisateur principal|Sélectionné - Liste déroulante|
|||Adresse de messagerie|Adresse SMTP principale|Sélectionné - Liste déroulante|
|||Méthode d’authentification|Nom d’utilisateur et mot de passe|Sélectionné - Liste déroulante|
||Paramètres de synchronisation|Nombre de jours de courrier électronique à synchroniser|Deux semaines|Sélectionné - Liste déroulante|
|||Utiliser SSL|true|Vérification|

#### <a name="android-for-work-app-sharing-profile"></a>Profil de partage d’application Android for Work
Dans le [Portail de gestion Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), créez les stratégies de configuration ci-après à l’emplacement **Stratégie > Stratégies de configuration > Ajouter > iOS > Configuration générale (Android for Work)**.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Sécurité**|Mot de passe|Longueur minimale du mot de passe|Non configuré||
|||Nombre d’échecs de connexion successifs avant la suppression du profil professionnel|Non configuré||
|||Minutes d’inactivité avant le verrouillage de l’appareil|Non configuré||
|||Expiration du mot de passe (jours)|Non configuré||
|||Mémoriser l’historique des mots de passe|Non configuré||
|||Exiger un mot de passe pour déverrouiller l’appareil mobile|Non configuré||
|||Autoriser le déverrouillage par empreinte digitale (Android 6.0+)|Non configuré||
|||Autoriser Smart Lock et d’autres agents de confiance (Android 6.0+)|Non configuré||
||Paramètres du profil professionnel|Autoriser le partage de données entre profils professionnels et personnels|Les applications dans le profil professionnel peuvent gérer la demande de partage du profil personnel|Sélectionné - Liste déroulante|
|||Masquer les notifications du profil professionnel quand l’appareil est verrouillé (Android 6.0+)|Non configuré||
|||Définir une stratégie d’autorisation d’application par défaut (Android 6.0+)|Non configuré|||

#### <a name="device-compliance-policy"></a>Stratégie de conformité de l’appareil
Dans le [Portail de gestion Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), créez les stratégies de configuration ci-après à l’emplacement **Stratégie > Stratégie de conformité > Ajouter**.

|Catégories|Type|Propriétés|Valeurs|Remarques|
|:---------|:---|:---------|:-----|:----|
|**Sécurité système**|Mot de passe|Exiger un mot de passe pour déverrouiller les appareils mobiles (...)|Oui|Sélectionné - Liste déroulante|
|||Autoriser les mots de passe simples (...)|Non|Sélectionné - Liste déroulante|
|||Longueur minimale du mot de passe (...)|6|Sélectionné - Liste|
||Paramètres de mot de passe avancés|Tout|Non configuré||
||Chiffrement|Exiger le chiffrement sur l’appareil mobile (...)|Oui|Sélectionné - Liste déroulante|
||Profils de messagerie|Le compte e-mail doit être géré par Intune (iOS 8.0+)|Oui| Sélectionné - Liste déroulante|
|||Sélectionner||Doit sélectionner la stratégie de configuration de messagerie pour iOS : stratégie de messagerie iOS (voir les stratégies de configuration ci-dessus)|
|**Intégrité de l’appareil**|Attestation d’intégrité d’un appareil Windows|Exiger que les appareils soient signalés sains (Windows 10 Desktop/Mobile et ultérieur)|Oui||
||Paramètres de sécurité de l’appareil|Tout|Non configuré||
||Protection de l’appareil contre les menaces|Tout|Non configuré||
||Jailbreak|L’appareil ne doit pas être jailbreaké ou rooté (iOS 8.0+, Android 4.0+)|Oui||
|**Propriétés de l’appareil**|Version de système d’exploitation|Tout|Non configuré|||

Pour que toutes les stratégies ci-dessus soient considérées comme déployées, elles doivent cibler des groupes d’utilisateurs. Vous pouvez effectuer cette opération en créant la stratégie (lors de l’enregistrement) ou ultérieurement en sélectionnant Gérer le déploiement dans la section Stratégie (au même niveau que l’option Ajouter).

## <a name="remediating-events-that-have-results-in-medium-or-high-risk-access"></a>Résolution des événements qui signalent un accès présentant un niveau de risque moyen ou élevé
Si un utilisateur signale qu’il est désormais censé effectuer une authentification multifacteur alors qu’il n’y était pas contraint auparavant, le support technique peut examiner son état sur le plan des risques.  

Les utilisateurs au sein de l’organisation qui sont dotés d’un rôle Administrateur général ou Administrateur de la sécurité peuvent recourir à Azure AD Identity Protection pour examiner les événements à risque qui ont été pris en compte dans l’indice de risque calculé. Si un administrateur identifie certains événements qui ont été indiqués comme suspects, mais qui se sont révélés corrects (tels qu’une connexion à partir d’un emplacement inhabituel lorsqu’un employé est en vacances), il peut résoudre l’événement afin que ce dernier ne soit plus pris en compte dans l’indice de risque.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les services Office 365 et Enterprise Mobility + Security](secure-email-ems-office365-service-descriptions.md)

