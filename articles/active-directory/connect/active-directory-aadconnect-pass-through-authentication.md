---
title: 'Azure AD Connect : authentification directe | Microsoft Docs'
description: "Cette rubrique vous fournit les informations nécessaires sur le fonctionnement de l’authentification directe dans Azure AD Connect avec un répertoire Active Directory (AD) local en vue d’accéder à un répertoire Azure Active Directory (Azure AD) et aux services connectés."
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
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 67b832253619afe789a4dfdb95893e8c0ae62bee
ms.openlocfilehash: 17890fddf948ddc0e89a9107ac5fe65223cd05e1
ms.lasthandoff: 01/30/2017

---

# <a name="what-is-azure-ad-pass-through-authentication"></a>Qu’est-ce que l’authentification directe Azure AD
L’utilisation des mêmes informations d’identification (nom d’utilisateur et mot de passe) pour accéder aux ressources d’entreprise et aux services cloud permet aux utilisateurs de ne pas avoir à mémoriser d’autres informations d’identification. Le risque qu’ils oublient leurs identifiants s’en trouve réduit, de même que l’implication du support technique dans les événements de réinitialisation de mot de passe.

Bien que de nombreuses sociétés soient prêtes à utiliser la [synchronisation de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) Azure AD pour que les utilisateurs disposent des mêmes informations d’identification pour accéder aux services locaux et cloud, d’autres exigent que les mots de passe, même au format haché, ne sortent pas de l’enceinte de leur organisation interne.

L’authentification directe Azure AD constitue une solution simple pour ces clients. Elle garantit que les mots de passe des services Azure AD sont validés par rapport à leur annuaire Active Directory local. La validation des mots de passe ne nécessite aucune infrastructure réseau complexe et n’exige pas la présence des mots de passe locaux dans le cloud.

Quand l’authentification directe est combinée avec l’[authentification unique](active-directory-aadconnect-sso.md), les utilisateurs n’ont pas besoin de taper leur mot de passe pour se connecter à Azure AD ou à d’autres services cloud. Cette fonctionnalité offre aux clients une expérience véritablement intégrée sur leurs ordinateurs d’entreprise.

![Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

L’authentification directe peut être configurée par le biais d’Azure AD Connect. Elle utilise un agent local simple qui écoute les demandes de validation de mot de passe. L’agent peut facilement être déployé sur plusieurs ordinateurs afin de fournir une haute disponibilité et un équilibrage de charge. Étant donné que toutes les communications sont uniquement sortantes, il n’est pas obligatoire de disposer d’une zone DMZ ou d’installer le connecteur dans une zone DMZ. La configuration requise pour le connecteur est la suivante :

- Windows Server 2012 R2 ou version ultérieure
- Ordinateur joint à un domaine dans la forêt dans laquelle les utilisateurs sont validés

>[!NOTE]
>La prise en charge des environnements à plusieurs forêts est possible s’il existe des approbations entre les forêts et que le routage du suffixe de leurs noms est configuré correctement.

## <a name="supported-clients-in-the-preview"></a>Clients pris en charge dans la version préliminaire

L’authentification directe est prise en charge par les clients basés sur le navigateur web et les clients Office qui prennent en charge l’[authentification moderne](https://aka.ms/modernauthga). Pour les clients qui ne sont pas pris en charge, par exemple les clients Office hérités ou la synchronisation active Exchange (c’est-à-dire des clients de messagerie natifs sur des appareils mobiles), les clients sont encouragés à utiliser l’authentification moderne équivalente. Ces clients permettent non seulement l’authentification directe, mais aussi l’application d’un accès conditionnel (par exemple l’authentification multifacteur).

Pour les clients utilisant Windows 10 joint à Azure AD, l’authentification directe n’est actuellement pas prise en charge. Cependant, les clients peuvent utiliser la synchronisation de mot de passe comme solution de secours automatique pour Windows 10, en plus des autres clients hérités.

>[!NOTE]
>Dans la préversion, la synchronisation de mot de passe est activée par défaut lorsque l’authentification directe est sélectionnée en tant qu’option de connexion dans Azure AD Connect. Ce paramètre peut être désactivé dans la page Options d’Azure AD Connect.

## <a name="how-azure-ad-pass-through-authentication-works"></a>Fonctionnement de l’authentification directe Azure AD
Quand un utilisateur entre son nom d’utilisateur et son mot de passe dans la page de connexion d’Azure AD, Azure AD place le nom d’utilisateur et le mot de passe dans la file d’attente du connecteur local approprié pour validation. L’un des connecteurs locaux disponibles récupère alors le nom d’utilisateur et le mot de passe, puis les valide en les comparant à Active Directory. La validation se produit par le biais d’API Windows standard, de manière similaire à la validation du mot de passe par Active Directory Federation Services.

Le contrôleur de domaine local évalue ensuite la requête et renvoie une réponse au connecteur, qui la transmet à son tour à Azure AD. Azure AD évalue alors la réponse et répond de manière appropriée à l’utilisateur, par exemple en émettant un jeton ou en demandant l’authentification multifacteur. Le diagramme suivant illustre ces étapes :

![Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="azure-ad-pass-through-prerequisites"></a>Conditions préalables pour l’authentification directe Azure AD
Avant de pouvoir activer et utiliser l’authentification directe Azure AD, vous devez disposer des éléments suivants :

- Azure AD Connect
- Un client Azure AD dont vous êtes un administrateur global.

>[!NOTE]
>Il est recommandé que le compte soit un compte d’administrateur cloud uniquement, afin que vous puissiez gérer la configuration de votre client en cas de panne ou d’indisponibilité de vos services locaux.

- Un serveur Windows Server 2012 R2 ou ultérieur sur lequel exécuter Azure AD Connect. Cet ordinateur doit être membre de la même forêt que les utilisateurs qui sont validés.
- Si vous avez plusieurs forêts contenant des utilisateurs à valider avec Azure AD, les forêts doivent avoir des approbations entre elles.
- Le UserPrincipalName local doit être utilisé en tant que nom d’utilisateur Azure AD.
- Un second serveur exécutant Windows Server 2012 R2 ou version ultérieure sur lequel exécuter un deuxième connecteur pour la haute disponibilité et l’équilibrage de charge. Vous trouverez ci-dessous des instructions pour déployer ce connecteur.
- S’il existe un pare-feu entre le connecteur et Azure AD, assurez-vous que :
    - Si le filtrage d’URL est activé, assurez-vous que le connecteur peut communiquer avec les URL suivantes :
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net.  
        -  Le connecteur établit également des connexions IP directes vers les [plages IP de centre de données Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Assurez-vous que le pare-feu n’effectue pas d’inspection SSL, car le connecteur utilise les certificats clients pour communiquer avec Azure AD.
    - Assurez-vous que le connecteur peut envoyer des requêtes HTTPS (TCP) à Azure AD sur les ports ci-dessous.

|Numéro de port|Description
| --- | ---
|80|Active le trafic HTTP sortant pour valider la sécurité, comme les listes de révocation de certificats SSL.
|443|    Active l’authentification utilisateur auprès d’Azure AD.
|8080/443|    Active la séquence de démarrage du connecteur et la mise à jour automatique du connecteur.
|9090|    Active l’enregistrement du connecteur (obligatoire uniquement pour le processus d’inscription du connecteur).
|9091|    Active le renouvellement automatique de certificat de confiance du connecteur.
|9352, 5671|    Active la communication entre le connecteur et le service Azure AD pour les requêtes entrantes.
|9350|    [Facultatif] Offre de meilleures performances pour les requêtes entrantes.
|10100 à&10120;|    Active les réponses à partir du connecteur vers Azure AD.

Si votre pare-feu régule le trafic en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau. Assurez-vous également d’activer le port 8080 pour NT Authority\System.

## <a name="enabling-pass-through-authentication"></a>Activation de l’authentification directe
L’authentification directe Azure AD peut être activée via Azure AD Connect. L’activation de l’authentification directe déploie le premier connecteur sur le même serveur qu’Azure AD Connect. Pendant l’installation d’Azure AD Connect, sélectionnez une installation personnalisée et l’authentification directe dans la page des options de connexion. Pour plus d’informations, consultez [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Authentification unique](./media/active-directory-aadconnect-sso/sso3.png)

Par défaut, le premier connecteur de l’authentification directe est installé sur le serveur Azure AD Connect. Vous devez déployer un second connecteur sur un autre ordinateur pour assurer la haute disponibilité et l’équilibrage de charge des demandes d’authentification.

Pour déployer le deuxième connecteur, procédez comme suit :

### <a name="step-1-install-the-connector-without-registration"></a>Étape 1 : Installation du connecteur sans inscription

1.    Téléchargez le dernier [connecteur](https://go.microsoft.com/fwlink/?linkid=837580).
2.    Ouvrez une invite de commandes en tant qu’administrateur.
3.    Exécutez la commande suivante dans laquelle /q signifie une installation silencieuse ; l’installation ne vous demande pas d’accepter le Contrat de Licence Utilisateur Final.

```
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
```

### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Étape 2 : Inscription du connecteur avec Azure AD pour l’authentification directe

1.    Ouvrez une fenêtre PowerShell en tant qu’administrateur.
2.    Accédez à **C:\Program Files\Microsoft AAD App Proxy Connector** et exécutez le script.  
`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Lorsque vous y êtes invité, entrez les informations d’identification de votre compte d’administrateur de client Azure AD.

## <a name="troubleshooting-pass-through-authentication"></a>Dépannage de l’authentification directe
Des problèmes de différents types peuvent survenir dans le cadre de l’authentification directe. Selon le type de problème, vous devrez peut-être étudier plusieurs possibilités.

Pour les erreurs relatives au connecteur, vous pouvez consulter le journal des événements de l’ordinateur connecteur sous **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**. Si nécessaire, vous pouvez accéder à des journaux plus détaillés en affichant les journaux d’analyse et de débogage, et en activant le journal de session du connecteur. Il est déconseillé de mener cette opération lorsque ce fichier journal est activé par défaut et que le contenu est uniquement visible une fois le journal désactivé.

Vous trouverez également des informations supplémentaires dans les journaux de suivi pour le connecteur sous **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Ces journaux incluent également la raison pour laquelle l’authentification directe a échoué pour un utilisateur donné, par exemple l’entrée ci-dessous qui comprend le code d’erreur 1328 :

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Vous pouvez obtenir les détails de l’erreur en démarrant une invite de commande et en exécutant la commande suivante (remplacez « 1328 » par le numéro d’erreur présent dans la requête) :

`Net helpmsg 1328`

Le résultat est semblable à la réponse suivante :

![Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

Si l’enregistrement d’audit est activé, des informations complémentaires se trouvent également dans les journaux de sécurité des contrôleurs de domaine. Une requête simple du connecteur pour les demandes d’authentification se présente comme suit :

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

Les autres erreurs signalées sur l’écran de connexion Azure AD sont détaillées ci-dessous, ainsi que les étapes de résolution appropriées.

|Erreur|Description|Résolution :
| --- | --- | ---
|AADSTS80001|Impossible de se connecter à Active Directory|Assurez-vous que les ordinateurs du connecteur sont joints au domaine et sont en mesure de se connecter à Active Directory.  
|AADSTS8002|Délai d’attente dépassé lors de la connexion à Active Directory|Vérifiez qu’Active Directory est disponible et répond aux demandes du connecteur.
|AADSTS80004|Le nom d’utilisateur envoyé au connecteur n’était pas valide|Vérifiez que l’utilisateur tente de se connecter avec le nom d’utilisateur correct.
|AADSTS80005|La validation a rencontré une WebException imprévisible|Cette erreur est un problème temporaire. relancez la requête. Si l’erreur se reproduit, contactez le support Microsoft.
|AADSTS80007|Une erreur s’est produite lors de la communication avec Active Directory|Consultez les journaux du connecteur pour plus d’informations et vérifiez qu’Active Directory fonctionne comme prévu.

