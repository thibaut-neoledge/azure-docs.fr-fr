---
title: 'Azure AD Connect : authentification unique | Microsoft Docs'
description: "Cette rubrique vous fournit les informations nécessaires sur l’authentification unique à partir d’un répertoire Active Directory (AD) local vers un répertoire Azure Active Directory (Azure AD) dans le cloud et les services connectés."
services: active-directory
keywords: "Qu’est-ce qu’Azure AD Connect, Installation d’Active Directory, Composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e208b2bf861d698901b287458a3969e833540e44
ms.openlocfilehash: f8f67af3cb6adb333924714bd758609b950845af
ms.lasthandoff: 02/17/2017

---

# <a name="what-is-single-sign-on-sso-preview"></a>Qu’est-ce que l’authentification unique (SSO) (version préliminaire)
L’authentification unique est une option qui peut être activée dans Azure Active Directory Connect, soit par [synchronisation de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md), soit par [authentification directe](active-directory-aadconnect-pass-through-authentication.md). Quand elle est activée, les utilisateurs doivent uniquement entrer leur nom d’utilisateur et n’ont pas besoin de saisir leur mot de passe pour se connecter à Azure Active Directory (Azure AD) ou à d’autres services cloud s’ils utilisent un ordinateur d’entreprise connecté au réseau d’entreprise.

![Authentification unique](./media/active-directory-aadconnect-sso/sso1.png)

Si vos utilisateurs finaux ont la possibilité d’utiliser l’authentification unique, l’accès aux services dans le cloud devient plus familier et fournit à l’entreprise un processus simple et sécurisé qui ne nécessite aucun composant local supplémentaire.

L’authentification unique est une fonctionnalité qui est activée par le biais d’Azure AD Connect. Elle fonctionne avec la synchronisation de mot de passe ou l’authentification directe et votre annuaire Active Directory local. Pour que les utilisateurs finaux utilisent l’authentification unique dans votre environnement, vous devez vérifier que les utilisateurs :

- Sont sur une machine jointe à un domaine.
- Disposent d’une connexion directe à un contrôleur de domaine, par exemple sur le réseau câblé ou sans fil de l’entreprise ou via une connexion d’accès à distance telle qu’une connexion VPN.
- Définissent les points de terminaison Kerberos dans le cloud comme faisant partie de la zone intranet du navigateur.

Si l’une de ces conditions n’est pas remplie, par exemple si l’ordinateur est déconnecté du réseau d’entreprise, l’utilisateur est invité à entrer son mot de passe, comme en l’absence de l’authentification unique.

