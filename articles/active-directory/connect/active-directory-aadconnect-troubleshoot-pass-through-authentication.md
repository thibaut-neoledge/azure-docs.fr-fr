---
title: "Azure AD Connect : résolution des problèmes d’authentification directe | Microsoft Docs"
description: "Cet article décrit la résolution des problèmes d’authentification directe d’Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Résolution des problèmes d’authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: aceba2cfeac03eae78e89ef46926b2a388663f09
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-pass-through-authentication"></a>Comment résoudre les problèmes d’authentification directe Azure Active Directory

Cet article fournit des informations permettant de résoudre des problèmes courants lors de l’installation, l’inscription ou la désinstallation des connecteurs d’authentification directe (via Azure AD Connect ou de manière autonome). Et pendant l’activation et l’utilisation de la fonctionnalité d’authentification directe Azure Active Directory (Azure AD) sur votre client.

## <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>Problèmes lors de l’installation des connecteurs (via Azure AD Connect ou de manière autonome)

### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Un connecteur de proxy d’application Azure AD existe déjà.

Un connecteur d’authentification directe ne peut pas être installé sur le même serveur qu’un connecteur de [proxy d’application Azure AD](../../active-directory/active-directory-application-proxy-get-started.md). Vous devez installer le connecteur d’authentification directe sur un serveur distinct.

### <a name="an-unexpected-error-occured"></a>Une erreur inattendue s’est produite

