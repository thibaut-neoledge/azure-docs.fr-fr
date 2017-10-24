---
title: "Immersion dans la sécurité de l’authentification directe Azure Active Directory | Microsoft Docs"
description: "Cet article décrit comment l’authentification directe Azure Active Directory (Azure AD) protège vos comptes locaux."
services: active-directory
keywords: "Authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 7a886cdb0c36008bdb66592a8d3428889739627e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Immersion dans la sécurité de l’authentification directe Azure Active Directory

Cet article décrit plus en détail le fonctionnement de l’authentification directe. Il se concentre plus précisément sur les aspects de la fonctionnalité relevant de la sécurité. Cet article est destiné aux administrateurs de la sécurité et informatiques, aux personnes en charge de la conformité et de la sécurité et aux autres professionnels de l’informatique responsables de la sécurité et de la conformité informatiques dans les PME et dans les grandes entreprises.

Les sujets abordés sont les suivants :
- informations techniques détaillées sur l’installation et l’inscription des agents d’authentification ;
- informations techniques détaillées sur le chiffrement des mots de passe lors de la connexion de l’utilisateur ;
- sécurité des canaux entre les agents d’authentification locale et Azure Active Directory (Azure AD) ;
- informations techniques détaillées sur la façon dont les agents d’authentification sont maintenus sécurisés ;
- autres rubriques relatives à la sécurité.

## <a name="key-security-capabilities"></a>Principales fonctionnalités de sécurité

Voici les aspects clés de cette fonctionnalité relevant de la sécurité :
- Elle repose sur une architecture mutualisée sécurisée qui assure l’isolation des demandes de connexion entre les locataires.
- Les mots de passe locaux ne sont jamais stockés dans le cloud sous quelque forme que ce soit.
- Les agents d’authentification locale, qui écoutent et répondent aux demandes de validation de mot de passe, établissent uniquement des connexions sortantes à partir de votre réseau. Il n’est pas nécessaire d’installer ces agents d’authentification dans un réseau de périmètre (DMZ).
- Seuls les ports standard (80 et 443) permettent d’établir une communication sortante des agents d’authentification à Azure AD. Aucun port entrant ne doit nécessairement être ouvert sur votre pare-feu. 
  - Le port 443 est utilisé pour toutes les communications sortantes authentifiées.
  - Le port 80 est utilisé uniquement pour télécharger les listes de révocation de certificats (CRL) pour s’assurer que les certificats utilisés par la fonctionnalité n’ont pas été révoqués.
  - Consultez [ici](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites) la liste complète des exigences liées au réseau.
- Les mots de passe fournis par l’utilisateur pendant la connexion sont chiffrés dans le cloud avant d’être acceptés par les agents d’authentification locale pour être validés dans votre annuaire Active Directory.
- Le canal HTTPS entre Azure AD et un agent d’authentification locale est sécurisé par authentification mutuelle.
- Il s’intègre parfaitement aux fonctionnalités de protection du cloud d’Azure AD telles que les stratégies d’accès conditionnel (y compris Multi-Factor Authentication), Identity Protection et le verrouillage intelligent.

## <a name="components-involved"></a>Composants impliqués

