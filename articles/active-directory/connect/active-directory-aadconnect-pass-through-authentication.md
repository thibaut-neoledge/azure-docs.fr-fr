---
title: "Azure AD Connect : authentification directe | Microsoft Docs"
description: "Cet article présente l’authentification directe Azure Active Directory (Azure AD) et explique comment l’utiliser pour autoriser les connexions à Azure AD en validant les mots de passe des utilisateurs via l’annuaire Active Directory local."
services: active-directory
keywords: "qu’est-ce que l’authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 0f54fb7d2d8cf010baf79409bc6a528d34982500
ms.lasthandoff: 04/22/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Configurer la connexion de l’utilisateur avec l’authentification directe Azure Active Directory

## <a name="what-is-azure-active-directory-azure-ad-pass-through-authentication"></a>Qu’est-ce que l’authentification directe Azure Active Directory (Azure AD) ?

Il est préférable pour les utilisateurs comme pour votre entreprise d’autoriser l’utilisation des mêmes informations d’identification (mots de passe) pour la connexion aux ressources locales et aux services cloud. Les utilisateurs ne doivent se souvenir que d’un seul mot de passe. Ainsi, ils vivent une meilleure expérience et limitent les risques d’oubli des informations de connexion. De votre côté, cela vous permet de réduire les coûts de support technique car les problèmes de mot de passe consomment généralement la plus grande partie des ressources de support.

Beaucoup d’entreprises utilisent la [synchronisation de mot de passe Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), une fonctionnalité [d’Azure AD Connect](active-directory-aadconnect.md) qui synchronise les mots de passe des utilisateurs de l’annuaire Active Directory local avec Azure AD et permet ainsi aux utilisateurs de disposer des mêmes informations d’identification pour accéder aux ressources locales et aux services cloud. D’autres exigent que les mots de passe, même au format haché, ne sortent pas de l’enceinte de leur organisation interne.

L’authentification directe Azure AD constitue une solution simple pour ces entreprises. Lorsque les utilisateurs se connectent à Azure AD, elle garantit que les mots de passe des utilisateurs sont directement validés via votre annuaire Active Directory local. Cette fonctionnalité permet également de bénéficier des avantages suivants :

- Facilité d’utilisation
  - La validation du mot de passe ne nécessite aucun déploiement local, ni configuration réseau complexe.
  - Elle repose sur un connecteur local léger qui écoute et répond aux demandes de validation de mot de passe.
  - Elle peut être configurée en même temps que [Azure AD Connect](active-directory-aadconnect.md). Le connecteur local léger est installé sur le même serveur qu’Azure AD Connect.
- Sécuriser
  - Les mots de passe locaux ne sont jamais stockés dans le cloud sous quelque forme que ce soit.
  - Le connecteur local léger établit uniquement les connexions sortantes depuis votre réseau interne. Il n’est donc pas nécessaire d’installer le connecteur dans un DMZ.
  - L’authentification directe fonctionne de façon transparente avec Azure Multi-Factor Authentication.
- Fiabilité et évolutivité
  - Il est possible d’installer des connecteurs locaux légers supplémentaires sur plusieurs serveurs pour assurer la haute disponibilité des demandes de connexion.

