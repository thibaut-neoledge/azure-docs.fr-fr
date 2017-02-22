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
ms.date: 01/04/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 44c5726d0bf621e463aa2f3ab061ed12d48418b0
ms.openlocfilehash: b36d639b5d6b18de3a8233f801cdd0abf3f25825


---

# <a name="what-is-single-sign-on-sso-preview"></a>Qu’est-ce que l’authentification unique (SSO) (version préliminaire)
L’authentification unique est une option qui peut être activée dans Azure Active Directory Connect, soit par [synchronisation de hachage de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md), soit par [authentification directe](active-directory-aadconnect-pass-through-authentication.md).  Lorsqu’elle est activée, les utilisateurs doivent uniquement entrer leur nom d’utilisateur et n’ont pas besoin de saisir leur mot de passe pour se connecter à Azure Active Directory (Azure AD) ou aux autres services dans le cloud s’ils utilisent un ordinateur professionnel connecté au réseau d’entreprise.

![Authentification unique](./media/active-directory-aadconnect-sso/sso1.png)

Si vos utilisateurs finaux ont la possibilité d’utiliser l’authentification unique, l’accès aux services dans le cloud devient plus familier et fournit à l’entreprise un processus simple et sécurisé qui ne nécessite aucun composant local supplémentaire.

L’authentification unique est une fonctionnalité qui est activée via AAD Connect. Elle fonctionne à la fois avec la synchronisation de hachage de mot de passe ou l’authentification directe et votre répertoire Active Directory local.  Pour que les utilisateurs finaux utilisent l’authentification unique dans votre environnement, vous devez vous assurer :


- Que les utilisateurs sont sur une machine jointe à un domaine
- Que les utilisateurs disposent d’une connexion directe à un contrôleur de domaine, par exemple sur le réseau câblé ou sans fil de l’entreprise ou via une connexion d’accès à distance telle qu’une connexion VPN.
- Que les points de terminaison Kerberos dans le cloud ont été définis comme faisant partie de la zone Intranet des navigateurs.

Si l’une des conditions ci-dessus n’est pas remplie, par exemple si la machine est déconnectée du réseau d’entreprise et qu’Active Directory n’est pas disponible, l’utilisateur sera simplement invité à entrer son mot de passe, comme en l’absence de l’authentification unique.

