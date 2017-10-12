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
ms.date: 08/26/2017
ms.author: billmath
ms.openlocfilehash: 7eea3621a52bf13dc44e89c342c503905ff24a0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Résoudre les problèmes d’authentification unique transparente Azure Active Directory

Cet article fournit des informations sur les problèmes courants liés à l’authentification unique transparente Azure AD.

## <a name="known-issues"></a>Problèmes connus

- Dans certains cas, l’activation de l’authentification unique transparente peut prendre jusqu’à 30 minutes.
- La prise en charge du navigateur Edge n’est pas disponible.
- L’activation de la licence sur les clients Office, surtout dans les scénarios d’ordinateurs partagés, provoque l’affichage d’invites de connexion supplémentaires pour les utilisateurs.
- L’authentification unique transparente ne fonctionne pas en mode Navigation privée sur Firefox. and 
- L’authentification unique transparente ne fonctionne pas sur Internet Explorer quand le mode protégé amélioré est activé.
- L’authentification unique transparente ne fonctionne pas sur les navigateurs mobiles iOS et Android.
- Si vous synchronisez 30 forêts AD ou plus, vous ne pouvez pas activer l’authentification unique transparente à l’aide d’Azure AD Connect. En guise de solution de contournement, vous pouvez [activer manuellement](#manual-reset-of-azure-ad-seamless-sso) la fonctionnalité pour votre locataire.
- L’ajout des URL du service Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) à la zone « Sites de confiance » plutôt qu’à la zone « Intranet local », **empêche les utilisateurs de se connecter**.

## <a name="check-status-of-the-feature"></a>Vérifiez l’état de la fonctionnalité

Assurez-vous que la fonctionnalité Authentification unique transparente est toujours **activée** sur votre client. Vous pouvez vérifier l’état en accédant au panneau **Azure AD Connect** sur le [centre d’administration Azure Active Directory](https://aad.portal.azure.com/).

![Centre d’administration Azure Active Directory - panneau Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Raisons des échecs de connexion dans le Centre d’administration Azure Active Directory (licence Premium requise)

Si votre locataire dispose d’une licence Azure AD Premium, vous pouvez également consulter le [rapport d’activité de connexion](../active-directory-reporting-activity-sign-ins.md) dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/).

![Centre d’administration Azure Active Directory - rapport de connexions](./media/active-directory-aadconnect-sso/sso9.png)

Accédez à **Azure Active Directory** -> **Connexions** dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/), puis cliquez sur une activité de connexion. Recherchez le champ **Code d’erreur de connexion**. Notez la valeur de ce champ et cherchez dans le tableau suivant la raison de l’échec et la solution à appliquer :

|Code d’erreur de connexion|Raison de l’échec de connexion|Résolution :
| --- | --- | ---
| 81001 | Le ticket Kerberos de l’utilisateur est trop volumineux. | Réduire les appartenances à des groupes de l’utilisateur, puis réessayez.
| 81002 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81003 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81004 | Échec de la tentative d’authentification Kerberos. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81008 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81009 | « Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81010 | L’authentification unique transparente a échoué, car le ticket Kerberos de l’utilisateur est arrivé à expiration ou n’est pas valide. | L’utilisateur doit se connecter à partir d’un appareil joint à un domaine au sein de votre réseau d’entreprise.
| 81011 | Impossible de trouver l’objet utilisateur à partir des informations contenues dans le ticket Kerberos de l’utilisateur. | Utilisez Azure AD Connect pour synchroniser les informations de l’utilisateur dans Azure AD.
| 81012 | L’utilisateur qui tente de se connecter à Azure AD est différent de l’utilisateur connecté à l’appareil. | Connectez-vous à partir d’un autre appareil.
| 81013 | Impossible de trouver l’objet utilisateur à partir des informations contenues dans le ticket Kerberos de l’utilisateur. |Utilisez Azure AD Connect pour synchroniser les informations de l’utilisateur dans Azure AD. 

## <a name="troubleshooting-checklist"></a>Liste de contrôle pour la résolution des problèmes

Utilisez la liste de contrôle suivante pour résoudre les problèmes d’authentification unique transparente :

