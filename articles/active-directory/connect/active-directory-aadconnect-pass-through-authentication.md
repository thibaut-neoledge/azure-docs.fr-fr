---
title: 'Azure AD Connect : authentification directe | Microsoft Docs'
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
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4fc3c822e657ce1a66a59e15c1a7d636a9f699e6
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017

---

# <a name="configure-user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Configurer la connexion de l’utilisateur avec l’authentification directe Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Qu’est-ce que l’authentification directe Azure Active Directory ?

Il est préférable pour les utilisateurs comme pour votre entreprise d’autoriser l’utilisation des mêmes informations d’identification (mots de passe) pour la connexion aux ressources locales et aux services cloud. Les utilisateurs ont un mot de passe en moins à mémoriser, ce qui leur offre une meilleure expérience utilisateur. Cela réduit également les risques d’oublis de méthode de connexion. Cela vous permet de réduire les coûts de support technique, car les problèmes de mot de passe consomment généralement la plus grande partie des ressources de support.

Beaucoup d’entreprises utilisent la [synchronisation de mot de passe Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), une fonctionnalité [d’Azure AD Connect](active-directory-aadconnect.md) qui synchronise les hachages de mot de passe des utilisateurs de l’annuaire Active Directory local avec Azure Active Directory (Azure AD) et permet ainsi aux utilisateurs de disposer des mêmes informations d’identification pour accéder aux ressources locales et aux services cloud. D’autres exigent que les mots de passe, même au format haché, ne sortent pas de l’enceinte de leur organisation interne.

La fonctionnalité d’authentification directe Azure AD constitue une solution simple pour ces entreprises. Lorsque les utilisateurs se connectent à Azure AD, cette fonctionnalité garantit que les mots de passe des utilisateurs sont directement validés via votre annuaire Active Directory local. Cette fonctionnalité permet également de bénéficier des avantages suivants :

- Facilité d’utilisation
  - La validation du mot de passe ne nécessite aucun déploiement local, ni configuration réseau complexe.
  - Le connecteur local léger écoute et répond aux demandes de validation de mot de passe.
  - Le connecteur local reçoit automatiquement des améliorations de fonctionnalités et correctifs de bogues.
  - Elle peut être configurée en même temps que [Azure AD Connect](active-directory-aadconnect.md). Le connecteur local léger est installé sur le même serveur qu’Azure AD Connect.
- Sécuriser
  - Les mots de passe locaux ne sont jamais stockés dans le cloud sous quelque forme que ce soit.
  - Le connecteur local léger établit uniquement les connexions sortantes depuis votre réseau interne. Il n’est donc pas nécessaire d’installer le connecteur dans un réseau de périmètre, ou DMZ.
  - L’authentification directe fonctionne de façon transparente avec Azure Multi-Factor Authentication.
- Fiabilité et évolutivité
  - Il est possible d’installer des connecteurs locaux légers supplémentaires sur plusieurs serveurs pour assurer la haute disponibilité des demandes de connexion.

