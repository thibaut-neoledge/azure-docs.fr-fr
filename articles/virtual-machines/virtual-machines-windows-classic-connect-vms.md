<properties
	pageTitle="Connecter des machines virtuelles Windows dans un service cloud | Microsoft Azure"
	description="Connectez des machines virtuelles Windows créées avec le modèle de déploiement classique à un service cloud ou à un réseau virtuel Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="cynthn"/>

# Connecter des machines virtuelles Windows créées avec le modèle de déploiement classique à un réseau virtuel ou à un service cloud

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Les machines virtuelles Windows créées avec le modèle de déploiement classique sont toujours placées dans un service cloud. Le service cloud fait office de conteneur et fournit un nom DNS public unique, une adresse IP publique et un ensemble de points de terminaison pour accéder à la machine virtuelle via Internet. Le service cloud peut être dans un réseau virtuel, mais ce n’est pas obligatoire. Vous pouvez également [connecter des machines virtuelles Linux à un réseau virtuel ou à un service cloud](virtual-machines-linux-classic-connect-vms.md).

Si un service cloud n’est pas dans un réseau virtuel, il est nommé « service cloud *autonome* ». Les machines virtuelles de ce service cloud autonome peuvent seulement communiquer avec d’autres machines virtuelles à l’aide des noms DNS publics de ces dernières, et ce trafic circule sur Internet. Si un service cloud se trouve dans un réseau virtuel, les machines virtuelles de ce service cloud peuvent communiquer avec toutes les autres machines virtuelles du réseau virtuel sans envoyer aucun trafic sur Internet.

Si vous placez vos machines virtuelles dans le même service cloud autonome, vous pouvez toujours utiliser l’équilibrage de charge et des groupes à haute disponibilité. Pour plus d'informations, consultez les pages [Équilibrage de charge des machines virtuelles](virtual-machines-windows-load-balance.md) et [Gestion de la disponibilité des machines virtuelles](virtual-machines-windows-manage-availability.md). Toutefois, vous ne pouvez pas organiser les machines virtuelles sur des sous-réseaux ou connecter un service cloud autonome à votre réseau local. Voici un exemple :

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## Étapes suivantes

Une fois que vous avez créé une machine virtuelle, il convient de lui [ajouter un disque de données](virtual-machines-windows-classic-attach-disk.md) pour que vos services et charges de travail disposent d’un emplacement de stockage des données.

<!---HONumber=AcomDC_0706_2016-->