- Vérifiez si la fonctionnalité d’authentification unique transparente est activée dans Azure AD Connect. Si vous ne pouvez pas activer la fonctionnalité (par exemple, en raison d’un port bloqué), vérifiez que toutes les [conditions préalables](active-directory-aadconnect-sso-quick-start.md#step-1-check-prerequisites) sont bien respectées.
- Vérifiez que ces deux URL Azure AD (https://autologon.microsoftazuread-sso.com et https://aadg.windows.net.nsatc.net) figurent bien dans les paramètres Zone intranet de l’utilisateur.
- Vérifiez que l’appareil d’entreprise est joint au domaine AD.
- Vérifiez que l’utilisateur est connecté à l’appareil à partir d’un compte de domaine AD.
- Vérifiez que le compte de l’utilisateur provient d’une forêt AD dans laquelle l’authentification unique (SSO) transparente a été configurée.
- Vérifiez que l’appareil est connecté au réseau d’entreprise.
- Vérifiez que l’heure de l’appareil est synchronisée avec celle d’Active Directory et du contrôleur de domaine : elle ne doit pas compter plus de cinq minutes d’écart.
- Affichez la liste des tickets Kerberos existants sur l’appareil à l’aide de la commande **klist** dans une invite de commandes. Vérifiez si les tickets émis pour le compte d’ordinateur `AZUREADSSOACCT` y figurent. En règle générale, la durée de validité des tickets Kerberos des utilisateurs est de 12 heures. Votre instance Active Directory est peut-être paramétrée différemment.
- Videz les tickets Kerberos existants de l’appareil à l’aide de la commande **klist purge**, puis réessayez.
- Pour déterminer si des problèmes liés à JavaScript existent, passez en revue les journaux de console du navigateur (sous Outils de développement).
- Examinez également les [journaux des contrôleurs de domaine](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Journaux des contrôleurs de domaine

Si l’audit des réussites est activé sur votre contrôleur de domaine, chaque fois qu’un utilisateur se connecte à l’aide de l’authentification unique transparente, une entrée de sécurité est enregistrée dans le journal des événements. Vous pouvez trouver ces événements de sécurité à l’aide de la requête suivante (recherchez l’événement **4769** associé au compte d’ordinateur **AzureADSSOAcc$**) :

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Réinitialisation manuelle de la fonctionnalité

Si vous n’avez pas réussi à résoudre le problème, vous pouvez réinitialiser manuellement la fonctionnalité pour votre locataire. Effectuez les étapes suivantes sur le serveur local où vous exécutez Azure AD Connect :

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Étape 1 : Importer le module PowerShell Authentification unique (SSO) transparente

1. Pour commencer, téléchargez et installez l’[Assistant de connexion Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Ensuite, téléchargez et installez le [Module Azure Active Directory 64 bits pour Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Accédez au dossier `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importez le module PowerShell Authentification unique (SSO) transparente à l’aide de la commande suivante : `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>Étape 2 : Obtenir la liste des forêts AD dans lesquelles l’authentification unique (SSO) transparente a été activée

1. Exécutez PowerShell en tant qu’administrateur. Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Lorsque vous y êtes invité, fournissez les informations d’identification de l’administrateur général de votre locataire.
2. Appelez `Get-AzureADSSOStatus`. Cette commande vous fournit la liste des forêts AD (examinez la liste « Domaines ») dans lesquelles cette fonctionnalité a été activée.

### <a name="step-3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>Étape 3 : Désactiver l’authentification unique (SSO) transparente pour chaque forêt AD dans laquelle elle a été configurée

1. Appelez `$creds = Get-Credential`. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de domaine pour la forêt AD souhaitée.
2. Appelez `Disable-AzureADSSOForest -OnPremCredentials $creds`. Cette commande supprime le compte d’ordinateur `AZUREADSSOACCT` du contrôleur de domaine local pour cette forêt AD spécifique.
3. Répétez les étapes précédentes pour chaque forêt AD dans laquelle vous avez configuré la fonctionnalité.

### <a name="step-4-enable-seamless-sso-for-each-ad-forest"></a>Étape 4 : Activer l’authentification unique (SSO) transparente pour chaque forêt AD

1. Appelez `Enable-AzureADSSOForest`. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de domaine pour la forêt AD souhaitée.
2. Répétez les étapes précédentes pour chaque forêt AD dans laquelle vous voulez configurer la fonctionnalité.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Étape 5. Activer la fonctionnalité pour votre locataire

Appelez `Enable-AzureADSSO`, puis tapez « true » à l’invite `Enable: ` pour activer la fonctionnalité dans votre locataire.
