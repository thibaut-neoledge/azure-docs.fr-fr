---

title: Migration depuis Azure RemoteApp vers Citrix XenApp Essentials | Microsoft Docs
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
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: de47edc5ea4dc1b67bd69b74929acf249b0d43e6
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---

# <a name="how-to-migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Migration depuis Azure RemoteApp vers Citrix XenApp Essentials

Il existe quelques conditions préalables pour les clients Azure RemoteApp souhaitant migrer vers Citrix XenApp Essentials.  Nous vous recommandons de commencer par consulter le [guide de déploiement technique étape par étape pour Citrix XenApp Essentials](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf) de Citrix, ainsi que sa [bibliothèque technique en ligne](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html). 

Heureusement, vous pouvez réutiliser la plupart des investissements déjà réalisés dans Azure RemoteApp. Toutefois, quelques conditions préalables de haut niveau sont à remplir pour le déploiement de XenApp Essentials.

## <a name="prerequisites"></a>Composants requis

1. Créez un nouveau réseau virtuel, ou déterminez le réseau virtuel Azure d’Azure Resource Manager sur lequel vous allez déployer Citrix XenApp Essentials. Azure RemoteApp utilise Azure Classic ; Citrix XenApp Essentials prend uniquement en charge Azure Resource Manager.  
2. Vérifiez que le réseau virtuel sélectionné dispose d’un accès réseau au contrôleur de domaine, dans la mesure où Citrix prend uniquement en charge les déploiements hybrides. Si vous procédez à un déploiement cloud d’Azure RemoteApp, vous devez vous assurer que le réseau virtuel dispose d’un accès réseau à un contrôleur de domaine Active Directory. Nous vous recommandons également d’utiliser Azure Active Directly Domain Services (AAD-DS). 
3. Vérifiez que DNS est correctement configuré pour le réseau virtuel, afin que la jonction de domaine puisse réussir dès la première tentative. Vous pouvez créer une machine virtuelle dans le réseau virtuel sélectionné et effectuer une jonction de domaine manuelle pour vérifier que DNS et la jonction de domaine fonctionnent comme prévu. Cela garantit la réussite de votre premier déploiement de Citrix XenApp Essentials. 
4. Si nécessaire, créez une homologation de réseaux virtuels entre un réseau virtuel Azure Classic utilisé avec Azure RemoteApp et votre réseau virtuel Azure Resource Manager s’ils se trouvent dans la même région et ou utilisez S2S VPN s’ils ne sont pas en mesure de se connecter au réseau virtuel pour la mise en réseau. 
5. Si besoin, consultez [Migration de données dans et hors d’Azure RemoteApp](remoteapp-migrate.md). 
6. Mettez à jour votre image Azure RemoteApp existante afin d’inclure le composant Citrix VDA. Reportez-vous à la documentation Citrix relative aux instructions. 
7. Accédez à Place de marché Azure et commencez le déploiement de Citrix XenApp Essentials.

Bonne chance et merci d’utiliser Azure RemoteApp. 

## <a name="other-considerations"></a>Autres points à considérer :

- Citrix XenApp Essentials prend uniquement en charge les déploiements hybrides. En d’autres termes, un accès réseau est nécessaire à un contrôleur de domaine afin d’effectuer la jonction de domaine. Si vous procédez à un déploiement cloud d’Azure RemoteApp, vous devez utiliser AAD-DS ou vérifier que votre réseau virtuel a accès à Active Directory pour la jonction de domaine. 
- Pour savoir comment déplacer des données utilisateur vers CXE, consultez [Migration de données dans et hors d’Azure RemoteApp](remoteapp-migrate.md). 
- Citrix XenApp Essentials prend uniquement en charge les comptes Active Directory. Il ne prend pas en charge les comptes Microsoft (@outlook.com, @msn.com, @hotmail.com, etc..). 

## <a name="understanding-billing-for-citrix-xenapp-essentials"></a>Comprendre la facturation relative à Citrix XenApp Essentials 

Consultez la section [FAQ](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) et l’[article de présentation de Citrix](https://www.citrix.com/global-partners/microsoft/remote-app.html) pour plus d’informations sur la tarification. La facturation de Citrix XenApp Essentials se décompose comme suit :

1. Des frais de service Citrix mensuels de 12 $ par utilisateur. Comme tous les achats effectués sur la Place de marché Azure, la facturation s’applique au moyen de paiement associé à votre abonnement Azure. Pour les clients EA, il est impossible d’utiliser des crédits monétaires Azure. 
2. La licence d’accès client aux services Bureau à distance. Vous devez apporter votre propre licence d’accès client aux services Bureau à distance (à venir) ou payer les frais d’accès à distance associés au paiement de Citrix XenApp Essentials d’un montant de 6,25 euros. Pour les clients EA, il est possible d’utiliser des crédits monétaires Azure pour le règlement. Si vous souhaitez utiliser vos licences d’accès client aux services Bureau à distance existantes, contactez-nous [arainfo@microsoft.com] (mailto:arainfo@microsoft.com pour que nous puissions les appliquer à votre facture. 
3. Le calcul et le stockage Azure. Il s’agit du coût de stockage Azure et de la consommation de calcul de la machine virtuelle utilisée. N’oubliez pas de prendre la tarification en considération lors de la sélection de la taille de votre machine virtuelle et de la densité des utilisateurs. Pour les clients EA, il est possible d’utiliser des crédits monétaires Azure pour le règlement.

N’hésitez pas à nous contacter si vous avez des questions supplémentaires.
1. Envoyez-nous un e-mail à l’adresse [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2. [Contactez le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Commencez par [ouvrir un dossier de support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour faciliter les étapes 1 à 5 ci-dessus. Contactez Citrix en ouvrant un ticket de support sur le portail de gestion Citrix pour les étapes 6 à 7. 

