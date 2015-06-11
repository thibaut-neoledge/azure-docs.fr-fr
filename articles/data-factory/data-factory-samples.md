<properties 	
	pageTitle="Fabrique de données Azure - exemples" 
	description="Fournit des détails sur les exemples fournis avec le service de fabrique de données Azure." 
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
	ms.date="04/25/2015" 
	ms.author="spelluru"/>

# Fabrique de données Azure - exemples

## Exemples de portail Azure
Vous pouvez rapidement déployer, passez en revue et tester un exemple de fabrique de données Azure à l'aide de la **exemples de pipelines** lame dans le portail Azure.

1. Créer une nouvelle fabrique de données ou ouvrez une fabrique de données existant. Voir [mise en route de la fabrique de données Azure][data-factory-get-started] pour connaître les étapes créer une fabrique de données.
2. Dans le **DATA FACTORY** lame pour la fabrique de données, cliquez sur le **exemples de pipelines** vignette.

	![Exemple pipelines mosaïque](./media/data-factory-samples/SamplePipelinesTile.png)

2. Dans le **exemples de pipelines** lame, cliquez sur le **exemple** que vous souhaitez déployer.
	
	![Lame de pipelines exemple](./media/data-factory-samples/SampleTile.png)

3. Spécifier les paramètres de configuration de l'exemple. Par exemple, votre clé de compte et le nom de compte de stockage Azure, nom du serveur SQL Azure, base de données, ID d'utilisateur et mot de passe, etc.

	![Volet d'exemple](./media/data-factory-samples/SampleBlade.png)

4. Une fois que vous avez fini de spécifier les paramètres de configuration, cliquez sur **créer** créer/déployer l'exemple pipelines et les services/les tables liées par les pipelines.
5. L'état du déploiement s'affiche sur la vignette exemple que vous avez cliqué sur le **exemples de pipelines** lame.

	![État du déploiement](./media/data-factory-samples/DeploymentStatus.png)

6. Lorsque vous voyez le **déploiement réussi** message sur la vignette de l'exemple, fermez le **exemples de pipelines** lame.
5. Sur **DATA FACTORY** lame, vous verrez que les services liés, des jeux de données et des pipelines sont ajoutés à votre fabrique de données.  

	![Lame fabrique](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

Le tableau suivant fournit la brève description des exemples disponibles dans les **exemples de pipelines** vignette.

Exemple de nom | description
----------- | -----------
Création de profils utilisateur de jeu | Contoso est une société de jeu qui crée des jeux pour plusieurs plateformes : consoles de jeux, périphériques de poche et les ordinateurs personnels (PC). Chacun de ces jeux produit une très grande quantité de journaux. Objectif de Contoso est pour collecter et analyser les journaux générés par ces jeux pour obtenir des informations sur l'utilisation, identifiez les opportunités de vente incitative et de vente croisée, développer de nouvelles fonctionnalités intéressantes etc.... pour améliorer l'entreprise et offrir une meilleure expérience à ses clients. Cet exemple collecte des exemples de journaux, les processus et enrichit les données de référence et transforme les données pour évaluer l'efficacité d'une campagne marketing Contoso a récemment lancé.
 
## Exemples sur GitHub
Le [référentiel GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contient plusieurs exemples qui vous aideront à rapidement d'accélération avec le service de fabrique de données Azure (ou) modifier les scripts et l'utiliser dans une application propre. Le dossier Samples\\JSON contient des extraits de JSON pour les scénarios courants.

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=GIT-SubDir--> 