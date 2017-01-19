---
title: "Planifier votre mise à niveau vers SQL Database V12 | Microsoft Docs"
description: "Décrit la préparation et les limitations relatives à la mise à niveau vers la version V12 d’Azure SQL Database."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
ms.assetid: 8020f904-ad27-40c5-8c59-bdf2e690f77d
ms.service: sql-database
ms.custom: V11
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: genemi
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: 237aa26a40c34d8fb511881604cb26bf9380322e


---
# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>Planification et préparation de la mise à niveau vers SQL Database V12
Cette rubrique décrit la planification et la préparation nécessaires pour mettre à niveau vos bases de données SQL Azure de la version V11 vers la V12.

Un nouveau [portail Azure](https://portal.azure.com/) est disponible pour prendre en charge la mise à jour vers la V12.

Le tableau suivant répertorie les autres rubriques d’aide relatives à la version 12.

| Titre et lien | Description du contenu |
|:--- |:--- |
| [Fonctionnalités d’Azure SQL Database](sql-database-features.md) |Fournit une matrice des fonctionnalités d’Azure SQL Database comparées à celles de SQL Server. |
| [Création d’une base de données dans SQL Database V12](sql-database-get-started.md) |Décrit la procédure à suivre pour créer une base de données SQL Azure V12. Cette section décrit également les différentes options disponibles à partir d’une simple base de données vide. |

## <a name="plan-ahead"></a>Planification
Les sous-sections suivantes décrivent les connaissances nécessaires et les décisions à prendre avant d’effectuer la mise à jour de votre base de données SQL Azure vers la V12.

### <a name="version-clarification"></a>Clarification de version
Ce document concerne la mise à niveau de Microsoft Azure SQL Database de la version V11 à la version V12. De manière plus formelle, les numéros de version sont proches des deux valeurs suivantes, comme indiqué par l’instruction Transact-SQL **SELECT @@version;** :

* 12.0.2000.8 *(ou légèrement supérieure, version 12)*
* 11.0.9228.18 *(V11)*
  * Parfois, la V11 était également appelée SAWA v2.

### <a name="service-tier-planning"></a>Planification du niveau de service
À partir de la version V12, Azure SQL Database prend en charge uniquement les niveaux de service De base, Standard et Premium. Les niveaux de service Web et Business ne sont pas pris en charge dans la V12. Par conséquent, si vous prévoyez de mettre à jour votre base de données SQL Azure actuellement définie sur le niveau de service Web ou Business, vous devez choisir un niveau de service.

Pour obtenir des informations détaillées sur les niveaux de service De base, Standard et Premium, voir :

* [Niveaux de service SQL Database](sql-database-service-tiers.md)
* [Mise à niveau des bases de données SQL des éditions Web et Business vers les nouveaux niveaux de service](sql-database-upgrade-server-portal.md)

### <a name="review-the-geo-replication-configuration"></a>Examen de la configuration de géoréplication
Si votre base de données SQL Azure est configurée pour la géoréplication, vous devez documenter la configuration actuelle et arrêter la géoréplication avant de commencer à préparer la mise à niveau. Une fois la mise à jour terminée, vous devez reconfigurer votre base de données pour la géoréplication.

La stratégie consiste à laisser la source intacte et à effectuer les tests sur une copie de la base de données.

## <a name="preparation-actions"></a>Préparation
Une fois que vous avez terminé votre planification, vous pouvez effectuer les actions décrites dans les sous-sections suivantes pour vous préparer à la phase finale de la mise à niveau.

Une description détaillée de la dernière phase de la mise à niveau est disponible à partir des liens vers les rubriques d’aide en haut de cette page.

### <a name="service-tier-actions"></a>Actions relatives au niveau de service
Le niveau tarifaire du service Web et Business n’est pas pris en charge dans V12.

Si votre base de données SQL Azure V11 est une base de données Web ou Business, le processus de mise à jour vous propose de basculer votre base de données sur un niveau pris en charge. La mise à niveau recommande un niveau qui correspond à l’historique de la charge de travail de votre base de données. Toutefois, vous pouvez choisir n’importe quel niveau pris en charge.

Vous pouvez réduire le nombre d’étapes nécessaires pour la mise à jour en changeant le niveau Web et Business de votre base de données V11 avant de commencer le processus. Pour ce faire, utilisez le nouveau [portail Azure](https://portal.azure.com/).

Si vous n’êtes pas sûr du niveau de service vers lequel basculer, nous vous recommandons le niveau S2 de l’édition Standard. Si vous choisissez un niveau inférieur, vous disposerez probablement d’un nombre de ressources moins important que ce qu’offrait le niveau « Web et Business ».

### <a name="suspend-geo-replication-during-upgrade"></a>Suspension de la géoréplication pendant la mise à niveau
La mise à niveau vers la V12 ne peut pas s’exécuter si la géoréplication est active sur votre base de données. Vous devez d’abord reconfigurer votre base de données pour qu’elle n’utilise plus la géoréplication.

Une fois la mise à niveau terminée, vous pouvez configurer votre base de données pour qu’elle utilise à nouveau la géoréplication.

### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>Ouvrir des ports sur une machine virtuelle Azure pour la connectivité cliente
Si votre programme client se connecte à SQL Database V12 pendant que votre client s’exécute sur une machine virtuelle Azure, vous devez ouvrir les plages de ports suivantes sur la machine virtuelle :

* 11000-11999
* 14000-14999

Cliquez [ici](sql-database-develop-direct-route-ports-adonet-v12.md) pour plus d’informations sur les ports associés à SQL Database V12. Les ports sont requis pour prendre en charge les améliorations des performances apportées à SQL Database V12.

## <a name="a-idlimitationsalimitations-during-and-after-upgrade-to-v12"></a><a id="limitations"></a>Limitations pendant et après la mise à niveau vers la version 12
### <a name="portals-for-v12"></a>Portails relatifs à la version 12
Il existe trois portails pour Microsoft Azure ; chacun d’eux présente des capacités différentes concernant SQL Database V12.

* [http://portal.azure.com/](https://portal.azure.com/)<br/>Ce nouveau portail Microsoft Azure est toujours en version préliminaire. Il n’a pas encore atteint le niveau de disponibilité générale. Ce portail :
  
  * peut gérer vos serveur et base de données version 12 ;
  * peut mettre à niveau votre base de données version 11 vers la version 12.
* [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Ce portail Microsoft Azure Classic fera peut-être l’objet d’une transition. Ce portail :
  
  * peut gérer vos serveur et base de données version 12 ;
  * ne peut *pas* mettre à niveau votre base de données version 11 vers la version 12.
* (http://*votre_nom_utilisateur*.database.windows.net)<br/> Portail Azure SQL Database Classic :
  
  * ne peut*pas* gérer les serveurs version 12.

Nous vous encourageons à vous connecter à vos bases de données SQL Azure avec Visual Studio 2015 (VS2015). VS2015 peut être utilisé pour les tâches suivantes :

* Pour exécuter une instruction Transact-SQL.
* Pour concevoir un schéma.
* Pour développer une base de données en ligne ou hors connexion.

Vous pouvez également télécharger gratuitement [Visual Studio Community 2015](https://www.visualstudio.com/vs/community/), qui est une version complète de VS2015.

Dans l’ancien portail Azure Classic, sur la page des bases de données, vous pouvez cliquer sur **Ouvrir dans Visual Studio** pour lancer VS2015 sur votre ordinateur afin de vous connecter à votre base de données SQL Azure.

Vous pouvez également utiliser SQL Server Management Studio (SSMS) 2014 avec [CU6](http://support.microsoft.com/kb/3031047/) pour vous connecter à la base de données SQL Azure. Pour en savoir plus, consultez ce billet de blog :<br/>[Mises à jour des outils clients pour la base de données SQL Azure](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).

> [!IMPORTANT]
> Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="limitation-during-upgrade-to-v12"></a>Limitations à prendre en compte *durant* la mise à niveau vers la version 12
La base de données V11 est toujours disponible pour l’accès aux données pendant la mise à niveau vers la version 12. Il existe toutefois quelques limitations à prendre en compte.

| Limitations à prendre en compte  | Description |
|:--- |:--- |
| Durée de la mise à niveau |La durée de la mise à niveau dépend de la taille, de l’édition et du nombre de bases de données sur le serveur. La mise à niveau peut prendre plusieurs heures ou plusieurs jours, notamment pour les serveurs dont les bases de données présentent les caractéristiques suivantes :<br/><br/>* De plus de 50 Go ou<br/>* À un niveau de service non-premium<br/><br/>La création de bases de données sur le serveur pendant la mise à niveau peut également augmenter la durée de cette opération. |
| Aucune géoréplication |La géoréplication n’est pas prise en charge sur un serveur V12 impliqué dans une mise à niveau à partir de V11. |
| La base de données est temporairement indisponible à l’étape finale de la mise à niveau vers V12. |Les bases de données appartenant à votre serveur V11 restent disponibles pendant le processus de mise à niveau. Toutefois, la connexion au serveur et aux bases de données est temporairement indisponible à l’étape finale, quand le basculement commence du serveur V11 vers le serveur V12 prêt.<br/><br/>La période de basculement peut varier de 40 secondes à 5 minutes. Pour la plupart des serveurs, le basculement ne prend pas plus de 90 secondes. Le temps de basculement augmente pour les serveurs qui ont un grand nombre de bases de données ou lorsque les bases de données ont des charges de travail d’écriture lourdes. |

### <a name="limitation-after-upgrade-to-v12"></a>Limitations à prendre en compte *après* la mise à niveau vers la version 12
| Limitations à prendre en compte  | Description |
|:--- |:--- |
| Impossible de rétablir la V11 |Après une mise à niveau sur place, le résultat ne peut pas être restauré ou annulé. |
| Niveau Web ou Business |Une fois que la mise à niveau démarre, le serveur de la nouvelle base de données V12 ne peut plus reconnaître ou accepter le niveau de service Web ou Business. |

### <a name="export-and-import-after-upgrade-to-v12"></a>Exportation et importation *après* la mise à niveau vers la version 12
Vous pouvez exporter ou importer une base de données V12 à l’aide du [portail Azure](https://portal.azure.com/). Vous pouvez également l’exporter ou l’importer à l’aide des outils suivants :

* SQL Server Management Studio (SSMS)
* Visual Studio 2015
* Infrastructure d’application de la couche Données (DacFx)

Toutefois, pour utiliser les outils, vous devez d’abord installer leurs dernières mises à jour pour vous assurer qu’ils prennent en charge les nouvelles fonctionnalités de la V12 :

* [Mise à jour cumulative 6 pour SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
* [Mise à jour de février 2015 pour les outils de base de données SQL Server dans Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
* [Infrastructure d’application de la couche Données (DacFx) de février 2015 pour Azure SQL Database V12](http://www.microsoft.com/download/details.aspx?id=45886)

> [!NOTE]
> Les liens des outils précédents ont été mis à jour le 2 mars 2015 ou après. Nous vous recommandons d’utiliser les mises à jour plus récentes de ces outils.
> 
> 

### <a name="restore-to-v12-of-a-deleted-v11-database"></a>Restauration vers V12 d’une base de données V11 supprimée
Le scénario suivant explique qu’une base de données SQL Azure V11 supprimée peut être restaurée sur un serveur de base de données SQL Azure V12.

1. Supposons que vous disposez d’un serveur de base de données SQL Azure V11. <br/>  Sur le serveur se trouve une base de données présentant un niveau de service « Web et Business » obsolète.
2. Vous supprimez la base de données.
3. Vous mettez à jour le serveur vers V12.
4. Ensuite, vous restaurez la base de données sur le serveur. <br/>  La base de données est ainsi mise à niveau vers la version 12, au niveau S0 de l’édition Standard du service.
5. Vous pouvez basculer la base de données vers n’importe quel niveau de service pris en charge, si S0 ne vous convient pas.

### <a name="powershell-cmdlets"></a>Applets de commande PowerShell
Des applets de commande PowerShell sont disponibles pour démarrer, arrêter ou surveiller une mise à niveau vers Azure SQL Database V12, à partir d’une version 11 ou antérieure.

* [Mise à niveau vers SQL Database V12 à l’aide de PowerShell](sql-database-upgrade-server-powershell.md)

Pour obtenir une documentation de référence sur ces applets de commande Powershell, consultez :

* [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

L’applet de commande Stop- signifie « annuler », et non « interrompre ». Il est impossible de reprendre une mise à niveau en cours. Il faut la recommencer au début. L’applet de commande Stop- nettoie et libère toutes les ressources appropriées.

## <a name="failure-resolution"></a>Résolution des problèmes
Si la mise à niveau échoue pour une raison quelconque, votre base de données V11 reste active et disponible comme d’habitude.

## <a name="related-links"></a>Liens connexes
*  [Fonctionnalités de la version préliminaire](https://azure.microsoft.com/services/preview/)

<!--Anchors-->
[Subheading 1]: #subheading-1



<!--HONumber=Dec16_HO1-->


