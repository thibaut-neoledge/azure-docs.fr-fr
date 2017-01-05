---
title: "De quel type de collection avez-vous besoin pour Azure RemoteApp ? | Microsoft Docs"
description: "Découvrez les différents types de collections disponibles avec Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: c13ec78d-07e9-4646-8194-cf3efafc1760
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7c526faeef2418cfbbca7948004691e127327020


---
# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>De quel type de collection avez-vous besoin pour Azure RemoteApp ?
> [!IMPORTANT]
> Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Azure RemoteApp vous permet de partager des applications et des ressources avec des utilisateurs sur n’importe quel appareil. Vous devez pour cela créer des collections pour stocker les applications et les ressources, puis partager ces collections avec les utilisateurs. Il existe deux options de collection, avec différentes options de réseau et d’authentification. Laquelle vous convient ?

Examinons les différentes considérations et les choix que vous devez effectuer pour tirer le meilleur parti de votre collection Azure RemoteApp. 

## <a name="quick-differences-between-the-collection-types"></a>Aperçu des différences entre les types de collections
|  | Cloud | Hybride |
| --- | --- | --- |
| Utiliser un réseau virtuel existant |OUI |OUI |
| Nécessite des comptes connectés à AD (DirSync) |Non |OUI |
| Nécessite la jonction au domaine |Non |OUI |
| Nécessite un contrôleur de domaine accessible au réseau virtuel |Non |OUI |

## <a name="cloud-collections"></a>Collections cloud
* Création rapide : la collection est approvisionnée rapidement, ce qui signifie que les utilisateurs obtiennent vos applications plus rapidement.
* Apportez vos propres applications ou partagez les nôtres. Vous pouvez utiliser une image personnalisée (générée à partir d’une machine virtuelle Azure) ou l’une des images incluses dans votre abonnement.
* Vous n’avez pas besoin de configurer de connexion entre votre collection et votre domaine local.
* Vous pouvez toutefois utiliser votre propre réseau virtuel Azure pour fournir l’accès à votre environnement local pour le partage de données ou pour utiliser l’authentification non-Windows pour des ressources telles que SQL Server (à l’aide de l’authentification de base de données).

Comment créer une collection ?

* Cloud uniquement ? Créez une collection avec l’option **Création rapide** dans le portail.
* Cloud + réseau virtuel ? Créez une collection avec l’option **Créer avec un réseau virtuel** , mais ne choisissez PAS de rejoindre un domaine.

## <a name="hybrid-collections"></a>Collections hybrides
* Fournissent un accès complet au réseau local + réseau virtuel Azure.
* Offrent un accès avec jonction au domaine pour les applications et les données. Les applications distantes peuvent s’authentifier auprès de votre annuaire Active Directory local. Elles peuvent ensuite accéder aux ressources dans votre domaine.
* Permettent d’effectuer une surveillance et une gestion avancées avec des solutions System Center et des stratégies de groupe Windows existantes (via une image personnalisée basée sur Windows Server 2012 R2).
* Prennent en charge [ExpressRoute](https://azure.microsoft.com/services/expressroute/) pour connecter votre réseau virtuel Azure à votre réseau local virtuel.

Créez une collection avec l’option **Créer avec un réseau virtuel** et choisissez de rejoindre un domaine.

## <a name="authentication-options"></a>Options d’authentification
Azure RemoteApp prend en charge les comptes Microsoft et Azure Active Directory, mais les collections ne prennent pas toutes en charge toutes les méthodes. 

| Type de compte |  | Cloud | Cloud + réseau virtuel | Hybride |
| --- | --- | --- | --- | --- |
| Compte Microsoft | |OUI |OUI |Non |
| Azure Active Directory (Azure AD) | | | | |
| Azure AD uniquement |OUI |OUI |Non | |
| AD Connect avec synchronisation de mot de passe |OUI |OUI |OUI | |
| AD Connect sans synchronisation de mot de passe |OUI |OUI |Non | |
| AD Connect avec les services AD FS |OUI |OUI |OUI | |
| Fournisseurs d’identités tiers pris en charge par Azure (par exemple Ping) |OUI |OUI |OUI | |
| Azure Multi-Factor Authentication | |OUI |OUI |OUI |

### <a name="cloud-and-cloud--vnet"></a>Cloud et Cloud + réseau virtuel
Avec les collections cloud, vous pouvez utiliser des comptes Microsoft, des comptes Azure AD ou une combinaison des deux. Utilisez les comptes qui conviennent le mieux à vos utilisateurs.

Il n’existe aucune exigence particulière quant à l’utilisation de comptes Microsoft. 

Si vous souhaitez utiliser des comptes Azure AD, vous devez vous assurer que votre locataire Azure AD correspond à celui associé à votre abonnement. Quand vous avez créé votre abonnement Azure RemoteApp, le locataire Azure AD que vous utilisiez a été associé automatiquement à votre abonnement. Tout utilisateur Azure AD auquel vous affectez une autorisation doit être ce même locataire. Si nécessaire, vous pouvez [modifier le locataire Azure AD](remoteapp-changetenant.md) associé à votre abonnement.

### <a name="hybrid-or-cloud--azure-ad--ad"></a>Hybride (ou cloud + Azure AD + AD)
L’utilisation d’Azure AD + Active Directory local est une condition requise pour une collection hybride. Vous devez utiliser AD Connect pour intégrer les deux annuaires, mais vous avez le choix en ce qui concerne la configuration d’AD Connect. 

Il existe deux scénarios AD Connect : utilisation de la synchronisation de mot de passe ou utilisation de la fédération Active Directory. Consultez les [informations relatives à AD Connect](../active-directory/active-directory-aadconnect.md) pour déterminer l’option la mieux adaptée à votre cas.

Vous pouvez également utiliser Azure AD + AD avec une collection cloud. Assurez-vous de suivre les mêmes étapes de configuration.

Pour connaître les étapes nécessaires à la configuration d’Azure AD et d’Active Directory, consultez [Configuration d’Active Directory pour Azure RemoteApp](remoteapp-ad.md) .

## <a name="go-create-your-collection"></a>Passez à l’action !
Il ne vous reste plus qu’une chose à faire : créer votre première collection Azure RemoteApp.

[Créez une collection cloud](remoteapp-create-cloud-deployment.md) ou [créez une collection hybride](remoteapp-create-hybrid-deployment.md).




<!--HONumber=Nov16_HO3-->


