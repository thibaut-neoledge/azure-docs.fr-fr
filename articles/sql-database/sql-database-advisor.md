---
title: "Recommandations relatives au réglage des performances des requêtes - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database Advisor fournit des recommandations pour vos bases de données SQL existantes afin d’améliorer les performances actuelles des requêtes."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & manage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: cf627b92399856af2b9a58ab155fac6730128f85
ms.openlocfilehash: a8d0b08abc7e3c688f9ab79499b3459b33f06848
ms.contentlocale: fr-fr
ms.lasthandoff: 02/02/2017


---
# <a name="sql-database-advisor"></a>SQL Database Advisor

Azure SQL Database apprend et s’adapte à votre application afin de vous fournir des recommandations personnalisées qui vous permettront d’optimiser les performances de vos bases de données SQL. SQL Database Advisor fournit des recommandations concernant la création et la suppression d’index, le paramétrage des requêtes et la résolution des problèmes de schéma. SQL Database Advisor évalue les performances en analysant l’historique d’utilisation de votre base de données SQL. Les recommandations les plus adaptées pour exécuter la charge de travail standard de votre base de données sont fournies. 

Les recommandations suivantes sont disponibles pour les serveurs Azure SQL Database. Actuellement, vous pouvez définir l’application automatique des recommandations de création et de suppression d’index. Pour plus d’informations, consultez [Gestion automatique des index](sql-database-advisor-portal.md#enable-automatic-index-management).

## <a name="create-index-recommendations"></a>Recommandations de création d’index
**Créer un index** s'affichent lorsque le service de base de données SQL détecte un index manquant qui, s’il était créé, pourrait être utile à votre charge de travail de bases de données (index non ordonnés en cluster uniquement).

## <a name="drop-index-recommendations"></a>Recommandations de suppression d’index
**Supprimer un index** s'affichent lorsque le service de base de données SQL détecte des index dupliqués (actuellement en version préliminaire, s'applique aux index en double uniquement).

## <a name="parameterize-queries-recommendations"></a>Recommandations de paramétrage de requêtes
Les recommandations liées au **paramétrage des requêtes** s’affichent lorsque vous disposez d’une ou plusieurs requêtes qui sont constamment recompilées, mais ont en fin de compte le même plan d’exécution de requête. Cela ouvre la possibilité d’appliquer un paramétrage forcé, ce qui permet de mettre en cache les plans de requête et de les réutiliser pour améliorer les performances et réduire l’utilisation des ressources. 

Chaque requête adressée initialement à SQL Server doit être compilée pour générer un plan d’exécution. Chaque plan généré est ajouté au cache du plan et les exécutions ultérieures de la même requête peuvent réutiliser ce plan à partir du cache, évitant ainsi toute compilation supplémentaire. 

Les applications qui envoient des requêtes incluant des valeurs non paramétrées peuvent entraîner une baisse des performances, car le plan d’exécution est recompilé pour chaque requête de ce type avec des valeurs de paramètres différentes. Dans de nombreux cas, les mêmes requêtes avec différentes valeurs de paramètres génèrent les mêmes plans d’exécution, mais ces plans sont toujours ajoutés séparément au cache du plan. La recompilation des plans d’exécution utilise des ressources de base de données, augmente la durée la requête et dépasse la capacité du cache du plan, entraînant la suppression des plans de ce cache. Ce comportement de SQL Server peut être modifié en définissant l’option de paramétrage forcé au niveau de la base de données. 

Pour vous aider à évaluer l’impact de cette recommandation, nous vous fournissons une comparaison entre l’utilisation réelle et l’utilisation projetée du processeur (comme si la recommandation avait été appliquée). Outre le fait que le processeur est moins sollicité, la durée de la requête dans la compilation diminue. La surcharge sur le cache du plan sera également beaucoup moins importante, permettant ainsi à la majorité des plans de rester dans le cache et d’être réutilisés. Vous pouvez appliquer cette recommandation rapidement et facilement en cliquant sur la commande **Appliquer**. 

Une fois appliquée, cette recommandation active en quelques minutes le paramétrage forcé sur votre base de données et démarre le processus de surveillance qui dure environ 24 heures. Après cette période, vous pourrez consulter le rapport de validation qui indique l’utilisation du processeur de votre base de données 24 heures avant et après l’application de la recommandation. SQL Database Advisor intègre un mécanisme de sécurité qui annule automatiquement la recommandation appliquée si une baisse des performances est détectée.

## <a name="fix-schema-issues-recommendations"></a>Recommandations de résolution des problèmes de schéma
Les recommandations liées à la **résolution des problèmes de schéma** s’affichent lorsque le service SQL Database détecte une anomalie dans le nombre d’erreurs SQL liées au schéma qui se produisent dans Azure SQL Database. Cette recommandation s’affiche généralement lorsque votre base de données rencontre plusieurs erreurs liées au schéma (nom de colonne non valide, nom d’objet non valide, etc.) dans la même heure.

Les « problèmes de schéma » représentent une catégorie d’erreurs de syntaxe dans SQL Server, qui se produisent lorsque la définition de la requête SQL et la définition du schéma de la base de données ne sont pas alignées. Par exemple, une des colonnes attendues par la requête est manquante dans la table cible, ou inversement. 

Les recommandations « Résoudre les problèmes de schéma » s’affichent lorsque le service Azure SQL Database détecte une anomalie dans le nombre d’erreurs SQL liées au schéma qui se produisent dans Azure SQL Database. Le tableau suivant répertorie les erreurs liées à des problèmes de schéma :

| Code d'erreur SQL | Message |
| --- | --- |
| 201 |La procédure ou fonction '*’ attend le paramètre ’*', qui n’a pas été fourni. |
| 207 |Nom de colonne non valide '*'. |
| 208 |Nom d'objet non valide ’*’. |
| 213 |Le nom de la colonne ou le nombre de valeurs fournies ne correspond pas à la définition de la table. |
| 2812 |Impossible de trouver la procédure stockée '*'. |
| 8144 |La procédure ou la fonction * a trop d’arguments spécifiés. |

## <a name="next-steps"></a>Étapes suivantes
Surveillez vos recommandations et continuez à les appliquer pour affiner les performances. Les charges de travail d’une base de données sont dynamiques et évoluent en permanence. SQL Database Advisor continue à surveiller et à fournir des recommandations pouvant potentiellement améliorer les performances de votre base de données. 

* Consultez la page [SQL Database Advisor dans le portail Azure](sql-database-advisor-portal.md) pour savoir comment utiliser SQL Database Advisor dans le portail Azure.
* Pour connaître l’impact de vos principales requêtes sur les performances, consultez [Query Performance Insights](sql-database-query-performance.md) (Analyse des performances des requêtes).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md)


