<properties
   pageTitle="Problèmes de redémarrage ou de redimensionnement de machines virtuelles | Microsoft Azure"
   description="Résoudre les problèmes de déploiement classiques liés au redémarrage ou au redimensionnement d’une machine virtuelle Linux existante dans Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="delhan"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines"
   ms.topic="support-article"
   ms.tgt_pltfrm="virtual-machines"
   ms.workload="required"
   ms.date="04/28/2016"
   ms.devlang="na"
   ms.author="delhan"/>

# Résoudre les problèmes de déploiement classiques liés au redémarrage ou au redimensionnement d’une machine virtuelle Linux existante dans Azure

> [AZURE.SELECTOR]
- [Classique](../articles/virtual-machines/virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)
- [Gestionnaire de ressources](../articles/virtual-machines/virtual-machines-linux-arm-restart-resize-error-troubleshooting.md)

Lorsque vous essayez de démarrer une machine virtuelle Azure arrêtée, ou de redimensionner une machine virtuelle Azure existante, l’erreur la plus fréquemment rencontrée est un échec d’allocation. Cela se produit lorsque le cluster ou la région n’a pas de ressources disponibles ou ne prend pas en charge la taille de machine virtuelle requise.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Collecte de journaux d’audit

Pour commencer la résolution des problèmes, collectez les journaux d’audit afin d’identifier l’erreur associée au problème.

Dans le portail Azure, cliquez sur **Parcourir** > **Machines virtuelles** > _votre machine virtuelle Linux_ > **Paramètres** > **Journaux d’audit**.

## Problème : Erreur lors du démarrage d’une machine virtuelle arrêtée

Vous essayez de démarrer une machine virtuelle arrêtée, mais obtenez un échec d’allocation.

### Cause :

La demande de démarrage de la machine virtuelle arrêtée doit être exécutée sur le cluster d’origine qui héberge le service Cloud. Toutefois, le cluster n’a pas d’espace disponible pour répondre à la demande.

### Résolution :

* Créez un service Cloud et associez-le à une région ou à un réseau virtuel basé sur une région, mais pas à un groupe d’affinités.

* Supprimez la machine virtuelle arrêtée.

* Recréez la machine virtuelle dans le nouveau service Cloud à l’aide des disques.

* Démarrez la machine virtuelle recréée.

Si vous obtenez une erreur lorsque vous tentez de créer un service Cloud, vous pouvez soit réessayer ultérieurement, soit modifier la région du service Cloud.

> [AZURE.IMPORTANT] Le nouveau service Cloud aura un nouveau nom et une nouvelle adresse IP virtuelle. Vous devrez donc modifier ces valeurs pour toutes les dépendances qui utilisent ces informations pour leur service Cloud existant.

## Problème : Erreur lors du redimensionnement d’une machine virtuelle existante

Vous essayez de redimensionner une machine virtuelle existante, mais obtenez un échec d’allocation.

### Cause :

La demande de redimensionnement de la machine virtuelle doit être exécutée sur le cluster d’origine qui héberge le service Cloud. Cependant, le cluster ne prend pas en charge la taille de machine virtuelle demandée.

### Résolution :

Réduisez la taille de la machine virtuelle demandée, puis relancez la demande de redimensionnement.

* Cliquez sur **Parcourir tout** > **Machines virtuelles (classiques)** > _votre machine virtuelle_ > **Paramètres** > **Taille**. Pour connaître la procédure détaillée, consultez [Redimensionner la machine virtuelle](https://msdn.microsoft.com/library/dn168976.aspx).

S’il n’est pas possible de réduire la taille de la machine virtuelle, procédez comme suit :

  * Créez un service Cloud, en vous assurant qu’il n’est pas lié à un groupe d’affinités et pas associé à un réseau virtuel lié à un groupe d’affinités.

  * Créez dans ce service une machine virtuelle plus volumineuse.
  
Vous pouvez consolider toutes vos machines virtuelles dans le même service Cloud. Si votre service Cloud existant est associé à un réseau virtuel basé sur une région, vous pouvez connecter le nouveau service cloud sur le réseau virtuel existant.

Si le service Cloud existant n’est pas associé à un réseau virtuel basé sur une région, vous devez supprimer les machines virtuelles du service Cloud existant, puis les recréer dans le nouveau service Cloud à partir de leurs disques. Cependant, il est important de se rappeler que le nouveau service Cloud aura un nouveau nom et une nouvelle adresse IP virtuelle. Vous devrez donc mettre à jour ces valeurs pour toutes les dépendances qui utilisent actuellement ces informations pour leur service Cloud existant.

<!---HONumber=AcomDC_0511_2016-->