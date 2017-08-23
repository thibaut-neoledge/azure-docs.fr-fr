---
title: "Sources de données prises en charge dans Azure Data Catalog | Microsoft Docs"
description: "Cet article répertorie les spécifications des sources de données prises en charge actuellement."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 1ef3903bc4c401988c0f8b27dc9f9f0f3ec99466
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017

---

# <a name="supported-data-sources-in-azure-data-catalog"></a>Sources de données prises en charge dans Azure Data Catalog

Vous pouvez publier des métadonnées à l’aide d’une API publique ou d’un outil d’inscription par simple clic, ou en entrant manuellement les informations directement dans le portail web Azure Data Catalog. Le tableau ci-dessous récapitule toutes les sources de données actuellement prises en charge par le catalogue, ainsi que les fonctionnalités de publication disponibles pour chacune d’elles. Sont également répertoriés les outils de données externes que chaque source de données peut initier à partir du portail. Le deuxième tableau contient une spécification plus technique de chaque propriété de connexion de source de données.


## <a name="list-of-supported-data-sources"></a>Liste des sources de données prises en charge

<table>
    <tr>
       <td><b>Objet de source de données</b></td>
       <td><b>API</b></td>
       <td><b>Saisie manuelle</b></td>
       <td><b>Outil d’inscription</b></td>
       <td><b>Outils d’ouverture intégrés</b></td>
       <td><b>Remarques</b></td>
    </tr>
    <tr>
      <td>Répertoire Azure Data Lake Store</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Fichier Azure Data Lake Store</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Stockage d'objets blob Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Répertoire de stockage Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table de stockage Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>
    <tr>
      <td>Répertoire HDFS</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Fichier HDFS</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table hive</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Affichage hive</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table MySQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue MySQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table de base de données Oracle</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue de base de données Oracle</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Autre (ressource générique)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table Azure SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Dimension SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Indicateur de performance clé de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Mesure SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Rapport SQL Server Reporting Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Browser</font></td>
      <td><font size=2>Serveurs en mode natif uniquement. Le mode SharePoint n’est pas pris en charge.</font></td>
    </tr>
    <tr>
      <td>Table SQL Server</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue SQL Server</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table Teradata</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue Teradata</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue SAP HANA</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table DB2</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue DB2</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Fichier de système de fichiers</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Répertoire FTP</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Fichier FTP</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Rapport HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Point de terminaison HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Fichier HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Jeu d’entités OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Fonction OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table PostgreSQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue PostgreSQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue SAP HANA</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td> Objet Salesforce</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Liste SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Collection Azure Cosmos DB</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table ODBC générique</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue ODBC générique</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table Cassandra</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publier en tant que ressource ODBC générique</font></td>
    </tr>
    <tr>
      <td>Vue Cassandra</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publier en tant que ressource ODBC générique</font></td>
    </tr>
    <tr>
      <td>Table Sybase</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vue Sybase</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Table MongoDB</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publier en tant que ressource ODBC générique</font></td>
    </tr>
    <tr>
      <td>Vue MongoDB</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publier en tant que ressource ODBC générique</font></td>
    </tr>
</table>

