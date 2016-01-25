<properties
   pageTitle="Architecture d’Azure Resource Manager | Microsoft Azure"
   description="Découvrez l’architecture de Resource Manager et les relations entre les fournisseurs de ressources relatives au calcul, au réseau et au stockage."
   services="virtual-machines"
   documentationCenter=""
   authors="davidmu1"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Architecture d’Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique



Cet article présente une vue d’ensemble des architectures de Service Management et de Resource Manager permettant de créer des applications et charges de travail basées sur une infrastructure.

## Architecture de Service Management

Avant d’aborder l’architecture d’Azure Resource Manager et des différents fournisseurs de ressources, passons d’abord en revue l’architecture actuelle d’Azure Service Management. Dans Azure Service Management, les ressources relatives au calcul, au stockage ou au réseau qui permettent d’héberger des machines virtuelles sont fournies par :

- Un service cloud nécessaire qui agit en tant que conteneur pour héberger des machines virtuelles (calcul). Les machines virtuelles sont automatiquement fournies avec une carte d’interface réseau (NIC, Network Interface Card) et une adresse IP attribuée par Azure. En outre, le service cloud présente une instance d’équilibrage de charge externe, une adresse IP publique et des points de terminaison par défaut permettant un accès à distance et un trafic PowerShell distant pour les machines virtuelles Windows et un trafic Secure Shell (SSH) pour les machines virtuelles Linux.
- Un compte de stockage nécessaire qui stocke les disques durs virtuels d’une machine virtuelle, notamment le système d’exploitation, les disques de données temporaires et supplémentaires (stockage).
- Un réseau virtuel facultatif qui agit en tant que conteneur supplémentaire, dans lequel vous pouvez créer une structure de sous-réseaux et désigner le sous-réseau sur lequel se trouve la machine virtuelle (réseau).

Voici les composants et leurs relations concernant Azure Service Management.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Architecture de Resource Manager

Concernant Azure Resource Manager, les fournisseurs de ressources prennent en charge des ressources individuelles permettant de créer des machines virtuelles fonctionnelles, configurées selon vos besoins. Concernant les machines virtuelles, il existe trois principaux fournisseurs de ressources :

- Le fournisseur de ressources de calcul (CRP, Compute Resource Provider) : il prend en charge les instances des machines virtuelles et les groupes à haute disponibilité facultatifs.
- Le fournisseur de ressources de stockage (SRP, Storage Resource Provider) : il prend en charge les comptes de stockage nécessaires qui stockent les disques durs virtuels pour les machines virtuelles, notamment leur système d’exploitation et les disques de données supplémentaires.
- Le fournisseur de ressources réseau (NRP, Network Resource Provider) : il prend en charge les cartes réseau, adresses IP des machines virtuelles et sous-réseaux au sein de réseaux virtuels obligatoires, ainsi que les équilibreurs de charge facultatifs, les adresses IP des équilibreurs de charge et les groupes de sécurité réseau.

Il existe également des relations entre les ressources présentes au sein des fournisseurs de ressources :

- Une machine virtuelle dépend d’un compte de stockage spécifique défini dans le fournisseur de ressources de stockage pour stocker ses disques dans le stockage d’objets blob (obligatoire).
- Une machine virtuelle fait référence à une carte d’interface réseau spécifique définie dans le fournisseur de ressources réseau (obligatoire) et un groupe de haute disponibilité défini dans le fournisseur de ressources de calcul (facultatif).
- Une carte d’interface réseau fait référence à l’adresse IP attribuée à la machine virtuelle (obligatoire), au sous-réseau du réseau virtuel de la machine virtuelle (obligatoire) et à un groupe de sécurité réseau (facultatif).
- Un sous-réseau présent au sein d’un réseau virtuel fait référence à un groupe de sécurité réseau (facultatif).
- Une instance d’équilibreur de charge fait référence au pool principal d’adresses IP qui comporte la carte d’interface réseau d’une machine virtuelle (facultatif) et fait référence à l’adresse IP publique ou privée d’un équilibreur de charge (facultative).

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch2.png)

La modularité des ressources permet une plus grande flexibilité lors de la configuration de l’infrastructure d’une charge de travail informatique hébergée dans Azure. Les modèles Azure Resource Manager tirent parti de cette flexibilité pour créer le jeu de ressources dépendantes requises pour une configuration spécifique. Lors de l’exécution d’un modèle, Resource Manager s’assure que les ressources relatives à une configuration sont créées dans l’ordre approprié afin de préserver les dépendances et les références. Par exemple, Resource Manager ne crée pas la carte d’interface réseau d’une machine virtuelle avant d’avoir créé le réseau virtuel présentant un sous-réseau et une adresse IP (un groupe de sécurité réseau est facultatif).

Un groupe de ressources correspond à un conteneur logique qui comporte les ressources associées à une application ; il peut être composé de plusieurs machines virtuelles, cartes réseau, adresses IP, équilibreurs de charge, sous-réseaux et groupes de sécurité réseau. Par exemple, vous pouvez gérer toutes les ressources de l’application en tant qu’unité de gestion unique. Vous pouvez créer, mettre à jour et supprimer toutes les ressources conjointement. Voici un exemple d’application déployée au sein d’un groupe de ressources unique.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

L’application comporte les éléments suivants :

- deux machines virtuelles qui utilisent le même compte de stockage et sont présentes au sein du même groupe à haute disponibilité et au sein du même sous-réseau d’un réseau virtuel ;
- une carte d’interface réseau et une adresse IP uniques pour chaque machine virtuelle ;
- un équilibreur de charge externe qui répartit le trafic Internet vers les cartes réseau des deux machines virtuelles.

Toutes les ressources de cette application sont gérées via le groupe de ressources unique qui les contient.

Vous pouvez également observer la modularité et les relations dépendantes entre les ressources lorsque vous créez une machine virtuelle basée sur Resource Manager à l’aide d’Azure PowerShell ou d’Azure CLI. Avant de pouvoir exécuter la commande permettant de créer la machine virtuelle, vous devez créer un groupe de ressources, un compte de stockage, un réseau virtuel présentant un sous-réseau et une carte d’interface réseau présentant une adresse IP. Pour plus d’informations, consultez la rubrique [Créer et préconfigurer une machine virtuelle Windows avec Resource Manager et Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md).

## Étapes suivantes

Pour découvrir les ressources qui peuvent être déployées avec le modèle de déploiement Resource Manager, passez en revue [Fournisseurs de solutions de calcul, de réseau et de stockage Azure dans Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md).

<!---HONumber=AcomDC_0114_2016-->