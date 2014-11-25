<properties title="How to use diagnostics" pageTitle="How to use diagnostics" description="Learn how to set up diagnostics for your resources in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic" />

# Configuration des diagnostics

Dans la version préliminaire du portail Azure, vous pouvez désormais configurer des données riches, fréquentes, d'analyse et de diagnostic concernant vos machines virtuelles et comptes de stockage Windows.

## Collecte de données riches à partir des machines virtuelles

1.  Dans la [version préliminaire du portail Azure](https://portal.azure.com/), cliquez sur **Parcourir**, puis sur **Machines virtuelles**. Sélectionnez la machine virtuelle que vous souhaitez analyser.
2.  L'objectif **Analyse** contient certaines métriques par défaut, telles que **Pourcentage UC**, **Lecture et écriture du disque** et **Entrée et sortie réseau**. Cliquer sur n'importe laquelle de ces sections affiche le volet **Métric**.  
    ![Objectif Analyse](./media/insights-how-to-use-diagnostics/Insights_VMMonitoringLens.png)
3.  Le volet **Metric** affiche les détails des mesures que vous sélectionnez. En haut du volet se trouve un graphe et en dessous un tableau qui affiche l'agrégation de ces mesures, comme la moyenne, le minimum et le maximum. En dessous apparaît la liste des alertes que vous avez définies, filtrées en fonction des métriques affichées sur le volet.  
    ![Volet Métrique](./media/insights-how-to-use-diagnostics/Insights_VMMetricBlade.png)
4.  Pour activer les diagnostics riches, cliquez sur le bouton **Paramètres**, et le volet **Diagnostics** s'affiche. Sélectionnez **ON** :
    ![Volet Diagnostics](./media/insights-how-to-use-diagnostics/Insights_VMDiagnosticsBlade.png)

    -   **Métriques de base** : métriques d'état d'intégrité de votre machine virtuelle (par ex., processeur et mémoire)
    -   **Métriques par disque** : métriques concernant tous les disques associés à votre machine virtuelle
    -   **Métriques .NET** : métriques concernant les applications .NET et ASP.NET sur votre machine virtuelle
    -   **Métriques réseau** : métriques concernant vos connexions réseau et services Web
    -   **Journaux d'événements Windows de l'application** : événements Windows envoyés au canal Application
    -   **Journaux d'événements Windows du système** : événements Windows envoyés au canal Système Cela inclut également tous les événements de [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
    -   **Journaux d'événements Windows de la sécurité** : événements Windows envoyés au canal Sécurité
    -   **Journaux d'infrastructure de diagnostics** : journalisation concernant l'infrastructure de collecte de diagnostics
    -   **Journaux IIS** : journaux concernant votre serveur IIS
        Tous les journaux et métriques sont enregistrés à des intervalles d'une minute de façon à vous fournir les informations les plus récentes concernant votre machine.

Lorsque vous activez les informations de diagnostic vers un compte de stockage, ce compte est facturé aux coûts normaux de stockage, de transactions et de sortie. Toutefois, ces fonctionnalités ne génèrent pas de volumes importants de données, à l'exception, parfois, des journaux IIS. Afin de réduire au maximum les coûts de sortie, nous vous demandons de sélectionner un compte de stockage dans la même région que la machine virtuelle.

Dès que vous cliquez sur **OK**, les données commencent à s'afficher dans votre compte de stockage dans les minutes qui suivent. Vous ne pouvez pas activer les diagnostics pour les machines virtuelles fonctionnant sous Linux, et l'agent invité doit être installé pour pouvoir utiliser les diagnostics.

## Collecte de données riches à partir des comptes de stockage

Vous avez depuis toujours la possibilité de collecter des données depuis vos comptes de stockage, mais dans la version préliminaire du portail Azure, vous pouvez désormais collecter des données à des intervalles d'une minute et donc comprendre en détail ce qui se passe à l'intérieur de votre compte de stockage. La procédure d'activation des métriques par minute est similaire pour toutes les machines virtuelles :

1.  Accédez au volet **Métrique** en cliquant sur l'un des graphiques du volet **Compte de stockage**.
2.  Cliquez sur le bouton **Diagnostics** dans la barre de commandes.
3.  Sélectionnez les données à collecter à partir du compte de stockage :  
    ![Diagnostics de stockage](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4.  Cliquez sur **OK**. La première fois, les données s'afficheront après quelques minutes.

## Visualisation des données de diagnostic

Dès que vous avez activé les diagnostics, vous pouvez afficher une liste de toutes les métriques disponibles en cliquant avec le bouton droit de la souris sur n'importe quel graphique et en accédant à **Modifier la requête** :

![Modifier la requête](./media/insights-how-to-use-diagnostics/Insights_VMEditQuery.png)

Vous pouvez afficher graphiquement ces métriques et faire un zoom avant sur la **Dernière heure**, un zoom arrière sur la **Semaine dernière**, ou encore sélectionner une plage de temps **Personnalisée** :

![Plage de temps personnalisée](./media/insights-how-to-use-diagnostics/Insights_VMCustomTime.png)

Vous noterez que ces métriques sont beaucoup plus précises que celles qui étaient auparavant accessibles, et que le temps de retard est minime.

Il n'est actuellement pas encore possible d'afficher graphiquement les métriques associées à plusieurs instances, telles que les métriques par processus et par disque. Pour plus d'informations sur la personnalisation de vos graphiques d'analyse, voir la section [Personnalisation de l'analyse](http://go.microsoft.com/fwlink/?LinkID=394523&clcid=0x409).

## Alertes concernant les données de diagnostic

En plus de visualiser les métriques, vous pouvez recevoir des alertes sur n'importe laquelle d'entre elles via la version préliminaire du portail. Dans un premier temps, accédez à la section **Règles d'alerte** de la machine virtuelle ou du volet de stockage, puis cliquez sur **Ajouter une alerte** :

![Ajouter une alerte](./media/insights-how-to-use-diagnostics/Insights_VMAlerts.png)

Vous pouvez alors sélectionner n'importe laquelle des métriques que vous avez activées pour les diagnostics :

![Alerte JIT](./media/insights-how-to-use-diagnostics/Insights_VMJITAlert.png)

Le graphique affichera un aperçu de votre seuil d'alerte comparé à la métrique de la veille. Après avoir cliqué sur **Enregistrer**, vous êtes informé dans les minutes qui suivent de tout dépassement du seuil par la métrique sélectionnée.

Notez que les métriques qui ne s'affichent que dans la version préliminaire du portail ne peuvent pas être associées à des alertes dans la version complète du portail. Voilà pourquoi certaines règles d'alerte de la version préliminaire du portail ne sont pas visibles dans la version complète du portail.