## <a name="supported-clients"></a>Clients pris en charge
L’authentification unique est prise en charge par les clients basés sur le navigateur web et les clients Office qui prennent en charge l’[authentification moderne](https://aka.ms/modernauthga) sur les ordinateurs compatibles avec l’authentification Kerberos, par exemple Windows. Le tableau ci-dessous fournit des détails sur les clients basés sur le navigateur sur différents systèmes d’exploitation.

| Système d’exploitation\Navigateur |Internet Explorer|Chrome|Firefox|Edge
| --- | --- |--- | --- | --- |
|Windows 10|Oui|Oui|Oui*|Non
|Windows 8.1|Oui|Oui|Oui*|N/A
|Windows 8|Oui|Oui|Oui*|N/A
|Windows 7|Oui|Oui|Oui*|N/A
|Mac|N/A|N/A|N/A|N/A

\*Nécessite une configuration distincte.

>[!NOTE]
>Pour les clients basés sur Windows 10, il est recommandé d’utiliser [Azure AD join](../active-directory-azureadjoin-overview.md) pour une expérience optimale avec Azure AD.

## <a name="how-single-sign-on-works"></a>Fonctionnement de l’authentification unique

Quand vous activez l’authentification unique dans Azure AD Connect, un compte d’ordinateur nommé AZUREADSSOACCT est créé dans l’annuaire Active Directory local et la clé de déchiffrement Kerberos est partagée en toute sécurité avec Azure AD. En outre, deux noms de principal du service (SPN) Kerberos sont créés pour représenter les URL du cloud qui sont utilisées lors de l’authentification entre le client et Azure AD.

Une fois cette configuration terminée, le processus d’authentification est le même que pour toute application basée sur l’authentification Windows intégrée. Si vous connaissez le fonctionnement de l’authentification Windows intégrée, vous savez déjà comment l’authentification unique fonctionne avec Azure AD. Si c’est nouveau pour vous, voici comment fonctionne le processus d’authentification Windows intégrée :

![Authentification unique](./media/active-directory-aadconnect-sso/sso2.png)

Tout d’abord, l’utilisateur tente d’accéder à une ressource qui approuve les jetons émis à partir d’Azure AD, comme SharePoint Online. Puis SharePoint Online redirige l’utilisateur pour procéder à l’authentification auprès d’Azure AD. L’utilisateur fournit ensuite son nom d’utilisateur, pour qu’Azure AD puisse établir si l’authentification unique est activée pour son organisation. Le trafic suivant se produit si l’authentification unique est activée pour l’organisation.

1.    Azure AD demande au client, via une réponse 401 Non autorisé, de fournir un ticket Kerberos.
2.    Le client demande un ticket à Active Directory pour Azure AD.
3.    Active Directory localise le compte d’ordinateur créé par Azure AD Connect et renvoie un ticket Kerberos au client, chiffré avec la clé secrète du compte d’ordinateur. Le ticket inclut l’identité de l’utilisateur actuellement connecté à l’ordinateur.
4.    Le client envoie le ticket Kerberos reçu de la part d’Active Directory à Azure AD.
5.    Azure AD déchiffre le ticket Kerberos à l’aide de la clé partagée précédemment. Puis Azure AD renvoie un jeton à l’utilisateur ou invite l’utilisateur à fournir des preuves supplémentaires (telles que l’authentification multifacteur), si la ressource l’exige.

L’authentification unique est une fonctionnalité opportuniste, ce qui signifie que si elle échoue pour une raison quelconque, il suffit à l’utilisateur d’entrer son mot de passe sur la page de connexion, comme d’habitude.

## <a name="single-sign-on-sso-prerequisites"></a>Composants d’authentification unique (SSO) requis
Si vous activez l’option Authentification unique avec Authentification directe, il n’existe aucun composant requis supplémentaire au-delà de ce qui est nécessaire pour l’authentification directe.

Si vous activez l’option Authentification unique avec Synchronisation du mot de passe et s’il existe un pare-feu entre Azure AD Connect et Azure AD, vérifiez les points suivants :
- Le serveur Azure AD Connect peut communiquer avec *. msappproxy.net.
- Azure AD Connect peut envoyer des requêtes HTTPS à Azure AD sur les ports ci-dessous :

|Protocole|Numéro de port|Description
| --- | --- | ---
|HTTPS|9090|    Activez l’inscription SSO (obligatoire uniquement pour le processus d’inscription SSO).

## <a name="enabling-sso-with-pass-through-authentication-or-password-sync"></a>Activation de l’authentification unique avec l’authentification directe ou la synchronisation de mot de passe
Azure AD Connect propose un processus simple pour activer l’authentification unique avec l’authentification directe ou la synchronisation de mot de passe. Vérifiez que vous disposez de droits d’administrateur de domaine sur l’un des domaines de chaque forêt que vous synchronisez, afin de pouvoir configurer les noms de principal du service (SPN) Kerberos sur le compte d’ordinateur. Le nom d’utilisateur et le mot de passe ne sont pas stockés dans Azure AD Connect ou Azure AD et sont utilisés uniquement pour cette opération.

Quand vous installez Azure AD Connect, sélectionnez une installation personnalisée pour pouvoir sélectionner l’option d’authentification unique dans la page de connexion de l’utilisateur. Pour plus d’informations, consultez [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Authentification unique](./media/active-directory-aadconnect-sso/sso3.png)

Une fois l’authentification unique activée, vous pouvez continuer l’Assistant Installation jusqu’à la page d’authentification unique.

![Authentification unique](./media/active-directory-aadconnect-sso/sso4.png)

Pour chaque forêt répertoriée, indiquez les informations de compte attendues. L’authentification unique est activée pour votre répertoire Azure.

>[!NOTE]
>Azure AD Connect doit pouvoir communiquer avec \*.msappproxy.net sur le port 9090 (TCP) pour configurer l’authentification unique. Cette ouverture de port est uniquement nécessaire durant la configuration et n’est pas utile durant les authentifications par les utilisateurs finaux.

## <a name="ensuring-clients-sign-in-automatically"></a>Vérifier que les clients se connectent automatiquement
Par défaut, les navigateurs ne tentent pas d’envoyer les informations d’identification aux serveurs web, sauf si l’URL est définie comme faisant partie de la zone Intranet. En règle générale, le navigateur peut calculer la zone adéquate en examinant l’URL. Par exemple, si l’URL est http://intranet/, le navigateur envoie automatiquement les informations d’identification puisque l’URL se trouve dans la zone Intranet. Toutefois, si l’URL contient un point, par exemple http://intranet.contoso.com/, l’ordinateur n’envoie pas automatiquement les informations d’identification et traite l’URL comme tout autre site Internet.

Étant donné que les URL utilisées pour l’authentification unique dans Azure AD contiennent un point, elles doivent être ajoutées explicitement à la zone intranet de l’ordinateur. Ce paramètre permet au navigateur d’envoyer automatiquement à Azure AD les informations d’identification de l’utilisateur actuellement connecté sous forme d’un ticket Kerberos. Pour ajouter les URL exigées à la zone intranet, le plus simple consiste à créer une stratégie de groupe dans Active Directory.

1.    Ouvrez les outils de gestion de stratégie de groupe.
2.    Modifiez la stratégie de groupe appliquée à tous les utilisateurs, par exemple la **stratégie de domaine par défaut**.
3.    Accédez à **Configuration utilisateur\Modèles d’administration\Composants Windows\Internet Explorer\Panneau de configuration Internet\Page de sécurité** et sélectionnez **Liste des attributions de sites aux zones**.
![Authentification unique](./media/active-directory-aadconnect-sso/sso6.png)  
4.    Activez la stratégie, puis entrez les valeurs/données suivantes dans la boîte de dialogue.  

        Valeur : https://autologon.microsoftazuread-sso.com  
        Data 1  
        Valeur : https://aadg.windows.net.nsatc.net  
        Data 1  
5.    Le résultat doit être semblable à ce qui suit :  
![Authentification unique](./media/active-directory-aadconnect-sso/sso7.png)

6.    Cliquez sur **OK**, puis de nouveau sur **OK**.

Vos utilisateurs sont désormais prêts pour l’authentification unique.

>[!NOTE]
>Par défaut, Chrome utilise le même ensemble d’URL de site de confiance qu’Internet Explorer. Si vous avez configuré des paramètres différents pour Chrome, vous devez mettre à jour ces sites séparément.

## <a name="troubleshooting-single-sign-on-issues"></a>Résolution des problèmes d’authentification unique
Il est important de vérifier que le client est correctement configuré pour l’authentification unique, notamment :

1.    https://autologon.microsoftazuread-sso.com et https://aadg.windows.net.nsatc.net sont définis dans la zone Intranet.
2.    Assurez-vous que la station de travail est jointe au domaine.
3.    Assurez-vous que l’utilisateur est connecté avec un compte de domaine.
4.    Vérifiez que l’ordinateur est connecté au réseau d’entreprise.
5.    Assurez-vous que l’heure de la machine est synchronisée avec Active Directory et que l’heure du contrôleur de domaine n’a pas plus de 5 minutes d’écart avec l’heure correcte.
6.    Purgez les tickets Kerberos existants des clients. Vous pouvez par exemple exécuter la commande **klist purge** à partir d’une invite de commandes.

Si vous avez pu confirmer les points ci-dessus, vous pouvez consulter les journaux de la console du navigateur pour obtenir davantage d’informations. Vous trouverez les journaux de la console parmi les outils de développement. Ces journaux vous aideront à déterminer le problème potentiel.

## <a name="event-log-entries"></a>Entrées du journal des événements
Si l’audit des réussites est activé, une entrée est enregistrée dans le journal des événements du contrôleur de domaine chaque fois qu’un utilisateur se connecte avec l’authentification unique. Pour trouver ces événements, vous pouvez rechercher l’événement de sécurité 4769 associé au compte d’ordinateur **AzureADSSOAcc$** dans les journaux des événements. Le filtre ci-dessous recherche tous les événements de sécurité associés au compte d’ordinateur :

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

