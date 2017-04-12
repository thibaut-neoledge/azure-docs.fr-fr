---
title: "Ne stockez jamais de données sensibles sur des images personnalisées pour Azure RemoteApp | Microsoft Docs"
description: "En savoir plus sur les recommandations de stockage des données dans des images personnalisées dans Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 5a19903b-15f9-49d9-9bc1-ae80f2671aa1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 75d5415d33324d957617426e75909a6c6c58b1f9
ms.lasthandoff: 03/31/2017


---
# <a name="never-store-sensitive-data-on-custom-images"></a>Ne stockez jamais de données sensibles sur des images personnalisées
> [!IMPORTANT]
> Azure RemoteApp ne sera plus disponible à partir du 31 août 2017. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Lorsque vous hébergez votre propre application dans Azure RemoteApp, la première étape consiste à créer une image personnalisée. Nous utilisons cette image personnalisée pour créer des instances de machines virtuelles qui servent vos applications à vos utilisateurs. L'image personnalisée doit contenir UNIQUEMENT des applications et jamais des données sensibles qui peuvent être perdues, notamment des bases de données SQL, des fichiers du personnel ou des fichiers de données spéciaux tels que les fichiers d'entreprise QuickBooks. Toutes les données sensibles doivent être externes à Azure RemoteApp, sur un serveur de fichiers, une autre machine virtuelle Azure ou SQL Azure. L'image doit simplement héberger l'application qui se connecte à la source de données et présente les données. Consultez [Configuration requise pour les images Azure RemoteApp](remoteapp-imagereqs.md) pour plus d’informations. 

Pour comprendre pourquoi vous ne devez pas stocker de données sensibles, vous devez comprendre le fonctionnement d'Azure RemoteApp. Lorsqu'une collection est créée ou mise à jour, dans les coulisses, plusieurs clones ou des copies de l'image sont créés. Toutes les instances de ces machines virtuelles sont des copies exactes de l'image personnalisée ; lorsque les utilisateurs lancent des applications, ils sont connectés à une de ces instances de machines virtuelles. Mais la même instance n'est pas garantie et importe peu car elles ne sont pas persistantes. Les instances de machines virtuelles hébergeant les applications ne sont pas persistantes et peuvent être détruites ou supprimées, par exemple au cours de la mise à jour de la collection. 

Une fois que la collection est approvisionnée et que les utilisateurs commencent à se connecter aux machines virtuelles, les données utilisateur sont persistantes et protégées, car elles sont enregistrées sur un stockage distinct au sein d’un disque dur virtuel que nous appelons [disque de profil utilisateur (UPD)](remoteapp-upd.md) et qui correspond au profil utilisateur dans c:\users\<userprofile. Lorsqu'une application démarre, l'UPD est monté et traité de la même façon qu’un profil utilisateur local par le système d'exploitation. En savoir plus sur la façon dont [Azure RemoteApp enregistre les données et paramètres utilisateur](remoteapp-upd.md).

Exemples de données qui ne doivent pas se trouver dans l'image :

* Données partagées accessibles aux utilisateurs
* Base de données SQL ou QuickBooks
* Toutes les données de D:\

Exemples de données qui peuvent résider dans le profil par défaut à copier dans chaque UPD d’utilisateur :

* Fichiers de configuration par utilisateur
* Scripts que les utilisateurs ont besoin de conserver dans leur UPD

Points clés :

* Ne stockez jamais de données sensibles qui peuvent être perdues sur l'image lors de la création d'une image personnalisée.
* Les données sensibles doivent toujours résider sur un serveur de fichiers distinct, sur une machine virtuelle Azure séparée, sur le cloud. Elles doivent toujours être externes aux instances de machines virtuelles qui hébergent vos applications au sein d’Azure RemoteApp. 
* Les données utilisateur sont enregistrées et conservées dans le disque de profil utilisateur (UPD)


