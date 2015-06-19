<properties 
	pageTitle="Afficher, modifier, créer et télécharger des documents JSON à l'aide de l'Explorateur de documents DocumentDB | Azure" 
	description="Découvrez l'Explorateur de documents DocumentDB, un outil du portail Azure Preview qui permet d'afficher, de modifier, de créer et de télécharger des documents JSON avec DocumentDB." 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/13/2015" 
	ms.author="stbaro"/>

# Afficher, modifier, créer et télécharger des documents JSON à l'aide de l'Explorateur de documents DocumentDB #

Cet article fournit une vue d'ensemble de l'Explorateur de documents [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), un outil du portail Azure Preview qui vous permet d'afficher, de modifier, de créer et de télécharger des documents JSON avec DocumentDB. 

Ce didacticiel vous permettra de pouvoir répondre aux questions suivantes :  

-	Comment faire pour créer, afficher, modifier et supprimer facilement des documents DocumentDB spécifiques via un navigateur web ?
-	Comment faire pour afficher facilement les propriétés système d'un document DocumentDB via un navigateur web ?
-	Comment faire pour effectuer facilement une ingestion en bloc de documents dans DocumentDB via un navigateur web ?

##<a id="Launch"></a>Lancer et utiliser l'Explorateur de documents##

Vous pouvez lancer l'Explorateur de documents à partir de l'un des panneaux de compte DocumentDB, de base de données et de collection.  

1. Au bas de chaque panneau figure un filtre **Outils de développement** contenant le composant **Explorateur de documents**.

	![Screenshot of the Document Explorer part](./media/documentdb-view-JSON-document-explorer/documentexplorerpart.png) 

2. Cliquez simplement sur la vignette pour lancer l'Explorateur de documents.

	<p>Les zones de liste déroulante **Base de données** et **Collection** sont préremplies en fonction du contexte dans lequel vous lancez l'Explorateur de documents.  Par exemple, si vous le lancez à partir d'un panneau de base de données, la base de données active est préremplie.  Si vous le lancez à partir d'un panneau de collection, la collection active est préremplie.

	![Screenshot of Document Explorer](./media/documentdb-view-JSON-document-explorer/documentexplorerinitial.png)

3. L'Explorateur de documents charge jusqu'aux 100 premiers documents dans la collection sélectionnée.  Vous pouvez charger des documents supplémentaires (par lots de 100) en sélectionnant l'option **Charger plus** en bas du panneau Explorateur de documents.  

4. Vous pouvez utiliser les zones de liste déroulante **Base de données** et **Collection** pour modifier facilement la collection à partir de laquelle les documents sont affichés actuellement sans avoir à fermer, puis à relancer l'Explorateur de documents.  

5. L'Explorateur de documents prend également en charge le filtrage de l'ensemble de documents actuellement chargé par leur propriété ID.  Il vous suffit de taper dans la zone de filtre.

	![Screenshot of Document Explorer with filter highlighted](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png) 

	Les résultats dans la liste de l'Explorateur de documents sont alors filtrés en fonction de vos critères.

	![Screenshot of Document Explorer with filtered results](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)


	> [AZURE.IMPORTANT] La fonctionnalité de filtre de l'Explorateur de documents filtre uniquement l'ensemble de documents ***actuellement*** chargé et n'exécute pas de requête sur la collection actuellement sélectionnée.

6. Pour actualiser la liste des documents chargés par l'Explorateur de documents, cliquez simplement sur la commande **Actualiser** en haut du panneau.

	![Screenshot of Document Explorer refresh command](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)


##<a id="Create"></a>Afficher, créer et modifier des documents avec l'Explorateur de documents##

L'Explorateur de documents vous permet de créer, modifier et supprimer facilement des documents.  

- Pour créer un document, cliquez simplement sur la commande **Créer un document** et un extrait JSON minimal est fourni.

	![Screenshot of Document Explorer create document experience](./media/documentdb-view-JSON-document-explorer/createdocument.png) 

