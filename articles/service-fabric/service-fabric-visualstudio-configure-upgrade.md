---
title: Configuration de la mise à niveau d’une application Service Fabric | Microsoft Docs
description: Apprenez à configurer les paramètres de mise à niveau d'une application Service Fabric à l'aide de Microsoft Visual Studio.
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/29/2016
ms.author: cawa

---
# Configuration de la mise à niveau d’une application Service Fabric dans Visual Studio
Les outils Visual Studio pour Azure Service Fabric fournissent une prise en charge des mises à niveau pour la publication vers des clusters locaux ou distants. Le fait de mettre à niveau votre application vers une version plus récente au lieu de la remplacer durant les tests et le débogage présente deux avantages :

* Les données d’application ne sont pas perdues lors de la mise à niveau.
* La disponibilité reste élevée, car le service n’est pas interrompu au cours de la mise à niveau s’il y a suffisamment d’instances de service réparties sur plusieurs domaines de mise à niveau.

Une application peut faire l’objet de tests pendant sa mise à niveau.

## Paramètres nécessaires à la mise à niveau
Vous avez le choix entre deux types de déploiement : standard ou mise à niveau. Un déploiement standard efface les informations relatives à tout déploiement précédent ainsi que les données du cluster, tandis qu’un déploiement de mise à niveau les conserve. Lorsque vous mettez à niveau une application Service Fabric dans Visual Studio, vous devez fournir les paramètres de mise à niveau de l'application et les stratégies de contrôle d’intégrité. Les paramètres de mise à niveau de l’application permettent de contrôler la mise à niveau, tandis que les stratégies de contrôle d’intégrité déterminent si la mise à niveau a réussi. Pour en savoir plus, consultez [Mise à niveau d’une application Service Fabric : paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Il existe trois modes de mise à niveau : *Monitored*, *UnmonitoredAuto* et *UnmonitoredManual*.

* Une mise à niveau Monitored automatise la mise à niveau et le contrôle d’intégrité de l’application.
* Une mise à niveau UnmonitoredAuto automatise la mise à niveau, mais ignore le contrôle d’intégrité de l’application.
* Quand vous effectuez une mise à niveau UnmonitoredManual, vous devez mettre à niveau manuellement chaque domaine de mise à niveau.

Chaque mode de mise à niveau nécessite différents jeux de paramètres. Pour en savoir plus sur les options de mise à niveau disponibles, consultez [Paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md).

## Mise à niveau d’une application Service Fabric dans Visual Studio
Si vous utilisez les outils Service Fabric de Visual Studio pour mettre à niveau une application Service Fabric, vous pouvez préciser si le processus de publication doit être une mise à niveau plutôt qu’un déploiement standard en cochant la case **Mettre à niveau l’application**.

### Pour configurer les paramètres de mise à niveau
1. Cliquez sur le bouton **Paramètres** en regard de la case à cocher. La boîte de dialogue **Modifier les paramètres de mise à niveau** s’affiche. La boîte de dialogue **Modifier les paramètres de mise à niveau** prend en charge les modes de mise à niveau Monitored, UnmonitoredAuto et UnmonitoredManual.
2. Sélectionnez le mode de mise à niveau que vous souhaitez utiliser, puis remplissez la grille de paramètres.
   
    Chaque paramètre a des valeurs par défaut. Le paramètre facultatif *DefaultServiceTypeHealthPolicy* accepte une entrée de table de hachage. Voici un exemple du format d'entrée de table de hachage pour *DefaultServiceTypeHealthPolicy* :
   
    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```
   
    *ServiceTypeHealthPolicyMap* est un autre paramètre facultatif qui acceptant une entrée de table de hachage au format suivant :
   
    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```
   
    Voici un exemple concret :
   
    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Si vous sélectionnez le mode de mise à niveau UnmonitoredManual, vous devrez démarrer manuellement une console PowerShell pour continuer et terminer le processus de mise à niveau. Pour en savoir plus sur le fonctionnement de la mise à niveau manuelle, consultez [Mise à niveau d’une application Service Fabric : rubriques avancées](service-fabric-application-upgrade-advanced.md).

## Mettre à niveau une application à l’aide de PowerShell
Vous pouvez utiliser les applets de commande PowerShell pour mettre à niveau une application Service Fabric. Pour plus d’informations, consultez [Didacticiel sur la mise à niveau d’une application Service Fabric](service-fabric-application-upgrade-tutorial.md) et [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx).

## Spécifier une stratégie de contrôle d’intégrité dans le fichier manifeste d’application
Chaque service d’une application Service Fabric peut avoir ses propres paramètres de stratégie de contrôle d'intégrité, qui remplacent alors les valeurs par défaut. Vous pouvez définir les valeurs de ces paramètres dans le fichier manifeste d’application.

L'exemple suivant montre comment appliquer une stratégie de contrôle d'intégrité unique pour chaque service du manifeste d'application.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## Étapes suivantes
Pour plus d'informations sur le déploiement d'une application, consultez la page [Déploiement d’une application existante dans Azure Service Fabric](service-fabric-deploy-existing-app.md).

<!---HONumber=AcomDC_0803_2016-->