<properties
	pageTitle="Maintenance planifiée des machines virtuelles Linux | Microsoft Azure"
	description="Découvrez en quoi consiste la maintenance planifiée Azure et son incidence sur la disponibilité de vos machines virtuelles Linux s’exécutant dans Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="drewm"/>

# Maintenance planifiée des machines virtuelles Linux dans Azure

Découvrez en quoi consiste la maintenance planifiée Azure et son incidence possible sur la disponibilité de vos machines virtuelles Linux. Cet article est également disponible pour les [machines virtuelles Windows](virtual-machines-windows-planned-maintenance.md).

Cet article fournit des informations de base sur le processus de maintenance planifiée. Si vous souhaitez résoudre les problèmes qui ont entraîné le redémarrage de votre machine virtuelle, vous pouvez [lire ce billet de blog détaillant l’affichage des journaux de redémarrage de machine virtuelle](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Pourquoi Azure exécute une maintenance planifiée

Microsoft Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte qui supporte les machines virtuelles. Nombre de ces mises à jour sont exécutées sans impact sur les machines virtuelles ou les services cloud, y compris les mises à jour de préservation de la mémoire.

Cependant, certaines d’entre elles requièrent un redémarrage de vos machines virtuelles pour appliquer les mises à jour requises sur l’infrastructure. Les machines virtuelles sont éteintes lorsqu’on applique le correctif sur l’infrastructure, puis elles sont redémarrées.

Il existe deux sortes de maintenance qui peuvent avoir un impact sur la disponibilité de votre machine virtuelle : maintenance planifiée et non planifiée. Cette page décrit comment Microsoft Azure exécute une maintenance planifiée. Pour plus d'informations sur la maintenance non planifiée, consultez la page [Compréhension de la maintenance planifiée et non planifiée](virtual-machines-linux-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0525_2016-->