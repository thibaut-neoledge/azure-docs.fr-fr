---
title: "Stratégies de sécurisation de messagerie recommandées | Microsoft Docs"
description: "Décrit les recommandations de Microsoft en matière d’application de stratégies et de configurations de messagerie."
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
ms.openlocfilehash: 1d9577f44adcdefa0b0b8f0f2bafce8d0bda4697
ms.contentlocale: fr-fr
ms.lasthandoff: 07/15/2017

---

# <a name="recommended-policies"></a>Stratégies recommandées
 
Cet article décrit les stratégies recommandées pour aider nos clients à sécuriser la messagerie de leur organisation et les clients de messagerie qui prennent en charge l’authentification moderne et l’accès conditionnel. Cet article présente également les configurations de client de plateforme par défaut que nous vous recommandons pour garantir une expérience d’authentification unique optimale à vos utilisateurs, ainsi que les conditions préalables techniques pour l’accès conditionnel.

## <a name="prerequisites"></a>Composants requis

Avant d’implémenter les stratégies décrites dans le reste de ce document, votre organisation doit remplir plusieurs conditions préalables :
* [Configurer des réseaux nommés](https://docs.microsoft.com/azure/active-directory/active-directory-known-networks-azure-portal). Azure AD Identity Protection collecte et analyse toutes les données de session disponibles pour générer un indice de risque. Nous vous recommandons de spécifier les plages d’adresses IP publiques du réseau de votre organisation dans la configuration des réseaux nommés Azure Active Directory (Azure AD). Le trafic émanant de ces plages présente un indice de risque réduit, alors que le trafic provenant de l’extérieur de l’environnement d’entreprise est considéré comme doté d’un indice de risque plus élevé.
* [Inscrire tous les utilisateurs auprès du service Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-manage-users-and-devices). Azure AD Identity Protection utilise Azure MFA pour procéder à une vérification de sécurité supplémentaire. Nous vous recommandons d’imposer à tous les utilisateurs de s’inscrire préalablement auprès d’Azure MFA.
* [Activer l’inscription automatique des ordinateurs Windows joints à un domaine](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup). L’accès conditionnel peut garantir le fait que l’appareil qui se connecte au service est un appareil joint à un domaine ou un appareil conforme. Pour que cette fonctionnalité soit prise en charge sur les ordinateurs Windows, l’appareil doit être inscrit auprès d’Azure AD.  Cet article explique comment configurer l’inscription automatique des appareils.  Notez que cette opération requiert les services de fédération Active Directory (AD FS).
* **Préparer votre équipe de support**. Mettez en place un plan pour les utilisateurs qui ne sont pas en mesure d’effectuer l’authentification multifacteur. Ce plan peut consister à ajouter ces utilisateurs à un groupe d’exclusions de stratégie ou à inscrire de nouvelles informations d’authentification multifacteur pour ces utilisateurs. Avant d’effectuer l’une de ces modifications de sécurité sensibles, vous devez vous assurer que l’utilisateur lui-même en effectue la demande. Le fait d’impliquer les responsables des utilisateurs dans l’approbation de cette opération constitue une approche efficace.
* [Configurer l’écriture différée du mot de passe dans AD au niveau local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). L’écriture différée du mot de passe permet à Azure AD d’imposer aux utilisateurs de changer leur mot de passe local en cas de détection d’un risque élevé de compte compromis. Vous pouvez activer cette fonctionnalité à l’aide d’Azure AD Connect de deux manières. Vous pouvez activer l’écriture différée de mot de passe dans l’écran de fonctionnalités facultatives de l’Assistant Installation d’Azure AD Connect ou en utilisant Windows PowerShell.  
* [Activer l’authentification moderne](https://support.office.com/article/Enable-Exchange-Online-for-modern-authentication-58018196-f918-49cd-8238-56f57f38d662) et [protéger les points de terminaison hérités](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-supported-apps).  L’accès conditionnel fonctionne avec les applications mobiles et de bureau qui utilisent l’authentification moderne. Si l’application utilise des protocoles d’authentification hérités, elle peut bénéficier d’un accès en dépit des conditions appliquées. Il est important de connaître les applications qui peuvent utiliser les règles d’accès conditionnel, ainsi que les étapes à suivre pour sécuriser les autres points d’entrée des applications.
* [Activer Azure Information Protection](https://docs.microsoft.com/information-protection/get-started/infoprotect-tutorial-step1) en activant Rights Management. Utilisez Azure Information Protection avec la messagerie pour commencer à classer vos e-mails. Suivez le didacticiel de démarrage rapide pour personnaliser et publier la stratégie.  

> [!NOTE]
> Les recommandations ci-après reposent sur trois différents niveaux de sécurité et de protection de votre messagerie qui sont applicables en fonction de la granularité de vos besoins : [protection de référence, protection des données sensibles et protection des environnements hautement réglementés](https://docs.microsoft.com/azure/active-directory/secure-email-introduction). Pour plus d’informations, téléchargez le document infographique [Protection des appareils et de l’identité pour Office 365](https://go.microsoft.com/fwlink/p/?linkid=841656).  


## <a name="baseline"></a>Ligne de base 
Cette section décrit les recommandations en matière de sécurisation de messagerie pour le niveau de protection de référence des données, des identités et des appareils. Ces recommandations doivent répondre aux exigences de protection par défaut d’un grand nombre d’organisations.
>[!NOTE]
>Les stratégies ci-après sont cumulatives et reposent les unes sur les autres. Chaque section décrit uniquement les ajouts appliqués à chaque niveau.
>

### <a name="conditional-access-policy-settings"></a>Paramètres de stratégie d’accès conditionnel

#### <a name="identity-protection"></a>Identity Protection 
Vous pouvez offrir aux utilisateurs une expérience d’authentification unique (SSO), comme décrit dans les sections précédentes. Vous n’avez besoin d’intervenir que lorsque des [événements à risque](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) l’exigent.  

* Exiger l’authentification multifacteur pour les connexions présentant un niveau de risque moyen ou plus élevé
* Exiger le changement du mot de passe sécurisé pour les utilisateurs à haut risque

>[!IMPORTANT]
>Cette recommandation de stratégie requiert la [synchronisation de mot de passe](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) et la [réinitialisation du mot de passe en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords).
>

#### <a name="data-loss-prevention"></a>Prévention contre la perte de données 
Les stratégies de gestion des appareils et des applications ont pour fonction de vous protéger contre la perte de données si l’un de vos appareils est perdu ou volé. Vous pouvez effectuer cette opération en garantissant que l’accès aux données est protégé par un code confidentiel, que les données sont chiffrées sur l’appareil et que l’appareil n’est pas compromis.

|Recommandation de stratégie|Description|
|:--------------------|:----------|
|**Require user PC management** (Exiger la gestion des PC des utilisateurs)|Demander aux utilisateurs de joindre leurs PC à un domaine Active Directory ou d’inscrire leurs PC auprès du système de gestion avec Intune ou le Gestionnaire de configuration|
|**Apply security settings via group policy objects (GPO) or Configuration Manager policies for domain joined PCs** (Appliquer des paramètres de sécurité par le biais d’objets de stratégie de groupe (GPO) ou de stratégies du Gestionnaire de configuration pour les PC joints à un domaine)|Déployer des stratégies qui configurent les PC gérés de façon à activer BitLocker, l’antivirus et le pare-feu|
|**Require user mobile device management** (Exiger la gestion des appareils mobiles des utilisateurs)|Exiger que les appareils d’utilisateur permettant d’accéder à la messagerie soient gérés par Intune ou que la messagerie d’entreprise soit uniquement accessible par le biais d’applications de messagerie mobiles protégées par des stratégies Intune App Protection telles qu’Outlook Mobile|
|**Apply an Intune Device Compliance Policy on managed devices** (Appliquer une stratégie de conformité d’appareil Intune sur les appareils gérés)|Appliquer une stratégie de conformité d’appareil Intune pour les appareils mobiles d’entreprise gérés et les PC gérés par Intune qui requiert les éléments suivants : code confidentiel comprenant au moins 6 caractères, chiffrement de l’appareil, appareil intègre (non jailbreaké ni rooté ; attestation d’intégrité) et, le cas échéant, exiger des appareils à niveau de risque faible tel que déterminé par un MTP tiers comme Lookout ou SkyCure|
|**Apply an Intune App Protection Policy for managed apps running on unmanaged devices** (Appliquer une stratégie Intune App Protection pour les applications gérées s’exécutant sur des appareils non gérés)|Appliquer une stratégie Intune App Protection pour les applications gérées s’exécutant sur des appareils mobiles personnels non gérés qui requiert les éléments suivants : code confidentiel comprenant au moins 6 caractères, chiffrement de l’appareil et appareil intègre (non jailbreaké ni rooté ; attestation d’intégrité)|

### <a name="user-impact"></a>Impact sur les utilisateurs

Il est important que la plupart des organisations soient en mesure de définir les attentes concernant le moment et les conditions selon lesquels les utilisateurs seront censés se connecter à Office 365 pour accéder à leur courrier électronique.  

Les utilisateurs bénéficient généralement de l’authentification unique (SSO), sauf dans les situations suivantes : 
* Lors de la demande de jetons d’authentification pour Exchange Online :
  * Les utilisateurs peuvent être invités à effectuer une authentification multifacteur chaque fois qu’une connexion présentant un niveau de risque moyen ou plus élevé est détectée et que les utilisateurs n’ont pas encore procédé à une authentification MFA dans leur session actuelle.  
  * Les utilisateurs sont tenus d’utiliser des applications de messagerie qui prennent en charge le Kit de développement logiciel (SDK) Intune App Protection ou d’accéder à leurs e-mails à partir d’appareils conformes Intune ou joints à un domaine Active Directory. 
* Lorsque des utilisateurs à risque se connectent et parviennent à effectuer une authentification MFA, ils sont invités à changer leur mot de passe.

## <a name="sensitive"></a>Sensible

Cette section décrit les recommandations en matière de sécurisation de messagerie pour le niveau de protection sensible des données, des identités et des appareils. Ces recommandations sont destinées aux clients dont un sous-ensemble des données ou la totalité des données requièrent un niveau de protection supérieur. 

Vous pouvez appliquer une protection renforcée à tous les jeux de données ou à certains d’entre eux dans votre environnement Office 365. Par exemple, vous pouvez appliquer des stratégies garantissant que les données sensibles sont uniquement partagées par des applications protégées afin d’éviter toute perte de données. Nous vous recommandons de protéger les identités et les appareils accédant aux données sensibles avec des niveaux de sécurité comparables. 

### <a name="conditional-access-policy-settings"></a>Paramètres de stratégie d’accès conditionnel
#### <a name="identity-protection"></a>Identity Protection 

Vous pouvez offrir aux utilisateurs une expérience d’authentification unique (SSO), comme décrit dans les sections précédentes. Vous n’avez besoin d’intervenir que lorsque des [événements à risque](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) l’exigent.   

* Exiger l’authentification multifacteur dans les sessions à niveau de risque faible ou plus élevé
 * Exiger le changement du mot de passe sécurisé pour les utilisateurs à haut risque

>[!IMPORTANT]
>Cette recommandation de stratégie requiert la [synchronisation de mot de passe](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) et la [réinitialisation du mot de passe en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords).
>

#### <a name="data-loss-prevention"></a>Prévention contre la perte de données 

Ces stratégies de gestion des appareils et des applications ont pour fonction de vous protéger contre la perte de données si l’un de vos appareils est perdu ou volé. Vous pouvez effectuer cette opération en garantissant que l’accès aux données est protégé par un code confidentiel, que les données sont chiffrées sur l’appareil et que l’appareil n’est pas compromis.

|Recommandation de stratégie|Description|
|:--------------------|:----------|
|**Require user PC management** (Exiger la gestion des PC des utilisateurs)|Demander aux utilisateurs de joindre leurs PC à un domaine Active Directory ou d’inscrire leurs PC auprès du système de gestion avec Intune ou le Gestionnaire de configuration, et garantir que ces appareils sont conformes aux stratégies avant d’autoriser l’accès à la messagerie|
|**Apply security settings via group policy objects (GPO) or Configuration Manager policies for domain joined PCs** (Appliquer des paramètres de sécurité par le biais d’objets de stratégie de groupe (GPO) ou de stratégies du Gestionnaire de configuration pour les PC joints à un domaine)|Déployer des stratégies qui configurent les PC gérés de façon à activer BitLocker, l’antivirus et le pare-feu|
|**Require user mobile device management** (Exiger la gestion des appareils mobiles des utilisateurs)|Exiger que les appareils d’utilisateur permettant d’accéder à la messagerie soient gérés par Intune ou que la messagerie d’entreprise soit uniquement accessible par le biais d’applications de messagerie mobiles protégées par des stratégies Intune App Protection telles qu’Outlook Mobile|
|**Apply an Intune Device Compliance Policy on managed devices** (Appliquer une stratégie de conformité d’appareil Intune sur les appareils gérés)|Appliquer une stratégie de conformité d’appareil Intune pour les appareils mobiles d’entreprise gérés et les PC gérés par Intune qui requiert les éléments suivants : code confidentiel comprenant au moins 6 caractères, chiffrement de l’appareil, appareil intègre (non jailbreaké ni rooté ; attestation d’intégrité) et, le cas échéant, exiger des appareils à niveau de risque faible tel que déterminé par un MTP tiers comme Lookout ou SkyCure|
|**Apply an Intune App Protection Policy for managed apps running on unmanaged devices** (Appliquer une stratégie Intune App Protection pour les applications gérées s’exécutant sur des appareils non gérés)|Appliquer une stratégie Intune App Protection pour les applications gérées s’exécutant sur des appareils mobiles personnels non gérés qui requiert les éléments suivants : code confidentiel comprenant au moins 6 caractères, chiffrement de l’appareil et appareil intègre (non jailbreaké ni rooté ; attestation d’intégrité)|

### <a name="user-impact"></a>Impact sur les utilisateurs

Il est important que la plupart des organisations soient en mesure de définir les attentes concernant le moment et les conditions selon lesquels les utilisateurs seront censés se connecter à la messagerie Office 365. 

Les utilisateurs bénéficient généralement de l’authentification unique (SSO), sauf dans les situations suivantes : 
* Lors de la demande de jetons d’authentification pour Exchange Online :
  * Les utilisateurs sont invités à effectuer une authentification multifacteur chaque fois qu’une connexion présentant un niveau de risque faible ou plus élevé est détectée et que les utilisateurs n’ont pas encore procédé à une authentification MFA dans leur session actuelle.  
  * Les utilisateurs sont tenus d’utiliser des applications de messagerie qui prennent en charge le Kit de développement logiciel (SDK) Intune App Protection ou d’accéder à leurs e-mails à partir d’appareils conformes Intune ou joints à un domaine Active Directory. 
* Lorsque des utilisateurs à risque se connectent et parviennent à effectuer une authentification MFA, ils sont invités à changer leur mot de passe.

## <a name="highly-regulated"></a>Hautement réglementé
Cette section décrit les recommandations en matière de sécurisation de messagerie pour le niveau de protection hautement réglementé des données, des identités et des appareils. Ces recommandations sont destinées aux clients qui disposent d’une très petite quantité de données hautement classifiées, réglementées ou de type secret commercial. Microsoft offre des fonctionnalités pour aider les organisations à respecter ces exigences, y compris une protection renforcée des appareils et des identités. 

### <a name="conditional-access-policy-settings"></a>Paramètres de stratégie d’accès conditionnel
#### <a name="identity-protection"></a>Identity Protection 

Pour le niveau hautement réglementé, Microsoft recommande d’appliquer l’authentification multifacteur à toutes les nouvelles sessions.
* Exiger l’authentification multifacteur pour toutes les nouvelles sessions
* Exiger le changement du mot de passe sécurisé pour les utilisateurs à haut risque

>[!IMPORTANT]
>Cette recommandation de stratégie requiert la [synchronisation de mot de passe](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) et la [réinitialisation du mot de passe en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords).
>

#### <a name="data-loss-prevention"></a>Prévention contre la perte de données
Ces stratégies de gestion des appareils et des applications ont pour fonction de vous protéger contre la perte de données si l’un de vos appareils est perdu ou volé. Vous effectuez cette opération en garantissant que l’accès aux données est protégé par un code confidentiel, que les données sont chiffrées sur l’appareil et que l’appareil n’est pas compromis.

Pour le niveau hautement réglementé, nous vous recommandons d’exiger des applications qui prennent en charge une stratégie Intune App Protection s’exécutant uniquement sur des appareils conformes Intune ou joints à un domaine.

|Recommandation de stratégie|Description|
|:--------------------|:----------|
|**Require user PC management** (Exiger la gestion des PC des utilisateurs)|Demander aux utilisateurs de joindre leurs PC à un domaine Active Directory ou d’inscrire leurs PC auprès du système de gestion avec Intune ou le Gestionnaire de configuration, et garantir que ces appareils sont conformes aux stratégies avant d’autoriser l’accès à la messagerie|
|**Apply security settings via group policy objects (GPO) or Configuration Manager policies for domain joined PCs** (Appliquer des paramètres de sécurité par le biais d’objets de stratégie de groupe (GPO) ou de stratégies du Gestionnaire de configuration pour les PC joints à un domaine)|Déployer des stratégies qui configurent les PC gérés de façon à activer BitLocker, l’antivirus et le pare-feu|
|**Require user mobile device management** (Exiger la gestion des appareils mobiles des utilisateurs)|Exiger que les appareils d’utilisateur permettant d’accéder à la messagerie soient gérés par Intune ou que la messagerie d’entreprise soit uniquement accessible par le biais d’applications de messagerie mobiles protégées par des stratégies Intune App Protection telles qu’Outlook Mobile|
|**Apply an Intune Device Compliance Policy on managed devices** (Appliquer une stratégie de conformité d’appareil Intune sur les appareils gérés)|Appliquer une stratégie de conformité d’appareil Intune pour les appareils mobiles d’entreprise gérés et les PC gérés par Intune qui requiert les éléments suivants : code confidentiel comprenant au moins 6 caractères, chiffrement de l’appareil, appareil intègre (non jailbreaké ni rooté ; attestation d’intégrité) et, le cas échéant, exiger des appareils à niveau de risque faible tel que déterminé par un MTP tiers comme Lookout ou SkyCure|

### <a name="user-impact"></a>Impact sur les utilisateurs
Il est important que la plupart des organisations soient en mesure de définir les attentes concernant le moment et les conditions selon lesquels les utilisateurs seront censés se connecter à la messagerie Office 365. 

* La durée de vie maximale d’une session d’authentification unique est de 1 jour. Après l’arrivée à expiration de leur session, les utilisateurs doivent se ré-authentifier avec MFA.
* Lorsque des utilisateurs à risque se connectent et procèdent à une authentification MFA, ils sont invités à changer leur mot de passe.
* Lors de la demande de jetons d’authentification pour Exchange Online :
  * Les utilisateurs sont invités à effectuer une authentification multifacteur chaque fois qu’ils démarrent une session.  
  * Les utilisateurs sont tenus d’utiliser des applications de messagerie qui prennent en charge le Kit de développement logiciel (SDK) Intune App Protection.
  * Les utilisateurs doivent accéder à leurs e-mails à partir d’appareils conformes Intune ou joints à un domaine Active Directory. 
 
 ## <a name="next-steps"></a>Étapes suivantes
 [Déployer les stratégies recommandées pour sécuriser la messagerie](secure-email-deploy-recommended-policies.md)

