<properties
	pageTitle="Planifier les capacités pour la protection de machine virtuelle et de serveur physique dans Azure Site Recovery | Microsoft Azure"
	description="Microsoft Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et serveurs physiques situés en local sur Microsoft Azure, ou sur un site local secondaire." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/07/2015" 
	ms.author="raynew"/>

# Planifier la capacité pour la protection de machine virtuelle et de serveur physique dans Azure Site Recovery

L’outil Capacity Planner vous aide à prévoir vos besoins en capacité pour la protection des machines virtuelles Hyper-V, les machines virtuelles VMware et les serveurs physiques Windows/Linux avec Azure Site Recovery.


## Vue d’ensemble

Utilisez l’outil pour analyser votre environnement source et vos charges de travail et déterminez vos besoins en bande passante et les ressources serveur dont vous aurez besoin à l’emplacement source, et les ressources (machines virtuelles et stockage, etc.) qu’il vous faut à votre emplacement cible.

L’outil peut s’utiliser en deux façons :

- **Planification rapide** : exécutez l’outil dans ce mode pour obtenir des projections réseau et serveur sur la base de la quantité moyenne de machines virtuelles, de disques et de stockage, et sur le taux de changement moyen.
- **Planification détaillée** : exécutez l’outil dans ce mode et fournissez des détails de chaque charge de travail au niveau de la machine virtuelle. Analysez la compatibilité de machine virtuelle et obtenez des projections réseau et serveur.

## Avant de commencer

Avant d’exécuter l’outil :

1. Collecter des informations relatives à votre environnement, et notamment les machines virtuelles, le nombre de disques par machine virtuelle, le stockage par disque.
2. Déterminer le taux de modification (l’évolution) quotidienne des données répliquées. Pour ce faire :

	- Si vous répliquez des machines virtuelles Hyper-V, téléchargez l’[outil de planification de la capacité Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) pour obtenir le taux de modification. [En savoir plus](site-recovery-capacity-planning-for-hyper-v-replication.md) sur cet outil. Nous vous recommandons d’exécuter cet outil sur une semaine pour enregistrer les moyennes.
	- Si vous répliquez des machines virtuelles VMware, utilisez l’[équipement de planification de capacité vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) pour déterminer le taux d’évolution.
	- Si vous dupliquez des serveurs physiques, vous devrez faire les estimations manuellement.

## Exécutez Quick Planner
1.	Téléchargez et ouvrez l’outil [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel). Vous devrez exécuter des macros, et donc, sélectionner cette option pour activer la modification et activer le contenu lorsque vous y êtes invité. 
2.	Dans **Sélectionner un type de planificateur**, sélectionnez **Quick Planner** dans la zone de liste.

	![Prise en main](./media/site-recovery-capacity-planner/getting-started.png)

