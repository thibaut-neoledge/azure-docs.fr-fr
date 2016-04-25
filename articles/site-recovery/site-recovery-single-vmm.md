
<properties
	pageTitle="Azure Site Recovery : Répliquer des machines virtuelles Hyper-V sur un seul serveur VMM | Microsoft Azure"
	description="Cet article décrit comment répliquer des machines virtuelles Hyper-V quand vous avez uniquement un seul serveur VMM."
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
	ms.workload="backup-recovery"
	ms.date="03/30/2016"
	ms.author="raynew"/>

#  Répliquer des machines virtuelles Hyper-V sur un seul serveur VMM

Lisez cet article pour découvrir comment répliquer des machines virtuelles Hyper-V situées sur un serveur hôte Hyper-V dans un cloud VMM quand vous avez uniquement un seul serveur VMM dans votre déploiement.

Si, après avoir lu cet article, vous avez des questions, posez-les au bas de cet article ou sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Vue d’ensemble

Vous pouvez répliquer des machines virtuelles Hyper-V situées sur des hôtes Hyper-V dans des clouds VMM de deux façons :

- Réplication vers Azure. 
- Réplication vers un site VMM secondaire.

Que se passe-t-il si vous voulez effectuer la réplication vers un site VMM secondaire, mais que vous avez uniquement un seul serveur VMM dans votre déploiement ?

Dans ce scénario, vous avez deux options :

- Répliquer les machines virtuelles Hyper-V dans des clouds VMM vers Azure. Vous trouverez plus d’informations sur ce scénario [ici](site-recovery-vmm-to-azure.md).
- Répliquer entre des clouds sur le serveur VMM unique.

Nous vous recommandons la première option, car le basculement et la récupération ne sont pas transparents dans la deuxième option, et elle comporte également un certain nombre d’étapes manuelles. Si vous voulez effectuer la réplication entre des sites plutôt que vers Azure, vous avez deux options.


## Réplication entre des sites avec un seul serveur VMM autonome

Pour effectuer la réplication entre des sites dans ce scénario, vous déployez le serveur VMM unique comme machine virtuelle sur le site principal et répliquez cette machine virtuelle sur un site secondaire avec Site Recovery et le réplica Hyper-V. Pour ce faire :

1. Configurez VMM sur une machine virtuelle Hyper-V. Quand vous effectuez cette opération, nous vous conseillons d’envisager également de stocker l’instance SQL Server utilisée par VMM sur la même machine virtuelle De cette manière, vous pouvez économiser du temps, car une seule machine virtuelle doit être instanciée. Si vous souhaitez utiliser une instance distante de SQL Server, vous devrez récupérer cette instance avant de récupérer VMM en cas de défaillance.
2. Vérifiez que le serveur VMM a au moins deux clouds configurés. Un cloud contient les machines virtuelles à répliquer et l’autre cloud sert d’emplacement secondaire. Le cloud qui contient les machines virtuelles que vous souhaitez protéger doit avoir :

	- un ou plusieurs groupes hôtes VMM contenant un ou plusieurs serveurs hôtes Hyper-V dans chaque groupe hôte ;
	- au moins une machine virtuelle Hyper-V sur chaque serveur hôte Hyper-V.
3. Créez un coffre Site Recovery, générez et téléchargez une clé d’inscription du coffre et inscrivez le serveur VMM dans le coffre. Pendant l’inscription, vous devez installer le fournisseur Azure Site Recovery sur le serveur VMM.
4. Configurez un ou plusieurs clouds dans la machine virtuelle VMM et ajoutez les hôtes Hyper-V qui contiennent les machines virtuelles à protéger à ces clouds.
3. Dans Azure Site Recovery, configurez les paramètres de protection pour les clouds. Dans l’emplacement source et l’emplacement cible, spécifiez le nom du serveur VMM unique. Si vous configurez le mappage réseau, vous mappez le réseau de machines virtuelles pour le cloud qui contient des machines virtuelles à protéger sur le réseau de machines virtuelles pour le cloud dans lequel vous souhaitez effectuer la réplication.
4. Activez la réplication pour les machines virtuelles que vous souhaitez protéger avec **Via le réseau (Over the network)** comme méthode de réplication, car les deux clouds sont situés sur le même serveur.
4. Dans la console Gestionnaire Hyper-V, activez la réplication Hyper-V sur l’hôte Hyper-V qui contient la machine virtuelle VMM et activez la réplication sur la machine virtuelle. Assurez-vous que vous n’ajoutez pas la machine virtuelle VMM à des clouds protégés par Site Recovery afin de garantir que les paramètres de réplication Hyper-V ne sont pas remplacés par Site Recovery.
5. Si vous souhaitez créer des plans de récupération, vous spécifiez le même serveur VMM source et cible.

Suivez les instructions de [cet article](site-recovery-vmm-to-vmm.md) pour la création d’un archivage, l’obtention d'une clé, l’inscription du serveur et la configuration de la protection.

### Après une panne

En cas de défaillance, vous récupérez les charges de travail sur les machines virtuelles Hyper-V comme suit :

1. Basculez manuellement la machine virtuelle VMM sur le site secondaire à l’aide du Gestionnaire Hyper-V avec un basculement planifié. 
2. Une fois la machine virtuelle VMM récupérée, vous pouvez vous connecter au Gestionnaire de récupération Hyper-V à partir du site secondaire et effectuer un basculement non planifié des machines virtuelles du site principal vers le site secondaire. Notez que la machine virtuelle VMM doit être basculée manuellement vers le site secondaire avant que les machines virtuelles avec les charges de travail ne puissent être basculées.
3. Une fois que le basculement non planifié est terminé, l’utilisateur peut à nouveau accéder à toutes les ressources sur le site principal.


![Serveur VMM virtuel autonome](./media/site-recovery-single-vmm/single-vmm-standalone.png)

## Réplication entre des sites avec un seul serveur VMM dans un cluster étendu.

Au lieu de déployer un serveur VMM autonome en tant que machine virtuelle répliquée vers un site secondaire, vous pouvez rendre VMM hautement disponible en le déployant comme machine virtuelle dans un cluster de basculement Windows pour fournir une résilience de la charge de travail et une protection contre les défaillances matérielles. Pour effectuer un déploiement avec Site Recovery, la machine virtuelle VMM doit être déployée dans un cluster étendu sur des sites géographiquement distincts. Pour ce faire :

1. Installez VMM sur des machines virtuelles dans un cluster de basculement Windows et sélectionnez l’option permettant d’exécuter le serveur comme étant hautement disponible lors de configuration.
2. L’instance SQL Server utilisée par VMM doit être répliquée avec des groupes de disponibilité SQL Server AlwaysOn pour qu’un réplica de la base de données se trouve sur le site secondaire.

Notez que, quand vous configurez Site Recovery, vous devez inscrire chaque serveur VMM du cluster dans l’archivage Site Recovery. Pour ce faire, vous installez le fournisseur sur un nœud actif et terminez l’installation pour inscrire ce serveur VMM dans l’archivage. Vous installez ensuite le fournisseur sur les autres nœuds.
 
### Après une panne 

En cas de défaillance, le serveur VMM et la base de données SQL Server correspondante sont basculés et accessibles sur le site secondaire.

![Serveur VMM virtuel en cluster](./media/site-recovery-single-vmm/single-vmm-cluster.png)

## Étapes suivantes

[En savoir plus](site-recovery-vmm-to-vmm.md) sur le déploiement détaillé de Site Recovery pour la réplication VMM vers VMM.




 

<!---HONumber=AcomDC_0413_2016-->