[Collectez les journaux du connecteur](#collecting-pass-through-authentication-connector-logs) à partir du serveur et contactez le Support Microsoft Support pour lui faire part de votre problème.

## <a name="issues-during-registration-of-connectors"></a>Problèmes lors de l’inscription des connecteurs

### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>L’inscription du connecteur a échoué en raison de ports bloqués

Assurez-vous que le serveur sur lequel le connecteur a été installé peut communiquer avec nos URL de service et les ports répertoriés [ici](active-directory-aadconnect-pass-through-authentication.md#prerequisites).

### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>Échec de l’inscription du connecteur en raison d’erreurs d’autorisation lié au jeton ou au compte

Veillez à utiliser un compte d’administrateur général uniquement dans le cloud pour toutes les opérations d’installation et d’inscription Azure AD Connect ou de connecteur autonome. Il existe un problème connu avec les comptes d’administrateur général compatibles MFA ; désactivez MFA temporairement (uniquement pour effectuer les opérations) comme solution de contournement.

### <a name="an-unexpected-error-occurred"></a>Une erreur inattendue s’est produite

[Collectez les journaux du connecteur](#collecting-pass-through-authentication-connector-logs) à partir du serveur et contactez le Support Microsoft pour lui faire part de votre problème.

## <a name="issues-during-uninstallation-of-connectors"></a>Problèmes lors de la désinstallation des connecteurs

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Message d’avertissement lors de la désinstallation d’Azure AD Connect

Si l’authentification directe est activée sur votre client et que vous tentez de désinstaller Azure AD Connect, le message d’avertissement suivant apparaît : « Users will not be able to sign-in to Azure AD unless you have other pass-through authentication agents installed on other servers » (Les utilisateurs ne seront pas en métrique de se connecter, sauf si d’autres agents d’authentification directe sont installés sur d’autres serveurs).

Vous devez avoir configuré la [haute disponibilité](active-directory-aadconnect-pass-through-authentication.md) avant de pouvoir désinstaller Azure AD Connect pour éviter d’interrompre la connexion utilisateur.

## <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>Problèmes liés à l’activation de la fonctionnalité d’authentification directe

### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>L’activation de la fonctionnalité a échoué, car il n’y a aucun connecteur disponible

Vous devez disposer d’au moins un connecteur actif pour activer l’authentification directe sur votre client. Vous pouvez installer un connecteur en installant Azure AD Connect ou un connecteur autonome.

### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>L’activation de la fonctionnalité a échoué en raison de ports bloqués

Assurez-vous que le serveur sur lequel Azure AD Connect est installé peut communiquer avec nos URL de service et les ports répertoriés [ici](active-directory-aadconnect-pass-through-authentication.md#prerequisites).

### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>L’activation de la fonctionnalité a échoué en raison d’erreurs d’autorisation lié au jeton ou au compte.

Veillez à utiliser un compte d’administrateur général pour le cloud uniquement lors de l’activation de la fonctionnalité. Il existe un problème connu avec les comptes d’administrateur général compatibles MFA (Multi-Factor Authentication) ; désactivez MFA temporairement (uniquement pour effectuer l’opération) comme solution de contournement.

## <a name="issues-while-operating-the-pass-through-authentication-feature"></a>Problèmes liés au fonctionnement de la fonctionnalité d’authentification directe

### <a name="user-facing-sign-in-errors"></a>Erreurs de connexion rencontrées par l’utilisateur

La fonctionnalité signale les erreurs suivantes rencontrées par les utilisateurs sur l’écran de connexion à Azure AD. Elles sont détaillées ci-dessous avec les étapes de résolution appropriées.

|Erreur|Description|Résolution :
| --- | --- | ---
|AADSTS80001|Impossible de se connecter à Active Directory|Assurez-vous que les serveurs de connecteurs sont membres de la même forêt Active Directory que les utilisateurs dont les mots de passe doivent être validés et qu’ils peuvent se connecter à Active Directory.  
|AADSTS8002|Délai d’attente dépassé lors de la connexion à Active Directory|Vérifiez qu’Active Directory est disponible et répond aux demandes des connecteurs.
|AADSTS80004|Le nom d’utilisateur envoyé au connecteur n’était pas valide|Vérifiez que l’utilisateur tente de se connecter avec le nom d’utilisateur correct.
|AADSTS80005|La validation a rencontré une WebException imprévisible|Il s’agit probablement d’une erreur temporaire. relancez la requête. Si l’erreur se reproduit, contactez le Support Microsoft.
|AADSTS80007|Une erreur s’est produite lors de la communication avec Active Directory|Consultez les journaux du connecteur pour plus d’informations et vérifiez qu’Active Directory fonctionne comme prévu.

## <a name="collecting-pass-through-authentication-connector-logs"></a>Collecte des journaux de connecteur d’authentification directe

Selon le type de problème rencontré, que vous trouverez les journaux des connecteurs d’authentification directe dans différents emplacements.

### <a name="connector-event-logs"></a>Journaux d’événements des connecteurs

Pour les erreurs relatives au connecteur, ouvrez l’application Observateur d’événements sur le serveur et cherchez dans **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**.

Pour obtenir des journaux détaillés d’analyse et de débogage, activez le journal « Session ». N’exécutez pas le connecteur lorsque ce journal est activé pendant le fonctionnement normal. Utilisez-le uniquement pour la résolution des problèmes. Notez que le contenu du journal est visible uniquement une fois le journal à nouveau désactivé.

### <a name="detailed-trace-logs"></a>Journaux de suivi détaillés

Pour résoudre les problèmes d’échec de connexion utilisateur, recherchez les journaux de suivi dans **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Ces journaux incluent les raisons de l’échec de connexion utilisateur à l’aide de la fonctionnalité d’authentification directe. Un exemple d’entrée de journal est illustré ci-dessous :

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Vous pouvez obtenir une description détaillée de l’erreur (« 1328 » dans l’exemple ci-dessus) en ouvrant l’invite de commandes et en exécutant la commande suivante. Remarque : vous devez remplacer « 1328 » par le numéro d’erreur réel apparaissant dans vos journaux.

`Net helpmsg 1328`

Le résultat doit être similaire à celui-ci :

![Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Journaux des contrôleurs de domaine

Si la journalisation d’audit est activée, des informations complémentaires se trouvent dans les journaux de sécurité des contrôleurs de domaine. Vous pouvez interroger les demandes de connexion envoyées par les connecteurs d’authentification directe en utilisant la méthode simple suivante :

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

