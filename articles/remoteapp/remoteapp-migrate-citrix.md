---

title: "Migrer d’Azure RemoteApp vers Citrix XenApp Essentials | Microsoft Docs"
description: Migration depuis Azure RemoteApp vers Citrix XenApp Essentials
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 695a8165-3454-4855-8e21-f2eb2c61201b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: fcd96a466d1c0dad17d7012308281ef868463b19
ms.contentlocale: fr-fr
ms.lasthandoff: 06/01/2017


---

# <a name="migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Migrer d’Azure RemoteApp vers Citrix XenApp Essentials

Si vous utilisez Azure RemoteApp et que vous voulez migrer vers Citrix XenApp Essentials, il y a quelques prérequis que vous ne devez pas perdre de vue. Pour commencer, lisez le [guide de déploiement technique pas à pas de Citrix XenApp Essentials](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf), ainsi que la [bibliothèque technique en ligne](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html) de Citrix. 

## <a name="prerequisite-steps-for-migration"></a>Étapes préalable à la migration

1. Créez un réseau virtuel ou déterminez le réseau virtuel Azure Resource Manager sur lequel vous allez déployer Citrix XenApp Essentials. Azure RemoteApp utilise le portail Azure Classic ; Citrix XenApp Essentials prend uniquement en charge Azure Resource Manager.  
2. Vérifiez que le réseau virtuel sélectionné dispose d’un accès réseau au contrôleur de domaine, car Citrix prend uniquement en charge les déploiements hybrides. Si vous procédez à un déploiement cloud d’Azure RemoteApp, vérifiez que votre réseau virtuel dispose d’un accès réseau à un contrôleur de domaine Active Directory. Vous pouvez aussi utiliser Azure Active Directory Domain Services (Azure AD DS). 
3. Pour réussir la jonction de domaine à la première tentative, veillez à ce que le DNS soit correctement configuré pour le réseau virtuel. Vous pouvez créer une machine virtuelle dans le réseau virtuel sélectionné et effectuer une jonction de domaine manuelle pour vérifier que le DNS et la jonction de domaine fonctionnent comme prévu. Vous serez ainsi assuré de réussir votre premier déploiement Citrix XenApp Essentials. 
4. Si nécessaire, créez une homologation de réseau virtuel entre un réseau virtuel du portail Azure Classic que vous utilisez avec Azure RemoteApp et votre réseau virtuel Azure Resource Manager. Ce processus d’homologation fonctionne si les deux réseaux résident dans la même région. Si ce n’est pas le cas, utilisez un réseau privé virtuel de site à site pour connecter les réseaux virtuels pour une mise en réseau. 
5. Si besoin, consultez [Migration de données dans et hors d’Azure RemoteApp](remoteapp-migrate.md). 
6. Mettez à jour votre image Azure RemoteApp existante pour inclure le composant Citrix VDA (pour obtenir des instructions, consultez la documentation Citrix). 
7. Accédez à Place de marché Azure, puis lancez le déploiement de Citrix XenApp Essentials.

## <a name="other-considerations"></a>Autres points à considérer

Gardez à l’esprit ces autres considérations pendant la migration :
- Citrix XenApp Essentials prend uniquement en charge les déploiements hybrides. En d’autres termes, un accès réseau est nécessaire à un contrôleur de domaine afin d’effectuer la jonction de domaine. Si vous procédez à un déploiement cloud d’Azure RemoteApp, utilisez Azure AD DS ou vérifiez que votre réseau virtuel a accès à Active Directory pour la jonction de domaine. 
- Pour savoir comment déplacer des données utilisateur vers Citrix XenApp Essentials, consultez [Migration de données dans et hors d’Azure RemoteApp](remoteapp-migrate.md). 
- Citrix XenApp Essentials prend uniquement en charge les comptes Active Directory. Il ne prend pas en charge les comptes Microsoft (comme outlook.com, msn.com ou hotmail.com). 

## <a name="citrix-xenapp-essentials-billing"></a>Facturation de Citrix XenApp Essentials

Pour plus d’informations sur les tarifs, consultez la section [FAQ](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) et l’[article de présentation de Citrix](https://www.citrix.com/global-partners/microsoft/remote-app.html). La facturation de Citrix XenApp Essentials se décompose comme suit :

- Frais de service Citrix, soit 12 $ par utilisateur et par mois. Comme pour tous les achats effectués sur la Place de marché Azure, le mode de paiement associé à votre abonnement Azure est utilisé pour la facturation. Les clients titulaires d’un Contrat Entreprise (EA) ne peuvent pas avoir recours au crédit financier Azure. 
- Licences d’accès client (CAL) Remote Data Service (RDS). À l’heure actuelle, les frais d’accès distant sont intégrés au coût de Citrix XenApp Essentials, soit 6,25 $. Si vous êtes un client EA, vous pouvez vous en acquitter au moyen d’un crédit financier Azure. Si vous voulez utiliser vos licences d’accès client RDS, contactez-nous au [arainfo@microsoft.com](mailto:arainfo@microsoft.com) pour que nous puissions les prendre en compte dans la facturation. 
- Calcul et stockage Azure. Il s’agit du coût de stockage Azure et de la consommation de calcul pour les machines virtuelles utilisées. N’oubliez pas de prendre la tarification en considération lors de la sélection de la taille de votre machine virtuelle et de la densité des utilisateurs. Si vous êtes un client EA, vous pouvez vous en acquitter au moyen d’un crédit financier Azure.

Si vous vous posez encore des questions :
- Envoyez-nous un e-mail à l’adresse [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
- [Contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Commencez par [ouvrir un dossier de support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir de l’aide sur les étapes préalables 1 à 5. Pour les étapes 6 à 7, contactez Citrix en ouvrant un ticket de support sur le portail de gestion Citrix. 

