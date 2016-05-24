<properties
   pageTitle="Résoudre les problèmes de déploiement de machines virtuelles Windows - Classic | Microsoft Azure"
   description="Découvrez comment résoudre les problèmes de déploiement Classic lorsque vous créez une machine virtuelle Windows dans Azure."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="jiangchen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-windows"
  ms.workload="na"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="05/06/2016"
  ms.author="cjiang"/>

# Résoudre les problèmes de déploiement Classic liés à la création d’une machine virtuelle Windows dans Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Collecter des journaux d’audit

Pour commencer la résolution des problèmes, collectez les journaux d’audit afin d’identifier l’erreur associée au problème.

Dans le portail Azure, cliquez sur **Parcourir** > **Machines virtuelles** > *votre machine virtuelle Windows* > **Paramètres** > **Journaux d’audit**.

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**O :** si le système d’exploitation est de type Windows généralisé et qu’il est chargé et/ou capturé avec le paramètre généralisé, il n’y aura aucune erreur. De même, si le système d’exploitation est de type Windows spécialisé et qu’il est chargé et/ou capturé avec le paramètre spécialisé, il n’y aura aucune erreur.

**Erreurs de chargement :**

**N<sup>1</sup> :** si le système d’exploitation est de type Windows généralisé et qu’il est chargé avec le paramètre spécialisé, vous obtiendrez une erreur de délai d’attente d’approvisionnement, et la machine virtuelle sera bloquée au niveau de l’écran OOBE.

**N<sup>2</sup> :** si le système d’exploitation est de type Windows spécialisé et qu’il est chargé avec le paramètre généralisé, vous obtiendrez une erreur d’échec d’approvisionnement, et la machine virtuelle sera bloquée au niveau de l’écran OOBE, car la nouvelle machine virtuelle s’exécutera avec le nom d’ordinateur, le nom d’utilisateur et le mot de passe d’origine.

**Résolution :**

Pour corriger ces deux erreurs, chargez le disque dur virtuel d’origine, disponible en mode local, avec le même paramétrage que pour le système d’exploitation (généralisé/spécialisé). Pour effectuer un chargement de type généralisé, n’oubliez pas de commencer par exécuter sysprep. Pour plus d’informations, voir [Création et téléchargement d’un disque dur virtuel Windows Server dans Azure](virtual-machines-windows-classic-createupload-vhd.md).

**Erreurs de capture :**

**N<sup>3</sup> :** si le système d’exploitation est de type Windows généralisé et qu’il est capturé avec le paramètre spécialisé, vous obtiendrez une erreur de délai d’attente d’approvisionnement, car la machine virtuelle d’origine n’est pas utilisable tant qu’elle est marquée comme généralisée.

**N<sup>4</sup> :** si le système d’exploitation est de type Windows spécialisé et qu’il est capturé avec le paramètre généralisé, vous obtiendrez une erreur d’échec d’approvisionnement, car la nouvelle machine virtuelle s’exécutera avec le nom d’ordinateur, le nom d’utilisateur et le mot de passe d’origine. En outre, la machine virtuelle d’origine n’est pas utilisable tant qu’elle est marquée comme spécialisée.

**Résolution :**

Pour corriger ces deux erreurs, supprimez l’image actuelle du portail, et [effectuez une nouvelle capture à partir des disques durs virtuels en cours](virtual-machines-windows-classic-capture-image.md), avec le même paramétrage que celui du système d’exploitation (généralisé/spécialisé).

## Problème : image personnalisée / galerie / marketplace ; échec d’allocation
Cette erreur se produit lorsque la nouvelle demande de la machine virtuelle est envoyée à un cluster qui n’a pas d’espace libre pour prendre en charge la demande, ou qui ne peut pas prendre en charge la taille de machine virtuelle demandée. Il est impossible de combiner différentes séries de machines virtuelles dans un même service cloud. Par conséquent, si vous essayez de créer une machine virtuelle d’une taille que votre service cloud ne peut pas prendre en charge, la demande de calcul échoue.

Selon les contraintes du service cloud que vous utilisez pour créer la machine virtuelle, vous pouvez rencontrer une erreur provoquée par deux situations distinctes.

**Cause 1 :** le service cloud est épinglé à un cluster spécifique ou il est lié à un groupe d’affinités, et par conséquent épinglé à un cluster spécifique de par sa conception. Ainsi, les nouvelles demandes de ressources de calcul portant sur ce groupe d’affinités sont tentées dans le cluster hébergeant les ressources existantes. Toutefois, ce même cluster peut soit ne pas prendre en charge la taille de la machine virtuelle demandée, soit ne pas disposer de suffisamment d’espace, ce qui entraîne une erreur d’allocation. Cela est vrai que les ressources soient créées par le biais d’un nouveau service cloud ou d’un service cloud existant.

**Résolution 1 :**

- Créez un service cloud et associez-le à une région ou à un réseau virtuel basé sur une région.
- Créez une machine virtuelle dans le nouveau service cloud. Si vous obtenez une erreur lorsque vous tentez de créer un service cloud, vous pouvez soit réessayer ultérieurement, soit modifier la région du service cloud.

> [AZURE.IMPORTANT] Si vous avez essayé de créer une machine virtuelle dans un service cloud existant, mais que vous n’avez pas réussi, et que vous avez donc dû créer un autre service cloud pour votre nouvelle machine virtuelle, vous pouvez décider de consolider toutes vos machines virtuelles dans le même service cloud. Pour ce faire, supprimez les machines virtuelles du service cloud existant, puis recapturez-les à partir de leurs disques dans le nouveau service cloud. Toutefois, il est important de se rappeler que le nouveau service cloud aura un nouveau nom et une nouvelle adresse IP virtuelle. Vous devrez donc mettre à jour ces valeurs pour toutes les dépendances qui utilisent actuellement ces informations pour le service cloud existant.

**Cause 2 :** le service cloud est associé à un réseau virtuel qui est lié à un groupe d’affinités, et par conséquent épinglé à un cluster spécifique de par sa conception. Toutes les nouvelles demandes de ressources de calcul portant sur ce groupe d’affinités sont donc tentées dans le cluster hébergeant les ressources existantes. Toutefois, ce même cluster peut soit ne pas prendre en charge la taille de la machine virtuelle demandée, soit ne pas disposer de suffisamment d’espace, ce qui entraîne une erreur d’allocation. Cela est vrai que les ressources soient créées par le biais d’un nouveau service cloud ou d’un service cloud existant.

**Résolution 2 :**

- Créez un réseau virtuel régional.
- Créez la machine virtuelle dans le nouveau réseau virtuel.
- [Connectez votre réseau virtuel existant](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) au nouveau réseau virtuel. Pour plus d’informations, voir [Regional Virtual Networks](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) (Réseaux virtuels régionaux). Une autre possibilité consiste à [effectuer la migration de votre réseau virtuel basé sur un groupe d’affinités vers un réseau virtuel régional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), puis à créer la machine virtuelle.

<!---HONumber=AcomDC_0511_2016-->