<properties
   pageTitle="Vue d’ensemble de la testabilité"
   description="Cet article décrit la fonction de testabilité de Microsoft Azure Service Fabric."
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
   ms.date="07/13/2015"
   ms.author="rsinha"/>

# Vue d’ensemble de la testabilité

La testabilité est une suite d’outils conçus spécialement pour tester vos services développés sur Microsoft Azure Service Fabric. Les outils permettent au développeur de provoquer facilement des erreurs significatives et d'exécuter des scénarios de test pour explorer et valider les nombreux états et transitions qu'un service connaît tout au long de sa durée de vie, de manière contrôlée et sécurisée.

La testabilité fournit des actions et des scénarios qui prennent en charge ce processus. Les actions sont les erreurs isolées introduites dans un service pour le tester. Un développeur de services peut les utiliser en tant blocs de constructions afin d’écrire des scénarios compliqués. Par exemple :

  + Redémarrez un nœud pour simuler différentes situations où une machine ou machine virtuelle est redémarrée.
  + Déplacez un réplica de votre service avec état pour simuler l’équilibrage de charge, le basculement ou la mise à niveau de l’application.
  + Appelez la perte de quorum sur votre service avec état afin de créer une situation où les opérations d’écriture sont interrompues en raison d’un nombre insuffisant de réplicas de sauvegarde ou secondaire pour l’acceptation des nouvelles données.
  + Appelez la perte des données sur un service avec état pour créer une situation où l’intégralité de l’état en mémoire est effacé.

Les scénarios sont des tests complets constitués d’une ou de plusieurs actions. Ces dernières, qui sont de simples commandes PowerShell et fonctions C# peuvent prendre toutes formes : services de longue durée, commandes PowerShell, applications de ligne de commande, etc. Deux scénarios de testabilité sont fournis en standard :

  + Test chaos
  + Test de basculement

La testabilité expose des API PowerShell et C#. Cela procure au développeur de services une plus grande souplesse dans l’exécution des scripts PowerShell et un contrôle optimisé sur les API C#, lorsqu’ils sont nécessaires.

## Importance de la testabilité

Service Fabric simplifie considérablement les tâches d’écriture et de gestion des applications évolutives distribuées. Le composant de testabilité de Service Fabric a vocation à réduire la complexité des procédures de test d’une application distribuée. Trois problématiques liées au test doivent être résolues :

1. Simulation/Génération des défaillances réalistes : Service Fabric permet notamment aux applications distribuées de récupérer de diverses défaillances. Toutefois, pour évaluer la capacité de l’application à récupérer de la sorte, nous devons recourir à un mécanisme de simulation/de génération de ces défaillances réelles au sein d’un environnement de test contrôlé.
2. La capacité de générer les défaillances liées : contrairement aux défaillances basiques du système comme les défaillances réseau, les défaillances de machines sont faciles à générer de manière isolée. Les interactions entre les défaillances isolées peuvent entraîner un nombre incalculable de scénarios de dysfonctionnement. La génération de ces contextes revêt une importance toute particulière.
3. Expérience unifiée au sein de divers niveaux de développement et de déploiement : de nombreux systèmes d’injection d’erreurs prennent en charge la génération des différents types de défaillances. Toutefois, cette expérience unifiée est interrompue en transposant les scénarios de test de développement au sein des grands environnements de test, puis en production.

Il existe de nombreux mécanismes permettant de résoudre les problèmes évoqués ci-dessous, mais il manque un élément : un système qui joue le même rôle avec les garanties requises, de l’environnement de test des développeurs aux clusters de production. Grâce au composant de testabilité, les développeurs d’application peuvent davantage se concentrer sur le test de leur logique métier. La fonctionnalité de testabilité fournit toutes les capacités requises pour tester l’interaction du service avec le système distribué sous-jacent.

### Simulation/Génération de scénarios de défaillances réalistes
Pour éprouver la solidité d’un système distribué contre les défaillances, nous recourons à un mécanisme de génération d’erreurs. En théorie, il semble facile de générer une défaillance comme un arrêt de nœud. Néanmoins, cette opération soulève des problématiques de cohérence identiques à celles que Service Fabric tente de résoudre. Par exemple, si vous souhaitez arrêter un nœud, le flux de travail suivant est requis :

1. À partir du client, émettez une requête d’arrêt du nœud.
2. Envoyez la requête au nœud approprié.
	1. Si le nœud est introuvable, elle doit être mise en échec.
	2. Si le nœud est trouvé, elle doit être renvoyée uniquement si le nœud est arrêté.

