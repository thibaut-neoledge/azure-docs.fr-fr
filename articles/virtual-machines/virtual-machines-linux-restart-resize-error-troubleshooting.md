<properties
   pageTitle="Problèmes de redémarrage ou de redimensionnement de machines virtuelles | Microsoft Azure"
   description="Résoudre les problèmes de déploiement Resource Manager liés au redémarrage ou au redimensionnement d’une machine virtuelle Linux existante dans Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# Résoudre les problèmes de déploiement Resource Manager liés au redémarrage ou au redimensionnement d’une machine virtuelle Linux existante dans Azure

Lorsque vous essayez de démarrer une machine virtuelle Azure arrêtée ou de redimensionner une machine virtuelle Azure existante, l’erreur la plus fréquemment rencontrée est un échec d’allocation. Cette erreur se produit lorsque le cluster ou la région n’ont pas de ressources disponibles ou ne prennent pas en charge la taille de machine virtuelle demandée.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Collecter des journaux d’audit

Pour résoudre les problèmes, commencez par collecter les journaux d’audit afin d’identifier l’erreur associée au problème. Les liens suivants contiennent des informations détaillées sur le processus :

[Résolution des problèmes liés aux déploiements de groupes de ressources avec le portail Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Opérations d’audit avec Resource Manager](../resource-group-audit.md)

## Problème : erreur lors du démarrage d’une machine virtuelle arrêtée

Vous essayez de démarrer une machine virtuelle arrêtée, mais obtenez un échec d’allocation.

### Cause :

La demande de démarrage de la machine virtuelle arrêtée doit être exécutée sur le cluster d’origine qui héberge le service cloud. Toutefois, le cluster n’a pas d’espace libre pour répondre à la demande.

### Résolution :

*	Arrêtez toutes les machines virtuelles du groupe à haute disponibilité, puis redémarrez chacune d’elles.

  1. Cliquez sur **Groupes de ressources** > _votre groupe de ressources_ > **Ressources** > _votre groupe à haute disponibilité_ > **Machines virtuelles** > _votre machine virtuelle_ > **Arrêter**.

  2. Après l’arrêt de toutes les machines virtuelles, sélectionnez chacune des machines arrêtées et cliquez sur Démarrer.

*	Relancez la demande de redémarrage ultérieurement.

## Problème : erreur lors du redimensionnement d’une machine virtuelle existante

Vous essayez de redimensionner une machine virtuelle existante, mais obtenez un échec d’allocation.

### Cause :

La demande de redimensionnement de la machine virtuelle doit être exécutée sur le cluster d’origine qui héberge le service cloud. Toutefois, le cluster ne prend pas en charge la taille de machine virtuelle demandée.

### Résolution :

* Relancez la demande en utilisant une plus petite taille de machine virtuelle.

* Si la taille de la machine virtuelle demandée n’est pas modifiable :

  1. Arrêtez toutes les machines virtuelles du groupe à haute disponibilité.

    * Cliquez sur **Groupes de ressources** > _votre groupe de ressources_ > **Ressources** > _votre groupe à haute disponibilité_ > **Machines virtuelles** > _votre machine virtuelle_ > **Arrêter**.

  2. Après l’arrêt de toutes les machines virtuelles, redimensionnez la machine virtuelle souhaitée à une taille supérieure.
  3. Sélectionnez la machine virtuelle redimensionnée, cliquez sur **Démarrer**, puis démarrez chacune des machines virtuelles arrêtées.

## Étapes suivantes

Si vous rencontrez des problèmes lorsque vous créez une machine virtuelle Linux dans Azure, consultez [Résoudre les problèmes de déploiement liés à la création d’une machine virtuelle Linux dans Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).

<!---HONumber=AcomDC_0914_2016-->