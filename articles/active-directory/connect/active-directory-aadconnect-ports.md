---
title: "Azure AD Connect : Ports | Microsoft Docs"
description: "Cette page contient des informations de référence techniques sur les ports qui doivent être ouverts pour Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: 954c3d138b0fde54c1866135a20f0fc5ff05b85d


---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Ports et protocoles nécessaires à l’identité hybride
Le document suivant est une référence technique sur les ports et les protocoles nécessaires à l’implémentation d’une solution d’identité hybride. Consultez la figure ci-dessous et reportez-vous au tableau correspondant.

![Qu’est-ce qu’Azure AD Connect ?](./media/active-directory-aadconnect-ports/required2.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tableau 1 : Azure AD Connect et AD local
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre le serveur Azure AD Connect et l’AD local.

| Protocole | Ports | Description |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Recherches DNS dans la forêt de destination. |
| Kerberos |88 (TCP/UDP) |Authentification Kerberos auprès de la forêt AD. |
| MS-RPC |135 (TCP/UDP) |Utilisé pendant la configuration initiale de l’Assistant Azure AD au moment où d’établir une liaison avec la forêt AD. |
| LDAP |389 (TCP/UDP) |Utilisé pour l’importation de données à partir d’AD. Les données sont chiffrées à l’aide de Kerberos Sign & Seal. |
| LDAP/SSL |636 (TCP/UDP) |Utilisé pour l’importation de données à partir d’AD. Le transfert de données est signé et chiffré. Utilisé uniquement si vous utilisez SSL. |
| RPC |49152- 65535 (port RPC aléatoire élevé)(TCP/UDP) |Utilisé pendant la configuration initiale d’Azure AD au moment d’établir une liaison avec les forêts AD. Pour plus d’informations, consultez les sections [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) et [KB224196](https://support.microsoft.com/kb/224196). |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tableau 2 : Azure AD Connect et Azure AD
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre le serveur Azure AD Connect et Azure AD.

| Protocole | Ports | Description |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Utilisé pour télécharger des listes de révocation de certificats en vue de vérifier les certificats SSL. |
| HTTPS |443(TCP/UDP) |Utilisé pour établir une synchronisation avec Azure AD. |

Pour obtenir la liste des URL et adresses IP que vous devez ouvrir dans votre pare-feu, consultez [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tableau 3 - Azure AD Connect et serveurs de fédération AD FS/WAP
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre le serveur Azure AD Connect et les serveurs de fédération AD FS/WAP.  

| Protocole | Ports | Description |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Utilisé pour télécharger des listes de révocation de certificats en vue de vérifier les certificats SSL. |
| HTTPS |443(TCP/UDP) |Utilisé pour établir une synchronisation avec Azure AD. |
| WinRM |5985 |Écouteur WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tableau 4 - Serveurs de fédération et WAP
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre les serveurs de fédération et les serveurs WAP.

| Protocole | Ports | Description |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Utilisé pour l’authentification. |

## <a name="table-5---wap-and-users"></a>Tableau 5 - WAP et utilisateurs
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre les utilisateurs et les serveurs WAP.

| Protocole | Ports | Description |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Utilisé pour l’authentification des appareils. |
| TCP |49443 (TCP) |Utilisé pour l’authentification par certificat. |

## <a name="table-6---pass-through-authentication"></a>Tableau 6 - Authentification directe
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre le connecteur et Azure AD.

|Protocole|Numéro de port|Description
| --- | --- | ---
|HTTP|80|Active le trafic HTTP sortant pour valider la sécurité, comme SSL.
|HTTPS|443| Active l’authentification utilisateur auprès d’Azure AD
|HTTPS|10100 à 10120| Active les réponses à partir du connecteur vers Azure AD 
|Azure Service Bus|9352, 5671|  Active la communication depuis le connecteur vers le service Azure pour les requêtes entrantes.
|HTTPS|9350|    Facultatif ; pour de meilleures performances pour les requêtes entrantes
|HTTPS|8080/443|    Active la séquence de démarrage du connecteur et la mise à jour automatique du connecteur.
|HTTPS|9090|    Active l’enregistrement du connecteur (obligatoire uniquement pour le processus d’inscription du connecteur).
|HTTPS|9091|    Active le renouvellement automatique de certificat de confiance du connecteur.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tableau 7a & 7b - Agent Azure AD Connect Health pour (AD FS/Sync) et Azure AD
Les tableaux suivants décrivent les points de terminaison, les ports et les protocoles nécessaires à la communication entre les agents Azure AD Connect Health et Azure AD.

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tableau 7a - Ports et protocoles pour l’agent Azure AD Connect Health pour (AD FS/Sync) et Azure AD
Ce tableau décrit les ports et les protocoles sortants nécessaires à la communication entre les agents Azure AD Connect Health et Azure AD.  

| Protocole | Ports | Description |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Règle de trafic sortant |
| Azure Service Bus |5671 (TCP/UDP) |Règle de trafic sortant |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - Points de terminaison pour l’agent Azure AD Connect Health pour (AD FS/Sync) et Azure AD
Pour obtenir la liste des points de terminaison, consultez la section [Configuration requise pour l’agent Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements).




<!--HONumber=Dec16_HO3-->