![Authentification directe Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Lorsque cette fonctionnalité est combinée avec [l’authentification unique transparente](active-directory-aadconnect-sso.md), les utilisateurs n’ont pas besoin de saisir leur mot de passe pour se connecter à Azure AD s’ils utilisent un ordinateur professionnel au sein du réseau d’entreprise. L’expérience est totalement intégrée.

## <a name="whats-available-during-preview"></a>Quelles fonctions sont disponibles avec la version préliminaire ?

>[!NOTE]
>L’authentification directe Azure AD est actuellement en version préliminaire. Cette fonctionnalité est gratuite et il est inutile de disposer des éditions payantes d’Azure AD pour l’utiliser. 

Les scénarios suivants sont entièrement pris en charge dans la version préliminaire :

- Toutes les applications basées sur un navigateur Web.
- Les applications clientes Office 365 prenant en charge [l’authentification moderne](https://aka.ms/modernauthga).

Les scénarios suivants ne sont PAS pris en charge dans la version préliminaire :

- Les applications clientes Office héritées et Exchange ActiveSync (par exemple, les applications de messagerie natives sur les appareils mobiles).
  - Nous recommandons aux entreprises de basculer si possible vers l’authentification moderne. Cela permet non seulement de prendre en charge l’authentification directe, mais également de sécuriser les identités à l’aide des fonctionnalités [d’accès conditionnel](../active-directory-conditional-access.md), telles que l’authentification multifacteur.
- Azure AD Join pour les appareils Windows 10.

>[!IMPORTANT]
>Pour les scénarios dans lesquels l’authentification directe n’est pas prise en charge pour le moment (applications clientes Office héritées, Exchange ActiveSync et Azure AD Join pour les appareils Windows 10), il existe une solution de contournement : la synchronisation de mot de passe est également activée par défaut lorsque vous activez l’authentification directe. La synchronisation de mot de passe ne sert de solution de secours que pour ces scénarios spécifiques. Si vous n’en avez pas besoin, vous pouvez désactiver la synchronisation de mot de passe sur la page [Fonctionnalités facultatives](active-directory-aadconnect-get-started-custom.md#optional-features) dans Azure AD Connect.

## <a name="how-to-enable-azure-ad-pass-through-authentication"></a>Comment activer l’authentification directe Azure AD ?

### <a name="pre-requisites"></a>Conditions préalables

Avant de pouvoir activer et utiliser l’authentification directe Azure AD, les conditions préalables suivantes sont requises :

- Un client Azure AD dont vous êtes un administrateur global.

>[!NOTE]
>Il est recommandé que le compte d’administrateur général soit un compte cloud uniquement, afin que vous puissiez gérer la configuration de votre client en cas de panne ou d’indisponibilité de vos services locaux. Vous pouvez ajouter un compte d’administrateur général de type cloud uniquement comme indiqué [ici](../active-directory-users-create-azure-portal.md).

- Azure AD Connect 1.1.484.0 ou une version ultérieure. Nous vous recommandons d’utiliser la [version la plus récente d’Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
- Un serveur Windows Server 2012 R2 ou ultérieur sur lequel exécuter Azure AD Connect.
  - Ce serveur doit être un membre de la même forêt Active Directory que les utilisateurs dont les mots de passe doivent être validés.
  - Notez qu’un connecteur est installé sur le même serveur qu’Azure AD Connect.

>[!NOTE]
>Les environnements à plusieurs forêts sont pris en charge s’il existe des approbations entre les forêts AD et que le routage du suffixe de leurs noms est configuré correctement.

- La haute disponibilité exige que vous disposiez de serveurs supplémentaires exécutant Windows Server 2012 R2 ou une version ultérieure pour installer des connecteurs autonomes.
- S’il existe un pare-feu entre l’un des connecteurs et Azure AD, vérifiez les points suivants :
    - Si le filtrage d’URL est activé, assurez-vous que le connecteur peut communiquer avec les URL suivantes :
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - Les connecteurs établissent également des connexions IP directes vers les [plages IP de centre de données Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Assurez-vous que le pare-feu n’effectue pas d’inspection SSL, car les connecteurs utilisent les certificats clients pour communiquer avec Azure AD.
    - Assurez-vous que le connecteur peut envoyer des requêtes HTTPS (TCP) à Azure AD sur les ports 80 et 443.
      - Si votre pare-feu applique les règles en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau.

>[!NOTE]
>Nous avons récemment apporté des améliorations afin de réduire le nombre de ports requis par les connecteurs pour communiquer avec notre service. Si vous exécutez des versions antérieures d’Azure AD Connect et/ou des connecteurs autonomes, vous devez laisser ces ports supplémentaires (5671, 8080, 9090, 9091, 9350, 9352, 10100-10120) ouverts.

### <a name="enabling-azure-ad-pass-through-authentication"></a>Activation de l’authentification directe Azure AD

L’authentification directe Azure AD peut être activée via Azure AD Connect.

Si vous procédez à une nouvelle installation d’Azure AD Connect, choisissez le [chemin d’installation personnalisé](active-directory-aadconnect-get-started-custom.md). Dans la page de connexion de l’utilisateur, sélectionnez l’authentification directe. En cas de réussite de l’opération, le connecteur d’authentification directe est installé sur le même serveur qu’Azure AD Connect. En outre, la fonctionnalité d’authentification directe est activée sur votre client.

![Azure AD Connect - Connexion de l’utilisateur](./media/active-directory-aadconnect-sso/sso3.png)

Si Azure AD Connect n’est pas encore installé, effectuez l’installation à l’aide du chemin [d’installation rapide](active-directory-aadconnect-get-started-express.md) ou [d’installation personnalisée](active-directory-aadconnect-get-started-custom.md), sélectionnez « Modifier la connexion utilisateur » sur Azure AD Connect et cliquez sur « Suivant ». Puis sélectionnez « Authentification directe » pour installer un connecteur d’authentification directe sur le même serveur qu’Azure AD Connect et activer la fonctionnalité sur votre client.

![Azure AD Connect - Modifier la connexion utilisateur](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>L’authentification directe Azure AD est une fonctionnalité au niveau du client. Elle affecte la connexion utilisateur à tous les domaines gérés sur votre client. Toutefois, les utilisateurs des domaines fédérés continueront à se connecter à l’aide d’Active Directory Federation Services (ADFS) ou de tout autre fournisseur de fédération précédemment configuré. Si vous convertissez un domaine fédéré en domaine géré, tous les utilisateurs de ce domaine se connectent alors automatiquement à l’aide de l’authentification directe. L’authentification directe n’a pas d’incidence sur les utilisateurs cloud uniquement.

### <a name="ensuring-high-availability"></a>Haute disponibilité garantie

Si vous envisagez d’utiliser l’authentification directe dans un déploiement de production, nous vous recommandons d’installer un second connecteur sur un serveur distinct (autre que celui exécutant Azure AD Connect et le premier connecteur) pour assurer la haute disponibilité des demandes de connexion. Vous pouvez installer autant de connecteurs supplémentaires que nécessaire. Cela dépend des pics et des moyennes de demandes de connexion gérées par votre client.

Pour déployer un connecteur autonome, procédez comme suit :

#### <a name="step-1-download-and-install-the-connector"></a>Étape 1 : Téléchargement et installation du connecteur

Dans cette étape, vous téléchargez et installez le logiciel du connecteur sur votre serveur.

1.    [Téléchargez](https://go.microsoft.com/fwlink/?linkid=837580) le connecteur le plus récent.
2.    Ouvrez une invite de commandes en tant qu’administrateur.
3.    Exécutez la commande suivante (/q signifie une installation silencieuse ; l’installation ne vous demande pas d’accepter le Contrat de Licence Utilisateur Final) :

`
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`
>[!NOTE]
>Vous ne pouvez installer qu’un seul connecteur par serveur.

#### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Étape 2 : Inscription du connecteur avec Azure AD pour l’authentification directe

Dans cette étape, vous inscrivez le connecteur installé sur votre serveur avec notre service et vous le rendez disponible pour recevoir les demandes de connexion.

1.    Ouvrez une fenêtre PowerShell en tant qu’administrateur.
2.    Accédez à **C:\Program Files\Microsoft AAD App Proxy Connector** et exécutez le script suivant : `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Lorsque vous y êtes invité, entrez les informations d’identification de votre compte d’administrateur général sur votre client Azure AD.

## <a name="how-does-azure-ad-pass-through-authentication-work"></a>Comment fonctionne l’authentification directe Azure AD ?

Lorsqu’un utilisateur tente de se connecter à Azure AD (et si l’authentification directe est activée sur le client), voici ce qui se produit :

1. L’utilisateur entre son nom d’utilisateur et sont mot de passe dans la page de connexion à Azure AD. Notre service place le nom d’utilisateur et le mot de passe (chiffré à l’aide d’une clé publique) dans une file d’attente pour la validation.
2. L’un des connecteurs locaux disponibles effectue un appel sortant vers la file d’attente et récupère le nom d’utilisateur et le mot de passe.
3. Le connecteur valide ensuite le nom d’utilisateur et le mot de passe via votre annuaire Active Directory à l’aide des API Windows standard (un mécanisme similaire à celui utilisé par ADFS). Notez que le nom d’utilisateur peut être soit le nom d’utilisateur local par défaut (généralement, « userPrincipalName »), soit un autre attribut (appelé « Autre ID ») configuré dans Azure AD Connect.
4. Le contrôleur de domaine local évalue ensuite la requête et renvoie une réponse au connecteur (réussite ou échec).
5. Le connecteur renvoie à son tour cette réponse à Azure AD.
6. Azure AD évalue ensuite la réponse et répond à l’utilisateur comme il convient. Par exemple, il renvoie un jeton à l’application ou demande une authentification multifacteur.

Le diagramme ci-dessous illustre les différentes étapes. Notez que toutes les demandes et réponses sont transmises via le canal HTTPS.

![Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="note-about-password-writeback"></a>Remarques relatives à l’écriture différée du mot de passe

Dans le cas où vous avez configuré [l’écriture différée de mot de passe](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) sur votre client et pour un utilisateur spécifique, si cet utilisateur se connecte à l’aide de l’authentification directe, il peut modifier ou réinitialiser son mot de passe comme auparavant. Les mots de passe seront réécrits dans votre annuaire Active Directory local comme prévu.

Toutefois, si une de ces conditions n’est pas vraie (l’écriture différée de mot de passe n’est pas configurée sur votre client ou aucune licence Azure AD n’est affectée à l’utilisateur), l’utilisateur ne peut pas mettre à jour son mot de passe dans le cloud, notamment si le mot de passe a expiré. Dans ce cas , l’utilisateur voit le message suivant : « Votre organisation ne vous autorise pas à mettre à jour votre mot de passe sur ce site. Veuillez le mettre à jour en fonction de la méthode recommandée par votre organisation, ou contactez votre administrateur si vous avez besoin d’aide ».

## <a name="troubleshooting-pass-through-authentication"></a>Dépannage de l’authentification directe

Cette section vous fournit des informations permettant de résoudre certains problèmes courants lors de l’installation, l’inscription ou la désinstallation des connecteurs d’authentification directe (via Azure AD Connect ou de manière autonome), ainsi que pendant l’activation et l’utilisation de la fonctionnalité sur votre client.

### <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>Problèmes lors de l’installation des connecteurs (via Azure AD Connect ou de manière autonome)

#### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Un connecteur de proxy d’application Azure AD existe déjà.

Un connecteur d’authentification directe ne peut pas être installé sur le même serveur qu’un connecteur de [proxy d’application Azure AD](../../active-directory/active-directory-application-proxy-get-started.md). Vous devez installer le connecteur d’authentification directe sur un serveur distinct.

#### <a name="an-unexpected-error-occured"></a>Une erreur inattendue s’est produite

[Collectez les journaux du connecteur](#how-to-collect-pass-through-authentication-connector-logs?) à partir du serveur et contactez le Support Microsoft Support pour lui faire part de votre problème.

### <a name="issues-during-registration-of-connectors"></a>Problèmes lors de l’inscription des connecteurs

#### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>L’inscription du connecteur a échoué en raison de ports bloqués

Assurez-vous que le serveur sur lequel le connecteur a été installé peut communiquer avec nos URL de service et les ports répertoriés [ici](#pre-requisites).

#### <a name="an-unexpected-error-occurred"></a>Une erreur inattendue s’est produite

[Collectez les journaux du connecteur](#how-to-collect-pass-through-authentication-connector-logs?) à partir du serveur et contactez le Support Microsoft pour lui faire part de votre problème.

### <a name="issues-during-un-installation-of-connectors"></a>Problèmes lors de la désinstallation des connecteurs

#### <a name="warning-message-when-un-installing-azure-ad-connect"></a>Message d’avertissement lors de la désinstallation d’Azure AD Connect

Si l’authentification directe est activée sur votre client et que vous tentez de désinstaller Azure AD Connect, le message d’avertissement suivant apparaît : « Users will not be able to sign-in to Azure AD unless you have other pass-through authentication agents installed on other servers » (Les utilisateurs ne seront pas en mesure de se connecter, sauf si d’autres agents d’authentification directe sont installés sur d’autres serveurs).

Vous devez avoir configuré la [haute disponibilité](#ensuring-high-availability) avant de pouvoir désinstaller Azure AD Connect pour éviter d’interrompre la connexion utilisateur.

### <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>Problèmes liés à l’activation de la fonctionnalité d’authentification directe

#### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>L’activation de la fonctionnalité a échoué, car il n’y a aucun connecteur disponible

Vous devez disposer d’au moins un serveur de connecteur actif pour activer l’authentification directe sur votre client. Vous pouvez installer un connecteur en installant Azure AD Connect ou en installant un connecteur autonome.

#### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>L’activation de la fonctionnalité a échoué en raison de ports bloqués

Assurez-vous que le serveur sur lequel Azure AD Connect est installé peut communiquer avec nos URL de service et les ports répertoriés [ici](#pre-requisites).

### <a name="issues-while-operating-the-pass-through-authentication-feature"></a>Problèmes liés au fonctionnement de la fonctionnalité d’authentification directe

#### <a name="user-facing-sign-in-errors"></a>Erreurs de connexion rencontrées par l’utilisateur

La fonctionnalité signale les erreurs suivantes rencontrées par les utilisateurs sur l’écran de connexion à Azure AD. Elles sont détaillées ci-dessous avec les étapes de résolution appropriées.

|Erreur|Description|Résolution :
| --- | --- | ---
|AADSTS80001|Impossible de se connecter à Active Directory|Assurez-vous que les serveurs de connecteurs sont membres de la même forêt Active Directory que les utilisateurs dont les mots de passe doivent être validés et qu’ils peuvent se connecter à Active Directory.  
|AADSTS8002|Délai d’attente dépassé lors de la connexion à Active Directory|Vérifiez qu’Active Directory est disponible et répond aux demandes des connecteurs.
|AADSTS80004|Le nom d’utilisateur envoyé au connecteur n’était pas valide|Vérifiez que l’utilisateur tente de se connecter avec le nom d’utilisateur correct.
|AADSTS80005|La validation a rencontré une WebException imprévisible|Il s’agit probablement d’une erreur temporaire. relancez la requête. Si l’erreur se reproduit, contactez le Support Microsoft.
|AADSTS80007|Une erreur s’est produite lors de la communication avec Active Directory|Consultez les journaux du connecteur pour plus d’informations et vérifiez qu’Active Directory fonctionne comme prévu.

### <a name="how-to-collect-pass-through-authentication-connector-logs"></a>Comment collecter les journaux d’authentification directe ?

Selon le type de problème rencontré, que vous trouverez les journaux des connecteurs d’authentification directe dans différents emplacements.

#### <a name="connector-event-logs"></a>Journaux d’événements des connecteurs

Pour les erreurs relatives au connecteur, ouvrez l’application Observateur d’événements sur le serveur et cherchez dans **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**.

Pour obtenir des journaux détaillés d’analyse et de débogage, activez le journal « Session ». N’exécutez pas le connecteur lorsque ce journal est activé pendant le fonctionnement normal. Utilisez-le uniquement pour la résolution des problèmes. Notez que le contenu du journal est visible uniquement une fois le journal à nouveau désactivé.

#### <a name="detailed-trace-logs"></a>Journaux de suivi détaillés

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

#### <a name="domain-controller-logs"></a>Journaux des contrôleurs de domaine

Si la journalisation d’audit est activée, des informations complémentaires se trouvent dans les journaux de sécurité des contrôleurs de domaine. Vous pouvez interroger les demandes de connexion envoyées par les connecteurs d’authentification directe en utilisant la méthode simple suivante :

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="feedback"></a>Commentaires

Vos commentaires sont très importants pour nous. Vous pouvez nous envoyer un e-mail à l’adresse [aadopauthfeedback@microsoft.com](mailto:aadopauthfeedback@microsoft.com). Pour toute demande concernant les nouvelles fonctionnalités, utilisez le [forum UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Nous sommes à votre écoute.

