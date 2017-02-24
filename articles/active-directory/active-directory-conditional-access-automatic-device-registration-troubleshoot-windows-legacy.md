---
title: "Résolution des problèmes de l’inscription automatique des ordinateurs joints au domaine Azure Active Directory pour les clients de bas niveau Windows | Microsoft Docs"
description: "Résolution des problèmes de l’inscription automatique des ordinateurs joints au domaine Azure Active Directory pour les clients de bas niveau Windows."
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
ms.date: 01/31/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 1b12d8ef3bbbfd2688f7bbd47968332e83a35252
ms.openlocfilehash: 6cb5aee751e89ad1e825fae4fdfd460f1bfd8b6c


---
# <a name="troubleshooting-the-auto-registration-of-azure-ad-domain-joined-computers-for-windows-down-level-clients"></a>Résolution des problèmes de l’inscription automatique des ordinateurs joints au domaine Azure Active Directory pour les clients de bas niveau Windows 

Cette rubrique s’applique uniquement aux clients suivants : 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Pour Windows 10 ou Windows Server 2016, consultez [Résolution des problèmes de l’inscription automatique des ordinateurs joints au domaine Azure Active Directory pour Windows 10 et Windows Server 2016](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows.md).

Cette rubrique suppose que vous avez configuré l’inscription automatique d’appareils joints à un domaine comme décrit dans [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration-get-started.md).
 
Cette rubrique vous fournit des conseils sur la façon de résoudre les problèmes potentiels.  
Voici quelques points à noter pour obtenir des résultats corrects : 

- L’inscription de ces clients sur Azure AD est par utilisateur/appareil. Par exemple, si jdoe et jharnett se connectent à cet appareil, un enregistrement distinct (DeviceID) est créé pour chacun de ces utilisateurs dans l’onglet des informations UTILISATEUR.  

- L’inscription prédéfinie de ces clients est configurée pour être tentée à l’ouverture de session ou au verrouillage/déverrouillage, et il peut y avoir un délai de 5 minutes déclenché par une tâche du Planificateur de tâches. 

- Une réinstallation du système d’exploitation, ou une désinscription et une réinscription manuelles, peuvent créer une nouvelle inscription sur Azure AD et aboutir à plusieurs entrées sous l’onglet des informations UTILISATEUR dans le portail Azure. 


## <a name="step-1-retrieve-the-registration-status"></a>Étape 1 : Récupérer l’état de l’inscription 

**Pour vérifier l’état de l’inscription :**  

1. Ouvrez une invite de commandes en tant qu’administrateur. 

2. Saisissez `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

Cette commande affiche une boîte de dialogue qui vous donne plus de détails sur l’état de la jonction.

![Workplace Join pour Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-registration-status"></a>Étape 2 : Évaluer l’état de l’inscription 

Si la jonction n’a pas réussi, la boîte de dialogue vous fournit plus d’informations sur le problème qui s’est produit.

**Les tâches les plus courantes sont :**

- Une mauvaise configuration d’AD FS ou d’Azure AD

    ![Workplace Join pour Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/02.png)

- Vous n’êtes pas connecté en tant qu’utilisateur du domaine

    ![Workplace Join pour Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/03.png)

- Un quota a été atteint.

    ![Workplace Join pour Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/04.png)

- Le service ne répond pas. 

    ![Workplace Join pour Windows](./media/active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy/05.png)

Vous pouvez également trouver les informations d’état dans le journal des événements sous **Applications and Services Log\Microsoft-Workplace Join**.
  
**Les causes les plus courantes d’un échec d’inscription sont :** 

- Votre ordinateur n’est pas sur le réseau interne de l’entreprise ou un réseau privé virtuel sans connexion à un contrôleur de domaine Active Directory local.

- Vous êtes connecté à votre ordinateur avec un compte d’ordinateur local. 

- Problèmes de configuration du service : 

  - Le serveur de fédération a été configuré pour prendre en charge **WIAORMULTIAUTHN**. 

  - Il n’existe aucun objet de point de connexion de service qui pointe vers le nom de votre domaine vérifié dans Azure AD dans la forêt Active Directory à laquelle l’ordinateur appartient.

  - Un utilisateur a atteint la limite d’appareils. Consultez Prise en main du service Azure Active Directory Device Registration.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [FAQ sur l’inscription d’appareils automatique](active-directory-conditional-access-automatic-device-registration-faq.md) 



<!--HONumber=Feb17_HO2-->


