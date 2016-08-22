<properties 
	pageTitle="Assistant Data Factory Copy | Microsoft Azure" 
	description="Découvrez comment utiliser l’Assistant Data Factory Copy pour copier des données à partir de sources de données prises en charge dans des récepteurs." 
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
	ms.date="07/27/2016" 
	ms.author="spelluru"/>

# Assistant Data Factory Copy
**L’Assistant Data Factory Copy** vous permet de créer un pipeline pour copier des données de sources prises en charge vers des destinations sans avoir à écrire de définitions JSON pour les services liés, les jeux de données et les pipelines. Pour lancer l’Assistant Data Factory Copy, cliquez sur la vignette **Copier les données** sur la page d’accueil de votre fabrique de données.

![Assistant Copier des données](./media/data-factory-copy-wizard/copy-data-wizard.png)

## Caractéristiques

### Un Assistant intuitif et rapide pour copier des données 
Cet Assistant vous permet de déplacer facilement et rapidement des données d’une source vers une destination en procédant comme suit :

1.	Sélectionnez la **source**
2.	Sélectionnez la **destination**
3.	Configurez les **paramètres**

![Sélectionnez la source de données](./media/data-factory-copy-wizard/select-data-source-page.png)

### Des options complètes d’exploration des données et de mappage de schéma
Utilisez l’Assistant pour parcourir des tables et des dossiers, afficher un aperçu des données, mapper un schéma, valider des expressions et effectuer des transformations de données simples.

**Parcourir des tables et des dossiers** ![Parcourir des tables et des dossiers](./media/data-factory-copy-wizard/browse-tables-folders.png)

### Une expérience évolutive pour divers types d’objets et de données
Dès le début, l’expérience est conçue pour traiter des données volumineuses (Big Data). Vous disposez d’un outil simple et performant pour créer des pipelines Data Factory capables de déplacer des centaines de dossiers, fichiers ou tables.

**Afficher un aperçu des données, mapper un schéma et exécuter des transformations simples** ![Paramètres de format de fichier](./media/data-factory-copy-wizard/file-format-settings.png) ![Mappage de schéma](./media/data-factory-copy-wizard/schema-mapping.png) ![Valider des expressions](./media/data-factory-copy-wizard/validate-expressions.png)

### Une expérience évolutive pour divers types d’objets et de données
Dès le début, l’expérience est conçue pour traiter des données volumineuses (Big Data). L’Assistant Copy est un moyen simple et efficace de déplacer des centaines de dossiers, fichiers ou tables.

![Sélectionner des tables pour la copie de données](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

### Des options de planification enrichies
Vous pouvez effectuer l’opération de copie une seule fois ou la répéter selon une planification établie (horaire, quotidienne, etc.). Ces deux options peuvent être définies pour l’ensemble des connecteurs utilisés pour la copie locale, dans le cloud ou sur un Bureau local. La copie unique vous permet de déplacer une seule fois des données d’une source vers une destination. Elle peut être utilisée pour les données de toute taille et de n’importe quel format pris en charge. La copie planifiée vous permet de copier des données selon une périodicité définie. Vous pouvez configurer la copie planifiée à l’aide de paramètres avancés (nouvelle tentative, délai d’attente, alertes, etc.).

![Propriétés de planification](./media/data-factory-copy-wizard/scheduling-properties.png)


## Didacticiel
Pour découvrir une procédure pas à pas rapide sur l’utilisation de **l’Assistant Data Factory Copy** afin de créer un pipeline avec une activité de copie, consultez le [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md).


## Variables dans le chemin du dossier d’objets blob Azure
Vous pouvez utiliser des variables dans le chemin du dossier pour copier des données à partir d’un dossier qui est déterminé au moment de l’exécution par la [variable système WindowStart](data-factory-functions-variables.md#data-factory-system-variables). Les variables prises en charge sont les suivantes : **year**, **month**, **day**, **hour**, **minute** et **{custom}**. Exemple : inputfolder/{year}/{month}/{day}.

Supposons que vos dossiers d’entrée présentent le format suivant :
	
	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

Cliquez sur le bouton **Parcourir** à côté de **Fichier ou dossier**, accédez à l’un de ces dossiers (par exemple, 2016->03->01->02), puis cliquez sur **Choisir**. Vous devez maintenant voir **2016/03/01/02** dans la zone de texte. À présent, remplacez **2016** par **{year}**, **03** par **{month}**, **01** par **{day}** et **02** par **{hour}**, puis appuyez sur **la touche de tabulation**. Vous devez maintenant voir des listes déroulantes pour sélectionner le **format** de ces quatre variables, comme illustré ci-dessous :

![Utilisation de variables système](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)

Vous pouvez également configurer une variable **custom**, comme indiqué ci-dessous, pour utiliser l’une des [chaînes de format prises en charge](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Pour afficher la zone de texte où entrer la chaîne de format, vous devez d’abord sélectionner un dossier avec cette structure à l’aide du bouton Parcourir, remplacer une valeur par **{custom}** et appuyer sur **la touche de tabulation**.

![Utilisation de la variable custom](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## Étapes suivantes
- [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md)

<!---HONumber=AcomDC_0810_2016-->