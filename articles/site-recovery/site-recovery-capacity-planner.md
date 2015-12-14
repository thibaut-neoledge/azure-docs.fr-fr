<properties
	pageTitle="Planificateur de capacité de récupération de sites | Microsoft Azure" 
	description="Microsoft Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et serveurs physiques situés en local sur Microsoft Azure, ou sur un site local secondaire." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="11/27/2015" 
	ms.author="pratshar"/>

# Planificateur de capacité de récupération de sites

Ce document explique comment utiliser l’outil Microsoft ASR Capacity Planning, qui fournit des conseils sur les ressources qui doivent être configurées pour effectuer des opérations de récupération de sites transparentes. Vous pouvez utiliser cet outil de planification de la capacité pour analyser l’environnement source (charges de travail), les besoins en bande passante, les besoins en ressources (machines virtuelles, stockage) sur la cible et les éventuelles ressources serveur supplémentaires nécessaires du côté source (SC VMM, serveurs de configuration, serveurs de traitement, etc.). Télécharger l’outil [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel)
 
Vous pouvez utiliser le planificateur de capacité dans deux modes :
 
- **Planification rapide** : bénéficiez de projections réseau et serveur sur la base de la quantité moyenne de machines virtuelles, de disques et de stockage, et sur le taux de changement moyen. 
- **Planification détaillée** : fournissez les détails de chaque charge de travail au niveau de la machine virtuelle. Analysez la compatibilité au niveau de la machine virtuelle et obtenez également les projections réseau et serveur.
     
Ce document part du principe que l’utilisateur sait utiliser Azure Site Recovery. Consultez [Vue d’ensemble d’Azure Site Recovery](site-recovery-overview.md).

## Mise en route
###Conditions préalables
Les détails nécessaires pour continuer varient en fonction du mode dans lequel vous souhaitez utiliser le planificateur. Outre les détails de l’infrastructure tels que les machines virtuelles, le nombre de disques par machine virtuelle et le stockage par disque, certaines informations supplémentaires sont nécessaires. La plus importante est le taux de changement quotidien. Si l’environnement source est Hyper-V, utilisez l’[Outil de planification de la capacité Hyper-V](https://www.microsoft.com/en-in/download/details.aspx?id=39057) pour obtenir le taux de changement. Consultez les instructions d’utilisation de l’[Outil de planification de la capacité Hyper-v](site-recovery-capacity-planning-for-hyper-v-replication.md). Pour VMWare, utilisez l’[Outil de planification de la capacité VMware](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).

##Quick Planner
1.	Ouvrez le fichier **ASR Capacity Planner.xlsm**. Cela nécessite l’exécution de macros. Vous devez donc accepter d’**« Activer la modification »** et d’**« Activer le contenu »** quand vous y êtes invité. 
1.	Sélectionnez **Quick Planner** dans la zone de liste. Une autre feuille de calcul intitulée **Capacity Planner** s’ouvre.

	![Mise en route](./media/site-recovery-capacity-planner/getting-started.png)

1.	Dans la feuille de calcul « Capacity Planner », tapez les entrées en fonction des besoins. Tous les champs entourés sont des champs d’entrée obligatoires.
	1.	La zone de liste « Select your scenario » vous permet de choisir l’environnement source « Hyper-V to Azure » ou « VMware/Physical to Azure ».
	1. 	Vous devez mesurer la valeur Average Daily Data Change Rate. Dans les environnements Hyper-V, vous pouvez utiliser l’outil de planification de la capacité Hyper-V. Pour VMWare, vous pouvez utiliser l’outil VMWare Capacity Planner. Nous vous conseillons d’exécuter l’outil pendant au moins une semaine, pour que les éventuels pics de valeurs puissent être capturés et normalisés. 
	1. 	Compression : il s’agit de la compression offerte par ASR dans le scénario VMWare/Physical to Azure. Dans le scénario Hyper-V to Azure, cela est possible grâce à des appliances tierces telles que Riverbed. 
	1. Dans le scénario VMWare/Physical to Azure, vous devez entrer le nombre de rétentions en journées. Dans le scénario Hyper-V, vous devez l’entrer en heures. 
	1. Les deux dernières entrées servent à calculer la réplication initiale. Quand vous déployez le programme d’installation ASR, vous devez charger l’intégralité du jeu de données initial. Number of hours in which initial replication for the batch of virtual machines should complete et Number of virtual machines per initial replication batch : ces valeurs doivent être prises comme entrées. Vous pouvez également modifier ces chiffres pour régler la bande passante existante. 

	![Entrées](./media/site-recovery-capacity-planner/inputs.png)

1. Une fois que vous avez entré les détails de l’environnement source, la sortie affiche des conseils parmi lesquels :
	1.	Besoins en bande passante
		1. Bandwidth required for delta replication (in Megabits/sec). Cette valeur est calculée en fonction du taux de changement de données quotidien moyen. 
		1. Bandwidth required for initial replication (In Megabits/sec) is also presented. Cette valeur est calculée en fonction des entrées de réplication initiales présentées dans les entrées (deux dernières lignes). 
	1.	Conditions requises pour Azure
		1. 	Cette section détaille le stockage, les E/S par seconde, les comptes de stockage et les disques nécessaires dans Azure. 
	1. 	Autres conditions requises pour l’infrastructure 
		1. Exigences supplémentaires pour le scénario VMware/Physical to Azure, telles que celles liées aux serveurs de configuration et aux serveurs de traitement. 
		1. 	Exigences supplémentaires pour le scénario Hyper-V to Azure, telles que le stockage supplémentaire nécessaire sur la source.
			
	![Sortie](./media/site-recovery-capacity-planner/output.png)
 
##Detailed Planner

1.	Ouvrez le fichier **ASR Capacity Planner.xlsm**. Cela nécessite l’exécution de macros. Vous devez donc accepter d’**« Activer la modification »** et d’**« Activer le contenu »** quand vous y êtes invité. 
1.	Sélectionnez **Detailed Planner** dans la zone de liste. Une autre feuille de calcul intitulée **Workload Qualification** s’ouvre.

	![Mise en route](./media/site-recovery-capacity-planner/getting-started-2.png)


1.	Dans la feuille de calcul Workload Qualification, tapez les entrées en fonction des besoins. Toutes les colonnes marquées en rouge sont obligatoires. Les autres colonnes sont facultatives.
	1.	Process Cores : spécifiez le nombre total de cœurs d’un serveur source.
	1. Memory Allocation in MB : spécifiez la taille de mémoire RAM d’un serveur source.
	1.	Number of NICs : spécifiez le nombre de cartes réseau d’un serveur source.
	1. Total Storage (in GBs) : spécifiez la taille totale de votre stockage pour la machine virtuelle. Par exemple, si le serveur source contient trois disques de 500 Go chacun, la taille de stockage totale est de 1500 Go.
	1. Number of disks attached : spécifiez le nombre total de disques d’un serveur source.
	1. Disk capacity utilization : spécifiez l’utilisation moyenne. 
	1. Daily Change rate(%) : spécifiez le taux de changement de données quotidien d’un serveur source.
	1. Mapping Azure size : vous pouvez soit entrer la taille de machine virtuelle Azure que vous souhaitez mapper, soit utiliser le bouton Compute IaaS VMs pour calculer la meilleure correspondance possible. 

	![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification.png)
 

1. Un clic sur le bouton **Compute IaaS VMs** valide les entrées ci-dessus et suggère la meilleure correspondance Azure VM possible. Si l’outil ne peut pas trouver la taille de machine virtuelle Azure appropriée pour un serveur source, il signale une erreur pour le serveur. Par exemple, si vous spécifiez la valeur 65 comme nombre de disques attachés pour une machine virtuelle source, une erreur est générée car le nombre maximal de disques pouvant être attachés à une machine virtuelle Azure est de 64.


La commande **Compute IaaS VMs** calcule également si une machine virtuelle a besoin d’un compte de stockage Azure standard ou premium. Elle suggère aussi le nombre de comptes de stockage standard et premium nécessaires pour la charge de travail. Faites défiler vers le bas à droite pour afficher le type de stockage Azure et le compte de stockage qui peuvent être utilisés pour un serveur source.
 
**Exemple** : pour cinq machines virtuelles avec les valeurs suivantes, l’outil a calculé et affecté la meilleure correspondance de taille de machine virtuelle Azure et a suggéré si la machine virtuelle avait besoin de stockage standard ou premium.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-2.png)

