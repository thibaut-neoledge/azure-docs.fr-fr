<properties
   pageTitle="Architecture du Gestionnaire de ressources | Microsoft Azure"
   description="Vue d’ensemble de l’architecture du Gestionnaire de ressources Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Vue d’ensemble de l’architecture du Gestionnaire de ressources
Pour gérer les ressources de votre cluster, le Gestionnaire de ressources du cluster doit connaître plusieurs éléments. Il doit connaître les services actuels et le volume actuel (ou par défaut) des ressources que ceux-ci consomment. Il doit connaître la capacité réelle des nœuds du cluster et donc la quantité de ressources disponibles dans le cluster dans son ensemble et restant sur un nœud particulier. Nous devons gérer le fait que la consommation des ressources d’un service donné peut changer au fil du temps, ainsi que le fait que les services prennent généralement en charge plusieurs ressources. Dans de nombreux services différents, il peut y avoir aussi bien des ressources « réelles », comme la mémoire et la consommation de disque (généralement utilisées dans différents types de service), ainsi que des ressources gérées uniquement par un service donné.

Pour compliquer les choses, les propriétaires et les opérateurs du cluster sont parfois différents des auteurs du service ou une même personne a plusieurs rôles. Par exemple, lorsque vous développez votre service, vous connaissez en partie ce dont il a besoin en termes de ressources et comment les différents composants doivent être déployés idéalement. Toutefois, comme vous gérez un incident de site actif pour ce service en production, vous avez d’autres tâches à effectuer qui nécessitent différents outils. En outre, ni le cluster ni les services eux-mêmes ne sont configurés statiquement : le nombre de nœuds du cluster peut augmenter ou diminuer, les nœuds de différentes tailles peuvent aller et venir et les services peuvent changer d’allocation de ressources, être créés et supprimés. Les mises à niveau ou d’autres opérations de gestion peuvent impacter le cluster, et bien évidemment, il y a toujours un risque de défaillance.

Notre gestionnaire de ressources doit connaître de nombreux éléments sur le cluster lui-même, ainsi que les exigences de services spécifiques. Pour ce faire, dans Service Fabric, nous avons deux agents du Gestionnaire de ressources qui s’exécutent sur des nœuds individuels pour agréger les informations sur la consommation des ressources locales, ainsi qu’un service Gestionnaire de ressources centralisé et à tolérance de pannes qui regroupe toutes les informations sur les services et le cluster, et qui réagit aux modifications en fonction de la configuration d’état souhaité du service et du cluster. La tolérance de pannes est obtenue par le mécanisme que vous avez l’habitude de suivre pour vos services, à savoir la réplication de l’état du service dans un certain nombre de réplicas (généralement 7).

![Architecture de l'équilibreur de ressources][Image1]

Prenons comme exemple ce schéma (Figure 1). Au moment de l’exécution, de nombreux changements peuvent se produire : par exemple, la modification de la quantité de ressources consommées par les services, des échecs du service, certains nœuds qui rejoignent et quittent le cluster, etc. Toutes les modifications sur un nœud spécifique sont agrégées et régulièrement envoyées au service Gestionnaire de ressources central (1,2) où elles sont agrégées à nouveau, analysées et stockées. À une fréquence de quelques secondes, le service central examine toutes les modifications et détermine si des actions sont nécessaires (3). Par exemple, il peut remarquer que des nœuds vides ont été ajoutés au cluster et décider de déplacer certains services vers ces nœuds. Il peut également remarquer qu’un nœud particulier est surchargé ou que certains services ont échoué (ou ont été supprimés), en libérant des ressources sur d’autres nœuds.

Nous allons examiner le schéma suivant (Figure 2) et voir ce qui se passe dans cet exemple : supposons que le Gestionnaire de ressources détermine que des modifications sont nécessaires. Il communique avec d’autres services système (en particulier Failover Manager) pour apporter les modifications nécessaires. Les demandes de modification sont ensuite envoyées aux nœuds appropriés (4). Dans ce cas, nous supposons que le Gestionnaire de ressources a remarqué que le nœud 5 est surchargé et a donc décidé de déplacer le service B de N5 à N4. À la fin de la reconfiguration (5), le cluster a l’aspect suivant :

![Architecture de l'équilibreur de ressources][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [En savoir plus sur l’architecture du Gestionnaire de ressources de cluster](service-fabric-cluster-resource-manager-architecture.md)
- [Décrire le cluster](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!------HONumber=AcomDC_0309_2016-->