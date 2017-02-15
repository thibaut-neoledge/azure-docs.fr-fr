---
title: Installation de MySQL sur une machine virtuelle OpenSUSE | Microsoft Docs
description: "Découvrez comment installer MySQL sur une machine virtuelle Linux OpenSUSE dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bf51a9835448339b4fb4d35bd0c97097bf767ec1


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installation de MySQL sur une machine virtuelle exécutant OpenSUSE Linux dans Azure
[MySQL][MySQL] est une base de données SQL open source connue. Ce didacticiel vous montre comment créer une machine virtuelle sous OpenSUSE Linux, puis installer MySQL.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Création d'une machine virtuelle exécutant OpenSUSE Linux
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Installation et exécution de MySQL sur la machine virtuelle
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur MySQL, consultez la [Documentation MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com




<!--HONumber=Nov16_HO3-->


