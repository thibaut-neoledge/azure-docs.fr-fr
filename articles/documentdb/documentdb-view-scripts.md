<properties
	pageTitle="Explorateur de scripts de DocumentDB, un éditeur JavaScript | Microsoft Azure"
	description="Découvrez l’Explorateur de scripts de DocumentDB, un outil du portail Azure qui permet de gérer les artefacts de programmation côté serveur de DocumentDB, notamment les procédures stockées, les déclencheurs et les fonctions définies par l’utilisateur."
	keywords="éditeur JavaScript"
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="anhoh"/>

# Créer et exécuter des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur à l’aide de l’Explorateur de scripts de DocumentDB

Cet article fournit une présentation de l’Explorateur de scripts [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), éditeur JavaScript du portail Azure qui vous permet d’afficher et d’exécuter les artefacts de programmation côté serveur de DocumentDB, notamment les procédures stockées, les déclencheurs et les fonctions définies par l’utilisateur. Pour plus d’informations sur la programmation DocumentDB côté serveur, consultez l’article [Procédures stockées, déclencheurs de base de données et fonctions définies par l’utilisateur](documentdb-programming.md).

## Lancer l’Explorateur de scripts

1. Dans la barre de lancement du Portail Azure, cliquez sur **DocumentDB (NoSQL)**. Si l’option **Comptes DocumentDB** n’est pas affichée, cliquez sur **Plus de services**, puis sur **DocumentDB (NoSQL)**.

