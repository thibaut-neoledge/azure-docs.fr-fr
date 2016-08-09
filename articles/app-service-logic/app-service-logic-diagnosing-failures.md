<properties
   pageTitle="Diagnostic des échecs d’applications logiques | Microsoft Azure"
   description="Approches courantes pour comprendre les points de défaillance des applications logiques"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/18/2016"
   ms.author="jehollan"/>

# Diagnostic des échecs d’applications logiques

Si vous rencontrez des problèmes ou des échecs avec la fonctionnalité Logic Apps d’Azure App Service, quelques approches peuvent vous aider à mieux comprendre l’origine des défaillances.

## Outils du Portail Azure

Le Portail Azure fournit plusieurs outils permettant de diagnostiquer chaque application logique à différentes étapes.

### Historique du déclencheur

Chaque application logique comporte au moins un déclencheur. Si vous remarquez que les applications ne se déclenchent pas, vous devez en premier lieu rechercher des informations dans l’historique du déclencheur. Vous pouvez accéder à l’historique du déclencheur dans le panneau principal de l’application logique.

![Emplacement de l’historique du déclencheur][1]

Il répertorie tous les tentatives du déclencheur de votre application logique. Vous pouvez cliquer sur chaque tentative du déclencheur pour accéder au niveau suivant de détail (en particulier, les entrées ou sorties générées par la tentative du déclencheur). En cas de défaillance d’un déclencheur, cliquez sur la tentative de ce déclencheur et explorez le lien **Sorties** pour comprendre les messages d’erreur potentiellement générés (par exemple, des informations d’identification FTP incorrectes).

Voici les différents états que vous pouvez voir :

* **Ignoré**. Il a interrogé le point de terminaison pour vérifier les données et a reçu une réponse indiquant qu’aucune donnée n’était disponible.
* **Réussi**. Le déclencheur a reçu une réponse indiquant que les données étaient disponibles. Cela peut provenir d’un déclencheur manuel, d’un déclencheur de périodicité ou d’un déclencheur d’interrogation. Cet état est probablement accompagné d’un état **Déclenché**, sauf si vous avez une condition ou une commande splitOn en mode Code qui n’a pas été satisfaite.
* **Échec**. Une erreur a été générée.

#### Démarrage manuel d’un déclencheur

Si vous souhaitez que l’application logique recherche immédiatement un déclencheur disponible (sans attendre la prochaine récurrence), vous pouvez cliquer sur **Sélectionner le déclencheur** dans le panneau principal pour forcer une vérification. Par exemple, si vous cliquez sur ce lien avec un déclencheur Dropbox, le workflow interrogera immédiatement Dropbox pour rechercher les nouveaux fichiers.

### Historique d’exécution

Chaque déclencheur déclenché donne lieu à une exécution. Vous pouvez accéder aux informations d’exécution dans le panneau principal, qui contient un grand nombre d’informations pouvant être utiles pour comprendre le déroulement du flux de travail.

![Emplacement de l’historique d’exécution][2]

Une exécution affiche l’un des états suivants :

* **Réussi**. Toutes les actions ont réussi, ou, en cas de problème, cela a été corrigé par une action exécutée ultérieurement dans le workflow. Autrement dit, le problème a été corrigé par une action configurée pour s’exécuter après l’échec d’une autre action.
* **Échec**. Au moins une action a rencontré un échec qui n’a pas été traité par une action ultérieure dans le workflow.
* **Annulé**. Le workflow était en cours d’exécution mais a reçu une demande d’annulation.
* **Exécution en cours**. Si un workflow est en cours d’exécution. Cela peut se produire pour les workflows qui sont limités ou en raison du plan App Service actuel. Pour en savoir plus, consultez les limites d’action sur la [page de tarification](https://azure.microsoft.com/pricing/details/app-service/plans/). La configuration des diagnostics (les graphiques apparaissant en-dessous de l’historique des exécutions) peut également fournir des informations sur les événements de limitation qui se produisent.

Lorsque vous examinez un historique d’exécution, vous pouvez rechercher des détails supplémentaires.

#### Sorties du déclencheur

Les sorties du déclencheur affichent les données reçues du déclencheur. Cela peut vous aider à déterminer si toutes les propriétés ont renvoyé les valeurs attendues.

>[AZURE.NOTE] Il peut être utile de comprendre la manière dont la fonctionnalité Logic Apps [gère les différents types de contenu](app-service-logic-content-type.md) si vous voyez du contenu que vous ne comprenez pas.

![Exemples de sorties du déclencheur][3]

#### Entrées et sorties d’actions

Vous pouvez explorer les entrées et sorties associées à une action. Ces informations sont utiles non seulement pour comprendre la taille et la forme des sorties, mais aussi pour afficher tous les messages d’erreur qui ont été générés.

![Entrées et sorties d’actions][4]

## Débogage du runtime du workflow

Au-delà du contrôle des entrées, des sorties et des déclencheurs d’une exécution, il peut être utile d’ajouter des étapes à un workflow pour faciliter le débogage. [RequestBin](http://requestb.in) est un outil puissant que vous pouvez ajouter en tant qu’étape d’un workflow. À l’aide de RequestBin, vous configurez un inspecteur de requête HTTP qui détermine précisément la taille, la forme et le format d’une requête HTTP. Vous pouvez créer un nouvel élément RequestBin et coller l’URL dans une action HTTP POST de l’application logique, en utilisant le contenu du corps que vous voulez tester (par exemple, une expression ou une autre sortie de l’étape). Après avoir exécuté l’application logique, vous actualisez votre élément RequestBin pour voir comment la requête a été formée dans la mesure où elle a été générée à partir du moteur Logic Apps.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0727_2016-->