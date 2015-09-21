<properties
   pageTitle="Nouveautés d’Azure Data Catalog"
   description="Vue d’ensemble des nouvelles fonctionnalités de la version préliminaire d’Azure Data Catalog."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/04/2015"
   ms.author="maroche"/>

# Nouveautés d'Azure Data Catalog

Les mises à jour d’**Azure Data Catalog** sont publiées régulièrement. Comme certaines versions portent sur les fonctionnalités du service principal, chaque version ne contient pas nécessairement de nouvelles fonctionnalités orientées utilisateur. Cette page présente de nouvelles fonctionnalités orientées utilisateur ajoutées au service **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 4 Septembre 2015

À compter de la semaine du 4 septembre 2015, les fonctionnalités suivantes ont été ajoutées au **Azure Data Catalog** :

- Prise en charge de l'enregistrement manuel de types de sources de données inconnus. Les utilisateurs peuvent entrer manuellement des informations sur les sources de données à l'aide du portail **Azure Data Catalog**, afin que les sources de données non prises en charge explicitement par l'outil d'enregistrement de sources de données puissent être annotées et découvertes.
- Prise en charge de l'enregistrement et de la découverte des bases de données SQL Server en tant que conteneurs. Lors de l'enregistrement des tables et des vues SQL Server, **Azure Data Catalog** crée une entrée pour la base de données, ainsi que pour les tables et les vues. La base de données peut être découverte et annotée à l'aide du portail **Azure Data Catalog**. Les utilisateurs peuvent également rechercher et filtrer le contenu d'une base de données en plus de la recherche et du filtrage du contenu du catalogue.


> [AZURE.NOTE]Les tables et les vues SQL Server qui ont été enregistrées avant la version 2015-09-04 doivent être enregistrées de nouveau à l'aide de l'outil d'inscription de sources de données avant que l'entrée de base de données ne soit ajoutée au catalogue. Le réenregistrement d'une source de données n'affecte pas les annotations qui ont été ajoutées par des utilisateurs dans le portail **Azure Data Catalog**.

## Nouveautés de la version de la semaine du 28 août 2015

À compter de la semaine du 28 août 2015, les fonctionnalités suivantes ont été ajoutées au **Azure Data Catalog** :

- Prise en charge du profilage des données pour les sources de données SQL Server et Oracle. Lors de l'enregistrement des vues et des tables SQL Server et Oracle, les utilisateurs peuvent choisir d'inclure des informations de profil des données pour les objets en cours d'enregistrement. Le profil des données inclut des statistiques au niveau de l'objet et au niveau des colonnes.
- Prise en charge des sources de données HDFS Hadoop. Les utilisateurs peuvent désormais s'inscrire et découvrir les fichiers et répertoires HFDFS.

## Nouveautés de la version de la semaine du 21 août 2015

À compter de la semaine du 21 août 2015, les fonctionnalités suivantes ont été ajoutées au **Azure Data Catalog** :

- Prise en charge de la fourniture d’informations de demande d’accès pour les sources de données inscrites. Pour toute ressource de données inscrite, les utilisateurs peuvent maintenant fournir des instructions de demande d’accès, y compris des liens de messagerie ou des URL, en vue d’une intégration aisée aux outils et processus existants.
- Info-bulles pour balises et experts, pour savoir facilement quels utilisateurs ont fourni quelles métadonnées pour les ressources de données inscrites.
- Nous avons ajouté un nouveau bouton « Utilisateur » et un menu à notre barre de navigation supérieure. Ce menu indique à l'utilisateur le compte avec lequel il se connecte (et éventuellement se déconnecte) au **Azure Data Catalog**. Ce menu affiche également le nom du catalogue, qui est une information utile pour les développeurs qui utilisent l'API REST **Azure Data Catalog**.
- Édition Standard uniquement : quand vous ajoutez des propriétaires aux ressources de données, **Azure Data Catalog** prend désormais en charge les comptes d'utilisateurs et les groupes de sécurité en tant que propriétaires. Pour ajouter un groupe de sécurité en tant que propriétaire pour des ressources de données sélectionnées, vous pouvez entrer le nom d’affichage du groupe ou l’adresse de messagerie UPN du groupe, le cas échéant.
- Prise en charge des sources de données de stockage d’objets blob Azure. Les utilisateurs peuvent désormais s’inscrire et découvrir des objets blob Azure Storage et des répertoires.

<!---HONumber=Sept15_HO2-->