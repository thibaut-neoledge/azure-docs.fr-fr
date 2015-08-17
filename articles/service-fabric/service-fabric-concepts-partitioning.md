<properties
   pageTitle="Partitionnement des services Service Fabric"
   description="Explique comment partitionner les services Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="aprameyr"/>

# Partitionnement des services Service Fabric
Service Fabric facilite la création de services évolutifs avec état en prenant en charge le partitionnement de l'état du service et en faisant fonctionner chaque partition sur un sous-ensemble de l'état global. Chaque partition devient une unité offrant une [haute disponibilité](service-fabric-availability-services.md). Les réplicas des partitions sont distribués entre les nœuds du cluster et sont équilibrés.

> [AZURE.NOTE]Les services sans état peuvent également être partitionnés, mais ce scénario est rare et inutile pour la plupart des services Service Fabric.

Trois schémas de partitionnement différents sont disponibles.

## Schéma de partitionnement singleton
Il permet de spécifier que le service n'a pas besoin d'un partitionnement.

## Schéma de partitionnement nommé
Il permet de spécifier un ensemble fixe de noms pour chaque partition du service. Il est possible de rechercher des partitions individuelles par leur nom.

## Schéma de partitionnement par plage
Il permet de spécifier une plage d'entiers (identifiée par une clé basse et une clé haute) et un nombre de partitions (n). Il crée n partitions, chacune responsable d'une sous-plage qui ne chevauche pas les autres. Exemple : un schéma de partitionnement par plage (pour un service avec trois réplicas) doté d'une clé basse de 0, d'une clé haute de 99 et d'un nombre de 4 créerait 4 partitions telles qu'elles sont illustrées ci-dessous.

![Partitionnement par plage](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Le cas le plus fréquent consiste à créer un hachage pour une clé unique au sein d'un jeu de données. Un numéro d'immatriculation de véhicule, un ID d'employé ou une chaîne unique sont des exemples courants de clés. Vous utilisez cette clé unique pour créer un code de hachage long, modulo la plage de la clé, à utiliser comme clé. Vous pouvez spécifier les limites supérieure et inférieure de la plage de clé autorisée.

En outre, vous devez estimer un nombre de partitions suffisamment élevé pour pouvoir traiter une charge de ressources maximale (telle que les limitations de mémoire ou la bande passante réseau) mais pas si élevé que les partitions seraient extrêmement fragmentées.

### Sélection d'un algorithme de hachage
Une partie importante du hachage consiste à sélectionner l'algorithme de hachage. Il convient de tenir compte de l'objectif du hachage, à savoir si celui-ci est de regrouper des clés similaires proches les unes des autres (hachage sensible à la localité) ou si l'activité doit être distribuée de manière large entre toutes les partitions (hachage de distribution).

[La page Wikipedia sur les fonctions de hachage](http://en.wikipedia.org/wiki/Hash_function) est une ressource proposant de nombreux algorithmes de code de hachage généraux.

> [AZURE.NOTE]Il n'est pas possible de modifier le nombre de partitions ni le type de schéma de partitionnement utilisé pour un service après leur création.

## Étapes suivantes

Pour plus d'informations sur les concepts propres à Service Fabric, consultez les articles suivants :

- [Disponibilité des services Service Fabric](service-fabric-availability-services.md)

- [Extensibilité des services Service Fabric](service-fabric-concepts-scalability.md)
 

<!---HONumber=August15_HO6-->