---
title: "Valider le réseau virtuel Azure à utiliser avec Azure RemoteApp | Microsoft Docs"
description: "Découvrez comment vous assurer que votre réseau virtuel Azure est prêt à être utilisé avec Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b573ba02-4587-4be5-9821-27bd891a73b2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 05c8a0ff04293947cec391b6467cc4adddb6a7b0
ms.lasthandoff: 03/31/2017


---
# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Validation du réseau virtuel Azure à utiliser avec Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp ne sera plus disponible à partir du 31 août 2017. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Avant d'utiliser un réseau virtuel Azure avec Azure RemoteApp, vous pouvez valider le réseau virtuel. Cette action permet d'éviter les problèmes de connectivité.

Pour valider votre réseau virtuel Azure, procédez comme suit :

1. Créez une machine virtuelle Azure dans le sous-réseau du réseau virtuel Azure que vous souhaitez utiliser avec Azure RemoteApp.
2. Connectez-vous à la machine virtuelle à l'aide de l’option **Connexion** sur le portail de gestion.
3. Associez la machine virtuelle au domaine que vous souhaitez utiliser avec Azure RemoteApp. Si vous créez une collection hybride qui se connecte à votre réseau local, associez la machine virtuelle à votre domaine local.

Si l'opération réussit, le réseau virtuel Azure est prêt à être utilisé avec RemoteApp.

Pour plus d'informations sur le flux de travail de bout en bout des collections hybrides, consultez les articles suivants :

* [Comment planifier votre réseau virtuel pour Azure RemoteApp](remoteapp-planvnet.md)
* [Création d'une collection hybride](remoteapp-create-hybrid-deployment.md)
* [Déployer une collection Azure RemoteApp sur votre Azure Virtual Network (avec prise en charge d’ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)