Si vous avez besoin d’une prise en charge de sources supplémentaires, envoyez une demande de fonctionnalité au [forum Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


## <a name="data-source-reference-specification"></a>Spécification de référence de la source de données
> [!NOTE]
> La colonne **Structure DSL** du tableau ci-dessous répertorie uniquement les propriétés de connexion du conteneur de propriétés « address » qui sont utilisées par Azure Data Catalog. Autrement dit, le jeu de propriétés « address » peut contenir d’autres propriétés de connexion de la source de données qu’Azure Data Catalog conserve mais n’utilise pas.

<table>
    <tr>
       <td><b>Type de source</b></td>
       <td><b>Type de ressource</b></td>
       <td><b>Types d’objets</b></td>
       <td><b>Structure DSL<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Conteneur</td>
      <td>Data Lake</td>
      <td>
        <font size=2> Protocole : webhdfs <br>Authentification : {de base, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Table</td>
      <td>Répertoire, fichier</td>
      <td>
        <font size=2> Protocole : webhdfs <br>Authentification : {de base, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Conteneur</td>
      <td>Conteneur</td>
      <td>
        <font size=2> Protocole : azure-blobs <br>Authentication : {azure-access-key} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; container </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Table</td>
      <td>Objet blob, répertoire</td>
      <td>
        <font size=2> Protocole : azure-blobs <br>Authentication : {azure-access-key} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; conteneur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Conteneur</td>
      <td>Conteneur</td>
      <td>
        <font size=2> Protocole : azure-tables <br>Authentication : {azure-access-key} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        <font size=2> Protocole : azure-tables <br>Authentication : {azure-access-key} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Conteneur</td>
      <td>Cluster virtuel</td>
      <td>
        <font size=2> Protocole : cosmos <br>Authentification : {de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Table</td>
      <td>Flux, ensemble de flux, vue</td>
      <td>
        <font size=2> Protocole : cosmos <br>Authentification : {de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Conteneur</td>
      <td>Site</td>
      <td>
        <font size=2> Protocole : http <br>Authentification : {aucune, de base, windows, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Rapport</td>
      <td>Rapport, tableau de bord</td>
      <td>
        <font size=2> Protocole : http <br>Authentification : {aucune, de base, windows, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : db2 <br>Authentification : {de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Table</td>
      <td>Table, vue</td>
      <td>
        <font size=2> Protocole : db2 <br>Authentification : {de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema </font>
      </td>
    </tr>
    <tr>
      <td>Système de fichiers</td>
      <td>Table</td>
      <td>Fichier</td>
      <td>
        <font size=2> Protocole : file <br>Authentification : {aucune, de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Table</td>
      <td>Répertoire, fichier</td>
      <td>
        <font size=2> Protocol : ftp <br>Authentification : {aucune, de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Conteneur</td>
      <td>Cluster</td>
      <td>
        <font size=2> Protocole : webhdfs <br>Authentification : {de base, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Table</td>
      <td>Répertoire, fichier</td>
      <td>
        <font size=2> Protocole : webhdfs <br>Authentification : {de base, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : hive <br>Authentification : {hdinsight, de base, nom d’utilisateur, aucune} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>connectionProperties : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Table</td>
      <td>Table, vue</td>
      <td>
        <font size=2> Protocole : hive <br>Authentification : {hdinsight, de base, nom d’utilisateur, aucune} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>connectionProperties : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Conteneur</td>
      <td>Site</td>
      <td>
        <font size=2> Protocole : http <br>Authentification : {aucune, de base, windows, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Rapport</td>
      <td>Rapport, tableau de bord</td>
      <td>
        <font size=2> Protocole : http <br>Authentification : {aucune, de base, windows, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Table</td>
      <td>Point de terminaison, fichier</td>
      <td>
        <font size=2> Protocole : http <br>Authentification : {aucune, de base, windows, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : mysql <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Table</td>
      <td>Table, vue</td>
      <td>
        <font size=2> Protocole : mysql <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Conteneur</td>
      <td>Conteneur d’entités</td>
      <td>
        <font size=2> Protocole : odata <br>Authentification : {aucune, de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Table</td>
      <td>Jeu d’entités, fonction</td>
      <td>
        <font size=2> Protocole : odata <br>Authentification : {aucune, de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; resource </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : oracle <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Table</td>
      <td>Table, vue</td>
      <td>
        <font size=2> Protocole : oracle <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : postgresql <br>Authentification : {de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Table</td>
      <td>Table, vue</td>
      <td>
        <font size=2> Protocole : postgresql <br>Authentification : {de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Conteneur</td>
      <td>Site</td>
      <td>
        <font size=2> Protocole : http <br>Authentification : {aucune, de base, windows, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Rapport</td>
      <td>Rapport, tableau de bord</td>
      <td>
        <font size=2> Protocole : http <br>Authentification : {aucune, de base, windows, oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Table</td>
      <td>Application Web hybride</td>
      <td>
        <font size=2> Protocole : power-query <br>Authentification : {oauth} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Table</td>
      <td>Object</td>
      <td>
        <font size=2> Protocole : salesforce-com <br>Authentification : {de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Conteneur</td>
      <td>Serveur</td>
      <td>
        <font size=2> Protocole : sap hana-sql <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Table</td>
      <td>Affichage</td>
      <td>
        <font size=2> Protocole : sap hana-sql <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Table</td>
      <td>Énumérer</td>
      <td>
        <font size=2> Protocole : sharepoint-list <br>Authentification : {de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Commande</td>
      <td>Procédure stockée</td>
      <td>
        <font size=2> Protocole : tds <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Fonction table</td>
      <td>
        <font size=2> Protocole : tds <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : tds <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Table</td>
      <td>Table, vue</td>
      <td>
        <font size=2> Protocole : tds <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Commande</td>
      <td>Procédure stockée</td>
      <td>
        <font size=2> Protocole : tds <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Fonction table</td>
      <td>
        <font size=2> Protocole : tds <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : tds <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Table</td>
      <td>Table, vue</td>
      <td>
        <font size=2> Protocole : tds <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionnel</td>
      <td>Conteneur</td>
      <td>Modèle</td>
      <td>
        <font size=2> Protocole : analysis-services <br>Authentification : {windows, de base, anonyme, aucune} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionnel</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> Protocole : analysis-services <br>Authentification : {windows, de base, anonyme, aucune} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionnel</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> Protocole : analysis-services <br>Authentification : {windows, de base, anonyme, aucune} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionnel</td>
      <td>Table</td>
      <td>Dimension</td>
      <td>
        <font size=2> Protocole : analysis-services <br>Authentification : {windows, de base, anonyme, aucune} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>Table SQL Server Analysis Services</td>
      <td>Conteneur</td>
      <td>Modèle</td>
      <td>
        <font size=2> Protocole : analysis-services <br>Authentification : {windows, de base, anonyme, aucune} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model </font>
      </td>
    </tr>
    <tr>
      <td>Table SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> Protocole : analysis-services <br>Authentification : {windows, de base, anonyme, aucune} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Table SQL Server Analysis Services</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> Protocole : analysis-services <br>Authentification : {windows, de base, anonyme, aucune} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>Table SQL Server Analysis Services</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        <font size=2> Protocole : analysis-services <br>Authentification : {windows, de base, anonyme, aucune} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Conteneur</td>
      <td>Serveur</td>
      <td>
        <font size=2> Protocole : reporting-services <br>Authentification : {windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Rapport</td>
      <td>Rapport</td>
      <td>
        <font size=2> Protocole : reporting-services <br>Authentification : {windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : teradata <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Table</td>
      <td>Table, vue</td>
      <td>
        <font size=2> Protocole : teradata <br>Authentification : {protocole, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Conteneur</td>
      <td>Modèle</td>
      <td>
        <font size="2"> Protocole : mssql-mds <br>Authentification : {windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Table</td>
      <td>Entité</td>
      <td>
        <font size="2"> Protocole : mssql-mds <br>Authentification : {windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entity </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : document-db <br>Authentication : {azure-access-key} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Collection</td>
      <td>Collection</td>
      <td>
        <font size=2> Protocole : document-db <br>Authentication : {azure-access-key} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;Collection &nbsp;&nbsp;&nbsp;&nbsp;</font>
      </td>
    </tr>
    <tr>
      <td>ODBC générique</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : odbc <br>Authentification : {de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; options <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>ODBC générique</td>
      <td>Table</td>
      <td>Table, Vue</td>
      <td>
        <font size=2> Protocole : odbc <br>Authentification : {de base, windows} <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; options <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2> Protocole : sybase <br>authentification : {base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Table</td>
      <td>Table, Vue</td>
      <td>
        <font size=2> Protocole : sybase <br>authentification : {base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>Autre (aucune des options ci-dessus)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> Protocole : generic-asset <br>Adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>

