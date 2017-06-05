---
title: "Azure AD Connect : authentification unique transparente | Microsoft Docs"
description: "Cette rubrique décrit l’authentification unique transparente Azure Active Directory (Azure AD) et explique comment cette fonction vous permet de fournir une véritable authentification unique aux utilisateurs du réseau d’entreprise."
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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2601850f99188445cf63a6a4f185bdc4ebb92c29
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Authentification unique transparente Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Qu’est-ce que l’authentification unique transparente Azure Active Directory ?

L’authentification unique transparente Azure Active Directory fournit une véritable authentification unique aux utilisateurs qui se connectent à leurs postes de travail d’entreprise, eux-mêmes connectés au réseau d’entreprise. Lorsque cette fonctionnalité est activée, les utilisateurs n’ont plus besoin de taper leur mot de passe pour se connecter à Azure AD ni même, dans la plupart des cas, leur nom d’utilisateur. Cette fonctionnalité offre à vos utilisateurs un accès facilité à vos services cloud sans nécessiter de composants locaux supplémentaires.

L’authentification unique transparente peut être activée par le biais d’Azure AD Connect et être combinée avec la [synchronisation de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) ou l’[authentification directe](active-directory-aadconnect-pass-through-authentication.md).

>[!NOTE]
>Cette fonctionnalité N’EST PAS applicable à Active Directory Federation Services (ADFS), qui fournit déjà cette possibilité.

Pour un utilisateur spécifique, cette fonctionnalité doit satisfaire les conditions suivantes :

- Votre utilisateur se connecte à un bureau d’entreprise.
- Le bureau a déjà été joint à votre domaine Active Directory (AD).
- Le bureau dispose d’une connexion directe à votre contrôleur de domaine, soit sur le réseau câblé ou sans fil de l’entreprise, soit par le biais d’une connexion d’accès à distance, comme une connexion VPN.
- Nos points de terminaison de service ont été inclus dans la zone Intranet du navigateur.

Si l’une des conditions ci-dessus n’est pas remplie, l’utilisateur sera invité à entrer son nom d’utilisateur et son mot de passe, comme il le faisait auparavant.

![Authentification unique transparente](./media/active-directory-aadconnect-sso/sso1.png)

Les autres fonctionnalités de l’authentification unique transparente sont les suivantes :

- Si une demande de connexion Azure AD inclut le paramètre `domain_hint` ou `login_hint` (initié par une application de votre locataire), l’authentification unique transparente en profite et l’utilisateur n’a pas à entrer son nom d’utilisateur et son mot de passe.
- L’authentification unique transparente prend en charge le nom d’utilisateur, qui peut être le nom d’utilisateur local par défaut (généralement, "userPrincipalName") ou un autre attribut (appelé "Autre ID") configuré dans Azure AD Connect.
- L’authentification unique transparente est une fonctionnalité opportuniste, ce qui signifie que si elle échoue pour une raison quelconque, l’expérience de connexion utilisateur retrouve son comportement normal (l’utilisateur doit alors entrer son mot de passe dans la page de connexion).

## <a name="whats-available-during-preview"></a>Quelles fonctions sont disponibles avec la version préliminaire ?

>[!NOTE]
>L’authentification transparente Azure AD est actuellement dans la version préliminaire. Cette fonctionnalité est gratuite et il est inutile de disposer des éditions payantes d’Azure AD pour l’utiliser.

