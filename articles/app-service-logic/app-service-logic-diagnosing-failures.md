<properties
   pageTitle="Diagnostic des applications Logic Apps | Microsoft Azure"
   description="Comprendre les approches courantes pour comprendre les points de défaillance des applications"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/18/2016"
   ms.author="jehollan"/>
   
# Diagnostic des applications logiques

Si vous rencontrez des problèmes ou des défaillances avec une application logique, vous pouvez adopter plusieurs approches afin de mieux en comprendre les origines.

## Outils du portail de gestion

Le portail Azure fournit plusieurs outils permettant de diagnostiquer chaque application logique à différentes étapes.

### Historique du déclencheur

Chaque application logique comporte au moins un déclencheur. Si vous constatez que des applications ne se déclenchent pas, la première chose à faire est de consulter l’historique du déclencheur. Vous pouvez accéder à l’historique du déclencheur à partir du panneau principal Application logique :

![][1]

Vous obtenez la liste de toutes les tentatives de déclenchement de votre application logique. Vous pouvez cliquer sur chacune de ces entrées pour obtenir le niveau de détail supérieur (en particulier les entrées ou sorties qui ont été générées par la tentative du déclencheur). Si vous remarquez des échecs, vous devez cliquer dans la tentative de déclencheur et explorer le lien Sorties pour comprendre les messages d’erreur qui ont peut-être été générés (par exemple : informations d’identification FTP incorrectes).

Voici les différents états que vous pouvez voir :

* Ignoré : le déclencheur a interrogé le point de terminaison pour vérifier si des données étaient disponibles et obtenu une réponse indiquant qu’aucune donnée n’était disponible.
* Réussi : le déclencheur a reçu une réponse indiquant que les données étaient disponibles. Cela peut provenir d’un déclencheur manuel, d’un déclencheur de périodicité ou d’un déclencheur d’interrogation. Cet état sera probablement accompagné d’un « déclenchement », sauf si vous avez une condition ou un splitOn en mode Code qui n’a pas été satisfait.
* Échec : une erreur a été générée.

#### Démarrer un déclencheur manuellement

Si vous souhaitez que l’application logique recherche immédiatement un déclencheur disponible (sans attendre la prochaine récurrence), vous pouvez toujours cliquer sur le bouton **Sélectionner le déclencheur** dans le panneau principal pour forcer une vérification. Par exemple, si vous cliquez sur ce bouton avec un déclencheur Dropbox, le workflow interrogera immédiatement Dropbox pour rechercher les nouveaux fichiers.

### Historique d’exécution

Chaque fois qu’un déclencheur est activé, il produit une séquence d’exécution. Les exécutions sont accessibles à partir du panneau principal et contiennent un grand nombre d’informations utiles pour mieux comprendre le déroulement du flux.

![][2]

Une exécution peut prendre l’un des états suivants :

* Réussite : toutes les actions ont abouti ou, si un problème est survenu, il a été corrigé par une action ultérieure du workflow (par exemple, une action a été définie pour s’exécuter après une action en « échec »).
* Échec : au moins une action a rencontré un échec qui n’a pas été géré par une action ultérieure du workflow.
* Annulé : le workflow était en cours d’exécution mais a reçu une demande d’annulation.
* En cours d’exécution : si un workflow est en cours d’exécution. Cela peut se produire pour les flux qui sont également limités par le plan App Service actuel. Pour en savoir plus, consultez les limites d’action sur la [page de tarification](https://azure.microsoft.com/pricing/details/app-service/plans/). La configuration des diagnostics (graphiques sous l’historique d’exécution) vous informe également sur tous les événements de limitation survenus.

Une fois que vous vous trouvez dans une exécution, vous pouvez l’explorer pour obtenir plus de détails.

#### Sorties de déclencheur

Les sorties de déclencheur affichent les données qui ont été reçues du déclencheur. Elles peuvent être utiles pour comprendre si toutes les propriétés sont retournées comme prévu.

>[AZURE.NOTE] Il peut être utile de comprendre la manière dont Logic Apps [gère les différents types de contenu](app-service-logic-content-type.md) si vous rencontrez un contenu que vous ne comprenez pas.

![][3]

#### Entrées et sorties d’actions

Vous pouvez explorer les entrées et sorties associées à une action. Ces informations sont utiles non seulement pour comprendre la taille et la forme des sorties, mais aussi pour afficher tous les messages d’erreur qui ont été générés.

![][4]

## Débogage du runtime du workflow

Au-delà du contrôle des entrées, des sorties et des déclencheurs d’une exécution, il peut être utile d’ajouter des étapes à un workflow pour faciliter la résolution des problèmes. Vous pouvez par exemple ajouter [RequestBin](http://requestb.in) comme étape d’un workflow. RequestBin vous permet de configurer un inspecteur de requête HTTP afin de comprendre précisément la taille, la forme et le format d’une requête HTTP. Vous pouvez créer un nouvel élément RequestBin et coller l’URL dans une action HTTP POST de l’application logique, en utilisant le contenu du corps que vous souhaitez tester (une expression, une autre sortie de l’étape, etc.). Après avoir exécuté l’application logique, vous pouvez actualiser votre élément RequestBin pour voir la manière dont la requête a été formée puisqu’elle a été générée à partir du moteur d’applications logiques.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0601_2016-->