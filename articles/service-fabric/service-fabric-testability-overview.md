<properties
   pageTitle="Vue d’ensemble de la testabilité | Microsoft Azure"
   description="Cet article décrit le sous-système de testabilité dans Service Fabric qui permet de produire des erreurs et d’exécuter des scénarios de test sur vos services."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="rsinha"/>

# Vue d’ensemble de la testabilité

Le sous-système de testabilité est conçu pour tester les services qui s’appuient sur Microsoft Azure Service Fabric. La testabilité vous permet de provoquer des erreurs significatives et d’exécuter des scénarios. Ces erreurs et scénarios exercent et valident les nombreux états et transitions qu’un service connaît tout au long de sa durée de vie, le tout de manière contrôlée, sécurisée et cohérente.

La testabilité propose des actions et des scénarios qui permettent ces fonctionnalités. Les actions sont les erreurs isolées introduites dans un service pour le tester. Un développeur de services peut les utiliser en tant blocs de constructions afin d’écrire des scénarios compliqués. Par exemple :

  * Redémarrez un nœud pour simuler différentes situations dans lesquelles une machine ou une machine virtuelle est redémarrée.

  * Déplacez un réplica de votre service avec état pour simuler l’équilibrage de charge, le basculement ou la mise à niveau de l’application.

  * Appelez la perte de quorum sur votre service avec état afin de créer une situation où les opérations d’écriture sont interrompues en raison d’un nombre insuffisant de réplicas de sauvegarde ou secondaire pour l’acceptation des nouvelles données.

  * Appelez la perte des données sur un service avec état pour créer une situation où l’intégralité de l’état en mémoire est effacé.

Les scénarios sont des opérations complexes composées d’une ou plusieurs actions. Comme ces actions sont des commandes PowerShell et des appels d’API C#, elles peuvent prendre n’importe quelle forme : services de longue durée, commandes PowerShell, applications en ligne de commande, etc. Deux scénarios de testabilité sont fournis en standard :

  * Scénario de chaos
  * Scénario de basculement

La testabilité expose des API PowerShell et C#. Cela permet au développeur de service de disposer d’une plus grande souplesse avec les scripts PowerShell et d’un meilleur contrôle avec les API C# selon les besoins.

## Importance de la testabilité

Service Fabric simplifie considérablement les tâches d’écriture et de gestion des applications évolutives distribuées. De même, le sous-système de testabilité dans Service Fabric facilite le test d’une application distribuée. Trois problématiques liées au test doivent être résolues :

1. Simulation/Génération de défaillances réalistes : Service Fabric permet notamment aux applications distribuées de récupérer de diverses défaillances. Toutefois, pour évaluer la capacité de l’application à récupérer de la sorte, nous devons recourir à un mécanisme de simulation/de génération de ces défaillances réalistes au sein d’un environnement de test contrôlé.

2. La capacité à générer les défaillances liées : les défaillances basiques du système, comme les défaillances réseau et les défaillances de machines, sont faciles à générer de manière isolée. Les interactions entre les défaillances isolées peuvent entraîner un nombre incalculable de scénarios de dysfonctionnement. La génération de ces contextes revêt une importance toute particulière.

3. Expérience unifiée au sein de divers niveaux de développement et de déploiement : de nombreux systèmes d’injection d’erreurs peuvent générer différents types de défaillances. Toutefois, cette expérience unifiée est pauvre en transposant les scénarios de test de développement au sein des grands environnements de test, puis en production.

Il existe de nombreux mécanismes permettant de résoudre ces problèmes, mais il manque un élément : un système qui joue le même rôle avec les garanties requises, de l’environnement de test des développeurs aux clusters de production. Le sous-système de testabilité permet aux développeurs d’applications de se concentrer sur le test de leur logique métier. La testabilité fournit toutes les capacités requises pour tester l’interaction du service avec le système distribué sous-jacent.

### Simulation/Génération de scénarios de défaillances réalistes

Pour éprouver la solidité d’un système distribué contre les défaillances, nous recourons à un mécanisme de génération d’erreurs. En théorie, il semble facile de générer une défaillance comme un arrêt de nœud. Néanmoins, cette opération soulève des problématiques de cohérence identiques à celles que Service Fabric tente de résoudre. Par exemple, si vous voulez arrêter un nœud, le flux de travail suivant est requis :

1. À partir du client, émettez une requête d’arrêt du nœud.

2. Envoyez la requête au nœud approprié.

    a. Si le nœud est introuvable, elle doit être mise en échec.

    b. Si le nœud est trouvé, elle doit être renvoyée uniquement si le nœud est arrêté.