3.	Dans la feuille de calcul **Capacity Planner**, saisissez les informations requises. Vous devez renseigner tous les champs cerclés de rouge de la capture d’écran ci-dessous.

	- Dans **Sélectionner votre scénario**, choisissez **Hyper-V to Azure** (Hyper-V vers Azure) ou **VMware/Physical to Azure** (VMware/Physique vers Azure).
	- Dans **Taux de modification de données moyen par jour**, entrez les informations que vous recueillez à l’aide de l’[outil de planification de la capacité Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou l’[équipement de planification de capacités vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
	- Le paramètre de **compression** s’applique uniquement à la compression proposée lors de la réplication des machines virtuelles VMware ou des serveurs physiques vers Azure. Nous estimons ce paramètre à 30 % ou plus. Modifiez le paramètre comme il se doit. Pour répliquer des machines virtuelles Hyper-V pour la compression Azure, vous pouvez utiliser un équipement tiers, tels que Riverbed. 
	-  Dans **Number of retention** (Nombre de jours de rétention) spécifiez la durée de conservation des réplicas. Si vous répliquez des serveurs VMware ou des serveurs physiques, saisissez la valeur en jours. Si vous répliquez Hyper-V, spécifiez la durée en heures.
	-  Dans **Number of hours in which initial replication for the batch of virtual machines should complete** (Nombre d’heures prévu pour la réplication initiale du lot de machines virtuelles) et **Number of virtual machines per initial replication batch** (Nombre de machines virtuelles par lot de réplication initiale), vous devez saisir les paramètres de saisie utilisés pour calculer les exigences de réplication initiales. Lorsque vous déployez Site Recovery, vous devez charger l’intégralité du jeu de données initial. 

	![Entrées](./media/site-recovery-capacity-planner/inputs.png)

2.	Une fois que vous avez placé les valeurs de l’environnement source, la sortie affichée inclut :

	- Besoins en bande passante :
		- **Bande passante totale requise pour la réplication delta** (Mo/s) Cette valeur est calculée en fonction du taux de changement de données quotidien moyen.
		- **Bande passante requise pour la réplication initiale** (Mo/s) Elle est calculée à partir des valeurs de réplication initiales que vous avez saisies. 
	- Configuration requise pour Azure : fournit des détails sur les comptes de stockage, le nombre d’E/S par seconde, les comptes de stockage et les disques nécessaires dans Azure.
		- **Stockage requis (en Go)** correspond au stockage Azure total requis.
		- **Nombre d’E/S par seconde sur les comptes de stockage standard** est calculé en fonction d’une taille d’unité d’E/S par seconde de 8K sur le total de comptes de stockage standard. Pour Quick Planner, le nombre est calculé en fonction de l’ensemble des disques de machines virtuelles source et du taux de changement de données par jour. Pour Detailed Planner, le nombre est calculé en fonction du nombre total de machines virtuelles mappées sur des machines virtuelles Azure standard et du taux de modification de données sur ces dernières. 
		- **Nombre de comptes de stockage standard requis** fournit le nombre total de comptes de stockage standard nécessaires pour protéger les machines virtuelles. Notez qu’un compte de stockage standard peut contenir jusqu’à 20 000 E/S par secondes sur toutes les machines virtuelles appartenant à un stockage standard et qu’un maximum de 500 E/S par seconde est pris en charge pour chaque disque. 
		- **Nombre de disques blob requis** donne le nombre de disques qui seront créés sur le stockage Azure.
		- **Nombre de comptes de stockage premium requis** fournit le nombre total de comptes de stockage premium nécessaire pour protéger les machines virtuelles. Notez qu’une machine virtuelle source avec une valeur d’E/S par seconde élevée (supérieure à 20 000) a besoin d’un compte de stockage premium. Un compte de stockage premium peut contenir jusqu’à 80 000 E/S par seconde.
		- **Nombre d’E/S par seconde sur les comptes de stockage premium** est calculé en sur la base d’une taille d’unité d’E/S par seconde de 256K sur l’ensemble des comptes de stockage premium. Pour Quick Planner, le nombre est calculé en fonction de l’ensemble des disques de machines virtuelles source et du taux de changement de données par jour. Concernant le Detailed Planner, le nombre est calculé en fonction du nombre total de machines virtuelles mappées sur des machines virtuelles Azure premium (séries DS et GS) et du taux de modification des données sur ces dernières. 

	- Autres exigences de l’infrastructure :
		- Inclut des exigences supplémentaires pour VMware/la réplication physique sur Azure, et notamment les serveurs de configuration et de processus.
		- Nombre de serveurs de traitement supplémentaires requis : indique si des serveurs de traitement supplémentaires sont nécessaires en plus du serveur de traitement configuré sur le serveur de configuration par défaut. N
		- Indique les exigences supplémentaires pour la réplication d’Hyper-V vers Azure, par exemple, s’il faut davantage d’espace de stockage au niveau de la source.
			
	![Sortie](./media/site-recovery-capacity-planner/output.png)
 
## Exécuter Detailed Planner


1.	Téléchargez et ouvrez l’outil [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel). Vous devrez exécuter des macros, et donc, sélectionner cette option pour activer la modification et activer le contenu lorsque vous y êtes invité. 
2.	Dans **Sélectionner un type de planificateur**, sélectionnez **Detailed Planner** dans la zone de liste.

	![Mise en route](./media/site-recovery-capacity-planner/getting-started-2.png)

3.	Dans la feuille de calcul **Qualification de la charge de travail**, saisissez les informations obligatoires. Vous devez renseigner tous les champs marqués.

	- Dans **Cœurs de processeur**, spécifiez le nombre total de cœurs sur un serveur source.
	- Dans **Allocation de mémoire en Mo** spécifiez la taille de la mémoire RAM d’un serveur source. 
	- Le **nombre de cartes réseau** spécifie le nombre de cartes réseau sur un serveur source. 
	-  Dans **Stockage total : (en Go)** spécifiez la taille totale du stockage de machine virtuelle. Par exemple, si le serveur source contient trois disques de 500 Go chacun, la taille de stockage totale est de 1 500 Go.
	-  Dans **Nombre de disques rattaché** : spécifiez le nombre total de disques d’un serveur source.
	-  Dans **Taux d’utilisation de capacité de disque**, spécifiez son utilisation moyenne.
	-  Dans **Taux de changement par jour (%)** spécifiez le taux de changement de données par jour d’un serveur source.
	-  Dans **taille du mappage d’Azure** saisissez la taille de machine virtuelle Azure que vous souhaitez mapper. Si vous ne souhaitez pas effectuer cette opération manuellement, cliquez sur **Calcul de machines virtuelles IaaS**.

4.	Si vous cliquez sur **Calcul des machines virtuelles IaaS**, voilà les opérations qui seront effectuées :

	- Valide vos entrées et obtient la meilleure correspondance de machine virtuelle Azure pour chaque machine virtuelle éligible à la réplication vers Azure. Si la détection d’une machine virtuelle de taille inappropriée ne peut être détectée, une erreur est émise. Par exemple, si le nombre de disques rattaché est 65, une erreur est émise. En effet, la taille de machine la plus élevée est 64.
	- Suggère un compte de stockage pouvant être utilisé pour une machine virtuelle Azure.
	- Calcule le nombre total de comptes de stockage standard et premium nécessaires pour la charge de travail. Faites défiler vers la droite pour afficher le type de stockage Azure et le compte de stockage pouvant être utilisés pour un serveur source.
	- Se termine et trie le reste de la table en fonction du type de stockage (standard ou premium) affecté à une machine virtuelle et du nombre de disques attachés. Pour toutes les machines virtuelles qui répondent aux exigences de sauvegarde d’Azure, la colonne A (La machine virtuelle est-elle qualifiée ?) affiche Oui. Si une machine virtuelle ne peut pas être sauvegardée sur Azure, une erreur s’affiche.

	![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification.png)


- **Exemple**: pour les six machines virtuelles avec les valeurs indiquées dans le tableau, l’outil calcule et affecte la meilleure correspondance de machine virtuelle Azure, ainsi que les exigences de stockage Azure.

	![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-2.png)

- Dans le résultat de l’exemple, notez les points suivants :
	- Pour cinq machines virtuelles, deux comptes de stockage standard et un compte de stockage premium sont nécessaires. 
	-  VM 3 ne se qualifie pas pour la protection, car un ou plusieurs disques ont un volume supérieur à 1 To.
	-  VM1 et VM2 peuvent utiliser le premier compte de stockage standard
	-  VM4 peut utiliser le deuxième compte de stockage standard
	-  VM5 et VM6 ont besoin d’un compte de stockage premium et peuvent tous les deux utiliser un compte simple.

	![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-3.png)

	>[AZURE.NOTE]Les E/S par seconde du stockage sont calculées au niveau de la machine virtuelle et non au niveau du disque. Si l’un des disques d’une machine virtuelle source présente une valeur d’E/S par seconde supérieure à 500, mais que le nombre total d’E/S par seconde de la machine virtuelle est compris dans les limites de machine virtuelle Azure standard prises en charge, et si toutes les autres valeurs (nombre de disques, nombre de cartes réseau, nombre de cœurs de processeur, taille de la mémoire) sont comprises dans les limites de machine virtuelle standard, l’outil choisit une machine virtuelle standard plutôt que le stockage premium. Vous devez mettre à jour manuellement la correspondance des tailles de cellule Azure avec la machine virtuelle de série DS ou GS appropriée.

	>[AZURE.NOTE]La première colonne est une colonne de validation pour les machines virtuelles, les disques et les taux de changement. Les E/S par seconde du stockage sont calculées au niveau de la machine virtuelle et non au niveau du disque. Si l’un des disques d’une machine virtuelle source présente une valeur d’E/S par seconde supérieure à 500, mais que le nombre total d’E/S par seconde de la machine virtuelle est compris dans les limites de machine virtuelle Azure standard prises en charge, et si toutes les autres valeurs (nombre de disques, nombre de cartes réseau, nombre de cœurs de processeur, taille de la mémoire) sont comprises dans les limites de machine virtuelle standard, l’outil choisit une machine virtuelle standard plutôt que le stockage premium. Vous devez mettre à jour manuellement la correspondance des tailles de cellule Azure avec la machine virtuelle de série DS ou GS appropriée.

5. Une fois que tous les détails sont en place, cliquez sur **Envoyer des données à l’outil Planificateur** pour ouvrir **Capacity Planner** les charges de travail sont mis en surbrillance, afin d’indiquer si elles sont éligibles ou non à la protection.


### Exécuter l’outil de planification de la capacité

1.	Renseignez la feuille de calcul **Capacity Planner**. Le mot « Workload » apparaît dans la cellule de source des entrées Infra pour indiquer les entrées de la feuille de calcul **Qualification de la charge de travail**.  
2.	Modifiez les paramètres que vous souhaitez modifier, puis cliquez sur **Envoyer des données à l’outil Planificateur**. 

	![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1210_2015-->
