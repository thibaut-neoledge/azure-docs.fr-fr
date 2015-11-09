<properties
   pageTitle="Mise à niveau des applications Service Fabric"
   description="Cet article présente la mise à niveau d'une application Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>


# Mise à niveau des applications Service Fabric


Une application Service Fabric est une collection de services. Pendant une mise à niveau, Service Fabric compare le nouveau [manifeste d'application](service-fabric-application-model.md#describe-an-application) à la version précédente et détermine les services qui, dans l'application, nécessitent des mises à jour. À cette fin, les numéros de version dans les manifestes de service sont comparés à la version précédente. Si un service n'a pas changé, ce service n'est pas mis à niveau.

## Vue d'ensemble des mises à niveau propagées
Une mise à niveau d'application propagée s'effectue par étapes. À chaque étape, la mise à niveau est appliquée à un sous-ensemble de nœuds du cluster, appelé domaine de mise à niveau. Ainsi, l'application demeure disponible tout au long de la mise à niveau. Au cours de la mise à niveau, le cluster peut contenir une combinaison des versions ancienne et nouvelle. De ce fait, les deux versions doivent être mutuellement compatibles. Si ce n'est pas le cas, l'administrateur d'application doit effectuer une mise à niveau échelonnée pour maintenir la disponibilité. Pour cela, il effectue une mise à niveau avec une version intermédiaire de l'application qui est compatible avec la version précédente, puis procède à une mise à niveau vers la version finale.

Les domaines de mise à niveau sont spécifiés dans le manifeste de cluster quand vous configurez le cluster. Les domaines de mise à niveau ne sont pas censés recevoir les mises à jour dans un ordre particulier. Un domaine de mise à niveau est une unité logique de déploiement pour une application. Grâce aux domaines de mise à niveau, les services demeurent hautement disponibles pendant une mise à niveau.

Une mise à niveau non propagée est possible si elle est appliquée à tous les nœuds du cluster, ce qui est le cas quand l'application n'a qu'un seul domaine de mise à niveau. Cette méthode n'est pas recommandée, car le service est arrêté et indisponible au moment de la mise à niveau. En outre, Azure ne fournit aucune garantie quand un cluster est configuré avec un seul domaine de mise à niveau.

## Vérifications d'intégrité pendant les mises à niveau
Pour une mise à niveau, vous devez définir des stratégies d'intégrité (ou utiliser éventuellement des valeurs par défaut). Une mise à niveau est considérée comme ayant réussi quand tous les domaines de mise à niveau sont mis à niveau dans les délais spécifiés et que tous les domaines de mise à niveau sont considérés comme intègres. Un domaine de mise à niveau est considéré comme intègre quand il passe avec succès toutes les vérifications d'intégrité spécifiées dans la stratégie d'intégrité (par exemple, celle-ci peut imposer que tous les services dans une instance d'application soient <em>intègres</em>, l'intégrité étant définie par Service Fabric).

Pendant une mise à jour effectuée par Service Fabric, les stratégies et vérifications d'intégrité sont indépendantes du service et de l'application. Autrement dit, aucun test spécifique au service n'est mené. Par exemple, votre service nécessite un débit minimal. Toutefois, comme Service Fabric ne dispose pas des informations permettant de le vérifier, il ne vérifie pas le débit tel que défini pour votre application. Reportez-vous aux [articles sur l'intégrité](service-fabric-health-introduction.md) pour connaître les vérifications effectuées ; les vérifications pendant la mise à jour déterminent, entre autres, si le package d'application a été correctement copié et si l'instance a été démarrée.

L'intégrité de l'application est un regroupement des entités enfants de l'application. En bref, Service Fabric évalue l'intégrité de l'application au travers de l'intégrité établie au sujet de celle-ci et de l'intégrité de la totalité de ses services. L'intégrité des services d'application fait l'objet d'une évaluation supplémentaire par l'agrégation de l'intégrité de leurs enfants, tels que le réplica de service. Une fois que la stratégie d'intégrité d'application est satisfaite, la mise à niveau peut être poursuivie. Par contre, si la stratégie d'intégrité est violée, la mise à niveau de l'application échoue.

## Modes de mise à niveau

Le mode commun (et recommandé) pour la mise à niveau est le mode surveillé. Le mode surveillé effectue la mise à niveau sur un domaine de mise à niveau spécifique, puis, si toutes les vérifications d'intégrité sont satisfaites (suivant la stratégie spécifiée), il passe automatiquement au domaine de mise à niveau suivant, et ainsi de suite. Si les vérifications d'intégrité échouent et/ou que les délais sont atteints, la mise à niveau est restaurée pour le domaine de mise à niveau, ou le système bascule sur le mode manuel non surveillé si cette option a été sélectionnée au moment de la mise à niveau.

Le mode manuel non surveillé nécessite une intervention manuelle après chaque mise à niveau sur un domaine de mise à niveau pour lancer la mise à niveau sur le domaine de mise à niveau suivant. Service Fabric ne procède à aucune vérification d'intégrité ; il appartient à l'opérateur d'effectuer les vérifications d'intégrité ou d'état avant de commencer la mise à niveau dans le domaine de mise à niveau suivant.

## Organigramme de la mise à niveau d'application

L'organigramme ci-dessous facilite la compréhension du processus de mise à niveau d'une application Service Fabric. En particulier, le flux indique dans quelle mesure les délais, notamment *HealthCheckStableDuration*, *HealthCheckRetryTimeout* et *UpgradeHealthCheckInterval*, déterminent l'échec ou la réussite de la mise à niveau dans un domaine de mise à niveau donné.

![Processus de mise à niveau d'une application Service Fabric][image]


## Étapes suivantes

[Didacticiel de mise à niveau](service-fabric-application-upgrade-tutorial.md)

[Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md)

[Sérialisation des données](service-fabric-application-upgrade-data-serialization.md)

[Rubriques avancées](service-fabric-application-upgrade-advanced.md)

[Résolution des problèmes de mise à niveau d'une application](service-fabric-application-upgrade-troubleshooting.md)



[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
 

<!---HONumber=Nov15_HO1-->