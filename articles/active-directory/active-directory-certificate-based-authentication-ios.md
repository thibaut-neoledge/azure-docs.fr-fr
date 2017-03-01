---
title: Authentification par certificat Azure Active Directory sur iOS | Microsoft Docs
description: "En savoir plus sur les scénarios pris en charge et la configuration requise pour la configuration de l’authentification par certificat dans les solutions avec les appareils iOS"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: c4d95068b5e6472dbb809e675a47cb9eada7ca66
ms.openlocfilehash: f643deb0b76c598df9bd983474b4bd2bcba129e7
ms.lasthandoff: 02/16/2017


---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Authentification par certificat Azure Active Directory sur iOS

L’authentification par certificat (CBA) vous permet d’être authentifié par Azure Active Directory avec un certificat client sur un appareil Windows, Android ou iOS lors de la connexion de votre compte Exchange Online à : 

* Des applications mobiles Office, telles que Microsoft Outlook et Microsoft Word ;   
* Des clients Exchange ActiveSync (EAS). 

La configuration de cette fonctionnalité élimine le besoin d’entrer un nom d’utilisateur et un mot de passe dans certaines applications de messagerie et Microsoft Office sur votre appareil mobile. 

Cette rubrique vous indique la configuration requise et les scénarios pris en charge pour la configuration de l’authentification par certificat sur un appareil Android pour les utilisateurs de clients dans les plans Office 365 Enterprise, Business, Education et US Government. 

Cette fonctionnalité est disponible en version préliminaire dans les plans Office 365 US Government Defense et US Government Federal.




## <a name="office-mobile-applications-support"></a>Prise en charge des applications mobiles Office

| Applications | Support |
| --- | --- |
| Word / Excel / PowerPoint |![Vérification][1] |
| OneNote |![Vérification][1] |
| OneDrive |![Vérification][1] |
| Outlook |![Vérification][1] |
| Yammer |![Vérification][1] |
| Skype Entreprise |Bientôt disponible |

## <a name="requirements"></a>Configuration requise 

La version du système d’exploitation de l’appareil doit être iOS 9 ou toute version ultérieure 

Un serveur de fédération doit être configuré.  

Microsoft Authenticator est requis pour les applications Office sur iOS.  

Pour qu’Azure Active Directory révoque un certificat client, le jeton ADFS doit posséder les déclarations suivantes :  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (Le numéro de série du certificat client) 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (La chaîne de l’émetteur du certificat client) 

Azure Active Directory ajoute ces déclarations au jeton d’actualisation si elles sont disponibles dans le jeton ADFS (ou n’importe quel autre jeton SAML). Lorsque le jeton d’actualisation doit être validé, ces informations sont utilisées pour vérifier la révocation. 

La meilleure pratique consiste à mettre à jour les pages d’erreur ADFS avec les éléments suivants :

* La configuration requise pour l’installation de Microsoft Authenticator sur iOS
* Instructions sur l’obtention d’un certificat utilisateur. 

Pour plus d’informations, consultez [Personnalisation des pages de connexion AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Certaines applications Office (avec l’authentification moderne activée) envoient « *prompt=login* » à Azure AD dans leur demande. Par défaut, Azure AD traduit cela dans la demande aux services ADFS en « *wauth=usernamepassworduri* » (demande aux services ADFS d’effectuer l’authentification U/P) et « *wfresh=0* » (demande aux services ADFS d’ignorer l’état d’authentification unique et d’effectuer une nouvelle authentification). Si vous souhaitez activer l’authentification par certificat pour ces applications, vous devez modifier le comportement par défaut d’Azure AD. Définissez simplement « *PromptLoginBehavior* » dans vos paramètres de domaine fédéré sur « *Désactivé* ». Vous pouvez utiliser l’applet de commande [MSOLDomainFederationSettings](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msoldomainfederationsettings) pour effectuer cette tâche :

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`
  

## <a name="exchange-activesync-clients-support"></a>Prise en charge des clients Exchange ActiveSync
Dans iOS 9 ou version ultérieure, le client de messagerie iOS natif est pris en charge. Pour toutes les autres applications Exchange ActiveSync, pour savoir si cette fonctionnalité est prise en charge, contactez le développeur de votre application.  


## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez configurer l’authentification par certificat dans votre environnement, consultez [Bien démarrer avec l’authentification par certificat sur Android](active-directory-certificate-based-authentication-get-started.md) pour obtenir des instructions.


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png

