---
title: Utilisation d&quot;applications App-V avec Azure RemoteApp | Microsoft Docs
description: "Apprenez à utiliser des applications App-V dans Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e2292cb2-5c89-4b2b-ab11-74dbacd07c31
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 0e82c639ea7bfceb89f0cf9c59ab657a951f2d5f
ms.lasthandoff: 03/31/2017


---
# <a name="using-app-v-apps-in-azure-remoteapp"></a>Utilisation d'applications App-V dans Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp ne sera plus disponible à partir du 31 août 2017. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Vous pouvez utiliser des applications App-V dans une collection hybride Azure RemoteApp, ce qui nécessite la jonction de domaine.

Avant de commencer, assurez-vous d'installer le client App-V 5.1 avec les dernières mises à jour. Vous devrez créer une [image personnalisée](remoteapp-create-custom-image.md) qui inclut le client App-V.  

Il est facile d'utiliser votre infrastructure App-V avec Azure RemoteApp. Dans la mesure où une collection hybride est déployée dans un réseau virtuel (VNET) Azure qui a accès à votre contrôleur de domaine et les machines virtuelles sont jointes au domaine, vous pouvez exploiter vos méthodes de déploiement et votre infrastructure App-v existantes pour héberger facilement l'application App-V dans Azure RemoteApp. Voici quelques considérations à prendre en compte en fonction du type de déploiement App-V dont vous disposez actuellement :

| Options de configuration |  | Positive | Negative |
| --- | --- | --- | --- |
| Méthode de remise |Diffusion en continu (à la demande) |L'application est toujours à jour |Première latence |
| Montée |Plus rapide ; l'application est déjà présente sur la machine virtuelle |Encombrement - occupe de l'espace image (limite 127 Go) | |
| Stockage de l'emplacement d'application |Contenu partagé |L'application est exécutée dans la mémoire de l'instance Azure RemoteApp |Consomme de la mémoire et une bonne connexion au serveur de diffusion en continu (fichier) où l'application réside |
| Disque (en cache) |Exécution rapide. L'application ne dépend pas de la disponibilité du contenu source |Encombrement - occupe de l'espace image (limite 127 Go) | |
| Ciblage |Utilisateur |Nécessite une infrastructure App-V entièrement autonome | |
| Global (machine) |Pré-publication ou cible avec le serveur de publication |Besoin de mettre à jour votre image Azure si vous souhaitez mettre à jour de l'application (volumineuse). Occupe de l'espace sur l'image. | |

 Après avoir créé votre image personnalisée et votre collection hybride, publiez votre application, affectez des utilisateurs et profitez de vos applications App-V existantes hébergées dans Azure RemoteApp remis à n'importe quel appareil n'importe où.