Pour vérifier la défaillance dans le cadre d’un test, celui-ci doit savoir que quand cette défaillance est provoquée, elle se produit réellement. Avec Service Fabric, vous êtes assuré que le nœud va être arrêté ou est déjà arrêté, au moment où la commande l’atteint. Dans les deux cas, le test peut examiner correctement l’état et procéder de manière appropriée à la validation. Un système implémenté en dehors de Service Fabric pour exécuter le même ensemble de défaillances pourrait rencontrer une multitude de problèmes de réseau, matériels et logiciels, qui l’empêcheraient de fournir les garanties précédentes. Si les problèmes évoqués précédemment sont identifiés, Service Fabric reconfigure l’état du cluster afin de les contourner. En conséquence, le sous-système de testabilité vous procure les garanties adéquates.

### Génération des événements et scénarios requis

Tandis que la simulation des défaillances réalistes peut s’avérer difficile, la génération des défaillances liées est encore plus compliquée. Par exemple, une perte de données se produit dans un service persistant avec état quand les événements suivants se produisent :

1. Seul un quorum d’écriture est traité par la réplication. L’ensemble des réplicas secondaires sont derrière le réplica primaire.

2. L’arrêt des réplicas provoque l’arrêt du quorum d’écriture (en raison de l’arrêt du package de code ou du nœud).

3. Le quorum d’écriture ne peut pas réapparaître en raison de la perte des données associées aux réplicas (causée par la corruption des disques ou la réinitialisation de la machine).

Ces défaillances liées se produisent vraiment, mais pas aussi souvent que les défaillances isolées. Il est primordial de pouvoir tester ces scénarios avant qu’ils ne se produisent en environnement de production. La capacité à simuler ces scénarios avec des charges de travail de production dans des circonstances contrôlées (en plein milieu de la journée avec tous les ingénieurs sur le pont) est encore plus importante. De telles circonstances sont bien plus efficaces qu’un premier test en production à 2h00 du matin.

### Expérience unifiée au sein d’environnements différents

Traditionnellement, l’idée est de développer trois différents ensembles d’expériences, un pour l’environnement de développement, un pour les tests et un autre pour la production. Le modèle se présentait ainsi :

1. Dans l’environnement de développement, produisez des transitions d’état prenant en charge des tests d’unités de méthodes individuelles.

2. Dans l’environnement de test, produisez des erreurs dans le cadre de tests de bout en bout illustrant différents scénarios de défaillances.

3. Conservez l’environnement de production intact pour empêcher tout risque de défaillance non naturelle et garantir une réponse humaine extrêmement rapide aux défaillances.

Dans Service Fabric, par le biais du sous-système de testabilité, nous proposons de bouleverser cette définition en appliquant une méthodologie unique de l’environnement de développement à la production. Il existe deux moyens de parvenir à cet objectif :

1. Pour provoquer des défaillances contrôlées, utilisez les API de testabilité d’un environnement à boîtier unique sur l’ensemble du processus, jusqu’aux clusters de production.

2. Pour instaurer dans le cluster une atmosphère provoquant l’introduction automatique de défaillance, recourez au sous-système de testabilité afin de générer des erreurs automatiques. En configurant le taux de défaillances, vous êtes à même de tester un même service dans divers environnements.

Avec Service Fabric, la mise à l’échelle des défaillances ne serait pas la même au sein des différents environnements. En revanche, les mécanismes réels seraient identiques. Cela permet de diffuser un code plus rapide vers le pipeline de déploiement et de tester les services sous des charges de travail réelles.

## Utilisation de la testabilité

### Utilisation de la testabilité dans C##

Les fonctionnalités de testabilité se trouvent dans System.Fabric.dll. Ce fichier dll se trouve dans le package NuGet Microsoft.ServiceFabric.nupack. Pour pouvoir utiliser les fonctionnalités de testabilité, incorporez le package NuGet en tant que référence dans votre projet.

### Utilisation de la testabilité dans PowerShell

Pour utiliser le module PowerShell de testabilité, vous devez installer le runtime MSI. Une fois qu’il est installé, le module PowerShell ServiceFabric est chargé automatiquement, et rendu disponible aux développeurs.

## Conclusion

Pour créer des services vraiment à l’échelle du cloud, il est primordial de vérifier, avant et après le déploiement, que ces services peuvent résister à des défaillances réalistes. Dans le monde des services actuel, vous avez tout intérêt à démontrer une capacité à innover et à déployer rapidement du code en production. C’est précisément cela que permet la testabilité de Service Fabric aux développeurs de services.

## Étapes suivantes

- [Actions de testabilité](service-fabric-testability-actions.md)
- [Scénarios de testabilité](service-fabric-testability-actions.md)
- Procédure de test de votre service
  - [Simuler des défaillances au cours des charges de travail de services](service-fabric-testability-workload-tests.md)
  - [Échecs de communication de service à service](service-fabric-testability-scenarios-service-communication.md)

<!---------HONumber=AcomDC_0309_2016-->