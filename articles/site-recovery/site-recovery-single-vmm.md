
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
	ms.date="08/24/2016"
	ms.author="raynew"/>

#  Répliquer des machines virtuelles Hyper-V sur un seul serveur VMM

Lisez cet article pour découvrir comment répliquer des machines virtuelles Hyper-V situées sur un serveur hôte Hyper-V dans un cloud VMM quand vous avez uniquement un seul serveur VMM dans votre déploiement.

Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) différents pour créer et utiliser des ressources : le déploiement Azure Resource Manager et le déploiement classique. De plus, Azure propose deux portails : le portail Azure Classic, qui prend en charge le modèle de déploiement classique, et le portail Azure, qui gère les deux modèles de déploiement. Cet article contient des instructions pour la configuration de la réplication dans le portail Azure.


Si, après avoir lu cet article, vous avez des questions, posez-les dans les commentaires Disqus au bas de cet article ou sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Vue d'ensemble

Si vous souhaitez répliquer des machines virtuelles Hyper-V situées dans VMM et que vous avez un seul serveur VMM, vous pouvez [répliquer vers Azure](site-recovery-vmm-to-azure.md), ou entre des clouds sur le serveur VMM unique.

Nous vous recommandons de répliquer vers Azure, car le basculement et la récupération ne sont pas transparents lors de la réplication entre clouds, et un certain nombre d’étapes manuelles devient nécessaire. Si vous ne souhaitez pas répliquer en utilisant uniquement le serveur VMM, procédez comme suit :

- Réplication avec un seul serveur VMM autonome
- Répliquer avec un seul serveur VMM déployé dans un cluster Windows étiré


## Réplication entre des sites avec un seul serveur VMM autonome

![Serveur VMM virtuel autonome](./media/site-recovery-single-vmm/single-vmm-standalone.png)

Dans ce scénario, vous déployez le serveur VMM unique comme machine virtuelle sur le site principal et répliquez cette machine virtuelle sur un site secondaire avec Site Recovery et le réplica Hyper-V.

1. Configurez VMM sur une machine virtuelle Hyper-V. Pour gagner du temps, nous vous conseillons d’installer l’instance SQL Server utilisée par VMM sur la même machine virtuelle. Si vous souhaitez utiliser une instance distante de SQL Server, vous devrez récupérer cette instance pour pouvoir récupérer VMM en cas de défaillance.
2. Vérifiez que le serveur VMM a au moins deux clouds configurés. Un cloud contient les machines virtuelles à répliquer et l’autre cloud sert d’emplacement secondaire. Le cloud qui contient les machines virtuelles que vous souhaitez protéger doit avoir :

	- un ou plusieurs groupes hôtes VMM contenant un ou plusieurs serveurs hôtes Hyper-V dans chaque groupe hôte ;
	- au moins une machine virtuelle Hyper-V sur chaque serveur hôte Hyper-V.

3. Créez un coffre Recovery Services, générez et téléchargez une clé d’inscription du coffre et inscrivez le serveur VMM dans le coffre. Pendant l’inscription, vous devez installer le fournisseur Azure Site Recovery sur le serveur VMM.
4. Configurez un ou plusieurs clouds dans la machine virtuelle VMM et ajoutez les hôtes Hyper-V à ces clouds.
3. configurer les paramètres de protection des clouds. Dans l’emplacement source et l’emplacement cible, spécifiez le nom du serveur VMM unique. Pour configurer le mappage réseau, vous mappez le réseau de machines virtuelles pour le cloud avec des machines virtuelles à protéger sur le réseau de machines virtuelles pour le cloud de réplication.
4. Activez la réplication initiale pour les machines virtuelles que vous souhaitez protéger via le réseau, car les deux clouds sont situés sur le même serveur.
4. Dans la console Gestionnaire Hyper-V, activez la réplication Hyper-V sur l’hôte Hyper-V qui contient la machine virtuelle VMM et activez la réplication sur la machine virtuelle. Assurez-vous que vous n’ajoutez pas la machine virtuelle VMM à des clouds protégés par Site Recovery. Cela garantit que les paramètres de réplication Hyper-V ne sont pas remplacés par Site Recovery.
5. Si vous souhaitez créer des plans de récupération, vous spécifiez le même serveur VMM source et cible.

