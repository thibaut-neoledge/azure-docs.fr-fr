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
   ms.date="01/11/2016"
   ms.author="chackdan"/>

# Montée ou descente en puissance de cluster Service Fabric par ajout ou suppression de machines virtuelles.

Vous pouvez mettre à l’échelle des clusters Service Fabric pour qu’ils correspondent à la demande, en ajoutant ou en supprimant des machines virtuelles.

>[AZURE.NOTE]Cela suppose que votre abonnement a suffisamment de cœurs pour ajouter les nouvelles machines virtuelles qui composeront ce cluster.


## Mise à l’échelle manuelle d’un cluster Service Fabric

Si votre cluster comporte plusieurs types de nœuds, vous devrez ajouter des machines virtuelles à des types de nœuds spécifiques ou en supprimer. Vous pouvez ajouter des machines virtuelles à un type de nœud et supprimer des machines virtuelles d'un autre type de nœud, dans le même déploiement.

### Mise à niveau d'un cluster déployé à l'aide du portail

Étant donné qu'il s'agit d'un processus complexe, nous avons chargé un module PowerShell sur un référentiel Git qui l'effectue pour vous.

**Étape 1** : copiez ce dossier sur votre ordinateur à partir de ce [référentiel Git](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers).

**Étape 2** : assurez-vous que le Kit de développement logiciel (SDK) Azure 1.0 (ou une version supérieure) est installé sur votre ordinateur.

**Étape 3**: ouvrez une fenêtre PowerShell et importez ServiceFabricRPHelpers.psm.

```
Remove-Module ServiceFabricRPHelpers
```

Importez le module PowerShell que vous venez de copier. Vous pouvez simplement copier la commande suivante et modifier le chemin d'accès à .psm1 pour refléter celui de votre ordinateur.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

 **Étape 4** : connectez-vous à votre compte Azure.

```
Login-AzureRmAccount
```

Exécutez la commande Invoke-ServiceFabricRPClusterScaleUpgrade. Vérifiez que le nom du groupe de ressources et l'abonnement sont corrects.

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName <string> -SubscriptionId <string>
```

Voici un exemple renseigné de la même commande PS

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName chackod02 -SubscriptionId 18ad2z84-84fa-4798-ad71-e70c07af879f
```

  **Étape 5** : la commande va maintenant récupérer les informations du cluster et vous guidera dans tous les types de nœuds ; tout d'abord en vous indiquant le nombre actuel de machines virtuelles/nœuds pour ce type de nœud, puis en vous demandant d'indiquer le nouveau nombre de machines virtuelles/nœuds.

 **Pour la mise à l'échelle supérieure de ce type de nœud**, spécifiez un nombre plus élevé que le nombre actuel de machines virtuelles.

**Pour la mise à l'échelle inférieure de ce type de nœud**, spécifiez un nombre moins élevé que le nombre actuel de machines virtuelles.

Ces invites parcourent tous les types de nœuds en boucle. Si votre cluster n'a qu'un seul type de nœud, alors l'invite ne s'affiche qu'une seule fois.
 
  **Étape 6** : si vous ajoutez des machines virtuelles, une invite s'affiche vous demandant de fournir l'ID d'utilisateur et le mot de passe du bureau à distance pour les machines virtuelles que vous ajoutez.
 
**Étape 7** : dans la fenêtre de sortie, des messages vous informant de la progression de votre déploiement s'affichent. Une fois le déploiement terminé, votre cluster doit avoir le nombre de nœuds que vous avez spécifié à l'étape 5.


![ScaleupDownPSOut][ScaleupDownPSOut]


**Étape 8** : s'il s'agissait d'une demande de mise à l'échelle inférieure, alors il vous reste une étape à exécuter, celle de la suppression de machines virtuelles. Le script désactive toutes les machines virtuelles que vous voulez supprimer, c'est-à-dire qu'il n'existe aucun réplica d'application ou du système sur ces nœuds/machines virtuelles. Vous pouvez désormais supprimer ces machines virtuelles.

**REMARQUE** : même si les nœuds désactivés ne sont plus utilisés par votre cluster SF, vous devez supprimer les machines virtuelles désactivées afin de ne pas être facturé pour celles-ci.

**Étape 8.1** : connectez-vous au portail Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

**Étape 8.2** : accédez à la ressource de cluster à laquelle vous avez appliqué la mise à l'échelle inférieure et cliquez sur « Tous les paramètres » dans le composant web Essentials.

**Étape 8.3 **: cliquez sur Types de nœuds. Les nœuds désactivés s'affichent. Dans cette image, chackodnt15, chackodnt24, chackodnt25 et chackodnt26 sont les machines virtuelles que je dois maintenant supprimer.

![DeactivatedNodeList][DeactivatedNodeList]

**Étape 8.4** : supprimez ces machines virtuelles par le biais de PS ou du portail. Lorsqu'elles sont supprimées, vous avez terminé la mise à l'échelle inférieure de votre cluster.

```
Remove-AzureRmResource -ResourceName <Deactivated Node name without the understore> -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName <Resource Group name> -ApiVersion <Api version>
```
Voici un exemple renseigné de cette commande

```
Remove-AzureRmResource -ResourceName chackodnt26 -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName chackod02 -ApiVersion 2015-05-01-preview
```

### Mise à niveau d'un cluster déployé à l'aide de PowerShell/CLI ARM

Si vous aviez déployé votre cluster initialement à l'aide d'un modèle ARM, vous devez modifier le nombre de machines virtuelles pour un type de nœud donné et toutes les ressources qui prennent en charge la machine virtuelle. Le nombre minimal de machines virtuelles autorisées pour le type de nœud principal est de 5 pour les clusters de production et de 3 pour les clusters de test.

Une fois que vous avez le nouveau modèle, vous pouvez le déployer avec ARM à l'aide du même groupe de ressources que celui du cluster que vous mettez à niveau.


## Mise à l’échelle automatique d’un cluster Service Fabric

Pour l’instant, les clusters Service Fabric ne gèrent pas la mise à l'échelle automatique. Dans un avenir proche, les clusters seront créés par-dessus les jeux de mise à l’échelle de machine virtuelle (VMSS). La mise à l’échelle automatique sera alors possible et se comportera de manière similaire à la mise à l’échelle automatique disponible dans Cloud Services.


## Étapes suivantes

- [En savoir plus sur les mises à niveau de cluster](service-fabric-cluster-upgrade.md)
- [En savoir plus sur le partitionnement de services avec état pour une mise à l’échelle maximale](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ScaleupDownPSOut]: ./media/service-fabric-cluster-scale-up-down/ScaleupDownPSOut.png
[DeactivatedNodeList]: ./media/service-fabric-cluster-scale-up-down/DeactivatedNodeList.png

<!---HONumber=AcomDC_0114_2016-->