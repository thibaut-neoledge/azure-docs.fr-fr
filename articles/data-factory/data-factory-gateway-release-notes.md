<properties 
	pageTitle="Notes de version pour la passerelle de gestion des données | Microsoft Azure Data Factory" 
	description="Notes de version pour la passerelle de gestion des données" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2016" 
	ms.author="spelluru"/>

# Notes de version pour la passerelle de gestion des données

Un des défis de l’intégration de données modernes consiste à déplacer en toute transparence des données vers et depuis un site local et le cloud. Azure Data Factory facilite cette intégration avec la passerelle de gestion des données Microsoft, qui est un agent pouvant être installé en local pour activer le déplacement de données hybrides.

Consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus.

## Version actuelle (1.11.5918.1)

- La taille maximale du journal des événements de la passerelle a été augmentée de 1 Mo à 40 Mo.
- Une boîte de dialogue d’avertissement s’affiche si un redémarrage est nécessaire pendant la mise à jour automatique de la passerelle. Vous pouvez choisir de redémarrer immédiatement ou plus tard. 
- En cas d’échec de la mise à jour automatique, le programme d’installation de la passerelle retentera la mise à jour automatique 3 fois au maximum.
- Amélioration des performances
	- Améliorez les performances lors du chargement de tables de grande taille à partir du serveur local dans le scénario de copie sans code.
- Résolution des bogues

## Versions antérieures

## 1\.10.5892.1

- Amélioration des performances
- Résolution des bogues

## 1\.9.5865.2

- Capacité de mise à jour automatique Zero Touch
- Nouvelle icône de barre d’état système avec des indicateurs d’état de la passerelle
- Possibilité de mise à jour immédiate depuis le client
- Possibilité de définir l’heure de planification de la mise à jour
- Script PowerShell disponible pour activer/désactiver la mise à jour automatique
- Prise en charge du format JSON  
- Amélioration des performances
- Résolution des bogues

## 1\.8.5822.1

- Amélioration de l’expérience de résolution des problèmes
- Amélioration des performances
- Résolution des bogues

### 1\.7.5795.1

- Amélioration des performances
- Résolution des bogues

### 1\.7.5764.1

- Amélioration des performances
- Résolution des bogues

### 1\.6.5735.1

- Prise en charge de la source/du récepteur HDFS en local
- Amélioration des performances
- Résolution des bogues

### 1\.6.5696.1

- Amélioration des performances
- Résolution des bogues

### 1\.6.5676.1

- Prise en charge des outils de diagnostic dans le Gestionnaire de configuration
- Prise en charge des colonnes de table pour les sources de données tabulaires pour Microsoft Azure Data Factory
- Prise en charge de SQL DW pour Microsoft Azure Data Factory
- Prise en charge de Reclusive dans BlobSource et FileSource pour Microsoft Azure Data Factory
- Prise en charge de CopyBehavior (MergeFiles, PreserveHierarchy et FlattenHierarchy) dans BlobSink et FileSink avec copie binaire pour Microsoft Azure Data Factory
- Prise en charge du signalement de progression de l’activité de copie pour Microsoft Azure Data Factory
- Prise en charge de la validation de connectivité des sources de données pour Microsoft Azure Data Factory
- Résolution des bogues


### 1\.6.5672.1

- Prise en charge des noms de tables pour la source de données ODBC pour Microsoft Azure Data Factory
- Amélioration des performances
- Résolution des bogues

### 1\.6.5658.1

- Prise en charge du récepteur de fichier pour Microsoft Azure Data Factory
- Prise en charge de la conservation de la hiérarchie dans une copie binaire pour Microsoft Azure Data Factory
- Prise en charge de l’idempotence de l’activité de copie pour Microsoft Azure Data Factory
- Résolution des bogues

### 1\.6.5640.1

- Prise en charge de 3 sources de données supplémentaires pour Microsoft Azure Data Factory (ODBC, OData, HDFS)
- Prise en charge du caractère guillemet dans l’analyseur CSV pour Microsoft Azure Data Factory
- Prise en charge de la compression (BZip2)
- Résolution des bogues

### 1\.5.5612.1

- Prise en charge de 5 bases de données relationnelles pour Microsoft Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata et Sybase)
- Prise en charge de la compression (Gzip et Deflate)
- Amélioration des performances
- Résolution des bogues


### 1\.4.5549.1

- Ajout de la prise en charge de source de données Oracle pour Microsoft Azure Data Factory
- Amélioration des performances
- Résolution des bogues

### 1\.4.5492.1

- Binaire unifié qui prend en charge à la fois des services Microsoft Azure Data Factory et Office 365 Power BI
- Affinage du processus d’inscription et de l’interface utilisateur de configuration
- Microsoft Azure Data Factory : prise en charge des entrées et sorties Azure pour la source de données SQL Server

### 1\.2.5303.1

- 	Correction du problème de délai d’expiration pour la prise en charge de connexions de source de données chronophages supplémentaires. 
 	
### 1\.1.5526.8

- .NET Framework 4.5.1 est requis pour l’installation.

### 1\.0.5144.2

- Aucune modification affectant les scénarios Microsoft Azure Data Factory. 

## Questions et réponses

### Pourquoi le Gestionnaire de sources de données tente-t-il de se connecter à une passerelle ?
Il s’agit d’une conception de sécurité. Elle vous permet uniquement de configurer des sources de données locales pour l’accès au cloud au sein de votre réseau d’entreprise, et vos informations d’identification ne circulent pas en dehors de votre pare-feu d’entreprise. Assurez-vous que votre ordinateur peut atteindre l’ordinateur sur lequel la passerelle est installée.

<!---HONumber=AcomDC_0427_2016-->