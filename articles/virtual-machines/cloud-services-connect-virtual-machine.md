<properties
	pageTitle="Connectez des ordinateurs virtuels dans un service cloud | Microsoft Azure"
	description="Connectez une machine virtuelle créée avec le modèle de déploiement classique sur un service cloud ou un réseau virtuel Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="cynthn"/>


# Connectez des ordinateurs virtuels créés avec le modèle de déploiement classique avec un réseau virtuel ou un service cloud

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Les machines virtuelles créées avec le modèle de déploiement classique sont toujours placées dans un service cloud. Le service cloud fait office de conteneur et fournit un nom DNS public unique, une adresse IP publique et un ensemble de points de terminaison pour accéder à la machine virtuelle via Internet. Le service cloud peut être dans un réseau virtuel, mais ce n’est pas obligatoire.

Si un service cloud n’est pas dans un réseau virtuel, il est nommé « service cloud *autonome* ». Les machines virtuelles de ce service cloud autonome peuvent seulement communiquer avec d’autres machines virtuelles à l’aide des noms DNS publics de ces dernières, et ce trafic circule sur Internet. Si un service cloud se trouve dans un réseau virtuel, les machines virtuelles de ce service cloud peuvent communiquer avec toutes les autres machines virtuelles du réseau virtuel sans envoyer aucun trafic sur Internet.

Si vous placez vos machines virtuelles dans le même service cloud autonome, vous pouvez toujours utiliser l’équilibrage de charge et des groupes à haute disponibilité. Pour plus d'informations, consultez les pages [Équilibrage de charge des machines virtuelles](../articles/load-balance-virtual-machines.md) et [Gestion de la disponibilité des machines virtuelles](../articles/manage-availability-virtual-machines.md). Toutefois, vous ne pouvez pas organiser les machines virtuelles sur des sous-réseaux ou connecter un service cloud autonome à votre réseau local. Voici un exemple :

![Machines virtuelles dans un service cloud autonome](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)

Si vous placez vos machines virtuelles dans un réseau virtuel, vous pouvez décider combien de services cloud vous voulez utiliser pour utiliser l’équilibrage de charge et des groupes à haute disponibilité. En outre, vous pouvez organiser les machines virtuelles sur les mêmes sous-réseaux que votre réseau local et connecter le réseau virtuel à votre réseau local. Voici un exemple :

![Machines virtuelles dans un réseau virtuel](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

Les réseaux virtuels sont la méthode recommandée pour connecter des machines virtuelles dans Azure. La meilleure pratique consiste à configurer chaque couche de votre application dans un service cloud distinct. Toutefois, vous devrez peut-être combiner des machines virtuelles à partir de différentes couches d'application dans le même service cloud pour ne pas dépasser le nombre maximum de services cloud par abonnement (200). Pour vérifier cela et les autres limites, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

## Connexion de machines virtuelles dans un réseau virtuel

Pour connecter des machines virtuelles dans un réseau virtuel :

1.	Créez le réseau virtuel dans le [portail Azure](http://manage.windowsazure.com). Pour plus d’informations, consultez [Créer un réseau virtuel (classique) à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md).
2.	Créez l’ensemble des services cloud pour votre déploiement de manière à refléter votre conception des groupes à haute disponibilité et l’équilibrage de la charge. Dans le portail, cliquez sur **Nouveau > Calcul > Service cloud > Création personnalisée** pour chaque service cloud.
3.	Pour créer chaque machine virtuelle, cliquez sur **Nouveau > Calcul > Machine virtuelle > À partir de la galerie**. Choisissez le service cloud et le réseau virtuel appropriés à la machine virtuelle. Si le service cloud est déjà joint à un réseau virtuel, son nom est sélectionné pour vous.

![Sélection d'un service cloud pour une machine virtuelle](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## Connexion de machines virtuelles dans un service cloud autonome

Pour connecter des machines virtuelles dans un service cloud autonome :

1.	Créez le service cloud dans le [portail Azure](http://manage.windowsazure.com). Cliquez sur **Nouveau > Calculer > Service cloud > Création personnalisée**. Vous pouvez également créer le service cloud pour votre déploiement lorsque vous créez votre première machine virtuelle.
2.	Lorsque vous créez la machine virtuelle, sélectionnez le nom du service cloud créé à l’étape précédente. ![Ajouter un ordinateur virtuel à un service cloud existant](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Ressources
[Équilibrage de charge des machines virtuelles](../articles/load-balance-virtual-machines.md)

[Gestion de la disponibilité des machines virtuelles](../articles/manage-availability-virtual-machines.md)

[Tâches de configuration du réseau virtuel](../documentation/services/virtual-machines/)

Une fois que vous avez créé une machine virtuelle, il convient d'ajouter un disque de données pour que vos services et charges de travail disposent d'un emplacement de stockage des données. Consultez l'une des rubriques suivantes :

[Association d’un disque de données à une machine virtuelle Linux](../articles/virtual-machines/virtual-machines-linux-how-to-attach-disk.md)

[Attachement d'un disque de données à une machine virtuelle Windows](../articles/virtual-machines/storage-windows-attach-disk.md)

<!---HONumber=Nov15_HO1-->