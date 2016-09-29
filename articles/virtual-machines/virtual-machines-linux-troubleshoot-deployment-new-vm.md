<properties
   pageTitle="Résoudre les problèmes de déploiement de machines virtuelles Linux - Resource Manager| Microsoft Azure"
   description="Résoudre les problèmes de déploiement Resource Manager liés à la création d’une machine virtuelle Linux dans Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="jiangchen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# Résoudre les problèmes de déploiement Resource Manager liés à la création d’une machine virtuelle Linux dans Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Collecter des journaux d’audit

Pour résoudre les problèmes, commencez par collecter les journaux d’audit afin d’identifier l’erreur associée au problème. Les liens suivants contiennent des informations détaillées sur la marche à suivre.

[Résoudre les problèmes liés aux déploiements de groupes de ressources avec le portail Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Opérations d’audit avec Resource Manager](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**O :** si le système d’exploitation est de type Linux généralisé et s’il est chargé et/ou capturé avec le paramètre généralisé, il n’y aura aucune erreur. De même, si le système d’exploitation est de type Linux spécialisé et qu’il est chargé et/ou capturé avec le paramètre spécialisé, il n’y aura aucune erreur.

**Erreurs de chargement :**

**N<sup>1</sup> :** si le système d’exploitation est de type Linux généralisé et s’il est chargé avec le paramètre spécialisé, vous obtiendrez une erreur de délai d’attente d’approvisionnement, car la machine virtuelle est bloquée à l’étape d’approvisionnement.

**N<sup>2</sup> :** si le système d’exploitation est de type Linux spécialisé et s’il est chargé comme généralisé, vous obtiendrez une erreur d’échec d’approvisionnement, car la nouvelle machine virtuelle est exécutée avec le nom de l’ordinateur, le nom d’utilisateur et le mot de passe d’origine.

**Résolution :**

Pour corriger ces deux erreurs, chargez le disque dur virtuel d’origine, disponible en mode local, avec le même paramétrage que pour le système d’exploitation (généralisé/spécialisé). Pour effectuer un chargement de type généralisé, n’oubliez pas de commencer par exécuter -deprovision.

**Erreurs de capture :**

**N<sup>3</sup> :** si le système d’exploitation est de type Linux généralisé et s’il est capturé comme spécialisé, vous obtiendrez une erreur de délai d’attente d’approvisionnement, car la machine virtuelle d’origine n’est pas utilisable tant qu’elle est marquée comme généralisée.

**N<sup>4</sup> :** si le système d’exploitation est de type Linux spécialisé et s’il est capturé avec le paramètre généralisé, vous obtiendrez une erreur d’échec d’approvisionnement, car la nouvelle machine virtuelle est exécutée avec le nom de l’ordinateur, le nom d’utilisateur et le mot de passe d’origine. En outre, la machine virtuelle d’origine n’est pas utilisable tant qu’elle est marquée comme spécialisée.

**Résolution :**

Pour corriger ces deux erreurs, supprimez l’image actuelle du portail, et [effectuez une nouvelle capture à partir des disques durs virtuels en cours](virtual-machines-linux-capture-image.md), avec le même paramétrage que celui du système d’exploitation (généralisé/spécialisé).

## Problème : Image personnalisée / galerie / marketplace ; échec d’allocation
Cette erreur se produit lorsque la nouvelle demande de la machine virtuelle est épinglée à un cluster qui ne prend pas en charge la taille de machine virtuelle requise ou qui n’a pas d’espace libre suffisant pour prendre en charge la demande.

**Cause 1 :** le cluster ne peut pas prendre en charge la taille de machine virtuelle demandée.

**Résolution 1 :**

- Relancez la requête en utilisant une taille inférieure pour la machine virtuelle.
- Si la taille de la machine virtuelle requise ne peut pas être modifiée :
  - Arrêtez toutes les machines virtuelles dans le groupe à haute disponibilité. Cliquez sur **Groupes de ressources** > *votre groupe de ressources* > **Ressources** > *votre groupe à haute disponibilité* > **Machines virtuelles** > *votre machine virtuelle* > **Arrêter**.
  - Une fois que toutes les machines virtuelles sont arrêtées, créez une machine virtuelle à la taille souhaitée.
  - Démarrez la nouvelle machine virtuelle en premier, puis sélectionnez chacune des machines virtuelles arrêtées et cliquez sur **Démarrer**.

**Cause 2 :** le cluster n’a pas de ressources libres.

**Résolution 2 :**

- Relancez la demande ultérieurement.
- Si la nouvelle machine virtuelle peut faire partie d’un autre groupe à haute disponibilité
  - Créez une machine virtuelle dans un autre groupe à haute disponibilité (dans la même région).
  - Ajoutez la nouvelle machine virtuelle au même réseau virtuel.

## Étapes suivantes
Si vous rencontrez des problèmes lorsque vous démarrez une machine virtuelle Linux arrêtée ou que vous redimensionnez une machine virtuelle Linux existante dans Azure, consultez [Résoudre les problèmes de déploiement Resource Manager liés au redémarrage ou au redimensionnement d’une machine virtuelle Linux existante dans Azure](virtual-machines-linux-restart-resize-error-troubleshooting.md).

<!---HONumber=AcomDC_0914_2016-->