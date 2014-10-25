<properties title="How to scale a website" pageTitle="How to scale a website" description="Learn how to scale your hosting plan in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic"></tags>

# Mise à l'échelle d'un site web

Dans la version préliminaire du portail Azure, vous pouvez définir manuellement le nombre d'instances de votre plan d'hébergement Web ou bien définir les paramètres pour qu'il soit automatiquement mis à l'échelle. Avant de configurer l'extensibilité de votre plan d'hébergement Web, tenez compte du fait que la mise à l'échelle est affectée par la taille des instances. Plus la taille est importante, plus il y a de cœurs et de mémoire. À nombre égal d'instances, les performances sont donc meilleures.

La mise à l'échelle affecte l'ensemble du plan d'hébergement Web. Quand vous créez un site web, vous avez le choix entre créer un plan d'hébergement web et utiliser un plan d'hébergement web existant. Une fois que vous disposez d'un plan d'hébergement Web, tous les sites partagent les mêmes instances, ils sont donc mis à l'échelle en même temps.

## Mise à l'échelle d'un plan d'hébergement Web

1. Dans la [version préliminaire du portail Azure][], cliquez sur **Browse**, puis sur **Web Sites**. Cliquez ensuite sur le nom du site Web pour ouvrir le volet correspondant.
2. La partie **Scale** sous l'objectif **Operations** du volet du site Web, vous indique l'état du plan d'hébergement Web : **Off** lorsque vous effectuez la mise à l'échelle de façon manuelle, **Performance** lorsque vous effectuez la mise à l'échelle en fonction d'une ou plusieurs mesures de performances et **Schedule** lorsque vous avez activé plusieurs profils de mise à l'échelle automatique.  
    ![Partie Scale][]
3. Lorsque vous cliquez sur cette partie, le volet **Scale** s'affiche. Un historique des mises à l'échelle automatiques de votre plan d'hébergement Web est affiché en haut du volet Scale.

    ![Volet Scale][]
4. Vous pouvez définir de façon manuelle le nombre de machines virtuelles qui exécutent votre plan d'hébergement Web avec le curseur **Instance**.
5. Si vous voulez que le nombre d'instances soit automatiquement ajusté en fonction de la charge, sélectionnez **Performance** sous **Autoscale Mode**. À ce stade, vous ne pouvez pas sélectionner **Schedule** dans la version préliminaire du portail Azure.  
    ![Volet Scale avec pourcentage UC][]
6. Une fois que vous avez sélectionné Performance, deux changements se produisent :
    -   **Instance Range** vous permet maintenant de choisir un nombre minimum et maximum d'instances. Autoscale vous maintient dans cette plage, quelle que soit la charge.
    -   Vous pouvez définir les mesures de performances dans la section **Target Metrics**.
7. La section **CPU Percentage** vous permet de définir un objectif pour la moyenne du processeur sur toutes les instances de votre plan d'hébergement Web. L'extension se produit lorsque la moyenne du processeur dépasse le nombre maximum défini.

Avec la mise à l'échelle automatique activée, **Performance** s'affiche dans le volet du site web et l'historique des mises à l'échelle s'affiche dans le graphique :

![Volet Scale avec pourcentage UC][1]

Notez que dans la version préliminaire du portail Azure, vous ne pouvez pas modifier le nombre d'instances d'un plan d'hébergement Web partagé.

## Mise à l'échelle avancée

Nouveauté dans la version préliminaire du portail Azure : vous pouvez effectuer une mise à l'échelle en fonction de mesures autres que le pourcentage processeur, et vous pouvez même définir un jeu de règles complexes pour l'extension et la réduction.

### Mise à l'échelle en fonction d'autres mesures de performances

En plus du processeur, vous pouvez baser la mise à l'échelle sur les facteurs suivants :

-   Average Memory - Si le pourcentage moyen de mémoire utilisée sur l'instance est au-dessus ou en dessous des seuils spécifiés, des instances sont ajoutées ou supprimées.
-   HTTP Queue Depth or Disk Queue Depth - Si le nombre de messages dans une des files d'attente de demandes HTTP ou Disk est au-dessus ou en dessous des seuils spécifiés, des instances sont ajoutées ou supprimées.

La mise à l'échelle en fonction d'une autre mesure peut se faire de deux manières. Si vous ne souhaitez effectuer la mise à l'échelle qu'en fonction d'une seule mesure, sélectionnez la flèche à côté du curseur **CPU Percentage**. Cette opération ouvre le volet Metric Details :

![Point d'entrée des mesures de mise à l'échelle][]

Pour appliquer la mise à l'échelle en fonction de plusieurs mesures, vous pouvez cliquer sur **Add Metric** dans la barre de commandes :

![Add metrics][]

Le volet Metric Detail contient toutes les commandes dont vous avez besoin pour définir votre profil de mise à l'échelle optimal. En haut, choisissez la nouvelle mesure selon laquelle appliquer la mise à l'échelle.

### Mise à l'échelle en plusieurs étapes

Sous le graphique de cette mesure, deux sections sont présentes : **Scale up rules** et **Scale down rules**. Votre service est étendu si **une des règles** d'extension est vérifiée. À l'inverse, votre service est réduit si **toutes les règles** de réduction sont vérifiées.

Pour chaque règle, vous pouvez choisir :

-   Condition - Greater than ou Less than
-   Threshold - nombre que cette métrique doit passer pour déclencher l'action
-   Over Past - nombre de minutes sur lequel la moyenne de cette métrique est calculée
-   Scale up or down by - taille de l'action de mise à l'échelle
-   Cool down - temps d'attente de cette règle entre la dernière action de mise à l'échelle et la prochaine

![Règles de mise à l'échelle multiples][]

Avec les règles de mise à l'échelle multiples, vous pouvez définir des stratégies d'extension (ou de réduction) plus agressives en fonction des performances. Par exemple, vous pouvez définir deux règles de mise à l'échelle :

1.  Extension d'une instance si le pourcentage du processeur est supérieur à 60 %
2.  Extension de trois instances si le pourcentage du processeur est supérieur à 85 %

Avec cette règle supplémentaire, si la charge dépasse 85 % avant une action de mise à l'échelle, vous obtenez deux instances supplémentaires au lieu d'une.

  [version préliminaire du portail Azure]: https://portal.azure.com/
  [Partie Scale]: ./media/insights-how-to-scale/Insights_ScalePartOff.png
  [Volet Scale]: ./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png
  [Volet Scale avec pourcentage UC]: ./media/insights-how-to-scale/Insights_ScaleBladeCPU.png
  [1]: ./media/insights-how-to-scale/Insights_ScalePartBladeOn.png
  [Point d'entrée des mesures de mise à l'échelle]: ./media/insights-how-to-scale/Insights_ScaleMetricChevron.png
  [Add metrics]: ./media/insights-how-to-scale/Insights_AddMetric.png
  [Règles de mise à l'échelle multiples]: ./media/insights-how-to-scale/Insights_MultipleScaleRules.png
