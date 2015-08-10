<properties 
	pageTitle="Ajout de références de l'infrastructure élastique de Base de données SQL Azure à un projet Visual Studio" 
	description="Ajout de références .NET pour les API de mise à l'échelle flexible aux projets Visual Studio à l'aide de NuGet." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="sidneyh"/>

# Ajout de références de bibliothèque client d’une base de données élastiques à un projet Visual Studio 

### Configuration requise : 

- Installez [NuGet Visual Studio Extension Gallery](http://docs.nuget.org/docs/start-here/installing-nuget) pour Visual Studio 

### Ajouter des références de bibliothèque client d’une base de données élastiques à un projet Visual Studio 

1. Ouvrez un projet existant ou créez un nouveau projet à l’aide de la boîte de dialogue Nouveau projet, située dans **Fichier** --> **Nouveau** --> **Projet** 
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Références**, puis sélectionnez **Gérer les packages NuGet**
3. Dans le menu sur le côté gauche de la fenêtre Gérer les Packages NuGet, sélectionnez **En ligne**, puis **nuget.org** ou « Tous » 
4. Dans la boîte de dialogue **Rechercher en ligne**, tapez **Base de données élastique**, sélectionnez les **bibliothèques clientes de base de données élastique**, puis cliquez sur **Installer**.

	![Rechercher en ligne][1]
4. Passez en revue le contrat de licence, puis cliquez sur **J’accepte**. 

La référence de la bibliothèque cliente a maintenant été ajoutée à votre projet.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-add-references-visual-studio/search-online.png
<!--anchors-->

<!---HONumber=July15_HO5-->