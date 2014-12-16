<properties title="Add Azure SQL DB Elastic Scale References to a Visual Studio Project" pageTitle="Ajout de références de l'infrastructure élastique de Base de données SQL Azure à un projet Visual Studio" description="How to add .NET references for Elastic Scale APIs to Visual Studio projects using Nuget." metaKeywords="Azure SQL Database, elastic scale, Nuget references" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Ajout de références de l'infrastructure élastique de Base de données SQL Azure à un projet Visual Studio

###Conditions préalables : 

- Installez [NuGet Visual Studio Extension Gallery](http://docs.nuget.org/docs/start-here/installing-nuget) pour Visual Studio

###Pour ajouter une référence d'infrastructure élastique dans Visual Studio, procédez comme suit : 

1. Créez un nouveau projet via la boîte de dialogue Nouveau projet accessible par le biais de **Fichier** --> **Nouveau** --> **Projet** 
2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Références**, puis sélectionnez **Gérer les packages NuGet**
3. Dans le menu sur le côté gauche de la fenêtre Gérer les Packages NuGet, sélectionnez **En ligne**, puis **nuget.org** ou " Tous "
4. Dans la boîte de dialogue **Rechercher en ligne**, tapez **Infrastructure élastique**, sélectionnez les ** bibliothèques clientes d'infrastructure élastique**, puis cliquez sur **Installer**.
4. Passez en revue le contrat de licence, puis cliquez sur **J'accepte**. 

Les références de l'infrastructure élastique de Base de données SQL Azure ont été maintenant été ajoutées à votre projet. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