- Il vous suffit de taper ou de coller le contenu JSON du document que vous souhaitez créer et de cliquer sur la commande **Enregistrer** pour valider votre document.

	![Screenshot of Document Explorer save command](./media/documentdb-view-JSON-document-explorer/savedocument1.png)

	> [AZURE.NOTE] Si vous ne fournissez pas de propriété " id ", l'Explorateur de documents en ajoute automatiquement une et génère un GUID comme valeur d'ID. 

- Pour modifier un document existant, sélectionnez-le dans l'Explorateur de documents, modifiez-le comme vous le souhaitez, puis cliquez sur la commande **Enregistrer**.

	![Screenshot of Document Explorer edit document functionality](./media/documentdb-view-JSON-document-explorer/editdocument.png)

- Si vous modifiez un document et que vous décidez d'ignorer l'ensemble des modifications, cliquez sur la commande Ignorer, confirmez l'action de rejet et l'état précédent du document est rechargé.

	![Screenshot of Document Explorer discard command](./media/documentdb-view-JSON-document-explorer/discardedit.png)

- Vous pouvez supprimer un document en le sélectionnant, en cliquant sur la commande **Supprimer**, puis en confirmant la suppression. Après la confirmation, le document est immédiatement supprimé de la liste de l'Explorateur de documents :

	![Screenshot of Document Explorer delete command](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

- Notez que l'Explorateur de documents vérifie que les documents nouveaux ou modifiés contiennent du code JSON valide.  Vous pouvez même pointer sur la section incorrecte pour obtenir plus d'informations sur l'erreur de validation.

	![Screenshot of Document Explorer invalid JSON highlighting](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

- En outre, l'Explorateur de documents vous empêche d'enregistrer un document avec un contenu JSON non valide.

	![Screenshot of Document Explorer invalid JSON save error](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

- Pour finir, vous pouvez facilement afficher les propriétés du document actuellement chargé en cliquant sur la commande **Propriétés**.

	![Screenshot of Document Explorer document properties view](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

	> [AZURE.NOTE] La propriété timestamp (_ts) est représentée en interne comme heure d'époque, mais l'Explorateur de documents affiche la valeur dans un format GMT lisible.

##<a id="BulkAdd"></a>Ajouter des documents en bloc à l'aide de l'Explorateur de documents##

L'Explorateur de documents prend en charge l'ingestion en bloc d'un ou plusieurs documents JSON existants.  

1. Pour démarrer le processus de téléchargement, cliquez sur la commande **Ajouter un document**.
	
	![Screenshot of Document Explorer bulk ingestion functionality](./media/documentdb-view-JSON-document-explorer/adddocument1.png)

2. Un nouveau panneau s'ouvre.  Cliquez sur le bouton Parcourir pour ouvrir une fenêtre d'Explorateur de fichiers, puis sélectionnez un ou plusieurs documents JSON à télécharger.

	![Screenshot of Document Explorer bulk ingestion process](./media/documentdb-view-JSON-document-explorer/adddocument2.png)

	> [AZURE.NOTE] L'Explorateur de documents prend actuellement en charge jusqu'à 100 documents JSON par opération de téléchargement.

3. Une fois que vous êtes satisfait de votre sélection, cliquez sur le bouton **Télécharger**.  Les documents sont automatiquement ajoutés à la grille de l'Explorateur de documents et les résultats du téléchargement sont affichés à mesure que l'opération progresse. Les échecs d'importation sont signalés pour chaque fichier.

	![Screenshot of Document Explorer bulk ingestion results](./media/documentdb-view-JSON-document-explorer/adddocument3.png)

4. Une fois l'opération terminée, vous pouvez sélectionner jusqu'à 100 autres documents à télécharger.

##<a name="NextSteps"></a>Étapes suivantes

Pour en savoir plus sur DocumentDB, cliquez [ici](http://azure.com/docdb).

<!--HONumber=49--> 