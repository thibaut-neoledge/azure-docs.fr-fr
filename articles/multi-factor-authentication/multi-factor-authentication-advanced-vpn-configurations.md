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
ms.date: 05/13/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: f68cf75cb8261ddac3fd56a8718f900bedb51c2b
ms.openlocfilehash: 7e0f7d8fefad539960436a58a254087a4ebd2f78
ms.contentlocale: fr-fr
ms.lasthandoff: 02/15/2017

---

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Scénarios avancés avec l'authentification multifacteur Azure et des solutions VPN tierces
L'authentification multifacteur Azure permet la connexion de façon transparente avec différentes solutions VPN tierces. Cet article se concentre sur l'appareil Cisco® ASA VPN, l'appareil Citrix NetScaler SSL VPN et l'appareil Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN. Nous avons créé des guides de configuration qui portent sur ces trois appliances courantes, mais le serveur Multi-Factor Authentication peut s’intégrer à la plupart des systèmes qui utilisent RADIUS, LDAP, IIS ou l’authentification basée sur les revendications à AD FS. Pour plus de détails, consultez la rubrique [Configurations de serveur MFA](multi-factor-authentication-get-started-server.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Appareil Cisco ASA VPN et authentification multifacteur Azure
Azure Multi-Factor Authentication s’intègre à votre appliance VPN Cisco® ASA et fournit une couche de sécurité supplémentaire pour l’accès au portail et les connexions VPN Cisco AnyConnect®.  Cette opération peut être effectuée à l'aide du protocole RADIUS ou LDAP.  Sélectionnez l'une des opérations suivantes pour télécharger les guides de configuration détaillés.

| Guide de configuration | Description |
| --- | --- |
| [Cisco ASA avec Anyconnect VPN et Azure MFA Configuration pour LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Intégrer votre appliance VPN Cisco ASA avec Azure MFA à l’aide de LDAP |
| [Cisco ASA avec Anyconnect VPN et Azure MFA Configuration pour RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Intégrer votre appliance VPN Cisco ASA avec Azure MFA à l’aide de RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN et authentification multifacteur Azure
Azure Multi-Factor Authentication s’intègre à votre appliance VPN Citrix NetScaler SSL et fournit une couche de sécurité supplémentaire pour l’accès au portail et les connexions VPN Citrix NetScaler SSL.  Cette opération peut être effectuée à l'aide du protocole RADIUS ou LDAP.  Sélectionnez l'une des opérations suivantes pour télécharger les guides de configuration détaillés.

| Guide de configuration | Description |
| --- | --- |
| [Citrix NetScaler SSL VPN et Azure MFA Configuration pour LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Intégrer votre appliance VPN Citrix NetScaler SSL avec Azure MFA à l’aide de LDAP |
| [Citrix NetScaler SSL VPN et Azure MFA Configuration pour RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Intégrer votre appliance VPN Citrix NetScaler SSL avec Azure MFA à l’aide de RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Appareil Juniper/Pulse Secure SSL VPN et authentification multifacteur Azure
Azure Multi-Factor Authentication s’intègre à votre appliance VPN Juniper/Pulse Secure SSL et fournit une couche de sécurité supplémentaire pour l’accès au portail et les connexions VPN Juniper/Pulse Secure SSL.  Cette opération peut être effectuée à l'aide du protocole RADIUS ou LDAP.  Sélectionnez l'une des opérations suivantes pour télécharger les guides de configuration détaillés.

| Guide de configuration | Description |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN et Azure MFA Configuration pour LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Intégrer votre appliance VPN Juniper/Pulse Secure SSL avec Azure MFA à l’aide de LDAP |
| [Juniper/Pulse Secure SSL VPN et Azure MFA Configuration pour RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Intégrer votre appliance VPN Juniper/Pulse Secure SSL avec Azure MFA à l’aide de RADIUS |

## <a name="next-steps"></a>Étapes suivantes

- [Augmenter votre infrastructure d’authentification existante avec l’extension NPS pour Azure Multi-Factor Authentication](multi-factor-authentication-nps-extension.md)

- [Configurer les paramètres d’Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md)
