
<properties
	pageTitle="Azure Site Recovery : réplication de machines virtuelles Hyper-V (un seul serveur VMM)"
	description="Azure Site Recovery coordonne la réplication, le basculement et la récupération des ordinateurs virtuels Hyper-V situés dans des clouds VMM locaux sur Azure ou un cloud VMM secondaire."
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
	ms.date="12/01/2015"
	ms.author="raynew"/>

#  Azure Site Recovery : réplication de machines virtuelles Hyper-V (un seul serveur VMM)

Le service Microsoft Azure Site Recovery offre une solution fiable de continuité d’activité et de récupération d’urgence, en orchestrant et en automatisant la réplication de serveurs physiques locaux et les machines virtuelles vers Azure ou un centre de données secondaire local. Cet article décrit comment déployer Site Recovery pour protéger des machines virtuelles Hyper-V qui se trouvent dans un cloud VMM lorsque vous avez uniquement un seul serveur VMM dans votre déploiement. Si vous avez des questions après la lecture de cet article, posez-les sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Vue d'ensemble

Vous pouvez répliquer des machines virtuelles Hyper-V de deux manières :

- Répliquer des machines virtuelles Hyper-V qui se trouvent sur des hôtes Hyper-V qui ne se trouvent pas dans un cloud VMM dans Azure
- Répliquer des machines virtuelles Hyper-V qui se trouvent sur des hôtes Hyper-V dans un cloud VMM dans Azure
- Répliquer des machines virtuelles Hyper-V qui se trouvent sur des hôtes Hyper-V dans un cloud VMM dans Azure

Mais que se passe-t-il si vous souhaitez utiliser VMM alors que vous ne disposez que d’un seul serveur VMM dans votre infrastructure ?

Dans ce cas, vous avez deux options :

- Répliquer vos machines virtuelles Hyper-V dans vos clouds VMM dans Azure. Vous trouverez plus d’informations sur ce scénario [ici](site-recovery-vmm-to-azure.md).
- Répliquer entre des clouds sur le serveur VMM unique.

Nous vous recommandons la première option, car le basculement et la récupération ne sont pas transparentes dans la deuxième option, et elle comporte également un certain nombre d’étapes manuelles.


### Réplication entre des sites avec un seul serveur VMM autonome

Dans ce scénario, vous déployez votre serveur VMM unique autonome comme machine virtuelle sur un site primaire et répliquez cette machine virtuelle sur un site secondaire avec Site Recovery et le réplica Hyper-V. Pour ce faire :

1. Configurez VMM sur une machine virtuelle Hyper-V. Vous pouvez envisager de stocker l’instance SQL Server utilisée par VMM sur la même machine virtuelle De cette manière, vous pouvez économiser du temps, car une seule machine virtuelle doit être instanciée. Si vous souhaitez utiliser une instance distante, vous devrez récupérer cette instance avant de récupérer VMM en cas de défaillance.
2. Assurez-vous que le serveur VMM ait au moins deux clouds configurés. Un cloud contient les machines virtuelles à répliquer et l’autre cloud sert d’emplacement secondaire. Le cloud contient des machines virtuelles à protéger. Vous devez avoir un ou plusieurs groupes hôtes VMM avec un ou plusieurs serveurs hôtes Hyper-V dans chaque groupe hôte et au moins une machine virtuelle Hyper-V sur chaque serveur hôte.
2. Créez un coffre Site Recovery, générez et téléchargez une clé d’inscription du coffre et inscrivez le serveur VMM dans le coffre.
2. Configurez un ou plusieurs clouds dans la machine virtuelle VMM et ajoutez les hôtes Hyper-V qui contiennent les machines virtuelles à protéger à ces clouds.
3. Dans Azure Site Recovery, configurez les paramètres de protection pour les clouds. Dans l’emplacement source et l’emplacement cible, spécifiez le nom du serveur VMM unique. Si vous configurez le mappage réseau, vous mappez le réseau de machines virtuelles pour le cloud qui contient des machines virtuelles à protéger sur le réseau de machines virtuelles pour le cloud dans lequel vous souhaitez effectuer la réplication.
4. Activez la réplication pour les machines virtuelles que vous souhaitez protéger avec **Via le réseau (Over the network)** comme méthode de réplication, car les deux clouds sont situés sur le même serveur.
4. Dans la console Gestionnaire Hyper-V, activez la réplication Hyper-V sur l’hôte Hyper-V qui contient la machine virtuelle VMM et activez la réplication sur la machine virtuelle. Assurez-vous que vous n’ajoutez pas la machine virtuelle VMM à des clouds protégés par Site Recovery afin de garantir que les paramètres de réplication Hyper-V ne sont pas remplacés par Site Recovery.
5. Si vous souhaitez créer des plans de récupération, vous spécifiez le même serveur VMM source et cible. 

En cas de défaillance, vous récupérez les charges de travail sur les machines virtuelles Hyper-V comme suit :

1. Basculez manuellement le réplica de machine virtuelle VMM sur le site secondaire à l’aide du Gestionnaire Hyper-V avec un basculement planifié.
2. Une fois la machine virtuelle VMM récupérée, vous pouvez ouvrir une session dans le Hyper-V Recovery Manager à partir du site secondaire et effectuer un basculement non planifié des machines virtuelles du site principal vers le site secondaire.
3. Lorsque le basculement non planifié est terminé, l’utilisateur peut accéder à toutes les ressources sur le site principal.

Notez que la machine virtuelle VMM devra être basculée manuellement vers le site secondaire avant que les charges de travail ne puissent être basculées.

![Serveur VMM virtuel autonome](./media/site-recovery-single-vmm/single-vmm-standalone.png)

### Réplication entre des sites avec un seul serveur VMM dans un cluster étendu.

Au lieu de déployer un serveur VMM autonome en tant que machine virtuelle répliquée vers un site secondaire, vous pouvez rendre VMM hautement disponible en le déployant comme machine virtuelle dans un cluster de basculement Windows pour fournir une résilience de la charge de travail et une protection contre les défaillances matérielles. Pour effectuer un déploiement avec Site Recovery, VMM doit être déployé dans un cluster étendu sur des sites géographiquement distincts. Pour ce faire :

1. Vous installez VMM sur des machines virtuelles dans un cluster de basculement Windows et sélectionnez l’option permettant d’exécuter le serveur comme étant hautement disponible lors de configuration.
2. L’instance SQL Server utilisée par VMM doit être répliquée avec des groupes de disponibilité SQL Server AlwaysOn pour qu’un réplica de la base de données se trouve sur le site secondaire. 

En cas de défaillance, le serveur VMM et la base de données SQL Server correspondante sont basculés et accessibles à partir du site secondaire.

![Serveur VMM virtuel en cluster](./media/site-recovery-single-vmm/single-vmm-cluster.png)




 

<!---HONumber=AcomDC_1203_2015-->