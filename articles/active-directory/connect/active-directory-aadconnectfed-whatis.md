---
title: "Fédération avec Azure AD Connect | Microsoft Docs"
description: "Cette page centralise toute la documentation portant sur les opérations AD FS avec Azure AD Connect"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 39b0b15dba098c0ddc59e39a81bfb6809f8cb2eb
ms.lasthandoff: 03/07/2017


---
# <a name="azure-ad-connect-and-federation"></a>Fédération avec Azure AD Connect
Azure AD Connect vous permet de configurer la fédération avec AD FS et Azure AD au niveau local. Avec l’authentification de fédération, vous pouvez autoriser les utilisateurs à se connecter aux services Azure AD avec leurs mots de passe locaux sans avoir à les saisir de nouveau, et ce, alors qu’ils sont sur le réseau d’entreprise. L’option de fédération avec les services de fédération d’Azure Directory (AD FS) vous permet de déployer un nouveau service AD FS dans la batterie de serveurs Windows Server 2012 R2 ou d’en spécifier un existant.

Cette rubrique présente les fonctions associées à la fédération pour Azure AD Connect et répertorie les liens vers toutes les autres rubriques qui lui sont associées. Pour obtenir les liens vers Azure AD Connect, consultez Intégration de vos identités locales avec Azure Active Directory.

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect : rubriques sur la fédération
| Rubrique | Sujet qu’elle aborde et quand la consulter |
|:--- |:--- |
| **Options de connexion de l’utilisateur via Azure AD Connect** | |
| [Présentation des options de connexion de l’utilisateur](active-directory-aadconnect-user-signin.md) |Description des options de connexion de l’utilisateur et de leur impact sur l’expérience utilisateur de connexion à Azure |
| **Installation d’AD FS avec Azure AD Connect** | |
| [Conditions préalables](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Conditions préalables à l’installation d’AD FS via Azure AD Connect |
| [Configurer la batterie de serveurs AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Installation d’une nouvelle batterie de serveurs AD FS avec Azure AD Connect |
| **Modification de la configuration AD FS** | |
| [Réparation de l’approbation](active-directory-aadconnect-federation-management.md#repairthetrust) |Rétablissement de l’approbation actuelle entre AD FS et Office 365 / Azure au niveau local |
| [Ajout d’un nouveau serveur AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Extension de batterie de serveurs AD FS avec l’ajout d’un serveur AD FS après l’installation initiale |
| [Ajout d’un nouveau serveur WAP AD FS](active-directory-aadconnect-federation-management.md#addwapserver) |Extension de batterie de serveurs AD FS avec l’ajout d’un serveur WAP après l’installation initiale |
| [Ajouter un nouveau domaine fédéré](active-directory-aadconnect-federation-management.md#addfeddomain) |Ajout d’un domaine à fédérer avec Azure AD |
| [Mettre à jour le certificat SSL](active-directory-aadconnectfed-ssl-update.md)| Mettre à jour le certificat SSL pour une batterie de serveurs Active Directory Federation Services (AD FS)|
| **Tâches postérieures à l’installation** | |
| [Ajout du logo / de l’illustration personnalisé(e) de la société](active-directory-aadconnect-federation-management.md#customlogo) |Modifier l’expérience de connexion via la spécification du logo personnalisé qui apparaîtra sur la page de connexion AD FS |
| [Ajouter une description de connexion](active-directory-aadconnect-federation-management.md#addsignindescription) |Modification de la description de la connexion sur la page de connexion AD FS |
| [Modification des règles de revendication AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Modifier / ajouter des règles de revendication dans AD FS pour configurer la synchronisation Azure AD Connect |

## <a name="additional-resources"></a>Ressources supplémentaires
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
* [Déploiement des services AD FS dans Azure](active-directory-aadconnect-azure-adfs.md)
* [Déploiement des services AD FS haute disponibilité par-delà les frontières dans Azure avec Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

