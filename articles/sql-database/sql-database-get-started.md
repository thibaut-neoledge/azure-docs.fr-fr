<properties
	pageTitle="Prise en main d’une base de données SQL"
	description="Créez votre première base de données cloud en quelques minutes grâce à la base de données SQL Microsoft Azure, au système de gestion de base de données relationnelle de Microsoft (SGBDR) dans le cloud, au portail Microsoft Azure et à l’échantillon de base de données AdventureWorks."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/14/2015"
	ms.author="genemi"/>


# Créer votre première base de données SQL Microsoft Azure


Cet article vous explique comment créer un exemple de base de données SQL Microsoft Azure en moins de cinq minutes. Vous allez découvrir comment effectuer les actions suivantes :


- Configuration d’un serveur logique via le [portail Microsoft Azure](http://portal.azure.com/)
- Création d’une base de données incluant des exemples de données
- Définition d’une règle de pare-feu pour configurer les adresses IP autorisées à accéder à votre base de données


Ce didacticiel part du principe que vous disposez d’un abonnement Microsoft Azure. Le cas échéant, vous pouvez vous inscrire afin d’accéder à une version d’[essai gratuit](http://azure.microsoft.com/pricing/free-trial/) dès aujourd’hui.


## Étape 1 : Connexion


1. Connectez-vous au [portail Azure](http://portal.azure.com/).
2. Cliquez sur **Nouveau** > **Données et stockage** > **Base de données SQL**.


![New SQL Database][1]


## Étape 2 : Création de votre serveur logique



1. Dans le panneau Base de données SQL, choisissez un **Nom** pour votre base de données (dans cet exemple, le nom utilisé est **AdventureWorks**).
2. Pour créer un serveur logique pour votre base de données, cliquez sur **Serveur**, puis sur **Créer un serveur**.


## Étape 3 : Configuration de votre serveur


1. Dans le panneau **Serveur**, dans le champ **Nom du serveur**, indiquez un nom unique et simple à retenir.
2. Dans le champ **Connexion d’administrateur du serveur**, indiquez **AdventureAdmin**.
3. Entrez la valeur correcte dans les zones **Mot de passe** et **Confirmer le mot de passe**.
4. Dans **Emplacement**, choisissez l’emplacement géographique de votre choix. En général, cet emplacement est proche de l’emplacement d’exécution de l’application.
5. Cliquez sur **OK**.


![Créer un serveur][2]


## Étape 4 : Création de votre base de données


1. Dans le panneau Base de données SQL, spécifiez la source de la base de données en cliquant sur **Sélectionner une source**.
 - Si vous ignorez cette étape, une base de données vide est créée.
2. Sélectionnez **Échantillon**.
 - Cela crée une base de données, qui est une copie de l’échantillon de base de données standard appelé **AdventureWorks**.
 - Sur la base de données SQL Microsoft Azure, l’édition du *schéma léger* de la base de données AdventureWorks est utilisé.
3. Cliquez sur l’option **Créer** figurant en bas du panneau.


## Étape 5 : Configurer le pare-feu


Les étapes suivantes indiquent comment spécifier les plages d’adresses IP autorisées à accéder à votre base de données.


![Parcourir un serveur][3]


1. Dans le ruban figurant sur le côté gauche de l’écran, cliquez sur **Parcourir**, puis sur **Serveurs SQL**.
2. Parmi les options disponibles, sélectionnez le serveur SQL que vous avez créé précédemment.
3. Cliquez sur **Paramètres**, puis sur **Pare-feu**.
4. Cliquez sur ce lien pour obtenir l’adresse IP actuelle à partir de [Bing](http://www.bing.com/search?q=my%20ip%20address).
5. Dans Paramètres de pare-feu, saisissez un nom dans **Nom de la règle** et collez l’adresse IP publique obtenue à l’étape précédente dans les champs **Adresse IP de début** et **Adresse IP de fin**.
6. Lorsque vous avez terminé, cliquez sur l’option **Enregistrer** figurant en haut de la page.


![Pare-feu][4]


## Étapes suivantes


À présent, vous êtes prêt à écrire un petit programme client capable de se connecter à votre base de données. Pour obtenir un exemple de code, afin de démarrer rapidement, cliquez sur l’un des liens suivants :


- [Se connecter et interroger une base de données SQL avec C#](sql-database-connect-query.md)
- *Bientôt disponible :* Développement de clients et échantillons de démarrage rapide pour une base de données SQL


<!-- Media references. -->
[1]: ./media/sql-database-get-started/GettingStarted_NewDB.PNG
[2]: ./media/sql-database-get-started/GettingStarted_CreateServer.png
[3]: ./media/sql-database-get-started/GettingStarted_BrowseServer.png
[4]: ./media/sql-database-get-started/GettingStarted_FireWall.png
 

<!---HONumber=62-->