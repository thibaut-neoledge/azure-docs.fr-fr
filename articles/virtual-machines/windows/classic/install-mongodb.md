---
title: Installation de MongoDB sur une machine virtuelle Windows dans Azure | Microsoft Docs
description: "Apprenez à installer MongoDB sur une machine virtuelle Azure créée avec le modèle de déploiement classique exécutant Windows Server."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4095df41-bb69-4bbe-9c1c-70923b0d84ba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
ms.openlocfilehash: d70194f30b7866cab38cfbbe1ea06664b0ddc1f5
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2017
---
# <a name="install-mongodb-on-a-windows-vm-in-azure"></a>Installation de MongoDB sur une machine virtuelle Windows dans Azure
> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md).  Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour installer et configurer MongoDB à l’aide du modèle de déploiement Resource Manager, consultez [cet article](../install-mongodb.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[MongoDB][MongoDB] est une base de données NoSQL open-source qui offre des performances élevées. Cet article vous guide dans la création d’une machine virtuelle Windows Server à l’aide du [portail Azure][AzurePortal]. Vous devez ensuite créer et attacher un disque de données à la machine virtuelle avant de procéder à l’installation et à la configuration de MongoDB. Si vous souhaitez utiliser une machine virtuelle existante dans Azure, vous pouvez passer directement à l’étape [d’installation et de configuration de MongoDB](#install-and-run-mongodb-on-the-virtual-machine).

## <a name="create-a-virtual-machine-running-windows-server"></a>Création d’une machine virtuelle exécutant Windows Server
Pour créer une machine virtuelle, procédez comme suit :

[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> Vous pouvez ajouter un point de terminaison pour MongoDB lors de la création de la machine virtuelle et le configurer comme suit : attribuez-lui le nom **Mongo**, utilisez **TCP** comme protocole et attribuez aux ports publics et privés la valeur **27017**.
>
>

## <a name="attach-a-data-disk"></a>Association d’un disque de données
Pour fournir un stockage pour la machine virtuelle, associez un disque de données, puis initialisez-le de façon à ce qu'il puisse être utilisé par Windows. Si vous disposez déjà d’un disque de données, vous pouvez attacher ce disque existant. Sinon, vous pouvez attacher un disque vide.

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-windows-linux.md)]

Pour obtenir des instructions sur l’initialisation du disque, consultez la page « Initialisation d’un nouveau disque de données dans Windows Server », sous [Attachement d’un disque de données à une machine virtuelle Windows](attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Installation et exécution de MongoDB sur la machine virtuelle
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Résumé
Ce didacticiel vous a montré comment créer une machine virtuelle exécutant Windows Server, vous y connecter à distance et y associer un disque de données.  Vous avez également appris à installer et à configurer MongoDB sur la machine virtuelle Windows. Vous pouvez maintenant accéder à MongoDB sur la machine virtuelle Windows en suivant les rubriques avancées dans la [Documentation MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: https://portal.azure.com/

