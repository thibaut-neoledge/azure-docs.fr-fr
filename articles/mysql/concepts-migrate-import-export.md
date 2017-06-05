---
title: "Importation et exportation dans une base de données Azure pour MySQL | Microsoft Docs"
description: "Présente l’importation et l’exportation de bases de données dans la base de données Azure pour MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 582f83e3e80d855049abece42726748fcd113ab7
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-using-import-and-export"></a>Migrer une base de données MySQL à l’aide de l’importation et de l’exportation
Ce didacticiel vous explique le moyen le plus courant pour importer et exporter les données de votre base de données MySQL Azure à l’aide de MySQL Workbench. 

## <a name="before-you-begin"></a>Avant de commencer
Pour parcourir ce guide pratique, vous aurez besoin des éléments suivants :
- Un serveur de base de données Azure pour MySQL [Concevoir sa première base de données MySQL Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- MySQL Workbench [Télécharger MySQL Workbench](https://dev.mysql.com/downloads/workbench/)

## <a name="use-common-tools"></a>Utiliser des outils courants
Utilisez les outils courants tels que MySQL Workbench, Toad ou Navicat pour vous connecter à distance et importer ou exporter les données dans la base de données Azure pour MySQL. Utilisez ces outils sur votre ordinateur client disposant d’une connexion web pour se connecter à la base de données Azure pour MySQL. Utilisez une connexion chiffrée SSL pour appliquer les meilleures pratiques de sécurité ; consultez également la page [Configurer la connectivité SSL dans la base de données Azure pour MySQL](concepts-ssl-connection-security.md). Il est inutile de déplacer les fichiers d’importation et d’exportation dans un emplacement spécifique du cloud lors de la migration vers la base de données Azure pour MySQL. 

## <a name="create-a-database-on-the-target-azure-database-for-mysql-service"></a>Créer une base de données sur le service cible de base de données Azure pour MySQL
Vous devez créer une base de données vide sur le serveur cible de base de données Azure pour MySQL dans lequel vous souhaitez migrer les données avec MySQL Workbench, Toad, Navicat ou tout autre outil tiers pour MySQL. La base de données peut avoir le même nom que celle qui contient les données capturées, mais vous pouvez également créer une base de données avec un autre nom.

![Chaîne de connexion de la base de données Azure pour MySQL](./media/concepts-migrate-import-export/p5.png)

![Chaîne de connexion MySQL Workbench](./media/concepts-migrate-import-export/p4.png)

## <a name="import-and-export-using-mysql-workbench"></a>Importer et exporter à l’aide de MySQL Workbench
Il y a deux façons d’exporter et d’importer des données dans MySQL Workbench, chacune répondant à un objectif différent. 

## <a name="table-data-export-and-import-wizard-using-object-browser-context-menu"></a>Assistant Importation et Exportation de données de table à l’aide du menu contextuel de l’Explorateur d’objets
![Importation/exportation de MySQL Workbench à l’aide du menu contextuel de l’Explorateur d’objets](./media/concepts-migrate-import-export/p1.png)

Cet Assistant prend en charge les opérations d’importation et d’exportation à l’aide de fichiers CSV et JSON, et comprend plusieurs options de configuration (séparateurs, sélection de colonnes, sélection du codage, etc.). L’Assistant peut s’appliquer à des serveurs MySQL locaux ou connectés à distance, et l’action d’importation comprend le mappage de types, de colonnes et de tables. L’Assistant est accessible dans le menu contextuel de l’Explorateur d’objets : cliquez avec le bouton droit sur une table et choisissez **Assistant Exportation de données de table** ou **Assistant Importation de données de table**. 

## <a name="table-data-export-wizard"></a>Assistant Exportation de données de table
L’exemple suivant exporte la table dans un fichier CSV. 
- Cliquez avec le bouton droit sur la table de la base de données à exporter. 
- Sélectionnez **Assistant Exportation de données de table**. Sélectionnez les colonnes à exporter, le décalage de ligne (le cas échéant) et le nombre (le cas échéant). 
- Cliquez sur **Suivant** dans la fenêtre « Sélectionner les données à exporter ». Sélectionnez le chemin d’accès du fichier, le type de fichier CSV ou JSON, le séparateur de ligne, les caractères entourant les chaînes et le séparateur de champs. 
- Sélectionnez **Suivant** dans la fenêtre « Sélectionner l’emplacement du fichier de sortie » et cliquez sur Suivant dans la fenêtre « Exporter les données ».


## <a name="table-data-import-wizard"></a>Assistant Importation de données de table
L’exemple suivant importe la table à partir d’un fichier CSV.
- Cliquez avec le bouton droit sur la table de la base de données à importer. 
- Recherchez et sélectionnez le fichier CSV à importer, puis cliquez sur le bouton Suivant. 
- Sélectionnez la table de destination (nouvelle ou existante), cochez ou décochez la case « Tronquer la table avant l’importation » et cliquez sur le bouton Suivant.
- Sélectionnez le codage et les colonnes à importer, puis sélectionnez le bouton Suivant. 
- Sélectionnez Suivant sur la fenêtre Importer des données ; les données sont importées en conséquence.

## <a name="sql-data-export-and-import-wizard-from-management-navigator"></a>Assistant Importation et Exportation de données SQL à partir du navigateur de gestion
Utilisez cet Assistant pour exporter ou importer des requêtes SQL générées à partir de MySQL Workbench ou avec la commande mysqldump. Accédez à ces Assistants à partir du panneau de navigation ou en sélectionnant Serveur dans le menu principal, puis Importation de données ou Exportation de données. 

## <a name="data-export"></a>Exportation de données
![Exportation de données MySQL Workbench à l’aide du navigateur de gestion](./media/concepts-migrate-import-export/p2.png)

Cet onglet vous permet d’exporter vos données MySQL. 
- Sélectionnez chaque schéma que vous voulez exporter, choisissez éventuellement des tables/objets spécifiques pour chacun et générez l’exportation. Parmi les options de configuration figurent la possibilité d’exporter dans un dossier de projet ou un fichier SQL autonome, éventuellement de vider les événements et les routines stockés et d’ignorer les données de table. 
- Vous pouvez également utiliser **Exporter un jeu de résultats** pour exporter un jeu de résultats en particulier de l’éditeur SQL vers un autre format, par exemple CSV, JSON, HTML et XML. 
- Sélectionnez les objets de base de données à exporter et configurez les options associées. 
- Cliquez sur **Actualiser** pour charger les objets actifs. 
- Vous pouvez également ouvrir l’onglet Options avancées pour affiner l’opération d’exportation. Par exemple, ajoutez des verrous de table, utilisez des instructions replace à la place des instructions insert, placer les identificateurs entre accents graves, etc. 
- Cliquez sur **Démarrer l’exportation** pour commencer le processus d’exportation. 


## <a name="data-import"></a>Importation de données
![Importation de données MySQL Workbench à l’aide du navigateur de gestion](./media/concepts-migrate-import-export/p3.png)

Cet onglet permet d’importer ou de restaurer les données exportées avec l’opération Exportation de données ou d’autres données exportées avec la commande mysqldump. 
- Choisissez le dossier du projet ou un fichier SQL autonome, sélectionnez le schéma dans lequel les données seront importées, ou cliquez sur Nouveau pour définir un nouveau schéma. 
- Cliquez sur **Démarrer l’importation** pour commencer le processus d’importation ; les données sont importées en conséquence.

## <a name="next-steps"></a>Étapes suivantes
Si vous ne savez pas comment faire pour bien démarrer avec ce service de base de données, consultez :
-  [Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md) 
- [Création d’un serveur de base de données Azure pour MySQL à l’aide d’Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

