---
title: "Azure AD Connect : Résoudre les problèmes d’authentification unique transparente | Microsoft Docs"
description: "Cette rubrique explique comment résoudre les problèmes relatifs à l’authentification unique transparente Azure Active Directory (SSO transparente Azure AD)."
services: active-directory
keywords: "Qu’est-ce qu’Azure AD Connect, Installation d’Active Directory, Composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: a543be452abbbe3057a5e275612968cd52c8b7aa
ms.contentlocale: fr-fr
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Comment résoudre les problèmes d’authentification unique transparente Azure Active Directory

## <a name="known-issues"></a>Problèmes connus

- Si vous synchronisez plus de 30 forêts AD à l’aide d’Azure AD Connect, l’Assistant utilisé pour configurer l’authentification unique (SSO) transparente ne fonctionne pas correctement. Pour résoudre ce problème, vous pouvez [activer manuellement](#manual-reset-of-azure-ad-seamless-sso) la fonctionnalité d’authentification unique (SSO) transparente sur votre locataire.
- Ajout d’URL de service Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) à la zone « Sites de confiance » au lieu de la zone « Intranet local ».

## <a name="troubleshooting-checklist"></a>Liste de contrôle pour la résolution des problèmes

Utilisez la liste de contrôle suivante pour résoudre les problèmes d’authentification unique (SSO) transparente Azure AD :

1. Vérifiez si la fonctionnalité d’authentification unique transparente est activée sur votre locataire dans l’outil Azure AD Connect. Si vous ne pouvez pas activer la fonctionnalité (par exemple, en raison d’un port bloqué), assurez-vous que toutes les [conditions préalables](active-directory-aadconnect-sso.md#pre-requisites) sont bien respectées. Si vous rencontrez toujours des problèmes d’activation de la fonctionnalité, contactez le Support Microsoft.
2. Les deux URL de service (https://autologon.microsoftazuread-sso.com et https://aadg.windows.net.nsatc.net) sont définies dans les paramètres de la zone Intranet.
3. Vérifiez que le bureau d’entreprise est bien joint au domaine AD.
4. Vérifiez que l’utilisateur est connecté au bureau par le biais d’un compte de domaine AD.
5. Vérifiez que le compte de l’utilisateur provient d’une forêt AD dans laquelle l’authentification unique (SSO) transparente a été configurée.
6. Vérifiez que l’ordinateur est connecté au réseau d’entreprise.
7. Vérifiez que l’heure de l’ordinateur est synchronisée avec celle d’Active Directory et du contrôleur de domaine : elle ne doit pas compter plus de 5 minutes d’écart.
8. Videz les tickets Kerberos existants à partir de leur ordinateur. Pour ce faire, exécutez la commande **klist purge** à partir d’une invite de commandes. Les tickets Kerberos des utilisateurs sont généralement valides pendant 12 heures. Notez que vous avez peut-être configuré une autre valeur dans Active Directory.
9. Passez en revue les journaux de console du navigateur (sous Outils de développement) pour déterminer les problèmes potentiels.
10. Examinez également les [journaux des contrôleurs de domaine](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Journaux du contrôleur de domaine

Si l’audit des réussites est activé sur votre contrôleur de domaine, dès qu’un utilisateur se connecte à l’aide de l’authentification unique transparente, une entrée de sécurité (événement 4769 associé au compte d’ordinateur **AzureADSSOAcc$**) est enregistrée dans le journal des événements. Vous trouverez ces événements de sécurité au moyen de la requête suivante :

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-azure-ad-seamless-sso"></a>Réinitialisation manuelle de l’authentification unique (SSO) transparente Azure AD

Si la résolution des problèmes ne permet pas de résoudre le problème, effectuez les étapes suivantes pour réinitialiser/activer manuellement la fonctionnalité sur votre locataire :

### <a name="1-import-the-seamless-sso-powershell-module"></a>1. Importer le module PowerShell Authentification unique (SSO) transparente

- Premièrement, téléchargez et installez [l’Assistant de connexion Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
- Ensuite, téléchargez et installez le [Module Azure Active Directory 64 bits pour Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
- Accédez au dossier `%programfiles%\Microsoft Azure Active Directory Connect`.
- Importez le module PowerShell Authentification unique (SSO) transparente à l’aide de la commande suivante : `Import-Module .\AzureADSSO.psd1`.

### <a name="2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>2. Obtenir la liste des forêts AD sur lesquelles l’authentification unique (SSO) transparente a été activée

- Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Une fenêtre contextuelle vous permettant d’entrer vos informations d’identification d’administrateur de locataire Azure AD doit s’afficher.
- Appelez `Get-AzureADSSOStatus`. Cette opération vous fournit la liste des forêts AD (examinez la liste « Domaines ») sur lesquelles cette fonctionnalité a été activée.

### <a name="3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>3. Désactiver l’authentification unique (SSO) transparente pour chaque forêt AD sur laquelle elle a été configurée

- Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Une fenêtre contextuelle vous permettant d’entrer vos informations d’identification d’administrateur de locataire Azure AD doit s’afficher.
- Appelez `$creds = Get-Credential`. Une fenêtre contextuelle vous permettant d’entrer les informations d’identification de l’administrateur de domaine pour la forêt AD souhaitée doit s’afficher.
- Appelez `Disable-AzureADSSOForest -OnPremCredentials $creds`. Cette opération supprime le compte d’ordinateur AZUREADSSOACCT du contrôleur de domaine local et désactive cette fonctionnalité pour cette forêt AD spécifique.
- Répétez les étapes ci-dessus pour chaque forêt AD sur laquelle vous avez configurée la fonctionnalité.

### <a name="4-enable-seamless-sso-for-each-ad-forest"></a>4. Activer l’authentification unique (SSO) transparente pour chaque forêt AD

- Appelez `New-AzureADSSOAuthenticationContext`. Une fenêtre contextuelle vous permettant d’entrer vos informations d’identification d’administrateur de locataire Azure AD doit s’afficher.
- Appelez `Enable-AzureADSSOForest`. Une fenêtre contextuelle vous permettant d’entrer les informations d’identification de l’administrateur de domaine pour la forêt AD souhaitée doit s’afficher.
- Répétez les étapes ci-dessus pour chaque forêt AD sur laquelle vous souhaitez configurer la fonctionnalité.

### <a name="5-enable-seamless-sso-on-your-tenant"></a>5. Activer l’authentification unique (SSO) transparente sur votre locataire

- Appelez `New-AzureADSSOAuthenticationContext`. Une fenêtre contextuelle vous permettant d’entrer vos informations d’identification d’administrateur de locataire Azure AD doit s’afficher.
- Appelez `Enable-AzureADSSO`, puis tapez « true » à l’invite `Enable: ` pour activer la fonctionnalité de votre locataire.