## <a name="supported-clients"></a>Clients pris en charge
L’authentification unique est prise en charge par les clients basés sur navigateur web et par les clients Office qui prennent en charge l’[authentification moderne](https://aka.ms/modernauthga) sur les machines compatibles avec l’authentification Kerberos, par exemple Windows.  Le tableau ci-dessous fournit des détails sur les clients basés sur navigateur sur différents systèmes d’exploitation.

| Système d’exploitation\Navigateur |Internet Explorer|Chrome|Firefox|Edge
| --- | --- |--- | --- |--- |
|Windows 10|Oui|Oui|Oui*|Non
|Windows 8.1|Oui|Oui|Oui*|N/A
|Windows 8|Oui|Oui|Oui*|N/A
|Windows 7|Oui|Oui|Oui*|N/A
|Mac|N/A|N/A|N/A|N/A

*Requiert une configuration distincte.

>[!NOTE]
>Pour les clients basés sur Windows 10, il est recommandé d’utiliser [Azure AD join](../active-directory-azureadjoin-overview.md) pour une expérience optimale avec Azure AD.

## <a name="how-single-sign-on-works"></a>Fonctionnement de l’authentification unique

Lorsque vous activez l’authentification unique dans Azure AD Connect, un compte d’ordinateur nommé AZUREADSSOACCT est créé dans le répertoire Active Directory local et la clé de déchiffrement Kerberos est partagée en toute sécurité avec Azure AD.  En outre, deux noms de principal du service (SPN) Kerberos sont créés pour représenter les URL du cloud qui sont utilisées lors de l’authentification entre le client et Azure AD.

Une fois cette configuration terminée, le processus d’authentification est le même que pour toute application basée sur l’authentification Windows intégrée.  Si vous connaissez le fonctionnement de l’authentification Windows intégrée, vous savez déjà comment l’authentification unique fonctionne avec Azure AD.  Si c’est nouveau pour vous, voici comment fonctionne le processus d’authentification Windows intégrée :

![Authentification unique](./media/active-directory-aadconnect-sso/sso2.png)

Tout d’abord, l’utilisateur tente d’accéder à une ressource qui approuve les jetons émis à partir d’Azure AD, comme SharePoint Online.  Puis SharePoint Online redirige l’utilisateur pour procéder à l’authentification auprès d’Azure AD. L’utilisateur fournit ensuite son nom d’utilisateur, pour qu’Azure AD puisse établir si l’authentification unique est activée pour son organisation. Les actions suivantes se produisent alors si l’authentification unique est activée pour l’organisation.

1.  Azure AD demande au client, via une réponse 401 Non autorisé, de fournir un ticket Kerberos.
2.  Le client demande un ticket à Active Directory pour Azure AD.
3.  Active Directory localise le compte machine créé par Azure AD Connect et renvoie un ticket Kerberos au client, chiffrée avec la clé secrète du compte machine. Le ticket inclut l’identité de l’utilisateur actuellement connecté à l’ordinateur.
4.  Le client envoie le ticket Kerberos reçu de la part d’Active Directory à Azure AD.
5.  Azure AD déchiffre le ticket Kerberos à l’aide de la clé partagée précédemment, puis renvoie un jeton à l’utilisateur ou invite l’utilisateur à fournir des preuves supplémentaires (telles que l’authentification multifacteur), si requis par la ressource.

L’authentification unique est une fonctionnalité opportuniste, ce qui signifie que si elle échoue pour une raison quelconque, il suffit à l’utilisateur d’entrer son mot de passe sur la page de connexion, comme d’habitude.

## <a name="enabling-sso-with-pass-through-authentication-or-password-hash-sync"></a>Activation de l’authentification unique avec l’authentification directe ou la synchronisation de hachage de mot de passe
Azure AD Connect propose un processus simple pour activer l’authentification unique avec l’authentification directe ou la synchronisation de hachage de mot de passe.  Vous devez vous assurer que vous disposez des droits d’administrateur de domaine sur l’un des domaines de chaque forêt que vous synchronisez, afin de pouvoir configurer les noms de principal de service (SPN) Kerberos sur le compte machine.  Le nom d’utilisateur et le mot de passe ne sont pas stockés dans Azure AD Connect ou Azure AD et sont utilisés uniquement pour cette opération.

Lorsque vous installez Azure AD Connect, sélectionnez une installation personnalisée afin d’être en mesure de sélectionner l’option d’authentification unique sur la page de connexion de l’utilisateur. Pour plus d’informations, voir [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Authentification unique](./media/active-directory-aadconnect-sso/sso3.png)

Une fois l’authentification unique activée, vous pouvez continuer l’Assistant jusqu'à la page d’authentification unique.

![Authentification unique](./media/active-directory-aadconnect-sso/sso4.png)

Pour chaque forêt répertoriée, fournissez les informations de compte attendues et l’authentification unique sera activée pour votre répertoire Azure.

>[!NOTE]
>Azure AD Connect doit être en mesure de communiquer avec *.msappproxy.net sur le port 9090 (TCP) pour configurer l’authentification unique. Cela est nécessaire uniquement lors de la configuration et n’est pas utile lors des authentifications par les utilisateurs finaux.

## <a name="ensuring-clients-sign-in-automatically"></a>S’assurer que les clients se connectent automatiquement
Par défaut, les navigateurs ne tenteront pas d’envoyer les informations d’identification aux serveurs web, sauf si l’URL est définie comme faisant partie de la zone Intranet.  En règle générale, le navigateur peut calculer la zone adéquate en examinant l’URL.  Par exemple, si l’URL est http://intranet/, le navigateur envoie automatiquement les informations d’identification car il saura que l’URL appartient à la zone Intranet.  Toutefois, si l’URL contient un point, par exemple http://intranet.contoso.com/, le serveur n’envoie pas automatiquement les informations d’identification et traite l’URL comme s’il s’agissait de n’importe quel site Internet.

Dans la mesure où les URL utilisées pour l’authentification unique dans Azure AD contiennent un point car elles sont généralement des noms d’hôte routables, elles doivent être ajoutées explicitement à la zone Intranet de la machine, afin que le navigateur envoie automatiquement à Azure AD les informations d’identification de l’utilisateur actuellement connecté sous la forme d’un ticket Kerberos.  Pour ajouter les URL souhaitées à la zone Intranet, le plus simple consiste à créer une stratégie de groupe dans Active Directory.

1.  Ouvrir les outils de gestion de stratégie de groupe
2.  Modifiez la stratégie de groupe qui sera appliquée à tous les utilisateurs.  Par exemple, la stratégie de domaine par défaut.
3.  Accédez à **Configuration utilisateur\Modèles d’administration\Composants Windows\Internet Explorer\Panneau de configuration Internet\Page de sécurité** et sélectionnez **Liste des attributions de sites aux zones**.
![Authentification unique](./media/active-directory-aadconnect-sso/sso6.png) </br>
4.  Activez la stratégie, puis entrez les valeurs/données suivantes dans la boîte de dialogue.</br>

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1'
5.  Le résultat doit être semblable à ce qui suit : ![Authentification unique](./media/active-directory-aadconnect-sso/sso7.png)

6.  Cliquez sur OK, puis de nouveau sur OK.

Vos utilisateurs sont désormais prêts pour l’authentification unique.

>[!NOTE]
>Par défaut, Chrome utilisera le même ensemble d’URL de confiance qu’Internet Explorer.  Si vous avez configuré des paramètres différents pour Chrome, vous devez les mettre à jour séparément.

## <a name="troubleshooting-single-sign-on-issues"></a>Dépannage des problèmes d’authentification unique
Il est important de s’assurer que le client est correctement configuré pour l’authentification unique, notamment :

1.  https://autologon.microsoftazuread-sso.com et https://aadg.windows.net.nsatc.net sont définis dans la zone Intranet.
2.  Assurez-vous que la station de travail est jointe au domaine.
3.  Assurez-vous que l’utilisateur est connecté avec un compte de domaine.
4.  Vérifiez que la machine est connectée au réseau d’entreprise.
5.  Assurez-vous que l’heure de la machine est synchronisée avec Active Directory et que l’heure du contrôleur de domaine n’a pas plus de 5 minutes d’écart avec l’heure correcte.
6.  Purgez les clients existants des tickets Kerberos. Vous pouvez par exemple exécuter la commande « klist purge » à partir d’une invite de commandes.

Si vous avez pu vérifier les points ci-dessus, vous pouvez consulter les journaux de la console du navigateur pour plus d’informations.  Vous trouverez les journaux de la console parmi les outils de développement.  Cela vous aidera à déterminer le problème potentiel.

## <a name="event-log-entries"></a>Entrées du journal des événements
Chaque fois qu’un utilisateur ouvre une session avec l’authentification unique, une entrée est enregistrée dans le journal des événements du contrôleur de domaine, si l’audit des réussites est activé.  Pour rechercher ces événements, vous pouvez rechercher l’événement de sécurité 4769 associé au compte machine AzureADSSOAcc$ dans les journaux des événements.  Le filtre ci-dessous recherche tous les événements de sécurité associés au compte d’ordinateur :

    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>



<!--HONumber=Jan17_HO1-->


