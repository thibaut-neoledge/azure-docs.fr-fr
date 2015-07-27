<properties
	pageTitle="Afficher les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur à l'aide de l'Explorateur de scripts de DocumentDB | Microsoft Azure"
	description="Découvrez l'Explorateur de scripts de DocumentDB, un outil du portail Azure en version préliminaire qui permet d'afficher les artefacts de programmation côté serveur de DocumentDB, notamment les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur."
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
	ms.topic="get-started-article" 
	ms.date="06/10/2015"
	ms.author="stbaro"/>

# Afficher les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur à l'aide de l'Explorateur de scripts de DocumentDB

Cet article fournit une présentation de l'Explorateur de scripts [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), un outil du portail Azure en version préliminaire qui vous permet d'afficher les artefacts de programmation côté serveur de DocumentDB, notamment les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur. Plus d'informations sur la programmation côté serveur de DocumentDB sont disponibles [ici](documentdb-programming.md).

Ce didacticiel vous permettra de pouvoir répondre aux questions suivantes :

-	Comment puis-je afficher facilement les procédures stockées de DocumentDB à l'aide d'un navigateur Web ?
-	Comment puis-je afficher facilement les déclencheurs de DocumentDB à l'aide d'un navigateur Web ?
-	Comment puis-je afficher facilement les fonctions définies par l'utilisateur de DocumentDB à l'aide d'un navigateur Web ?

## Lancer et utiliser l'Explorateur de scripts

L'Explorateur de scripts peut être lancé à partir des panneaux de compte, de base de données et de collection DocumentDB.

1. En haut du panneau de compte ou de base de données DocumentDB, cliquez simplement sur la commande **Explorateur de scripts**.

	![Capture d'écran de la commande de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
2. Ou, au bas de chaque panneau figure un filtre **Outils de développement** contenant le composant **Explorateur de scripts**.

	![Capture d'écran du composant Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerpart.png)

2. Cliquez simplement sur la commande ou le composant pour lancer l'Explorateur de scripts.

	<p>Les zones de liste déroulante **Base de données** et **Collection** sont préremplies en fonction du contexte de lancement de l'Explorateur de scripts. Par exemple, si vous le lancez à partir d'un panneau de base de données, la base de données active est préremplie. Si vous le lancez à partir d'un panneau de collection, la collection active est préremplie.

	![Capture d'écran de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerinitial.png)


3. Vous pouvez utiliser les zones de liste déroulante **Base de données** et **Collection** pour modifier facilement la collection à partir de laquelle les scripts sont affichés actuellement sans avoir à fermer, puis à relancer l'Explorateur de scripts.

4. L'Explorateur de scripts prend également en charge le filtrage de l'ensemble de scripts actuellement chargé par leur propriété ID. Il vous suffit de taper dans la zone de filtre.

	![Capture d'écran de l'Explorateur de scripts avec le filtre en surbrillance](./media/documentdb-view-scripts/scriptexplorerfilter.png)

	Les résultats dans la liste de l'Explorateur de scripts sont alors filtrés en fonction de vos critères.

	![Capture d'écran de l'Explorateur de scripts avec les résultats filtrés](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT]La fonctionnalité de filtre de l'Explorateur de scripts filtre uniquement l'ensemble de scripts ***actuellement*** chargé et n'actualise pas automatiquement la collection actuellement sélectionnée.

5. Pour actualiser la liste des scripts chargés par l'Explorateur de scripts, cliquez simplement sur la commande **Actualiser** en haut du panneau.

	![Capture d'écran de la commande Actualiser de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## Afficher les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur avec l'Explorateur de scripts

L'Explorateur de scripts vous permet d'afficher facilement les artefacts de programmation côté serveur de DocumentDB.

- Cliquez simplement sur le script qui vous intéresse pour en afficher le contenu.

	![Capture d'écran de l'expérience d'affichage de scripts de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- L'Explorateur de scripts vous permet aussi d'afficher facilement les propriétés du script actuellement chargé en cliquant sur la commande **Propriétés**.

	![Capture d'écran de l'affichage des propriétés du script de l'Explorateur de scripts](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE]La propriété timestamp (_ts) est représentée en interne comme heure d'époque, mais l'Explorateur de scripts affiche la valeur dans un format GMT lisible.

## Étapes suivantes

Pour en savoir plus sur DocumentDB, cliquez [ici](http://azure.com/docdb).
 

<!---HONumber=July15_HO3-->