Suivez les instructions de [cet article](site-recovery-vmm-to-vmm.md) pour créer un coffre, inscrire le serveur et configurer la protection.

### Que faire en cas de panne

Si une panne complète se produit et que vous avez besoin de fonctionner sur le site secondaire, procédez comme suit :

1.	Dans la console Hyper-V Manager sur le site secondaire, exécutez un basculement non planifié pour basculer la machine virtuelle VMM du site principal vers le site secondaire.
2.	Vérifiez que la machine virtuelle VMM est en cours d’exécution sur le site secondaire.
3.	Dans le coffre Recovery Services, exécutez un basculement non planifié pour les machines virtuelles de charge de travail du site principal vers des clouds secondaires. Pour effectuer le basculement non planifié des machines virtuelles, validez le basculement ou sélectionnez un autre point de récupération en fonction de vos besoins.
4.	Une fois le basculement non planifié terminé, les utilisateurs peuvent accéder aux ressources de la charge de travail sur le site secondaire.

Lorsque le site principal fonctionne à nouveau normalement, procédez comme suit :

1.	Dans la console Hyper-V Manager, activez la réplication inverse pour la machine virtuelle VMM, pour démarrer la réplication du site secondaire vers le site principal.
2.	Dans la console Hyper-V Manager, exécutez un basculement planifié pour effectuer une restauration de la machine virtuelle VMM vers le site principal. Validez le basculement pour le terminer. Ensuite, activez la réplication inverse pour commencer la réplication de VMM du site principal vers le site secondaire.
3.	Dans le coffre Recovery Services, activez la réplication inverse pour les machines virtuelles de charge de travail pour démarrer la réplication du site secondaire vers le site principal.
4.	Dans le coffre Recovery Services, exécutez un basculement planifié pour restaurer les machines virtuelles de charge de travail vers le site principal. Validez le basculement pour le terminer. Ensuite, activez la réplication inverse pour commencer la réplication des machines virtuelles de charge de travail du site principal vers le site secondaire.



## Réplication entre des sites avec un seul serveur VMM dans un cluster étendu.

![Serveur VMM virtuel en cluster](./media/site-recovery-single-vmm/single-vmm-cluster.png)

Au lieu de déployer un serveur VMM autonome en tant que machine virtuelle répliquée vers un site secondaire, vous pouvez rendre VMM hautement disponible en le déployant comme machine virtuelle dans un cluster de basculement Windows. pour fournir une résilience de la charge de travail et une protection contre les défaillances matérielles. Pour effectuer un déploiement avec Site Recovery, la machine virtuelle VMM doit être déployée dans un cluster étendu sur des sites géographiquement distincts. Pour ce faire :

1. Installez VMM sur des machines virtuelles dans un cluster de basculement Windows et sélectionnez l’option permettant d’exécuter le serveur comme étant hautement disponible lors de configuration.
2. L’instance SQL Server utilisée par VMM doit être répliquée avec des groupes de disponibilité SQL Server AlwaysOn pour qu’un réplica de la base de données se trouve sur le site secondaire.
3. Suivez les instructions de [cet article](site-recovery-vmm-to-vmm.md) pour créer un coffre, inscrire le serveur et configurer la protection. Vous devez inscrire chaque serveur VMM dans le cluster du coffre Recovery Services. Pour ce faire, vous installez le fournisseur sur un nœud actif et inscrivez le serveur VMM. Vous installez ensuite le fournisseur sur les autres nœuds.

### Que faire en cas de panne

En cas de défaillance, le serveur VMM et la base de données SQL Server correspondante sont basculés et accessibles sur le site secondaire.


## Étapes suivantes

[En savoir plus](site-recovery-vmm-to-vmm.md) sur le déploiement détaillé de Site Recovery pour la réplication VMM vers VMM.

<!---HONumber=AcomDC_0824_2016-->