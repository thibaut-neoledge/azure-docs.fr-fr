---
title: "Créer une identité professionnelle ou scolaire dans AAD pour Windows | Microsoft Docs"
description: "Découvrez comment créer une identité professionnelle ou scolaire dans Azure Active Directory à utiliser avec vos machines virtuelles Windows."
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: d07dca34-618a-48aa-9971-03d9c1210f4a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 7694b959a384aaed213adc31e02debca31b7c131
ms.lasthandoff: 04/03/2017


---
# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-windows-vms"></a>Création d’une identité professionnelle ou scolaire dans Azure Active Directory à utiliser avec des machines virtuelles Windows
Si vous avez créé un compte Azure personnel ou si vous disposez d’un abonnement MSDN personnel et avez créé le compte Azure pour profiter des crédits Azure MSDN, cela signifie que vous avez utilisé une identité de *compte Microsoft* pour le créer. Pour fonctionner correctement, de nombreuses fonctionnalités d'Azure, parmi lesquelles les [modèles de groupes de ressources](../../azure-resource-manager/resource-group-overview.md) , nécessitent un compte professionnel ou scolaire (une identité gérée par Azure Active Directory). Vous pouvez suivre les instructions ci-dessous pour créer un compte professionnel ou scolaire car, heureusement, l’un des atouts de votre compte personnel Azure est qu'il est fourni avec un domaine Azure Active Directory par défaut que vous pouvez utiliser pour créer un nouveau compte professionnel ou scolaire à utiliser avec les fonctionnalités Azure qui le nécessitent.

Toutefois, de récentes modifications permettent de gérer votre abonnement avec n’importe quel type de compte Azure grâce à la `azure login` méthode de connexion interactive décrite [ici](../../xplat-cli-connect.md). Vous pouvez utiliser ce mécanisme ou suivre les instructions ci-dessous. Vous pouvez également [créer une identité professionnelle ou scolaire dans Azure Active Directory à utiliser avec des machines virtuelles Linux](../linux/create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

[!INCLUDE [virtual-machines-common-create-aad-work-id](../../../includes/virtual-machines-common-create-aad-work-id.md)]


