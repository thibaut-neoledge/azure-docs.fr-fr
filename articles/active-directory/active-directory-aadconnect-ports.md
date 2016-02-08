<properties
	pageTitle="Azure AD Connect : Ports | Microsoft Azure"
	description="Cette page contient des informations de référence techniques sur les ports qui doivent être ouverts pour Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="billmath"/>

# Ports et protocoles nécessaires à l’identité hybride

Le document suivant contient des informations de référence techniques destinées à vous renseigner sur les ports et les protocoles nécessaires à l’implémentation d’une solution d’identité hybride. Consultez l’illustration suivante et reportez-vous au tableau correspondant.

![Qu’est-ce qu’Azure AD Connect ?](./media/active-directory-aadconnect-ports/required1.png)


## Tableau 1 : Azure AD Connect et AD local
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre le serveur Azure AD Connect et l’AD local.

| Protocole |Ports |Description
| --------- | --------- |--------- |
| DNS|53 (TCP/UDP)| Recherches DNS dans la forêt de destination.
|Kerberos|88 (TCP/UDP)| Authentification Kerberos auprès de la forêt AD.
|MS-RPC |135 (TCP/UDP)| Utilisé pendant la configuration initiale de l’Assistant Azure AD au moment où d’établir une liaison avec la forêt AD.
|LDAP|389 (TCP/UDP)|Utilisé pour l’importation de données à partir d’AD. Les données sont chiffrées à l’aide de Kerberos Sign & Seal.
|LDAP/SSL|636 (TCP/UDP)|Utilisé pour l’importation de données à partir d’AD. Le transfert de données est signé et chiffré. Utilisé uniquement si vous utilisez SSL.
|RPC |1024-65353 (port RPC aléatoire élevé)(TCP/UDP)|Utilisé pendant la configuration initiale d’Azure AD au moment d’établir une liaison avec les forêts AD.

## Tableau 2 : Azure AD Connect et Azure AD
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre le serveur Azure AD Connect et Azure AD.

| Protocole |Ports |Description
| --------- | --------- |--------- |
| HTTP|80 (TCP/UDP)|Utilisé pour télécharger des listes de révocation de certificats en vue de vérifier les certificats SSL.
|HTTPS|443(TCP/UDP)|Utilisé pour établir une synchronisation avec Azure AD.

Pour obtenir la liste des ports et des adresses IP Office 365, consultez [URL et plages d’adresses IP Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

## Tableau 3 - Azure AD Connect et serveurs de fédération/WAP
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre le serveur Azure AD Connect et les serveurs de fédération/WAP. Bientôt disponible !

## Tableau 4 - Serveurs de fédération et WAP
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre les serveurs de fédération et les serveurs WAP.

| Protocole |Ports |Description
| --------- | --------- |--------- |
|HTTPS|443(TCP/UDP)|Utilisé pour l’authentification.

## Tableau 5 - WAP et utilisateurs
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre les utilisateurs et les serveurs WAP.

| Protocole |Ports |Description
| --------- | --------- |--------- |
|HTTPS|443(TCP/UDP)|Utilisé pour l’authentification des appareils.
|TCP|49443 (TCP)|Utilisé pour l’authentification par certificat.


## Tableau 6a & 6b - Agent Azure AD Connect Health pour (AD FS/Sync) et Azure AD
Les tableaux suivants décrivent les points de terminaison, les ports et les protocoles nécessaires à la communication entre les agents Azure AD Connect Health et Azure AD.

### Tableau 6a - Ports et protocoles pour l’agent Azure AD Connect Health pour (AD FS/Sync) et Azure AD
Ce tableau décrit les ports et les protocoles nécessaires à la communication entre les agents Azure AD Connect Health et Azure AD.

| Protocole |Ports |Description
| --------- | --------- |--------- |
| HTTP|80 (TCP/UDP)|
|HTTPS|443(TCP/UDP)|
|Azure Service Bus|5671 (TCP/UDP)|

### 6b - Points de terminaison pour l’agent Azure AD Connect Health pour (AD FS/Sync) et Azure AD
Pour obtenir la liste des points de terminaison, consultez la [section Configuration requise pour l’agent Azure AD Connect Health](active-directory-aadconnect-health.md#requirements).

<!---HONumber=AcomDC_0128_2016-->