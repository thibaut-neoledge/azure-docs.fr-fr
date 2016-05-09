<properties
	pageTitle="Maintenance planifiée des machines virtuelles Windows | Microsoft Azure"
	description="Découvrez en quoi consiste la maintenance planifiée Azure et son incidence possible sur vos machines virtuelles Windows s’exécutant dans Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="drewm"/>

# Maintenance planifiée des machines virtuelles dans Azure


Découvrez en quoi consiste la maintenance planifiée Azure et son incidence possible sur la disponibilité de vos machines virtuelles Windows. Cet article est également disponible pour les [machines virtuelles Linux](virtual-machines-linux-planned-maintenance.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Pourquoi Azure exécute une maintenance planifiée

Microsoft Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte qui supporte les machines virtuelles. Nombre de ces mises à jour sont exécutées sans impact sur les machines virtuelles ou les services cloud, y compris les mises à jour de préservation de la mémoire.

Cependant, certaines d’entre elles requièrent un redémarrage de vos machines virtuelles pour appliquer les mises à jour requises sur l’infrastructure. Les machines virtuelles sont éteintes lorsqu’on applique le correctif sur l’infrastructure, puis elles sont redémarrées.

Il existe deux sortes de maintenance qui peuvent avoir un impact sur la disponibilité de votre machine virtuelle : maintenance planifiée et non planifiée. Cette page décrit comment Microsoft Azure exécute une maintenance planifiée. Pour plus d'informations sur la maintenance non planifiée, consultez la page [Compréhension de la maintenance planifiée et non planifiée](virtual-machines-windows-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0427_2016-->