![Authentification directe Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Vous pouvez combiner l’authentification directe avec la fonctionnalité [d’authentification unique transparente](active-directory-aadconnect-sso.md) (SSO). De cette façon, les utilisateurs n’ont pas besoin de saisir leur mot de passe pour se connecter à Azure AD s’ils utilisent un ordinateur professionnel au sein du réseau d’entreprise. L’expérience est totalement intégrée.

## <a name="whats-available-during-preview"></a>Quelles fonctions sont disponibles avec la version préliminaire ?

>[!NOTE]
>L’authentification directe Azure AD est actuellement en version préliminaire. Cette fonctionnalité est gratuite et il est inutile de disposer des éditions payantes d’Azure AD pour l’utiliser.

Les scénarios suivants sont entièrement pris en charge dans la version préliminaire :

- Toutes les applications basées sur un navigateur web
- Les applications clientes Office 365 prenant en charge [l’authentification moderne](https://aka.ms/modernauthga)

Les scénarios suivants ne sont _pas_ pris en charge dans la version préliminaire :

- Les applications clientes Office héritées et Exchange ActiveSync (c’est-à-dire les applications de messagerie natives sur les appareils mobiles). <br>Nous recommandons aux entreprises de basculer si possible vers l’authentification moderne. Cela permet non seulement de prendre en charge l’authentification directe, mais également de sécuriser les identités à l’aide des fonctionnalités [d’accès conditionnel](../active-directory-conditional-access.md), telles que l’authentification multifacteur.
- Azure AD Join pour les appareils Windows 10.

>[!IMPORTANT]
>Pour les scénarios dans lesquels la fonctionnalité d’authentification directe n’est pas prise en charge pour le moment (applications clientes Office héritées, Exchange ActiveSync et Azure AD Join pour les appareils Windows 10), il existe une solution de contournement : la synchronisation de mot de passe est également activée par défaut lorsque vous activez l’authentification directe. La synchronisation de mot de passe ne sert de solution de secours que pour ces scénarios spécifiques. Si vous n’en avez pas besoin, vous pouvez désactiver la synchronisation de mot de passe sur la page [Fonctionnalités facultatives](active-directory-aadconnect-get-started-custom.md#optional-features) dans l’Assistant Azure AD Connect.

### <a name="prerequisites"></a>Composants requis

Avant de pouvoir activer l’authentification directe Azure AD, les conditions préalables suivantes sont requises :

- Un client Azure AD dont vous êtes un administrateur global.

  >[!NOTE]
  >Il est vivement recommandé que le compte d’administrateur général soit un compte exclusivement dans le cloud. De cette façon, vous pouvez gérer la configuration de votre client si vos services locaux venaient à échouer ou ne plus être disponibles. Découvrez comment [ajouter un compte d’administrateur général de type cloud uniquement](../active-directory-users-create-azure-portal.md).

- Azure AD Connect 1.1.486.0 ou une version ultérieure. Nous vous recommandons d’utiliser la [version la plus récente d’Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
- Un serveur tournant sous Windows Server 2012 R2 ou version ultérieure sur lequel exécuter Azure AD Connect.
  - Ce serveur doit être un membre de la même forêt Active Directory que les utilisateurs dont les mots de passe doivent être validés.
  - Notez qu’un connecteur d’authentification directe est installé sur le même serveur qu’Azure AD Connect. Vérifiez que la version de connecteur est 1.5.58.0 ou une version ultérieure.

    >[!NOTE]
    >Les environnements à plusieurs forêts sont pris en charge s’il existe des approbations entre les forêts AD et que le routage du suffixe de leurs noms est configuré correctement.

- La haute disponibilité exige que vous disposiez de serveurs supplémentaires exécutant Windows Server 2012 R2 ou une version ultérieure pour installer des connecteurs autonomes. (La version du connecteur doit être 1.5.58.0 ou une version ultérieure.)
- S’il existe un pare-feu entre l’un des connecteurs et Azure AD :
    - Si le filtrage d’URL est activé, assurez-vous que le connecteur peut communiquer avec les URL suivantes :
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net
    - Vérifiez que les connecteurs établissent également des connexions IP directes vers les [plages IP de centre de données Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Assurez-vous que le pare-feu n’effectue pas d’inspection SSL, car les connecteurs utilisent les certificats clients pour communiquer avec Azure AD.
    - Vérifiez que les connecteurs peuvent envoyer des requêtes sortantes à Azure AD sur les ports 80 et 443.
      - Si votre pare-feu applique les règles en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau.
      - Les connecteurs effectuent des requêtes HTTP sur le port 80 pour télécharger des listes de révocation de certificats SSL. C’est également nécessaire pour que la fonctionnalité de mise à jour automatique fonctionne correctement.
      - Les connecteurs effectuent des requêtes HTTPS sur le port 443 pour toutes les autres opérations telles que l’activation et la désactivation de la fonctionnalité, l’inscription des connecteurs, le téléchargement des mises à jour du connecteur et la gestion des demandes de connexion de tous les utilisateurs.

     >[!NOTE]
     >Nous avons récemment apporté des améliorations afin de réduire le nombre de ports requis par les connecteurs pour communiquer avec notre service. Si vous exécutez des versions antérieures d’Azure AD Connect et/ou des connecteurs autonomes, vous devez laisser ces ports supplémentaires (5671, 8080, 9090, 9091, 9350, 9352, 10100-10120) ouverts.

### <a name="enable-azure-ad-pass-through-authentication"></a>Activer l’authentification directe Azure AD

L’authentification directe Azure AD peut être activée via Azure AD Connect.

Si vous procédez à une nouvelle installation d’Azure AD Connect, choisissez le [chemin d’installation personnalisé](active-directory-aadconnect-get-started-custom.md). Dans la page **Connexion de l’utilisateur**, sélectionnez **l’authentification directe**. En cas de réussite de l’opération, le connecteur d’authentification directe est installé sur le même serveur qu’Azure AD Connect. En outre, la fonctionnalité d’authentification directe est activée sur votre client.

![Azure AD Connect - Connexion de l’utilisateur](./media/active-directory-aadconnect-sso/sso3.png)

Si vous avez déjà installé Azure AD Connect configurez-le à l’aide du chemin [d’installation rapide](active-directory-aadconnect-get-started-express.md) ou [d’installation personnalisée](active-directory-aadconnect-get-started-custom.md), sélectionnez **Modifier la connexion utilisateur** sur Azure AD Connect et cliquez sur **Suivant**. Puis sélectionnez **Authentification directe** pour installer un connecteur d’authentification directe sur le même serveur qu’Azure AD Connect et activer la fonctionnalité sur votre client.

![Azure AD Connect - Modifier la connexion utilisateur](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>L’authentification directe Azure AD est une fonctionnalité au niveau du client. Elle affecte la connexion utilisateur à tous les domaines gérés sur votre client. Toutefois, les utilisateurs des domaines fédérés continueront à se connecter à l’aide d’Active Directory Federation Services (ADFS) ou de tout autre fournisseur de fédération précédemment configuré. Si vous convertissez un domaine fédéré en domaine géré, tous les utilisateurs de ce domaine se connectent alors automatiquement à l’aide de l’authentification directe. L’authentification directe n’a pas d’incidence sur les utilisateurs cloud uniquement.

Si vous envisagez d’utiliser l’authentification directe dans un déploiement de production, nous vous recommandons d’installer un second connecteur sur un serveur distinct (autre que celui exécutant Azure AD Connect et le premier connecteur). Cette opération garantit que vous disposez d’une haute disponibilité pour les demandes de connexion. Vous pouvez installer autant de connecteurs supplémentaires que nécessaire. Cela dépend des pics et des moyennes de demandes de connexion gérées par votre client.

Pour déployer un connecteur autonome, procédez comme suit.

#### <a name="step-1-download-and-install-the-connector"></a>Étape 1 : Téléchargement et installation du connecteur

Dans cette étape, vous téléchargez et installez le logiciel du connecteur sur votre serveur.

1.    [Téléchargez](https://go.microsoft.com/fwlink/?linkid=837580) le connecteur le plus récent. Vérifiez que la version de connecteur est 1.5.58.0 ou une version ultérieure.
2.    Ouvrez une invite de commandes en tant qu’administrateur.
3.    Exécutez la commande ci-dessous. L’option **/q** signifie une installation silencieuse ; l’installation ne vous demande pas d’accepter le Contrat de Licence Utilisateur Final : `
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
3. Le connecteur valide ensuite le nom d’utilisateur et le mot de passe via votre annuaire Active Directory à l’aide des API Windows standard (un mécanisme similaire à celui utilisé par AD FS). Notez que le nom d’utilisateur peut être soit le nom d’utilisateur local par défaut (généralement, `userPrincipalName`), soit un autre attribut (appelé `Alternate ID`) configuré dans Azure AD Connect.
4. Le contrôleur de domaine local évalue ensuite la requête et renvoie une réponse au connecteur (réussite ou échec).
5. Le connecteur renvoie à son tour cette réponse à Azure AD.
6. Azure AD évalue ensuite la réponse et répond à l’utilisateur comme il convient. Par exemple, il renvoie un jeton à l’application ou demande une authentification multifacteur.

Le schéma suivant illustre les diverses étapes. Toutes les demandes et réponses sont transmises via le canal HTTPS.

![Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

### <a name="password-writeback"></a>Écriture différée du mot de passe

Dans le cas où vous avez configuré [l’écriture différée de mot de passe](../active-directory-passwords-update-your-own-password.md) sur votre client et pour un utilisateur spécifique, si cet utilisateur se connecte à l’aide de l’authentification directe, il peut modifier ou réinitialiser son mot de passe comme auparavant. Les mots de passe seront réécrits dans l’annuaire Active Directory local comme prévu.

Toutefois, si l’écriture différée de mot de passe n’est pas configurée sur votre client ou aucune licence Azure AD n’est affectée à l’utilisateur, l’utilisateur ne peut pas mettre à jour son mot de passe dans le cloud. Cela est vrai même si leur mot de passe a expiré. À la place, l’utilisateur voit le message : « Votre organisation ne vous autorise pas à mettre à jour votre mot de passe sur ce site. Veuillez le mettre à jour en fonction de la méthode recommandée par votre organisation, ou contactez votre administrateur si vous avez besoin d’aide ».

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment activer la fonctionnalité [d’authentification unique transparente Azure AD](active-directory-aadconnect-sso.md) sur votre client.
- Lisez notre [guide de dépannage](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) pour découvrir comment résoudre les problèmes courants avec l’authentification directe Azure AD.

## <a name="feedback"></a>Commentaires
Vos commentaires sont très importants pour nous. Utilisez la section de commentaires si vous avez des questions. Utilisez notre [forum UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) pour les demandes de nouvelles fonctionnalités.