Dans l’exemple, deux comptes de stockage standard et un compte de stockage premium sont nécessaires pour cinq machines virtuelles. VM1 et VM2 peuvent utiliser le premier compte de stockage standard, tandis que VM3 peut utiliser le deuxième compte de stockage standard. VM4 et VM5 ont besoin d’un compte de stockage premium et peuvent partager le même compte.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-3.png)


>[AZURE.NOTE]Les E/S par seconde sont calculées au niveau de la machine virtuelle, et non au niveau du disque. Si l’un des disques d’une machine virtuelle source présente une valeur d’E/S par seconde supérieure à 500, mais que le nombre total d’E/S par seconde de la machine virtuelle est compris dans les limites de machine virtuelle Azure standard prises en charge, et si toutes les autres valeurs (nombre de disques, nombre de cartes réseau, nombre de cœurs de processeur, taille de la mémoire) sont comprises dans les limites de machine virtuelle standard, l’outil choisit une machine virtuelle standard plutôt que le stockage premium. L’utilisateur doit mettre à jour manuellement la cellule Mapping Azure size avec la machine virtuelle de série DS ou GS appropriée.


1.	La première colonne est une colonne de validation pour les machines virtuelles, disques et taux de changement. 
1.	Une fois tous les détails en place, cliquez sur le bouton **Submit data to the planner tool** affiché en haut. La feuille de calcul **Capacity Planner** s’ouvre et indique les moyennes remplies automatiquement, comme illustré dans la figure ci-dessous. 
1.	Cette action met également en évidence les charges de travail qui sont éligibles pour la protection et celles qui ne le sont pas.


###Capacity Planner

1.	Dans la feuille de calcul **Capacity Planner**, la première ligne Infra Inputs source **Workload** suggère que les informations d’entrée soient remplies à partir de la feuille de calcul **Workload Qualification**.  
1.	Chaque fois que des modifications sont nécessaires, apportez ces modifications dans la feuille de calcul **Workload Qualification** et appuyez sur le bouton **Submit Data To the planner tool**. 

	![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1203_2015-->