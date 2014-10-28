<properties linkid="develop-net-architecture sublanding" urlDisplayName="" pageTitle="Architecture" metaKeywords="" description="Architecture overview that covers common design patterns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Architecture Overview" authors="robb" solutions="" manager="johndaw" editor="mattshel" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robb"></tags>

# Architecture

Découvrez comment implémenter les modèles de conception courants dans Azure.

### Ensemble Symbole/Icône Azure

[Téléchargez l'ensemble Symbole/Icône Azure][] pour créer des supports techniques qui décrivent ou utilisent des éléments Azure, tels que des schémas d'architecture, des supports de formation, des présentations, des feuilles de données, des éléments d'infographie et des livres blancs. Vous pouvez télécharger les symboles au format PPT, Visio ou PNG. Comme nous apprécions de recevoir des commentaires de nos clients, des instructions sont disponibles à ce sujet dans le téléchargement.

![Ensemble Symbole/Icône Azure][]

## Modèles de conception

### [Consommateurs concurrents][]

![Consommateurs concurrents][1]

Autorisez plusieurs consommateurs concurrents à traiter les messages reçus sur le même canal de messagerie. Ce modèle permet à un système de traiter plusieurs messages simultanément pour optimiser le débit, améliorer l'extensibilité et la disponibilité, et équilibrer la charge de travail.

### [Répartition de la responsabilité des requêtes et commandes][]

![Répartition de la responsabilité des requêtes et commandes][2]

Faites la distinction entre les opérations qui lisent des données et celles qui les mettent à jour en utilisant des interfaces distinctes. Ce modèle permet d'optimiser les performances, l'extensibilité et la sécurité, de prendre en charge l'évolution du système au fil du temps grâce à une flexibilité accrue et d'empêcher les commandes de mise à jour d'entraîner des conflits de fusion au niveau du domaine.

### [Choix de l'instance responsable][]

![Choix de l'instance responsable][3]

Coordonnez les actions réalisées par un ensemble d'instances de tâche collaborant dans une application distribuée en choisissant l'une de ces instances comme responsable chargée de la gestion des autres instances. Ce modèle permet de s'assurer que les instances de tâche n'entrent pas en conflit les unes avec les autres, n'entraînent pas la contention des ressources partagées ou n'interfèrent pas de manière accidentelle avec le travail effectué par d'autres instances de tâche.

### [Canaux et filtres][]

![Canaux et filtres][4]

Décomposez une tâche qui effectue un traitement complexe en une série d'éléments distincts pouvant être réutilisés. Ce modèle permet d'améliorer les performances, l'extensibilité et la réutilisabilité en autorisant le déploiement et l'extensibilité des éléments de tâche qui effectuent le traitement de manière indépendante.

### [Clé valet][]

![Clé valet][5]

Utilisez un jeton ou une clé qui fournit aux clients un accès direct restreint à une ressource ou un service spécifique afin de décharger les opérations de transfert de données à partir du code de l'application. Ce modèle est particulièrement utile dans les applications qui utilisent des files d'attente ou systèmes de stockage hébergés sur le cloud. Il permet de réduire les coûts et d'accroître l'extensibilité et les performances.

### Conseils supplémentaires

Pour plus d'informations sur les modèles de conception courants dans Azure, consultez la page [Modèles de conception du cloud][].

  [Téléchargez l'ensemble Symbole/Icône Azure]: http://www.microsoft.com/fr-fr/download/details.aspx?id=41937
  [Ensemble Symbole/Icône Azure]: ./media/architecture-overview/AzureSymbols.png
  [Consommateurs concurrents]: http://msdn.microsoft.com/fr-fr/library/dn568101.aspx
  [1]: ./media/architecture-overview/CompetingConsumers.png
  [Répartition de la responsabilité des requêtes et commandes]: http://msdn.microsoft.com/fr-fr/library/dn568103.aspx
  [2]: ./media/architecture-overview/CQRS.png
  [Choix de l'instance responsable]: http://msdn.microsoft.com/fr-fr/library/dn568104.aspx
  [3]: ./media/architecture-overview/LeaderElection.png
  [Canaux et filtres]: http://msdn.microsoft.com/fr-fr/library/dn568100.aspx
  [4]: ./media/architecture-overview/PipesAndFilters.png
  [Clé valet]: http://msdn.microsoft.com/fr-fr/library/dn568102.aspx
  [5]: ./media/architecture-overview/ValetKey.png
  [Modèles de conception du cloud]: http://msdn.microsoft.com/fr-fr/library/dn568099.aspx
