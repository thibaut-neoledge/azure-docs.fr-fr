<properties
	pageTitle="Installation de MySQL sur une machine virtuelle OpenSUSE | Microsoft Azure"
	description="Découvrez comment installer MySQL sur une machine virtuelle Linux OpenSUSE dans Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="cynthn"/>

# Installation de MySQL sur une machine virtuelle exécutant OpenSUSE Linux dans Azure

[MySQL][MySQL] est une base de données SQL open source connue. Ce didacticiel vous montre comment créer une machine virtuelle sous OpenSUSE Linux, puis installer MySQL.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


<br>


## Création d'une machine virtuelle exécutant OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Installation et exécution de MySQL sur la machine virtuelle

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Étapes suivantes
Pour plus d’informations sur MySQL, consultez la [Documentation MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

<!---HONumber=AcomDC_0727_2016-->