2. Dans le menu des ressources, cliquez sur **Explorateur de scripts**.

	![Capture d'écran de la commande de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
    Les zones de liste déroulante **Base de données** et **Collection** sont préalablement remplies en fonction du contexte de lancement de l’Explorateur de scripts. Par exemple, si vous le lancez à partir d'un panneau de base de données, la base de données active est préremplie. Si vous le lancez à partir d'un panneau de collection, la collection active est préremplie.

4.  Utilisez les zones de liste déroulante **Base de données** et **Collection** pour modifier facilement la collection à partir de laquelle les scripts sont affichés actuellement sans avoir à fermer, puis à relancer l’Explorateur de scripts.

5. L'Explorateur de scripts prend également en charge le filtrage de l'ensemble de scripts actuellement chargé par leur propriété ID. Tapez simplement dans la zone de filtre ; les résultats dans la liste de l’Explorateur de scripts sont alors filtrés en fonction de vos critères.

	![Capture d'écran de l'Explorateur de scripts avec les résultats filtrés](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT] La fonctionnalité de filtre de l'Explorateur de scripts filtre uniquement l'ensemble de scripts ***actuellement*** chargé et n'actualise pas automatiquement la collection actuellement sélectionnée.

5. Pour actualiser la liste des scripts chargés par l'Explorateur de scripts, cliquez simplement sur la commande **Actualiser** en haut du panneau.

	![Capture d'écran de la commande Actualiser de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## Créer, afficher et modifier des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur

L'Explorateur de scripts vous permet d'exécuter facilement des opérations CRUD sur les artefacts de programmation côté serveur de DocumentDB.

- Pour créer un script, il vous suffit de cliquer sur la commande de création applicable dans l’Explorateur de scripts, de fournir un ID, d’entrer le contenu du script, puis de cliquer sur **Enregistrer**.

	![Capture d’écran de l’option de création de l’Explorateur de scripts, affichant l’éditeur JavaScript](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- Lorsque vous créez un déclencheur, vous devez également spécifier le type et le fonctionnement du déclencheur

	![Capture d'écran de l'option de création de déclencheur de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- Pour afficher un script, cliquez simplement sur celui qui vous intéresse.

	![Capture d'écran de l'expérience d'affichage de scripts de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- Pour modifier un script, apportez les modifications souhaitées dans l’éditeur JavaScript, puis cliquez sur **Enregistrer**.

	![Capture d'écran de l'expérience d'affichage de scripts de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- Pour ignorer les modifications d'un script, cliquez simplement sur la commande **Ignorer**.

	![Capture d'écran de l'expérience de suppression des modifications de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- L'Explorateur de scripts vous permet aussi d'afficher facilement les propriétés du script actuellement chargé en cliquant sur la commande **Propriétés**.

	![Capture d'écran de l'affichage des propriétés du script de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE] La propriété timestamp (\_ts) est représentée en interne comme heure d'époque, mais l'Explorateur de scripts affiche la valeur dans un format GMT lisible.

- Pour supprimer un script, sélectionnez-le dans l'Explorateur de scripts et cliquez sur la commande **Supprimer**.

	![Capture d'écran de la commande Supprimer de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- Confirmez l'action de suppression en cliquant sur **Oui** ou annulez l'action de suppression en cliquant sur **Non**.

	![Capture d'écran de la commande Supprimer de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## Exécuter une procédure stockée

> [AZURE.WARNING] L’exécution de procédures stockées dans l'Explorateur de scripts n'est pas encore possible pour les collections partitionnées côté serveur. Pour plus d’informations, consultez [Partitionnement et mise à l’échelle dans DocumentDB](documentdb-partition-data.md).

L’Explorateur de scripts vous permet d’exécuter des procédures stockées côté serveur à partir du portail Azure.

- Quand vous ouvrez le panneau de création de procédure stockée, un script par défaut (*prefix*) est déjà fourni. Pour exécuter le script *prefix* ou votre propre script, ajoutez un *ID* et des *entrées*. Pour les procédures stockées qui acceptent plusieurs paramètres, toutes les entrées doivent se trouver dans un tableau (par exemple, *["foo", "bar"]*).

	![Capture d’écran du panneau Procédures stockées de l’Explorateur de scripts, pour ajouter une entrée et exécuter une procédure stockée](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)

- Pour exécuter une procédure stockée, cliquez simplement sur la commande **Enregistrer et exécuter** dans le volet de l’éditeur de scripts.

	> [AZURE.NOTE] La commande **Enregistrer et exécuter** enregistre votre procédure stockée avant de l’exécuter, remplaçant ainsi la version précédemment enregistrée de la procédure stockée.

- Quand une procédure stockée s’exécute normalement, son état est *Procédure stockée enregistrée et exécutée avec succès* et les résultats retournés apparaissent dans le volet *Résultats*.

	![Capture d’écran du panneau Procédures stockées de l’Explorateur de scripts, pour exécuter une procédure stockée](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)

- Si l’exécution rencontre une erreur, celle-ci est mentionnée dans le volet *Résultats*.

	![Capture d’écran de l’affichage des propriétés du script de l’Explorateur de scripts. Exécuter une procédure stockée comportant des erreurs](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## Utiliser des scripts en dehors du portail

L’Explorateur de scripts dans le portail Azure n’est qu’un des moyens d’utiliser des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur dans DocumentDB. Vous pouvez également travailler avec des scripts utilisant l’API REST et les [Kits de développement logiciel (SDK) clients](documentdb-sdk-dotnet.md). La documentation de l’API REST inclut des exemples d’utilisation des [procédures stockées utilisant REST](https://msdn.microsoft.com/library/azure/mt489092.aspx), des [fonctions définies par l’utilisateur utilisant REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) et des [déclencheurs utilisant REST](https://msdn.microsoft.com/library/azure/mt489116.aspx). Des exemples montrant comment [travailler avec des scripts utilisant C#](documentdb-dotnet-samples.md#server-side-programming-examples) et [travailler avec des scripts utilisant Node.js](documentdb-nodejs-samples.md#server-side-programming-examples) sont également proposés.

## Étapes suivantes

Pour plus d’informations sur la programmation côté serveur de DocumentDB, consultez l’article [Procédures stockées, déclencheurs de base de données et fonctions définies par l’utilisateur](documentdb-programming.md).

Le [parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/documentdb/) est également une ressource utile pour vous guider au fil de votre apprentissage de DocumentDB.

<!---HONumber=AcomDC_0831_2016-->