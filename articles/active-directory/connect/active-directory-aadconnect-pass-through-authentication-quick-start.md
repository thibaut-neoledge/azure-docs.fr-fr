---
title: "Authentification directe Azure AD : Démarrage rapide | Microsoft Docs"
description: "Cet article explique comment commencer à utiliser l’authentification directe d’Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c1bc7cc5fe53d04019f68a520fb03c9187a6148b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Authentification directe Azure Active Directory : Démarrage rapide

L’authentification directe Azure Active Directory (Azure AD) permet à vos utilisateurs de se connecter aux applications locales et aux applications cloud à l’aide des mêmes mots de passe. Elle connecte les utilisateurs en validant leurs mots de passe directement par rapport à votre annuaire Active Directory local.

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Comment déployer l’authentification directe Azure AD ?

Pour déployer l’authentification directe, vous devez effectuer les étapes suivantes :
1. *Vérifier les prérequis* : configurez correctement votre environnement local et votre locataire avant d’activer la fonctionnalité.
2. *Activer la fonctionnalité* : activez l’authentification directe sur votre locataire et installez un agent local allégé pour gérer les demandes de validation de mot de passe.
3. *Tester la fonctionnalité* : testez la connexion des utilisateurs à l’aide de l’authentification directe.
4. *Garantir une haute disponibilité* : installez un deuxième agent autonome afin de fournir une haute disponibilité pour les demandes de connexion.

## <a name="step-1-check-prerequisites"></a>Étape 1 : Vérifier les prérequis

Vérifiez que les prérequis suivants sont remplis :

### <a name="on-the-azure-portal"></a>Dans le portail Azure

1. Créez un compte d’administrateur général « cloud uniquement » dans votre locataire Azure AD. De cette façon, vous pouvez gérer la configuration de votre locataire si vos services locaux venaient à échouer ou ne plus être disponibles. Découvrez comment [ajouter un compte d’administrateur général de type cloud uniquement](../active-directory-users-create-azure-portal.md). Cette étape est essentielle si vous voulez éviter de vous retrouver en dehors de votre locataire.
2. Ajoutez un ou plusieurs [noms de domaine personnalisés](../active-directory-add-domain.md) à votre locataire Azure AD. Vos utilisateurs se connectent à l’aide de l’un de ces noms de domaine.

### <a name="in-your-on-premises-environment"></a>Dans votre environnement local