Durant le test, le mécanisme doit avoir confirmation de l’action de la défaillance, afin de procéder à son contrôle. Avec Windows Fabric, vous êtes assuré que le nœud va être arrêté ou est déjà arrêté, au moment où la commande l’atteint. Dans les deux cas, le test peut examiner correctement l’état et procéder de manière appropriée à la validation. Un système implémenté en dehors de Service Fabric pour exécuter le même ensemble de défaillances pourrait rencontrer une multitude de problèmes de réseau, matériels et logiciels, qui l’empêcheraient de fournir les garanties mentionnées plus haut. Si les problèmes évoqués précédemment sont identifiés, Service Fabric reconfigure l’état du cluster afin de les contourner. En conséquence, le module de testabilité vous procure les garanties adéquates.

### Génération des événements et du scénario requis
Tandis que la simulation des défaillances réalistes peut s’avérer difficile, la génération des défaillances liées est encore plus compliquée. Par exemple, une perte de données se produit dans un service persistant avec état lorsque les événements suivants se produisent :

1. Seul un quorum d’écriture est traité par la réplication. L’ensemble des réplicas secondaires sont derrière le réplica primaire.
2. L’arrêt des réplicas provoque l’arrêt du quorum d’écriture (en raison de l’arrêt du package de code ou du nœud).
3. Le quorum d’écriture ne peut pas réapparaître en raison de la perte des données associées aux réplicas (causée par la corruption des disques ou la réinitialisation de la machine).

Ces défaillances liées se produisent vraiment (quoique moins fréquemment que les défaillances isolées) dans la réalité. Il est primordial de pouvoir tester ces scénarios avant qu’ils ne se produisent en environnement de production. Par ailleurs, la capacité à les tester de manière contrôlée en production (milieu de la journée, avec tous les ingénieurs présents) permet de s’y préparer confortablement. Que se passerait-il s’ils se produisaient pour la première fois à 2 h du matin en production ?

### Expérience unifiée au sein d’environnements différents
Traditionnellement, l’idée est de développer trois différents ensembles d’expériences, une pour l’environnement de développement, une pour les tests et une autre pour la production. Le modèle se présentait ainsi :

1. Dans l’environnement de développement, produisez des transitions d’état prenant en charge des tests d’unités de méthodes individuelles.
2. Dans l’environnement de test, produisez des erreurs dans le cadre de tests de bout en bout illustrant différents scénarios de défaillances.
3. Conservez l’environnement de production intact, en écartant tout risque de défaillance non naturelle et en garantissant une réponse humaine extrêmement rapide aux défaillances.

Dans Service Fabric, via le module et le service de testabilité, nous proposons de bouleverser cette définition en appliquant une méthodologie unique de l’environnement de développement à la production. Il existe deux moyens de parvenir à cet objectif : 1. Pour provoquer des défaillances contrôlées, utilisez les API de testabilité d’un environnement à boîtier unique sur l’ensemble du processus, jusqu’aux clusters de production. 2. Pour instaurer dans le cluster une atmosphère provoquant l’introduction automatique de défaillance, recourez au service de testabilité afin de générer des erreurs automatiques. En configurant le taux de défaillances, vous êtes à même de tester un même service dans divers environnements.

Avec Service Fabric, la mise à l’échelle des défaillances ne serait pas la même au sein des différents environnements. En revanche, le mécanisme serait identique. Cela permet de diffuser un code plus rapide vers le pipeline de déploiement et de tester les services sous une charge de travail réelle.

## Utilisation de la testabilité
### Utilisation de la testabilité dans C## 
Les différentes fonctions de testabilité sont accessibles dans System.Fabric.Testability.dll. Vous trouverez ce DLL dans le package NuGet Microsoft.ServiceFabric.Testability.nupack. Pour pouvoir utiliser les fonctions de testabilité, incorporez le package NuGet en tant que référence dans votre projet.

## Utilisation de la testabilité dans PowerShell
Pour utiliser le module PowerShell de testabilité, vous devez installer le runtime MSI. Une fois qu’il est installé, le module PowerShell ServiceFabricTestability est chargé automatiquement, et rendu disponible aux développeurs.

## Conclusion
Pour créer de véritables services de mise à l’échelle du cloud, vous devez vous assurer que vos solutions résistent aux défaillances réelles, préalablement au déploiement et au sein du déploiement de production. Dans le monde des services actuel, vous avez tout intérêt à démontrer une capacité à innover et à déployer rapidement du code en production. En s’appuyant sur la fonction de testabilité de Service Fabric, les développeurs ont les cartes en main pour atteindre ces objectifs.

## Étapes suivantes

- [Actions de testabilité](service-fabric-testability-actions.md)
- [Scénarios de testabilité](service-fabric-testability-actions.md)
- Procédure de test de votre service
	- [Simuler des défaillances au cours des charges de travail de services](service-fabric-testability-workload-tests.md)
   - [Échecs de communication de service à service](service-fabric-testability-scenarios-service-communication.md)

 

<!---HONumber=August15_HO6-->