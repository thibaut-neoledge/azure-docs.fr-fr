<properties 
	pageTitle="Analyser vos applications logiques" 
	description="Comment voir ce que vos applications logiques ont fait." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="stepsic"/>

#Analyser vos applications logiques

Après avoir créé une application logique en suivant les étapes décrites dans [Créer une application logique](app-service-logic-create-a-logic-app.md), vous pouvez consulter l’historique complet de son exécution dans le portail Azure. Pour afficher l’historique, cliquez sur **Parcourir** sur le côté gauche de l’écran du portail et sélectionnez **Logic Apps**. Une liste d'applications logiques dans votre abonnement s'affiche. Une application logique peut être **Activée** ou **Désactivée**. **Activée** signifie que des déclencheurs exécuteront votre application logique en réponse à des événements déclencheurs. Une application logique **Désactivée** ne s’exécutera pas en réponse aux événements.

![Vue d'ensemble](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Quand le panneau de votre application logique s'affiche, deux sections sont dignes d'intérêt :

- **Résumé** : indique l’état le plus récent et constitue un point d’entrée pour la modification de votre application logique.
- **Toutes les exécutions** : affiche la liste des exécutions de cette application logique.

##Exécutions

![Toutes les exécutions](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

Cette liste d’exécutions montre l’**Heure de début**, l’**Identificateur d’exécution** (vous pouvez l’utiliser lors de l’appel de l’API REST) et la **Durée** des exécutions. Cliquez sur n'importe quelle ligne pour afficher les détails de cette exécution.

Le panneau de détails contient un graphique avec la durée d'exécution et la séquence de toutes les actions de l'exécution. En dessous figure la liste complète de toutes les actions qui ont été exécutées.

![Exécution et actions](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

Pour finir, sur une action particulière, vous pouvez obtenir toutes les données qui ont été passées à l’action et qui ont été reçues à partir de l’action dans les sections **Entrées** et **Sorties**. Cliquez sur les liens pour consulter la totalité du contenu (vous pouvez également copier les liens pour télécharger le contenu).

L’**ID de suivi** est une autre information importante. Cet identificateur est transmis dans les en-têtes de tous les appels d'actions. Si vous utilisez la journalisation dans votre propre service, nous vous recommandons d'enregistrer l'ID de suivi. Vous pourrez ainsi rechercher cet identificateur dans vos propres journaux.

##Historique du déclencheur 

Des déclencheurs d’interrogation vérifient une API à intervalles réguliers, mais ne démarrent pas nécessairement une exécution ; tout dépend de la réponse (par exemple `200` signifie qu’il faut exécuter et `202` signifie qu’il ne faut pas exécuter). L’historique de déclencheur vous permet de voir tous les appels qui ont lieu, mais qui n’exécutent pas l’application logique (les réponses `202`).

![Historique du déclencheur](./media/app-service-logic-monitor-your-logic-apps/triggerhistory.png)

Pour chaque déclencheur, vous pouvez voir s’il a été **déclenché**, s’il ne l’a pas été ou si une erreur a été générée (il a **échoué**). Pour découvrir la raison pour laquelle le déclencheur a échoué, vous pouvez cliquer sur le lien **Sorties**. S’il s’est déclenché, cliquez sur le lien **Exécuter** pour voir ce qui est arrivé après le déclenchement.

Notez que pour les déclencheurs *d’émission*, vous ne pourrez *pas* voir les heures auxquelles l’exécution a commencé ; vous verrez cependant les appels *Inscription pour rappel* qui sont passés lorsque l’application logique s’inscrit pour être rappelée. Si votre déclencheur d’émission ne fonctionne pas, il peut s’agir d’un problème d’inscription (que vous pouvez voir au niveau des Sorties) ; sinon, vous devez examiner cette API en particulier.

##Contrôle de version

Il existe une fonctionnalité supplémentaire qui n’est actuellement pas possible dans l’interface utilisateur (elle le sera prochainement), mais est cependant disponible via [l’api REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409). Quand vous mettez à jour la définition d’une application logique, la version précédente de la définition est stockée. En effet, si une exécution est déjà en cours, elle fait référence à la version de l'application logique qui existait lors du démarrage de l'exécution. Les définitions d'exécutions ne peuvent pas changer quand elles sont en cours. L'API REST d'historique des versions vous donne accès à ces informations.
 

<!---HONumber=August15_HO6-->