L’authentification unique transparente est prise en charge par les clients basés sur un navigateur web et les clients Office qui prennent en charge l’[authentification moderne](https://aka.ms/modernauthga) sur les postes de travail compatibles avec l’authentification Kerberos, comme les bureaux Windows. Le tableau ci-dessous fournit des détails sur les clients basés sur le navigateur sur différents systèmes d’exploitation.

| Système d’exploitation\Navigateur |Internet Explorer|Google Chrome|Mozilla Firefox|Edge
| --- | --- |--- | --- | --- |
|Windows 10|Oui|Oui|Oui\*|Non
|Windows 8.1|Oui|Oui|Oui\*|N/A
|Windows 8|Oui|Oui|Oui\*|N/A 
|Windows 7|Oui|Oui|Oui\*|N/A
|Mac OS X|N/A|Oui\*|Oui\*|N/A 

\*Requiert une configuration supplémentaire.

>[!NOTE]
>Concernant Windows 10, il est recommandé d’utiliser [Azure AD Join](../active-directory-azureadjoin-overview.md) pour une expérience optimale avec Azure AD.

## <a name="how-does-azure-ad-seamless-sso-work"></a>Comme l’authentification unique transparente Azure AD fonctionne-t-elle ?

Vous pouvez activer l’authentification unique transparente dans Azure AD Connect comme indiqué [ci-dessous](#how-to-enable-azure-ad-seamless-sso?). Une fois l’authentification activée, un compte d’ordinateur nommé AZUREADSSOACCT est créé dans votre annuaire Active Directory (AD) local et sa clé de déchiffrement Kerberos est partagée en toute sécurité avec Azure AD. En outre, deux noms de principal du service (SPN) Kerberos sont créés pour représenter deux URL de service qui sont utilisées lors de la connexion à Azure AD.

>[!NOTE]
> Le compte d’ordinateur et les SPN Kerberos doivent être créés dans chaque forêt AD que vous synchronisez avec Azure AD (via Azure AD Connect) et pour les utilisateurs pour lesquels vous voulez activer l’authentification unique transparente. Si votre forêt AD contient des unités d’organisation pour les comptes d’ordinateurs, une fois que vous avez activé la fonctionnalité d’authentification unique transparente, déplacez le compte d’ordinateur AZUREADSSOACCT dans une unité d’organisation pour éviter qu’il soit supprimé et pour faire en sorte qu’il soit géré de la même façon que les autres comptes d’ordinateurs.

Une fois la configuration terminée, la connexion Azure AD fonctionne exactement comme n’importe quelle autre connexion utilisant l’authentification Windows intégrée. Le processus d’authentification unique transparente fonctionne comme suit :

Supposons que votre utilisateur tente d’accéder à une ressource basée sur le cloud et sécurisée par Azure AD, comme SharePoint Online. SharePoint Online redirige le navigateur de l’utilisateur vers Azure AD pour la connexion.

Si la demande de connexion à Azure AD inclut un paramètre `domain_hint` (identifie votre locataire Azure AD, par exemple contoso.onmicrosoft.com) ou un paramètre `login_hint` (identifie le nom d’utilisateur, par exemple user@contoso.onmicrosoft.com ou user@contoso.com), les étapes 1 à 5 sont effectuées.

Si l’un de ces deux paramètres n’est pas inclus dans la demande, l’utilisateur est invité à fournir son nom d’utilisateur dans la page de connexion Azure AD. Les étapes 1 à 5 ne sont effectuées qu’à partir du moment où l’utilisateur quitte le champ de nom d’utilisateur ou qu’il clique sur le bouton Continuer.

1. Azure AD demande au client, via une réponse 401 Non autorisé, de fournir un ticket Kerberos.
2. Le client demande un ticket à Active Directory pour Azure AD (représenté par le compte d’ordinateur qui a été configuré précédemment).
3. Active Directory localise le compte d’ordinateur et retourne un ticket Kerberos au client, chiffré avec le secret du compte d’ordinateur. Le ticket inclut l’identité de l’utilisateur actuellement connecté à l’ordinateur.
4. Le client envoie le ticket Kerberos reçu de la part d’Active Directory à Azure AD.
5. Azure AD déchiffre le ticket Kerberos à l’aide de la clé partagée précédemment. S’il y parvient, Azure AD renvoie un jeton à l’utilisateur ou invite l’utilisateur à fournir des preuves supplémentaires (telles que l’authentification multifacteur), si la ressource l’exige.

L’authentification unique transparente est une fonctionnalité opportuniste, ce qui signifie que si elle échoue pour une raison quelconque, l’expérience de connexion utilisateur retrouve son comportement normal (l’utilisateur doit alors entrer son mot de passe dans la page de connexion).

Le processus est aussi illustré dans le schéma ci-dessous :

![Authentification unique transparente](./media/active-directory-aadconnect-sso/sso2.png)

## <a name="how-to-enable-azure-ad-seamless-sso"></a>Comment activer l’authentification unique transparente Azure AD ?

### <a name="pre-requisites"></a>Conditions préalables

Si vous activez l’authentification unique transparente avec authentification directe, aucun composant supplémentaire n’est requis au-delà de ce qui est nécessaire pour l’authentification directe.

Si vous activez l’authentification unique transparente avec synchronisation du mot de passe et s’il existe un pare-feu entre Azure AD Connect et Azure AD, vérifiez les points suivants :

- Le serveur Azure AD Connect peut communiquer avec les URL `*.msappproxy.net`.
- Azure AD Connect (version 1.1.484.0 ou versions supérieures) peut envoyer des requêtes HTTPS à Azure AD sur le port 443. Cette procédure sert uniquement à activer la fonctionnalité, et non les connexions d’utilisateur.
- Azure AD Connect peut aussi établir des connexions IP directes avec les [plages d’adresses IP de centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653). Là encore, cette procédure sert uniquement à activer la fonctionnalité.

>[!NOTE]
> Les versions précédentes d’Azure AD Connect (versions antérieures à la version 1.1.484.0) doivent pouvoir communiquer avec Azure AD par le port 9090.

### <a name="enabling-the-azure-ad-seamless-sso-feature"></a>Activation de la fonctionnalité d’authentification unique transparente Azure AD

L’authentification unique transparente Azure AD peut être activée par le biais d’Azure AD Connect.

Si vous procédez à une nouvelle installation d’Azure AD Connect, choisissez le [chemin d’installation personnalisé](active-directory-aadconnect-get-started-custom.md). Sur la page Connexion utilisateur, cochez la case Activer l’authentification unique.

![Azure AD Connect - Connexion utilisateur](./media/active-directory-aadconnect-sso/sso8.png)

Si vous disposez déjà d’une installation d’Azure AD Connect, effectuez l’installation à l’aide du chemin [d’installation rapide](active-directory-aadconnect-get-started-express.md) ou [d’installation personnalisée](active-directory-aadconnect-get-started-custom.md), sélectionnez Modifier la connexion utilisateur sur Azure AD Connect et cliquez sur Suivant. Cochez ensuite la case Activer l’authentification unique.

![Azure AD Connect - Modifier la connexion utilisateur](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Suivez les instructions de l’Assistant jusqu’à ce que vous accédiez à la page Activer l’authentification unique. Vous devrez fournir les informations d’identification d’administrateur de domaine pour chaque forêt AD que vous synchronisez avec Azure AD (par le biais d’Azure AD Connect) et pour les utilisateurs pour lesquels vous souhaitez activer l’authentification unique transparente. Notez que les informations d’identification d’administrateur de domaine ne sont stockées ni dans Azure AD Connect ni dans Azure AD. Elles ne servent qu’à créer le compte d’ordinateur et à configurer les SPN Kerberos comme décrit précédemment.

À la fin de l’Assistant, l’authentification unique transparente est activée sur votre client. Vous devez toujours effectuer les étapes décrites dans la section suivante pour que les utilisateurs puissent bénéficier de cette fonctionnalité.

## <a name="rolling-the-feature-out-to-your-users"></a>Déploiement de la fonctionnalité pour vos utilisateurs

Pour déployer la fonctionnalité d’authentification unique transparente pour les besoins de vos utilisateurs, vous devez ajouter deux URL AD Azure (https://autologon.microsoftazuread-sso.com et https://aadg.windows.net.nsatc.net) aux paramètres de zone Intranet des utilisateurs via la stratégie de groupe dans Active Directory. Cette procédure ne vaut que pour les navigateurs Internet Explorer et Google Chrome (si ce dernier partage le même ensemble d’URL de site de confiance qu’Internet Explorer). Vous devez procéder à une configuration distincte pour Mozilla Firefox.

### <a name="why-do-you-need-this"></a>Pourquoi est-ce nécessaire ?

Par défaut, les navigateurs n’envoient pas de ticket Kerberos à un point de terminaison cloud sauf si son URL est définie comme faisant partie de la zone du navigateur Intranet. Le navigateur calcule automatiquement la zone appropriée (Internet ou Intranet) à partir de l’URL. Par exemple, http://contoso/ sera être mappée à la zone Intranet, tandis que http://intranet.contoso.com/ sera mappée à la zone Internet (car l’URL contient un point).

Comme les URL Azure AD utilisées pour l’authentification unique transparente contiennent un point, elles doivent être ajoutées explicitement aux paramètres de zone Intranet du navigateur. Ainsi, le navigateur envoie automatiquement les tickets Kerberos de l’utilisateur actuellement connecté à Azure AD. Même si vous pouvez effectuer cette opération manuellement sur chaque poste de travail, le moyen le plus simple d’ajouter les URL nécessaires à la zone Intranet pour tous les utilisateurs est de créer une stratégie de groupe dans Active Directory.

### <a name="detailed-steps"></a>Procédure détaillée

1. Ouvrez l’outil de gestion de stratégie de groupe.
2. Modifiez la stratégie de groupe appliquée à tous les utilisateurs, par exemple la **stratégie de domaine par défaut**.
3. Accédez à **Configuration utilisateur\Modèles d’administration\Composants Windows\Internet Explorer\Panneau de configuration Internet\Page de sécurité** et sélectionnez **Liste des attributions de sites aux zones**.
![Authentification unique](./media/active-directory-aadconnect-sso/sso6.png)  
4. Activez la stratégie, puis entrez les valeurs/données suivantes dans la boîte de dialogue. Ce sont les URL AD Azure vers lesquelles les tickets Kerberos sont envoyés.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
5. Cliquez sur **OK**, puis de nouveau sur **OK**.

Il doit se présenter comme suit :

![Authentification unique](./media/active-directory-aadconnect-sso/sso7.png)

>[!NOTE]
>Par défaut, Chrome utilise le même ensemble d’URL de site de confiance qu’Internet Explorer. Si vous avez configuré des paramètres différents pour Chrome, vous devez mettre à jour ces paramètres séparément.

## <a name="disabling-azure-ad-seamless-sso"></a>Désactivation de l’authentification unique transparente Azure AD

L’authentification unique transparente Azure AD peut être désactivée à l’aide d’Azure AD Connect.

Exécutez Azure AD Connect, cliquez sur "Modifier la connexion utilisateur" puis sur "Suivant". Ensuite, désactivez l’option "Activer l’authentification unique". Suivez les instructions de l’Assistant. À la fin de l’Assistant, l’authentification unique transparente est désactivée sur votre client. Toutefois, le message suivant s’affiche :

"L’authentification unique est désormais désactivée, mais des étapes manuelles supplémentaires restent à effectuer pour terminer le nettoyage. En savoir plus."

Voici les étapes manuelles que vous devez effectuer :

- Obtenir la liste des forêts AD dans lesquelles l’authentification unique transparente a été activée
  - Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. La fenêtre contextuelle qui s’affiche vous permet d’entrer vos informations d’identification d’administrateur de locataire Azure AD.
  - Appelez `Get-AzureADSSOStatus`. La liste des forêts AD (dans la liste "Domaines") sur lesquelles cette fonctionnalité a été activée s’affiche.
- Supprimez manuellement le compte d’ordinateur AZUREADSSOACCT de chaque forêt AD répertoriée ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

- Consultez notre [guide de dépannage](active-directory-aadconnect-troubleshoot-sso.md) pour savoir comment résoudre les problèmes courants avec l’authentification unique transparente dans Azure AD.

## <a name="feedback"></a>Commentaires

Vos commentaires sont très importants pour nous. Utilisez la section de commentaires si vous avez des questions. Utilisez notre [forum UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) pour les demandes de nouvelles fonctionnalités.

