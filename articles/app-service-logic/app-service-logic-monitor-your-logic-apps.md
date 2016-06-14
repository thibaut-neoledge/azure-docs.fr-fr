<properties 
	pageTitle="Analyser vos applications logiques dans Azure App Service | Microsoft Azure" 
	description="Comment voir ce que vos applications logiques ont fait" 
	authors="stepsic-microsoft-com" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="stepsic"/>

# Analyser vos applications logiques

Une fois que vous avez [créé une application logique](app-service-logic-create-a-logic-app.md), vous pouvez consulter l’historique complet de son exécution dans le Portail Azure. Pour afficher l’historique, sélectionnez **Parcourir**, puis **Applications logiques**. Une liste de toutes les applications logiques de votre abonnement s’affiche. Vous pouvez sélectionner n’importe quelle application logique et **l’Activer** ou la **Désactiver**. Lorsqu’une application logique est **activée**, les déclencheurs l’exécutent en réponse à des événements déclencheurs. Une application logique **désactivée** n’est pas exécutée en réponse à des événements.

![Vue d'ensemble](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Quand le panneau de votre application logique s’affiche, deux sections sont dignes d’intérêt :

- **Résumé** : indique l’état le plus récent et constitue un point d’entrée pour la modification de votre application logique.
- **Toutes les exécutions** : affiche la liste des exécutions de cette application logique.

## Afficher les exécutions de votre application

![Toutes les exécutions](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

Cette liste d’exécutions indique l’**Heure de début**, l’**Identificateur d’exécution** (vous pouvez l’utiliser lors de l’appel de l’API REST) et la **Durée** des exécutions. Sélectionnez n’importe quelle ligne pour afficher les détails de cette exécution.

Le panneau de détails contient un graphique avec la durée d'exécution et la séquence de toutes les actions de l'exécution. Voici la liste complète de toutes les actions qui ont été exécutées :

![Exécution et actions](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

Pour finir, sur une action particulière, vous pouvez obtenir toutes les données qui ont été passées à l’action et qui ont été reçues à partir de l’action dans les sections **Entrées** et **Sorties**. Sélectionnez les liens pour consulter la totalité du contenu (vous pouvez également copier les liens pour télécharger le contenu).

L’**ID de suivi** est une autre information importante. Cet identificateur est transmis dans les en-têtes de tous les appels d'actions. Si vous utilisez la journalisation dans votre propre service, nous vous recommandons d'enregistrer l'ID de suivi. Vous pourrez ainsi rechercher cet identificateur dans vos propres journaux.

## Afficher l’historique du déclencheur 

Les déclencheurs d’interrogation vérifient une API à intervalles réguliers, mais ne démarrent pas nécessairement une exécution ; tout dépend de la réponse (par exemple `200` signifie qu’il faut exécuter et `202` signifie qu’il ne faut pas exécuter). L’historique du déclencheur vous permet de voir tous les appels qui ont lieu, mais qui n’exécutent pas l’application logique (les réponses `202`).

![Historique du déclencheur](./media/app-service-logic-monitor-your-logic-apps/triggerhistory.png)

Pour chaque déclencheur, vous pouvez voir s’il s’est **déclenché**, s’il ne s’est pas déclenché ou s’il a rencontré une erreur (il a alors **échoué**). Pour découvrir la raison pour laquelle le déclencheur a échoué, sélectionnez le lien **Sorties**. S’il s’est déclenché, sélectionnez le lien **Exécuter** pour voir ce qui est arrivé après le déclenchement.

Notez que pour les déclencheurs d’*émission*, l’heure de début de l’exécution n’est *pas* indiquée ici. Au lieu de cela, vous voyez les appels *Inscription pour rappel*, qui sont passés lorsque l’application logique s’inscrit pour être rappelée. Si votre déclencheur d’émission ne fonctionne pas, il peut s’agir d’un problème d’inscription (que vous pouvez voir au niveau des Sorties) ; sinon, vous devez examiner cette API en particulier.

## Activation des diagnostics Azure

Vous pouvez activer les informations de diagnostic pour récupérer et stocker les données de runtime de vos flux de travail. Dans le panneau de l’application logique, faites défiler jusqu’aux graphiques **Monitoring** (Surveillance) et sélectionnez **Click here to enable diagnostics** (Cliquez ici pour activer les diagnostics). Vous pouvez ensuite configurer un compte de stockage dans la région de l’application logique et vous abonner à **Logs** (Journaux) ou **Metrics** (Mesures) à partir de l’application logique. **Logs** (Journaux) envoie un événement chaque fois qu’une exécution, une action ou un événement démarre ou se termine. **Metrics** (Mesures) fournit des données agrégées concernant le nombre d’exécutions effectuées dans une fenêtre de temps.

## Activer le contrôle de version

Il existe une fonctionnalité supplémentaire qui n’est pas disponible actuellement dans l’interface utilisateur (elle le sera prochainement), mais qui est accessible via [l’API REST](https://msdn.microsoft.com/library/azure/mt643788.aspx). Quand vous mettez à jour la définition d’une application logique, la version précédente de la définition est stockée. En effet, si une exécution est déjà en cours, elle fait référence à la version de l’application logique qui existait lors du démarrage de l’exécution. Les définitions d'exécutions ne peuvent pas changer quand elles sont en cours. L’API REST d’historique des versions vous donne accès à ces informations.

<!---HONumber=AcomDC_0601_2016-->