Pour plus d’informations générales sur la sécurité opérationnelle et la sécurité des données et des services Azure AD, consultez le [Centre de confidentialité](https://azure.microsoft.com/support/trust-center/). Les composants suivants sont impliqués lorsque l’authentification directe est utilisée pour la connexion de l’utilisateur :
- **Azure AD STS** : service d’émission de jeton de sécurité (STS) sans état qui traite les demandes de connexion et émet des jetons de sécurité pour les navigateurs, les clients ou les services des utilisateurs en fonction des besoins.
- **Azure Service Bus** : offre une communication cloud avec une messagerie d’entreprise et une communication relayée qui vous aide à connecter des solutions locales au cloud.
- **Agent d’authentification Azure AD Connect (agent d’authentification)** : composant local qui écoute et répond aux demandes de validation de mot de passe.
- **Azure SQL Database** : contient des informations sur les agents d’authentification de votre locataire, y compris ses clés de chiffrement et de métadonnées.
- **Active Directory (AD)** : annuaire Active Directory local, où sont stockés vos comptes d’utilisateurs (et leurs mots de passe).

## <a name="installation-and-registration-of-authentication-agents"></a>Installation et inscription des agents d’authentification

Les agents d’authentification sont installés et inscrits auprès d’Azure AD lorsque vous [activez l’authentification directe à l’aide d’Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature) ou lorsque vous [ajoutez des agents d’authentification supplémentaires pour garantir une haute disponibilité des demandes de connexion](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability). Le fonctionnement d’un agent d’authentification implique trois phases principales :

- Installation de l’agent d’authentification
- Inscription de l’agent d’authentification
- Initialisation de l’agent d’authentification

Chacune de ces étapes est abordée en détail dans les rubriques suivantes.

### <a name="authentication-agent-installation"></a>Installation de l’agent d’authentification

Seuls les administrateurs généraux peuvent installer un agent d’authentification (à l’aide d’Azure AD Connect ou de façon autonome) sur un serveur local. Cette installation ajoute ces deux nouvelles entrées dans la liste **Panneau de configuration -> Programmes -> Programmes et fonctionnalités** :
- L’application de l’agent d’authentification proprement dite. Elle s’exécute avec les privilèges [Service réseau](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx).
- L’application de mise à jour utilisée pour mettre à jour automatiquement l’agent d’authentification. Elle s’exécute avec les privilèges [Système Local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx).


### <a name="authentication-agent-registration"></a>Inscription de l’agent d’authentification

Une fois l’agent d’authentification installé, il doit être inscrit auprès d’Azure AD. Pour ce faire, Azure AD affecte à chaque agent d’authentification un certificat d’identité numérique unique qu’il peut utiliser pour sécuriser la communication avec Azure AD.

La procédure d’inscription lie également l’agent d’authentification à votre locataire afin qu’Azure AD sache que cet agent d’authentification spécifique est le seul autorisé à gérer les demandes de validation de mot de passe de votre locataire. Cette procédure est répétée pour chaque nouvel agent d’authentification que vous inscrivez.

Voici comment se déroule l’inscription des agents d’authentification auprès d’Azure AD :

![Inscription de l’agent](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD demande tout d’abord à un administrateur général de se connecter à Azure AD avec ses informations d’identification. Pendant la connexion, l’agent d’authentification acquiert un jeton d’accès qu’il peut utiliser pour le compte de l’administrateur général.
2. L’agent d’authentification génère ensuite une paire de clés : une clé publique et une clé privée.
    - Cette paire de clés est générée à l’aide du chiffrement standard **RSA 2048 bits**.
    - La clé privée ne quitte jamais le serveur local sur lequel l’agent d’authentification a été installé.
3.  L’agent d’authentification effectue une demande d’inscription auprès d’Azure AD via le protocole HTTPS, les composants suivants étant inclus dans la demande :
    - le jeton d’accès obtenu à l’étape 1 ;
    - la clé publique générée à l’étape 2 ;
    - une **demande de signature de certificat** (CSR ou demande de certificat). Elle doit s’appliquer à un certificat d’identité numérique, avec Azure AD comme autorité de certification.
4. Azure AD valide le jeton d’accès dans la demande d’inscription et vérifie que la demande provient d’un administrateur général.
5. Azure AD signe ensuite et émet un certificat d’identité numérique vers l’agent d’authentification.
    - Le certificat est signé à l’aide de l’**autorité de certification racine d’Azure AD**. Notez que cette autorité de certification ne figure _pas_ dans le magasin des **autorités de certification racine reconnues approuvées**.
    - L’objet du certificat (**nom unique ou DN**) est défini sur votre **ID de locataire**. Il s’agit d’un GUID qui identifie de manière unique votre locataire. Le certificat ne peut donc être utilisé qu’avec votre locataire uniquement.
6. Azure AD stocke la clé publique de l’agent d’authentification dans une base de données SQL Azure accessible uniquement à Azure AD.
7. Le certificat (émis à l’étape 5) est stocké sur le serveur local dans le **magasin de certificats Windows** (plus précisément à l’emplacement **[CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)**) ; il est utilisé par les applications de l’agent d’authentification et de mise à jour.

### <a name="authentication-agent-initialization"></a>Initialisation de l’agent d’authentification

Lorsque l’agent d’authentification démarre, pour la première fois après son inscription ou après le redémarrage d’un serveur, il recherche un moyen de communiquer en toute sécurité avec le service Azure AD et accepte les demandes de validation de mot de passe.

![Initialisation de l’agent](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Voici comment les agents d’authentification sont initialisés :



1. L’agent d’authentification démarre en adressant une demande de démarrage sortante à Azure AD. 
    - Cette demande est effectuée sur le port 443 et via un canal HTTPS mutuellement authentifié (en utilisant le certificat émis lors de l’inscription de l’agent d’authentification).
2. Azure AD répond à cette demande de démarrage en fournissant une **clé d’accès** à une file d’attente Azure Service Bus qui est propre à votre locataire (identifié par votre ID de locataire).
3. L’agent d’authentification établit une connexion HTTPS sortante persistante (sur le port 443) avec la file d’attente. 
    - Il est maintenant prêt à récupérer et à gérer les demandes de validation de mot de passe.

Si vous avez inscrit plusieurs agents d’authentification sur votre locataire, la procédure d’initialisation permet de s’assurer que chacun d’eux se connecte à la même file d’attente Azure Service Bus. 

## <a name="processing-sign-in-requests"></a>Traitement des demandes de connexion 

Le diagramme ci-dessous montre comment l’authentification directe traite les demandes de connexion de l’utilisateur.

![Traitement d’une connexion](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

L’authentification directe traite une demande de connexion de l’utilisateur comme suit : 

1. Un utilisateur tente d’accéder à une application (par exemple, Outlook Web App à l’adresse [https://outlook.office365.com/owa](https://outlook.office365.com/owa)).
2. Si l’utilisateur n’est pas déjà connecté, l’application redirige le navigateur vers la page de connexion d’Azure AD.
3. Le service Azure AD STS répond avec la page de connexion de l’utilisateur.
4. L’utilisateur entre son nom d’utilisateur et sont mot de passe dans la page de connexion à Azure AD, puis clique sur le bouton « Se connecter ».
5. Le nom d’utilisateur et le mot de passe sont envoyés à Azure AD STS dans une requête POST HTTPS.
6. Azure AD STS récupère les clés publiques de tous les agents d’authentification inscrits sur votre locataire à partir de la base de données SQL Azure et les utilise pour chiffrer le mot de passe. 
    - Azure AD STS produit « N » valeurs de mot de passe chiffré pour « N » agents d’authentification inscrits sur votre locataire.
7. Azure AD STS place la demande de validation de mot de passe (le nom d’utilisateur et les valeurs de mot de passe chiffré) dans la file d’attente Azure Service Bus propre à votre locataire.
8. Comme les agents d’authentification initialisés sont connectés en permanence à la file d’attente Azure Service Bus, l’un des agents d’authentification disponibles récupère la demande de validation de mot de passe.
9. L’agent d’authentification localise la valeur de mot de passe chiffré propre à sa clé publique (à l’aide d’un identificateur) et la déchiffre à l’aide de sa clé privée.
10. L’agent d’authentification tente de valider le nom d’utilisateur et le mot de passe dans votre annuaire Active Directory local à l’aide de l’**[API LogonUser Win32](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)** (avec le paramètre **dwLogonType** défini sur **LOGON32_LOGON_NETWORK**). 
    - Il s’agit de l’API utilisée également par les services de fédération Active Directory (AD FS) pour connecter les utilisateurs dans un scénario de connexion fédérée.
11. L’agent d’authentification reçoit le résultat depuis Active Directory (réussite, nom d’utilisateur ou mot de passe incorrect, mot de passe expiré, utilisateur verrouillé et ainsi de suite).
12. L’agent d’authentification retransmet le résultat à Azure AD STS via un canal HTTPS mutuellement authentifié sortant sur le port 443. L’authentification mutuelle utilise le certificat précédemment émis pour l’agent d’authentification lors de l’inscription.
13. Azure AD STS vérifie que ce résultat est mis en corrélation avec la demande de connexion spécifique sur votre locataire.
14. Azure AD STS poursuit avec la procédure de connexion configurée. Par exemple, si la validation du mot de passe aboutit, l’utilisateur peut devoir s’authentifier via Multi-Factor Authentication ou être redirigé vers l’application.

## <a name="operational-security-of-authentication-agents"></a>Sécurité opérationnelle des agents d’authentification

Pour vous assurer que l’authentification directe reste sécurisée sur le plan opérationnel, Azure AD renouvelle régulièrement leurs certificats. Ces renouvellements sont déclenchés à partir d’Azure AD et ne sont pas régis par les agents d’authentification proprement dits.

![Sécurité opérationnelle](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Voici comment un agent d’authentification renouvelle sa relation d’approbation avec Azure AD :

1. L’agent d’authentification effectue régulièrement un test ping dans Azure AD (toutes les heures) pour vérifier s’il est temps de renouveler son certificat. 
    - Cette vérification est effectuée via un canal HTTPS mutuellement authentifié (à l’aide du certificat émis lors de l’inscription).
2. Si le service indique qu’il est temps de procéder au renouvellement, l’agent d’authentification génère une nouvelle paire de clés : une clé publique et une clé privée.
    - Ces clés sont générées à l’aide du chiffrement standard **RSA 2048 bits**.
    - La clé privée ne quitte jamais le serveur local.
3. L’agent d’authentification effectue ensuite une demande de renouvellement de certificat auprès d’Azure AD via le protocole HTTPS, les composants suivants étant inclus dans la demande :
    - Le certificat existant (récupéré à partir de l’emplacement **CERT_SYSTEM_STORE_LOCAL_MACHINE** dans le magasin de certificats Windows). Comme aucun administrateur général n’est impliqué dans cette procédure, aucun jeton d’accès n’est nécessaire pour le compte de l’administrateur général.
    - La clé publique générée à l’étape 2.
    - Une **demande de signature de certificat** (CSR ou demande de certificat). Elle doit s’appliquer à un nouveau certificat d’identité numérique, avec Azure AD comme autorité de certification.
4. Azure AD valide le certificat existant dans la demande de renouvellement de certificat. Il vérifie ensuite que la demande provient d’un agent d’authentification inscrit sur votre locataire.
5. Si le certificat existant est toujours valide, Azure AD signe un nouveau certificat d’identité numérique et le délivre à l’agent d’authentification. 
6. Si le certificat existant est arrivé à expiration, Azure AD supprime l’agent d’authentification dans la liste des agents d’authentification inscrits de votre locataire. Un administrateur général doit alors installer et inscrire un nouvel agent d’authentification manuellement.
    - Le certificat est signé à l’aide de l’**autorité de certification racine d’Azure AD**.
    - L’objet du certificat (**nom unique ou DN**) est défini sur votre **ID de locataire**, qui est un GUID qui identifie de façon unique votre locataire. Autrement dit, le certificat est limité à votre locataire uniquement.
6. Azure AD stocke la nouvelle clé publique de l’agent d’authentification dans une base de données SQL Azure accessible uniquement à Azure AD. Il invalide l’ancienne clé publique associée à l’agent d’authentification.
7. Le nouveau certificat (émis à l’étape 5) est ensuite stocké sur le serveur dans le **magasin de certificats Windows** (plus précisément à l’emplacement **[CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)**).
    - Comme la procédure de renouvellement d’approbation se produit de manière non interactive (sans la présence de l’administrateur général), l’agent d’authentification n’a plus accès pour mettre à jour le certificat existant à l’emplacement **CERT_SYSTEM_STORE_LOCAL_MACHINE**. Notez que le certificat proprement dit qui se trouve dans **CERT_SYSTEM_STORE_LOCAL_MACHINE** n’est pas supprimé lors de cette procédure.
8. Le nouveau certificat est utilisé dorénavant pour l’authentification. Tous les renouvellements ultérieurs du certificat remplacent le certificat dans **CERT_SYSTEM_STORE_LOCAL_MACHINE**.

## <a name="auto-update-of-authentication-agents"></a>Mise à jour automatique des agents d’authentification

L’application de mise à jour met automatiquement à jour l’agent d’authentification lors de la sortie d’une nouvelle version. Il ne traite pas les demandes de validation de mot de passe de votre locataire. 

Azure AD héberge la nouvelle version du logiciel en tant que **package Windows Installer (MSI)** signé. Le package MSI est signé à l’aide de [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) avec l’algorithme de chiffrement **SHA256**. 

![Mise à jour automatique](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Voici comment un agent d’authentification est mis à jour automatiquement :

1. Le programme de mise à jour effectue régulièrement un test ping dans Azure AD (toutes les heures) pour vérifier si une nouvelle version de l’agent d’authentification est disponible. 
    - Cette vérification est effectuée via un canal HTTPS mutuellement authentifié (à l’aide du certificat émis lors de l’inscription). L’agent d’authentification et le programme de mise à jour partagent le certificat stocké sur le serveur.
2. Si une nouvelle version est disponible, Azure AD retourne le MSI signé au programme de mise à jour.
3. Le programme de mise à jour vérifie que le MSI est signé par Microsoft.
4. Le programme de mise à jour exécute le MSI. Cette action exécute les étapes ci-dessous (notez que le programme de mise à jour s’exécute avec les privilèges [Système Local](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)) :
    - Arrête le service Agent d’authentification.
    - Installe la nouvelle version de l’agent d’authentification sur le serveur.
    - Redémarre le service Agent d’authentification.

>[!NOTE]
>Si vous avez inscrit plusieurs agents d’authentification sur votre locataire, Azure AD ne renouvelle pas leurs certificats ou ne les met pas à jour en même temps. Azure AD effectue ces procédures progressivement pour s’assurer de la haute disponibilité des demandes de connexion.


## <a name="next-steps"></a>Étapes suivantes
- [**Limitations actuelles**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) : découvrez les scénarios pris en charge et ceux qui ne le sont pas.
- [**Démarrage rapide**](active-directory-aadconnect-pass-through-authentication-quick-start.md) : soyez opérationnel avec l’authentification directe Azure AD.
- [**Verrouillage intelligent**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) : configurez la fonctionnalité Verrouillage intelligent sur votre locataire pour protéger les comptes d’utilisateur.
- [**Fonctionnement**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) : découvrez les principes de fonctionnement de l’authentification directe Azure AD.
- [**Questions fréquentes (FAQ)**](active-directory-aadconnect-pass-through-authentication-faq.md) : réponses aux questions fréquentes.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants susceptibles de se produire avec cette fonctionnalité.
- [**Authentification unique transparente Azure AD**](active-directory-aadconnect-sso.md) : explorez en détail cette fonctionnalité complémentaire.

