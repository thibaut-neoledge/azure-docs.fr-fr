<properties
	pageTitle="Didacticiel Power BI pour connecteur DocumentDB | Microsoft Azure"
	description="Utilisez ce didacticiel Power BI pour importer JSON, créer des rapports et visualiser les données à l'aide de DocumentDB et du connecteur Power BI."
	keywords="didacticiel power BI, visualiser les données, connecteur Power BI"
	services="documentdb"
	authors="h0n"
	manager="jhubbard"
	editor="mimig"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="hawong"/>

# Didacticiel de Power BI pour DocumentDB : visualiser les données à l'aide du connecteur Power BI
[PowerBI.com](https://powerbi.microsoft.com/) est un service en ligne qui vous permet de créer et partager des tableaux de bord et des rapports à partir de données véritablement significatives pour vous et votre organisation. Power BI Desktop est un outil de création de rapports dédié qui vous permet de récupérer des données à partir de diverses sources, de les fusionner et de les transformer, de créer de puissants rapports et visualisations, puis de publier ces rapports sur Power BI. Grâce à la dernière version de Power BI Desktop, vous pouvez maintenant vous connecter à votre compte DocumentDB via le connecteur DocumentDB pour Power BI.

Dans ce didacticiel pour Power BI, nous allons passer en revue les différentes étapes qui vous permettront de vous connecter à un compte DocumentDB dans Power BI Desktop, d’accéder à une collection à partir de laquelle extraire des données à l’aide du Navigateur, de convertir les données JSON en tableau en utilisant l’éditeur de requête de Power BI Desktop, et de générer et publier un rapport sur PowerBI.com.

Après avoir terminé ce didacticiel pour Power BI, vous serez en mesure de répondre aux questions suivantes :

-	Comment générer des rapports avec des données issues de DocumentDB à l’aide de Power BI Desktop ?
-	Comment me connecter à un compte DocumentDB dans Power BI Desktop ?
-	Comment récupérer des données d’une collection dans Power BI Desktop ?
-	Comment transformer des données JSON imbriquées dans Power BI Desktop ?
-	Comment publier et partager mes rapports dans PowerBI.com ?

## Composants requis

Avant de suivre les instructions de ce didacticiel pour Power BI, assurez-vous de disposer des éléments suivants :

- [La dernière version de Power BI Desktop](https://powerbi.microsoft.com/desktop).
- Un accès à notre compte de démonstration ou aux données de votre compte Azure DocumentDB.
	- Le compte de démonstration comprend les données relatives au volcan présentées dans ce didacticiel. Ce compte de démonstration n’est lié à aucun contrat de niveau de service et est réservé uniquement à des fins de démonstration. Nous nous réservons le droit d’apporter à tout moment des modifications à ce compte de démonstration sans préavis et sans raison, y compris sans toutefois s’y limiter, à résilier le compte, à modifier la clé d’accès, à restreindre l’accès, à modifier et supprimer les données.
		- URL :https://analytics.documents.azure.com
		- Clé en lecture seule : MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR + YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw ==
	- Sinon, créez votre propre compte ; pour cela, consultez l’article [Création d’un compte de base de données DocumentDB avec le portail Azure](https://azure.microsoft.com/documentation/articles/documentdb-create-account/). Ensuite, pour obtenir des exemples de données de volcan semblables à celles utilisées dans ce didacticiel (mais qui ne contiennent pas les blocs GeoJSON), consultez le [site NOAA](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) puis importez les données à l’aide de l’[outil de migration de données DocumentDB](https://azure.microsoft.com/documentation/articles/documentdb-import-data/).


Pour partager vos rapports dans PowerBI.com, vous devez disposer d’un compte sous PowerBI.com. Pour en savoir plus sur la version gratuite de Power BI et sur Power BI Pro, visitez le site [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## Prise en main
Dans ce didacticiel, imaginons que vous êtes géologue et que vous devez étudier les volcans du monde entier. Les données relatives aux volcans sont stockées dans un compte DocumentDB et les documents JSON sont semblables à ce que vous pouvez voir ci-dessous.

	{
    	"Volcano Name": "Rainier",
   		"Country": "United States",
  		"Region": "US-Washington",
  		"Location": {
    		"type": "Point",
    		"coordinates": [
      		-121.758,
      		46.87
    		]
  		},
  		"Elevation": 4392,
  		"Type": "Stratovolcano",
  		"Status": "Dendrochronology",
  		"Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
	}

Vous souhaitez récupérer les données de volcan du compte DocumentDB et visualiser les données pour produire un rapport Power BI similaire à ce qui suit.

![En terminant ce didacticiel Power BI avec le connecteur Power BI, vous serez en mesure de visualiser les données du rapport sur les volcans Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

Prêt à vous lancer ? Allons-y.


1. Exécutez Power BI Desktop sur votre station de travail.
2. Après le démarrage de Power BI Desktop, un écran d’*accueil* s’affiche.

	![Power BI Desktop - Écran d’accueil - Connecteur Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_welcome.png)

3. Vous pouvez **obtenir des données**, consulter les **Sources récentes** ou **Ouvrir d’autres rapports** directement à partir de cet écran d’*accueil*. Cliquez sur le symbole X en haut à droite pour fermer l’écran. La vue **Rapport** de Power BI Desktop s’affiche.

	![Power BI Desktop - Vue Rapport - Connecteur Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview.png)

4. Sélectionnez le ruban **Accueil**, puis cliquez sur **Obtenir des données**. La fenêtre **Obtenir des données** doit alors s’afficher.

5. Cliquez sur **Azure**, sélectionnez **Microsoft Azure DocumentDB (Beta)**, puis cliquez sur **Se connecter**. La fenêtre de connexion à **Microsoft Azure DocumentDB** doit s’afficher.

	![Power BI Desktop - Obtenir des données - Connecteur Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbigetdata.png)

6. Spécifiez l’URL du point de terminaison du compte DocumentDB dont vous souhaitez récupérer les données (voir ci-dessous), puis cliquez sur **OK**. Vous pouvez récupérer l’URL dans la zone URI du panneau **Clés** du portail Azure. Sinon, vous pouvez utiliser les informations du compte de démonstration fournies ci-dessus. Pour plus d’informations, consultez la page relative aux [Clés](documentdb-manage-account.md#keys).


	*Remarque : dans ce didacticiel, nous n’allons pas spécifier ni le nom de la base de données, ni le nom de la collection ni l’instruction SQL car ces champs sont facultatifs. Nous allons utiliser le Navigateur pour sélectionner la base de données et la collection afin d’identifier la provenance des données.*

    ![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Fenêtre de connexion](./media/documentdb-powerbi-visualize/power_bi_connector_pbiconnectwindow.png)

7. Si vous vous connectez pour la première fois à ce point de terminaison, le système vous demandera la clé du compte. Entrez la clé du compte et cliquez sur **Se connecter**.

	*Remarque : nous vous recommandons d’utiliser la clé en lecture seule lorsque vous créez des rapports, ce afin de ne pas exposer inutilement la clé principale à des risques de sécurité potentiels. La clé en lecture seule est disponible à partir du panneau correspondant du portail Azure. Sinon, vous pouvez utiliser les informations du compte de démonstration fournies ci-dessus.*

    ![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Clé de compte](./media/documentdb-powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)

8. Une fois le compte connecté, le **Navigateur** s’affiche. Le **Navigateur** affiche une liste des bases de données attachées au compte.
9. Sélectionnez puis développez la base de données d’où proviennent les données du rapport. Vous obtenez la liste des collections attachées à la base de données.  

10. À présent, sélectionnez une collection d’où vous allez récupérer les données (par exemple, volcano1).

	*Remarque : le volet d’aperçu affiche une liste des éléments d’**enregistrement**. Dans Power BI, un Document est représenté sous la forme d’un type d’**enregistrement**. De même, un bloc JSON imbriqué à l’intérieur d’un document est également considéré comme un **enregistrement**.*

    ![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Fenêtre du navigateur](./media/documentdb-powerbi-visualize/power_bi_connector_pbinavigator.png)

11. Cliquez sur **Modifier** pour lancer l’éditeur de requête et transformer les données.

## Mise à plat et transformation de documents JSON
1. Dans l’éditeur de requête de Power BI, une colonne **Document** doit normalement apparaître dans le volet central. ![Power BI Desktop - Éditeur de requête](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditor.png)

2. Cliquez sur l’icône de développement à droite de l’en-tête de colonne **Document**. Un menu contextuel s’affiche avec une liste de champs. Sélectionnez les champs dont vous avez besoin pour votre rapport (par exemple, nom du volcan, pays, région, emplacement, altitude, type, état et dernière éruption connue), puis cliquez sur **OK**.

	![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Développer des documents](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)

3. Le volet central affiche un aperçu du résultat avec les champs sélectionnés.

	![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Aplatir les résultats](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflatten.png)

4. Dans notre exemple, la propriété Emplacement est un bloc GeoJSON contenu dans un document. Comme vous pouvez le constater, l’emplacement est représenté en tant que type d’**enregistrement** dans Power BI Desktop.
5. Cliquez sur l’icône de développement à droite de l’en-tête de colonne Emplacement. Un menu contextuel s’affiche avec les champs « type » et « coordonnées ». Sélectionnez le champ de coordonnées et cliquez sur **OK**.

    ![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Enregistrement des emplacements](./media/documentdb-powerbi-visualize/power_bi_connector_pbilocationrecord.png)

6. Le volet central affiche maintenant une colonne de coordonnées de type **Liste**. Comme indiqué au début de ce didacticiel, les données GeoJSON utilisées dans ce didacticiel sont de type « Point », avec des valeurs de latitude et longitude enregistrées dans le tableau de coordonnées.

	*Remarque : l’élément coordinates[0] représente la longitude et l’élément coordinates[1] la latitude.* ![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Liste des coordonnées](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)

7. Pour mettre à plat le tableau de coordonnées, nous allons créer une **colonne personnalisée** appelée LatLong. Sélectionnez le ruban **Add column** (Ajouter une colonne), puis cliquez sur **Add Custom Column** (Ajouter une colonne personnalisée). La fenêtre **Add Custom Column** (Ajouter une colonne personnalisée) s’affiche.

8. Indiquez un nom pour la nouvelle colonne, par exemple LatLong.

9. Spécifiez ensuite la formule personnalisée à appliquer à la nouvelle colonne. Dans notre exemple, nous allons concaténer les valeurs de latitude et longitude séparées par une virgule, comme indiqué ci-dessous, à l’aide de la formule suivante : Text.From([coordinates]{1})&","&Text.From([coordinates]{0}). Cliquez sur **OK**.

    *Remarque : pour plus d’informations sur le langage DAX (Data Analysis Expressions) et notamment sur les fonctions DAX, consultez la page [DAX Basic in Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop) (en anglais).*

    ![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Ajouter une colonne personnalisée](./media/documentdb-powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Le volet central affiche maintenant la nouvelle colonne LatLong contenant les valeurs de latitude et de longitude, séparées par une virgule.

	![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Colonne LatLong personnalisée](./media/documentdb-powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)

11. Nous avons terminé de convertir les données au format tabulaire. Vous pouvez tirer parti de toutes les fonctionnalités disponibles dans l’éditeur de requête pour modeler et transformer des données dans DocumentDB. Par exemple, vous pouvez modifier le type de données pour le champ Altitude en **Nombre décimal** en modifiant le **Type de données** sur le ruban **Accueil**.

    ![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Modifier le type de colonne](./media/documentdb-powerbi-visualize/power_bi_connector_pbichangetype.png)

12. Cliquez sur **Fermer et appliquer** pour enregistrer le modèle de données.

    ![Didacticiel de Power BI pour DocumentDB - Connecteur Power BI - Fermet et appliquer](./media/documentdb-powerbi-visualize/power_bi_connector_pbicloseapply.png)

## Création de rapports
La vue Rapport de Power BI Desktop vous permet de créer des rapports pour visualiser des données. Pour créer des rapports, vous pouvez simplement faire glisser des champs et les déposer dans la zone de dessin **Rapport**.

![Power BI Desktop - Vue Rapport - Connecteur Power BI](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview2.png)

Dans la vue Rapport, vous devriez trouver les éléments suivants :

 1. Le volet **Champs**, qui contient une liste de modèles de données avec des champs que vous pouvez utiliser pour vos rapports.

 2. Le volet **Visualisations**. Un rapport peut contenir une ou plusieurs visualisations. Dans le volet **Visualisations**, sélectionnez les types visuels adaptés à vos besoins.

 3. La zone de dessin **Rapport**, où vous allez créer les éléments visuels de votre rapport.

 4. La page **Rapport**. Vous pouvez ajouter plusieurs pages de rapport dans Power BI Desktop.

Vous trouverez ci-dessous les principales étapes de création d’un rapport de vue cartographique interactif simple.

1. Dans notre exemple, nous allons créer une vue cartographique indiquant l’emplacement de chaque volcan. Dans le volet **Visualisations**, cliquez sur le type d’élément visuel « carte », comme illustré dans la capture d’écran ci-dessus. Le type d’élément visuel « carte » devrait s’afficher dans la zone de dessin **Rapport**. Le volet **Visualisations** doit également afficher un ensemble de propriétés associées au type d’élément visuel « carte ».

2. Maintenant, faites glissez et déplacez le champ LatLong du volet **Champs** vers la propriété **Emplacement** du volet **Visualisations**.
3. Faites glissez le champ Nom du volcan vers la propriété **Légende**.  

4. Puis déplacez le champ Altitude vers la propriété **Valeurs**.

5. Vous devriez maintenant voir une carte contenant un ensemble de bulles qui indiquent l’emplacement de chaque volcan, la taille de la bulle étant proportionnelle à l’altitude des volcans.

6. Vous avez maintenant créé un rapport de base. Vous pouvez continuer à personnaliser votre rapport en ajoutant davantage de visualisations. Dans notre cas, nous avons ajouté un segment Type de volcan pour obtenir un rapport interactif.

    ![Capture d'écran du rapport final Power BI Desktop après avoir terminé le didacticiel Power BI pour DocumentDB](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

## Publier et partager votre rapport
Pour partager votre rapport, vous devez disposer d’un compte dans PowerBI.com.

1. Dans Power BI Desktop, cliquez sur le ruban **Accueil**.
2. Cliquez sur **Publier**. Vous devrez entrer le nom d’utilisateur et le mot de passe associés à votre compte PowerBI.com.
3. Une fois les informations d’identification authentifiées, le rapport est publié dans votre compte PowerBI.com.
4. Vous pouvez ensuite partager votre rapport dans PowerBI.com.

## Étapes suivantes
- Cliquez [ici](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) pour en savoir plus sur Power BI.
- Pour en savoir plus sur DocumentDB, cliquez [ici](https://azure.microsoft.com/documentation/services/documentdb/).

<!---HONumber=AcomDC_0608_2016-->