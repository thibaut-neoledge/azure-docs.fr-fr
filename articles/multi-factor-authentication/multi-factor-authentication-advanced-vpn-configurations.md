---
title: "Scénarios avancés avec Azure multifacteur et des VPN tiers"
description: "Guides de configuration détaillés pour Azure MFA à intégrer à Cisco, Citrix et Juniper."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 1f94a214-d6f6-48a8-8a12-006b5896ae45
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 6c8420a154d998aa95c0220049ee54b3039a872b
ms.openlocfilehash: 1724d9d482597cd8491ad1ea86f48beefbfa1531

---

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Scénarios avancés avec l'authentification multifacteur Azure et des solutions VPN tierces
L'authentification multifacteur Azure permet la connexion de façon transparente avec différentes solutions VPN tierces. Cet article se concentre sur l'appareil Cisco® ASA VPN, l'appareil Citrix NetScaler SSL VPN et l'appareil Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN. Nous avons créé des guides de configuration pour ces trois appliances courantes, mais le serveur Azure Multi-Factor Authentication peut s'intégrer à la plupart des systèmes qui utilisent RADIUS, LDAP, IIS ou l’authentification basée sur les revendications pour AD FS. Pour plus de détails, consultez la rubrique [Configurations de serveur MFA](multi-factor-authentication-get-started-server.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Appareil Cisco ASA VPN et authentification multifacteur Azure
L'authentification multifacteur Azure s'intègre parfaitement avec votre appareil Cisco® ASA VPN pour fournir une sécurité supplémentaire pour les connexions et l'accès au portail VPN Cisco AnyConnect®.  Cette opération peut être effectuée à l'aide du protocole RADIUS ou LDAP.  Sélectionnez l'une des opérations suivantes pour télécharger les guides de configuration détaillés.

| Guide de configuration | Description |
| --- | --- |
| [Cisco ASA avec Anyconnect VPN et Azure MFA Configuration pour LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) |Intégrez votre appareil Cisco ASA VPN de façon transparente avec l'authentification multifacteur Azure à l'aide de LDAP |
| [Cisco ASA avec Anyconnect VPN et Azure MFA Configuration pour RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) |Intégrez votre appareil Cisco ASA VPN de façon transparente avec l'authentification multifacteur Azure à l'aide de RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN et authentification multifacteur Azure
L'authentification multifacteur Azure s'intègre parfaitement avec votre appareil Citrix NetScaler SSL VPN pour fournir une sécurité supplémentaire pour les connexions et l'accès au portail Citrix NetScaler SSL VPN.  Cette opération peut être effectuée à l'aide du protocole RADIUS ou LDAP.  Sélectionnez l'une des opérations suivantes pour télécharger les guides de configuration détaillés.

| Guide de configuration | Description |
| --- | --- |
| [Citrix NetScaler SSL VPN et Azure MFA Configuration pour LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) |Intégrez votre Citrix NetScaler SSL VPN de façon transparente avec l'authentification multifacteur Azure à l'aide de LDAP |
| [Citrix NetScaler SSL VPN et Azure MFA Configuration pour RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) |Intégrez votre appareil Citrix NetScaler SSL VPN de façon transparente avec l'authentification multifacteur Azure à l'aide de RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Appareil Juniper/Pulse Secure SSL VPN et authentification multifacteur Azure
L'authentification multifacteur Azure s'intègre parfaitement avec votre appareil Juniper/Pulse Secure SSL VPN pour fournir une sécurité supplémentaire pour les connexions et l'accès au portail Juniper/Pulse Secure SSL VPN.  Cette opération peut être effectuée à l'aide du protocole RADIUS ou LDAP.  Sélectionnez l'une des opérations suivantes pour télécharger les guides de configuration détaillés.

| Guide de configuration | Description |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN et Azure MFA Configuration pour LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) |Intégrez votre appareil Juniper/Pulse Secure SSL VPN de façon transparente avec l'authentification multifacteur Azure à l'aide de LDAP |
| [Juniper/Pulse Secure SSL VPN et Azure MFA Configuration pour RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) |Intégrez votre appareil Juniper/Pulse Secure SSL VPN de façon transparente avec l'authentification multifacteur Azure à l'aide de RADIUS |



<!--HONumber=Jan17_HO1-->