1. Identifiez un serveur Windows Server 2012 R2 ou ultérieur sur lequel exécuter Azure AD Connect. Ajoutez ce serveur à la même forêt Active Directory que celle des utilisateurs dont les mots de passe doivent être validés.
2. Installez la [version la plus récente d’Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) sur le serveur identifié à l’étape 2. Si Azure AD Connect est déjà en cours d’exécution, vérifiez que la version est 1.1.486.0 ou ultérieure.
3. Identifiez un autre serveur Windows Server 2012 R2 ou ultérieur sur lequel exécuter l’agent d’authentification autonome. La version de l’agent d’authentification doit être 1.5.58.0 ou ultérieure. Ce serveur est nécessaire pour garantir une haute disponibilité des demandes de connexion. Ajoutez ce serveur à la même forêt Active Directory que celle des utilisateurs dont les mots de passe doivent être validés.
4. S’il existe un pare-feu entre vos serveurs et Azure AD, vous devez configurer les éléments suivants :
   - Ouvrez les ports : vérifiez que les agents d’authentification sur vos serveurs peuvent effectuer des demandes sortantes vers Azure AD par l’intermédiaire des ports 80 et 443. Si votre pare-feu applique les règles en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau.
   - Autorisez les points de terminaison Azure AD : si le filtrage d’URL est activé, vérifiez que les agents d’authentification peuvent communiquer avec **\*.msappproxy.net** et **\*.servicebus.windows.net**.
   - Vérifiez les connexions IP directes : vérifiez que les agents d’authentification sur vos serveurs peuvent établir une connexion IP directe aux [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

## <a name="step-2-enable-the-feature"></a>Étape 2 : Activer la fonctionnalité

Vous pouvez activer l’authentification directe par l’intermédiaire d’[Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Vous pouvez activer l’authentification directe sur le serveur principal ou sur un serveur intermédiaire Azure AD Connect. Nous vous recommandons vivement de l’activer à partir du serveur principal.

Si vous installez Azure AD Connect pour la première fois, choisissez le [chemin d’installation personnalisé](active-directory-aadconnect-get-started-custom.md). Dans la page **Connexion utilisateur**, choisissez **Authentification directe** comme méthode d’authentification. Si l’opération réussit, un agent d’authentification directe est installé sur le même serveur qu’Azure AD Connect. La fonctionnalité d’authentification directe est également activée sur votre locataire.

![Azure AD Connect - Connexion de l’utilisateur](./media/active-directory-aadconnect-sso/sso3.png)

Si vous avez déjà installé Azure AD Connect (à l’aide du chemin [d’installation rapide](active-directory-aadconnect-get-started-express.md) ou [d’installation personnalisée](active-directory-aadconnect-get-started-custom.md)), sélectionnez **Modifier la connexion utilisateur** sur Azure AD Connect, puis cliquez sur **Suivant**. Ensuite, sélectionnez **Authentification directe** comme mode d’authentification. Si l’opération réussit, un agent d’authentification directe est installé sur le même serveur qu’Azure AD Connect et la fonctionnalité est activée sur votre locataire.

![Azure AD Connect - Changer la connexion utilisateur](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>L’authentification directe est une fonctionnalité au niveau du locataire. Son activation a un impact sur la connexion des utilisateurs dans _tous_ les domaines gérés dans votre locataire.

## <a name="step-3-test-the-feature"></a>Étape 3 : Tester la fonctionnalité

Après l’étape 2, les utilisateurs de tous les domaines gérés dans votre locataire se connectent à l’aide de l’authentification directe. Toutefois, les utilisateurs des domaines fédérés continuent à se connecter à l’aide des services de fédération Active Directory (ADFS) ou d’un autre fournisseur de fédération précédemment configuré. Si vous convertissez un domaine fédéré en domaine géré, tous les utilisateurs de ce domaine se connectent alors automatiquement à l’aide de l’authentification directe. La fonctionnalité d’authentification directe n’a pas d’incidence sur les utilisateurs cloud uniquement.

## <a name="step-4-ensure-high-availability"></a>Étape 4 : Garantir une haute disponibilité

Si vous envisagez de déployer l’authentification directe dans un environnement de production, vous devez installer un agent d’authentification autonome. Installez ce deuxième agent d’authentification sur un serveur _autre_ qu’un serveur exécutant Azure AD Connect et le premier agent d’authentification. Cette installation procure une haute disponibilité des demandes de connexion. Pour déployer un agent d’authentification autonome, effectuez les étapes suivantes :

### <a name="download-and-install-the-authentication-agent-software-on-your-server"></a>Télécharger et installer le logiciel de l’agent d’authentification sur votre serveur

1.  [Téléchargez](https://go.microsoft.com/fwlink/?linkid=837580) la dernière version du logiciel de l’agent d’authentification. Vérifiez que la version est 1.5.58.0 ou ultérieure.
2.  Ouvrez une invite de commandes en tant qu’administrateur.
3.  Exécutez la commande suivante (l’option **/q** signifie une installation silencieuse ; l’installation ne vous demande pas d’accepter le Contrat de Licence Utilisateur Final) : `
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
`

>[!NOTE]
>Vous ne pouvez installer qu’un seul agent d’authentification par serveur.

### <a name="register-the-authentication-agent-with-azure-ad"></a>Inscrire l’agent d’authentification auprès d’Azure AD

1.  Ouvrez une fenêtre PowerShell en tant qu’administrateur.
2.  Accédez à **C:\Program Files\Microsoft AAD App Proxy Connector** et exécutez le script suivant : `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.  Lorsque vous y êtes invité, entrez les informations d’identification de votre compte d’administrateur général sur votre client Azure AD.

## <a name="next-steps"></a>Étapes suivantes
- [**Limitations actuelles**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) : cette fonctionnalité est actuellement en préversion. Découvrez les scénarios pris en charge et ceux qui ne le sont pas.
- [**Immersion technique**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) : découvrez comment fonctionne cette fonctionnalité.
- [**Forum aux questions**](active-directory-aadconnect-pass-through-authentication-faq.md) : réponses aux questions fréquentes.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants susceptibles de se produire avec cette fonctionnalité.
- [**Authentification unique transparente Azure AD**](active-directory-aadconnect-sso.md) : explorez en détail cette fonctionnalité complémentaire.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour le dépôt de nouvelles demandes de fonctionnalités.

