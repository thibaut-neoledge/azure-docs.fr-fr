---
title: "Résolution des problèmes de l’inscription automatique des ordinateurs joints au domaine Azure Active Directory pour Windows 10 et Windows Server 2016 | Microsoft Docs"
description: "Résolution des problèmes de l’inscription automatique des ordinateurs joints au domaine Azure Active Directory pour Windows 10 et Windows Server 2016."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 2faf328e6622b9a1e3b529d62b61061659041fbd
ms.contentlocale: fr-fr
ms.lasthandoff: 03/10/2017


---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad--windows-10-and-windows-server-2016"></a>Résolution des problèmes de l’inscription automatique des ordinateurs joints au domaine à Azure AD – Windows 10 et Windows Server 2016

Cette rubrique s’applique aux clients suivants :

-   Windows 10
-   Windows Server 2016

Pour les autres clients Windows, consultez [Résolution des problèmes de l’inscription automatique des ordinateurs joints au domaine vers Azure AD pour les clients de bas niveau Windows](active-directory-device-registration-troubleshoot-windows-legacy.md).

Cette rubrique suppose que vous avez configuré l’inscription d’appareils automatique joints à un domaine comme décrit dans [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-device-registration-get-started.md) pour prendre en charge les scénarios suivants :

- [Accès conditionnel basé sur les appareils](active-directory-conditional-access-automatic-device-registration-setup.md)

- [Itinérance d’entreprise des paramètres](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello Entreprise](active-directory-azureadjoin-passport-deployment.md)


Ce document fournit des conseils sur la façon de résoudre les problèmes potentiels. 

L’inscription est prise en charge dans la Mise à jour Windows 10 de novembre 2015 et les versions ultérieures.  
Nous vous recommandons d’utiliser la mise à jour anniversaire pour activer les scénarios ci-dessus.

## <a name="step-1-retrieve-the-registration-status"></a>Étape 1 : Récupérer l’état de l’inscription 

**Pour récupérer l’état de l’inscription :**

1. Ouvrez une invite de commandes en tant qu’administrateur.

2. Entrez **dsregcmd /status**



    +----------------------------------------------------------------------+
    | Device State                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined : YES
     EnterpriseJoined : NO DeviceId : 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint : B753A6679CE720451921302CA873794D94C6204A KeyContainerId : bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider : Microsoft Platform Crypto Provider TpmProtected : YES KeySignTest: : MUST Run elevated to test.
                  Idp : login.windows.net TenantId : 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName : Contoso AuthCodeUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion : 1.0 JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion : 1.0 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId : urn:ms-drs:enterpriseregistration.windows.net DomainJoined : YES DomainName : CONTOSO
    
    +----------------------------------------------------------------------+
    | User State                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet : YES
               NgcKeyId : {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined : NO
          WamDefaultSet : YES
    WamDefaultAuthority : organizations         WamDefaultId : https://login.microsoft.com       WamDefaultGUID : {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt : YES



## <a name="step-2-evaluate-the-registration-status"></a>Étape 2 : Évaluer l’état de l’inscription 

Examinez les champs suivants et assurez-vous qu’ils disposent des valeurs attendues :

### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

Ce champ indique si l’appareil est inscrit auprès d’Azure AD. Si la valeur affichée est « NO », l’inscription n’est pas terminée. 

**Causes possibles :**

- Échec de l’authentification de l’ordinateur pour l’inscription.

- Il existe un proxy HTTP dans l’organisation qui ne peut pas être détecté par l’ordinateur

- L’ordinateur ne peut pas atteindre Azure AD pour l’authentification ou Azure DRS pour l’inscription

- L’ordinateur n’est peut-être pas sur le réseau interne de l’entreprise ou un réseau privé virtuel avec une vue directe sur un contrôleur de domaine AD local.

- Si l’ordinateur dispose d’un module de plateforme sécurisée, celui-ci est peut être en mauvais état.

- Il peut y avoir un problème de configuration des services mentionnés plus haut dans ce document que vous devez vérifier à nouveau. Voici des exemples courants :

    - Votre serveur de fédération n’a pas de points de terminaison WS-Trust activés

    - Votre serveur de fédération n’autorise peut-être pas l’authentification entrante à partir d’ordinateurs de votre réseau à l’aide de l’authentification Windows intégrée.

    - Il n’existe aucun objet de point de connexion de service qui pointe vers le nom de votre domaine vérifié dans Azure AD dans la forêt Active Directory à laquelle l’ordinateur appartient

---

### <a name="domainjoined--yes"></a>DomainJoined : YES  

Ce champ indique si l’appareil est joint à un répertoire Active Directory local ou non. Si la valeur affichée est **NO**, l’appareil ne peut pas s’enregistrer automatiquement auprès d’Azure AD. Vérifiez d’abord que l’appareil est joint au répertoire Active Directory local pour pouvoir s’enregistrer auprès d’Azure AD. Si vous voulez joindre l’ordinateur à Azure AD directement, consultez « Learn about capabilities of Azure Active Directory Join » (En savoir plus sur les fonctionnalités d’Azure Active Directory Join).

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

Ce champ indique si l’appareil est inscrit auprès d’Azure AD mais en tant qu’appareil personnel (avec la mention « Joint à l’espace de travail »). La valeur affichée doit être « NO » pour un ordinateur joint à un domaine inscrit auprès d’Azure AD, cependant si la valeur affichée est « YES », cela signifie qu’un compte professionnel ou scolaire a été ajouté avant que l’inscription de l’ordinateur ne soit terminée. Dans ce cas, le compte sera ignoré si la version Mise à jour Anniversaire de Windows 10 est utilisée (1607 avec l’exécution de la commande WinVer dans la fenêtre « Exécuter » ou une fenêtre d’invite de commandes).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : YES et AzureADPrt : YES
  
Ces champs indiquent que l’utilisateur s’est correctement authentifié auprès d’Azure AD lors de la connexion à l’appareil. Si la valeur affichée est « NO », voici quelques causes possibles :

- Clé de stockage défectueuse (STK) dans le module de plateforme sécurisée associé à l’appareil lors de l’inscription (vérifiez KeySignTest en cours d’exécution avec élévation de privilèges).

- ID de connexion de substitution

- Proxy HTTP introuvable

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [FAQ sur l’inscription d’appareils automatique](active-directory-device-registration-faq.md) 
