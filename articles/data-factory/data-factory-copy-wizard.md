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
L’Assistant Azure Data Factory Copy vous permet de créer un pipeline pour copier des données de sources prises en charge vers des destinations sans avoir à écrire de définitions JSON pour les services liés, les jeux de données et les pipelines. Pour lancer l’Assistant Data Factory Copy, cliquez sur la mosaïque **Copier les données** sur la page d’accueil de votre fabrique de données.

![Assistant de copie](./media/data-factory-copy-wizard/copy-data-wizard.png)

## Caractéristiques

### Un Assistant intuitif et rapide pour copier des données
Cet Assistant vous permet de déplacer facilement et rapidement des données d’une source vers une destination en procédant comme suit :

1.	Sélectionnez la source.
2.	Sélectionnez la destination.
3.	Configurez les paramètres.

![Sélectionnez la source de données](./media/data-factory-copy-wizard/select-data-source-page.png)

### Des options complètes d’exploration des données et de mappage de schéma
Utilisez l’Assistant pour parcourir des tables et des dossiers, afficher un aperçu des données, mapper un schéma, valider des expressions et effectuer des transformations de données simples.

**Parcourir des tables et des dossiers** ![Parcourir des tables et des dossiers](./media/data-factory-copy-wizard/browse-tables-folders.png)

### Une expérience évolutive pour divers types d’objets et de données
Dès le début, l’expérience est conçue pour traiter des données volumineuses (Big Data). Vous disposez d’un outil simple et performant pour créer des pipelines Data Factory capables de déplacer des centaines de dossiers, fichiers ou tables.

**Afficher un aperçu des données** ![Paramètres de format de fichier](./media/data-factory-copy-wizard/file-format-settings.png)

**Mapper un schéma** ![Mappage de schéma](./media/data-factory-copy-wizard/schema-mapping.png)

**Effectuer des transformations simples** ![Valider des expressions](./media/data-factory-copy-wizard/validate-expressions.png)

### Une expérience évolutive pour divers types d’objets et de données
À l’aide de l’Assistant de copie, vous pouvez déplacer simplement et efficacement des centaines de tables, fichiers ou dossiers.

![Sélectionnez les tables à partir desquelles copier les données](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

### Des options de planification enrichies
Vous pouvez effectuer l’opération de copie une seule fois ou la répéter selon une planification établie (horaire, quotidienne et ainsi de suite.). Ces deux options peuvent être définies pour l’ensemble des connecteurs utilisés pour la copie locale, dans le cloud ou sur un Bureau local.

Une opération de copie unique ne permet le déplacement des données à d’une source vers une destination qu’une seule fois. Elle s’applique aux données de toute taille et de n’importe quel format pris en charge. La copie planifiée vous permet de copier des données selon une périodicité définie. Vous pouvez utiliser les paramètres avancés (nouvelle tentative, délai d’attente, alertes, etc.) pour configurer la copie planifiée.

![Propriétés de planification](./media/data-factory-copy-wizard/scheduling-properties.png)

## Variables dans le chemin du dossier d’objets blob Azure
Vous pouvez utiliser des variables dans le chemin du dossier pour copier des données à partir d’un dossier qui est déterminé au moment de l’exécution par les [variables système WindowStart](data-factory-functions-variables.md#data-factory-system-variables). Les variables prises en charge sont les suivantes : **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** et **{custom}**. Exemple : inputfolder/{year}/{month}/{day}.

Supposons que vos dossiers d’entrée présentent le format suivant :

	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

Cliquez sur le bouton **Parcourir** à côté de **Fichier ou dossier**, accédez à l’un de ces dossiers (par exemple, 2016->03->01->02), puis cliquez sur **Choisir**. Vous devez voir **2016/03/01/02** dans la zone de texte. À présent, remplacez **2016** par **{year}**, **03** par **{month}**, **01** par **{day}** et **02** par **{hour}**, puis appuyez sur la touche de tabulation. Vous devez maintenant voir des listes déroulantes pour sélectionner le format de ces quatre variables :

![Utilisation de variables système](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)

Comme la capture d’écran suivante le montre, vous pouvez également configurer une variable **custom** pour utiliser l’une des [chaînes de format prises en charge](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Pour sélectionner un dossier avec cette structure, utilisez d’abord le bouton **Parcourir**. Remplacez alors une valeur avec **{custom}**, puis appuyez sur Tab pour afficher la zone de texte dans laquelle vous pouvez taper la chaîne de format.

![Utilisation de la variable custom](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## Étapes suivantes
Pour découvrir une procédure pas à pas rapide sur l’utilisation de l’Assistant Data Factory Copy afin de créer un pipeline avec une activité de copie, consultez le [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md).

<!---HONumber=AcomDC_0824_2016-->