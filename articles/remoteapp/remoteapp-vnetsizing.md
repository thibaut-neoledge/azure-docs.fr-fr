---
title: "Informations sur la taille des réseaux virtuels dans Azure RemoteApp | Microsoft Docs"
description: "Découvrez le nombre d’adresses IP requises pour l’exécution d&quot;Azure RemoteApp avec un réseau virtuel"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b6e1c4ba-0236-42b2-bced-69353bf211be
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9375981db64ec4a1ae523e958423b5f5787cec33
ms.lasthandoff: 03/31/2017


---
# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Informations sur la taille des réseaux virtuels dans Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp ne sera plus disponible à partir du 31 août 2017. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Lorsque vous utilisez Azure RemoteApp avec un réseau virtuel (VNET), RemoteApp utilise des adresses IP au sein du sous-réseau. En fonction de l’échelle de votre service RemoteApp, vous devez vous assurer que votre sous-réseau comprend suffisamment d’adresses IP pour les machines virtuelles RemoteApp. Bien que ces indications sur la taille ne soient pas parfaites, compte tenu de la manière dont RemoteApp monte ou descend dynamiquement les machines virtuelles au sein d’une collection, celles-ci vous aideront à évaluer votre plage de sous-réseau. Ceci est particulièrement important car, une fois qu’un service RemoteApp est placé dans un réseau virtuel, vous ne pouvez plus augmenter la taille du sous-réseau sans supprimer RemoteApp.

Pour chaque collection RemoteApp que vous souhaitez exécuter à capacité maximale, vous devez disposer de 100 adresses IP disponibles. Par exemple, si vous possédez une collection RemoteApp dans le plan Standard et que vous souhaitez atteindre le nombre maximal de 500 utilisateurs, vous devez disposer de 100 adresses IP pour cette collection. De même, vous devez disposer de 100 adresses IP pour une collection RemoteApp dans le plan de base, qui comprend 800 utilisateurs. Si vous pensez que le nombre d’utilisateurs sera inférieur au nombre maximal, vous pouvez réduire le nombre d’adresses IP requises par collection. La taille minimale requise du sous-réseau est de 30 adresses IP (/ 27).

Consultez les informations suivantes pour vérifier que votre réseau virtuel est configuré et fonctionne correctement :

* [Migration à partir d'un réseau virtuel personnel vers un réseau virtuel Azure](remoteapp-migratevnet.md)
* [Validation du réseau virtuel Azure à utiliser avec Azure RemoteApp](remoteapp-vnet.md)


