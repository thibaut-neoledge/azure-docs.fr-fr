<properties
   pageTitle="Montée ou descente en puissance d’un cluster Service Fabric | Microsoft Azure"
   description="Mise à l’échelle d’un cluster de Service Fabric pour qu’il corresponde à la demande en ajoutant ou en supprimant des nœuds de machine virtuelle"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/03/2015"
   ms.author="chackdan"/>

# Montée ou descente en puissance de cluster Service Fabric par ajout ou suppression de machines virtuelles.

Vous pouvez mettre à l’échelle des clusters Service Fabric pour qu’ils correspondent à la demande, en ajoutant ou en supprimant des machines virtuelles.

>[AZURE.NOTE]Cela suppose que votre abonnement a suffisamment de cœurs pour ajouter les nouvelles machines virtuelles qui composeront ce cluster.


## Mise à l’échelle manuelle d’un cluster Service Fabric

### Choisir le type de nœud à mettre à l’échelle

Si votre cluster comporte plusieurs types de nœud, vous devrez maintenant ajouter ou supprimer des ordinateurs virtuels de types de nœud spécifiques.

1. Connectez-vous au portail Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

2. Accédez aux clusters Service Fabric ![BrowseServiceFabricClusterResource][BrowseServiceFabricClusterResource]

3. Sélectionnez le cluster que vous souhaitez faire monter ou descendre en puissance

4. Accédez au panneau des paramètres sur le tableau de bord de cluster. Si vous ne voyez pas le panneau Paramètres, cliquez sur « Tous les paramètres » sur la partie principale du tableau de bord de cluster.

5. Cliquez sur les types de nœud, ce qui ouvrira le panneau de la liste des types de nœud.

7. Cliquez sur le type de nœud que vous souhaitez faire monter ou descendre en puissance, ce qui ouvrira le panneau des détails du type de nœud.

### Montée en puissance par ajout de nœuds

Ajustez le nombre d'ordinateurs virtuels souhaité et enregistrez. Les machines virtuelles/nœuds seront ajoutées une fois le déploiement terminé.

### Descente en puissance par suppression de nœuds

La suppression de nœuds est un processus en deux étapes :

1. Ajustez le nombre de machines virtuelles souhaité et enregistrez. L'extrémité inférieure du curseur indique la configuration minimale requise pour les machines virtuelles pour ce type de nœud.

  >[AZURE.NOTE]Vous devez conserver au moins cinq machines virtuelles pour le type de nœud principal.

	Once that deployment is complete, you will get notified of the VM names that can now be deleted. You now need to navigate to the VM resource and delete it.

2. Retournez au tableau de bord de cluster et cliquez sur le groupe de ressources. Le panneau Groupe de ressources s’ouvre alors.

3. Regardez sous Résumé ou ouvrez la liste des ressources en cliquant sur « ... »

4. Cliquez sur le nom de la machine virtuelle dont le système a indiqué qu’elle peut être supprimée.

5. Cliquez sur l’icône Supprimer pour supprimer la machine virtuelle.

## Mise à l’échelle automatique d’un cluster Service Fabric

Pour l’instant, les clusters Service Fabric ne gèrent pas la mise à l'échelle automatique. Dans un avenir proche, les clusters seront créés par-dessus les jeux de mise à l’échelle de machine virtuelle (VMSS). La mise à l’échelle automatique sera alors possible et se comportera de manière similaire à la mise à l’échelle automatique disponible dans Cloud Services.

## Mise à l’échelle à l’aide de PowerShell/de l’interface de ligne de commande

Cet article a abordé la mise à l’échelle des clusters à l'aide du portail. Toutefois, vous pouvez effectuer les mêmes actions à partir de la ligne de commande à l'aide de commandes ARM sur la ressource de cluster. La réponse GET de ClusterResource fournit la liste des nœuds qui ont été désactivés.

## Étapes suivantes

- [En savoir plus sur les mises à niveau de cluster](service-fabric-cluster-upgrade.md)
- [En savoir plus sur le partitionnement de services avec état pour une mise à l’échelle maximale](service-fabric-concepts-partitioning.md)


<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=Nov15_HO4-->