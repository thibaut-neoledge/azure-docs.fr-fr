<properties 	
	pageTitle="Azure Data Factory - Exemples" 
	description="Fournit des détails sur les exemples fournis avec le service Azure Data Factory." 
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
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - Exemples

## Exemples dans le portail Azure
Vous pouvez déployer, passer en revue et tester rapidement un exemple Azure Data Factory à l’aide du panneau **Exemples de pipelines** dans le portail Azure.

1. Créez une nouvelle fabrique de données ou ouvrez une fabrique de données existante. Consultez la page [Prise en main d’Azure Data Factory][data-factory-get-started] pour connaître les étapes de la création d’une fabrique de données.
2. Dans le panneau **DATA FACTORY** pour la fabrique de données, cliquez sur la vignette **Exemples de pipelines**.

	![Vignette Exemples de pipelines](./media/data-factory-samples/SamplePipelinesTile.png)

2. Dans le panneau **Exemples de pipelines**, cliquez sur l’**exemple** que vous souhaitez déployer.
	
	![Panneau Exemples de pipelines](./media/data-factory-samples/SampleTile.png)

3. Spécifiez les paramètres de configuration de l’exemple. Par exemple, votre clé de compte et votre nom de compte de stockage Azure, le nom du serveur SQL Azure, la base de données, l’ID d’utilisateur, le mot de passe, etc.

	![Panneau Exemple](./media/data-factory-samples/SampleBlade.png)

4. Une fois que vous avez fini de spécifier les paramètres de configuration, cliquez sur **Créer** pour créer/déployer les exemples de pipelines et les tables/services liés utilisés par les pipelines.
5. L’état du déploiement s’affiche sur l’exemple de vignette sur laquelle vous avez cliqué dans le panneau **Exemples de pipelines**.

	![État du déploiement](./media/data-factory-samples/DeploymentStatus.png)

6. Lorsque vous voyez le message **Déploiement réussi** sur la vignette de l’exemple, fermez le panneau **Exemples de pipelines**.
5. Dans le panneau **DATA FACTORY**, vous pouvez voir que les services liés, les jeux de données et les pipelines sont ajoutés à votre fabrique de données.  

	![Panneau Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

La table suivante fournit une brève description des exemples disponibles dans la vignette **Exemples de pipelines**.

Exemple de nom | description
----------- | -----------
Profilage des utilisateurs de jeux | Contoso est une société de jeu qui crée des jeux pour plusieurs plateformes : des consoles de jeux, des périphériques de poche et des ordinateurs personnels (PC). Chacun de ces jeux produit une très grande quantité de journaux. L’objectif de Contoso est de collecter et d’analyser les journaux générés par ces jeux pour obtenir des informations sur l’utilisation, identifier les opportunités de vente incitative et de vente croisée, développer de nouvelles fonctionnalités intéressantes, etc. tout cela afin d’optimiser vos affaires et fournir une meilleure expérience aux clients. Cet exemple collecte des exemples de journaux, les traite et les enrichit avec des données de référence, puis transforme les données pour évaluer l’efficacité d’une campagne marketing lancée dernièrement par Contoso.
 
## Exemples sur GitHub
Le [référentiel GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contient plusieurs exemples qui vous aideront à prendre en main rapidement le service Azure Data Factory (ou) à modifier les scripts et à utiliser ce service dans votre propre application. Le dossier Samples\\JSON contient des extraits de code JSON pour les scénarios courants.

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=August15_HO6-->