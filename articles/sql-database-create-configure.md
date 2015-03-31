<properties 
	pageTitle="Création et configuration d'une base de données SQL Azure - Didacticiel Azure" 
	description="Création et configuration d'une base de données SQL Azure" 
	services="sql-database" 
	documentationCenter="" 
	authors="sidneyh" 
	manager="jhubbard" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2014" 
	ms.author="sidneyh"/>

# Création et configuration d'une base de données SQL Azure

Dans cette rubrique, vous allez créer et configurer une base de données SQL Azure à l'aide de l'option **CRÉATION RAPIDE** du portail de gestion Azure. Ce processus vous montre comment créer une base de données SQL à l'aide d'un serveur existant et montre également comment créer un nouveau serveur, si nécessaire.

> [AZURE.NOTE] La création d'une base de données SQL avec la **CRÉATION RAPIDE** configure une base de données Standard (S0). Pour créer une base de données SQL à un niveau de performances et un niveau de service autres que Standard (S0), utilisez la **CRÉATION PERSONNALISÉE**. Pour plus d'informations sur la création d'une base de données SQL Azure à l'aide de la **CRÉATION PERSONNALISÉE**, consultez [Prise en main de la base de données SQL Microsoft Azure](http://azure.microsoft.com/documentation/articles/sql-database-get-started/).

##  Création d'une base de données SQL Azure

1. Connectez-vous au [Portail de gestion](https://portal.azure.com/).

2. En bas de la page, cliquez sur **NOUVEAU**.

	![Click SQL Databases][1]

3. Cliquez sur **SERVICES DE DONNÉES**, **BASE DE DONNÉES SQL**, puis sur **CRÉATION RAPIDE**.

	![Click New, Data Services, and Quick Create][2]
	 
5. Dans **CRÉATION RAPIDE**, entrez un nom pour la nouvelle base de données, sélectionnez un abonnement, puis sélectionnez le serveur à partir de la liste **SERVEUR** (passez à l'étape suivante pour créer un nouveau serveur).

	![Create a new SQL Database in an existing server][7]

	Si vous le souhaitez, créez un nouveau serveur en sélectionnant **Nouveau serveur de base de données SQL**.
    ![Create a new SQL Database and a new server][8]

	1. Choisissez une région. La région détermine l'emplacement géographique du serveur. Choisissez une région qui est pertinente pour ce serveur, car il n'est pas possible de basculer facilement d'une région à l'autre. Choisissez un emplacement le plus proche possible de vous. Vous économisez sur les coûts de la bande passante de sortie et réduisez la latence des données en maintenant l'application et la base de données Azure dans la même région.
	2. Entrez un nom de connexion (sans espace). 

		La base de données SQL utilise l'authentification SQL sur une connexion chiffrée. Une nouvelle connexion d'authentification SQL Server attribuée au rôle serveur administrateur système fixe est créée avec le nom indiqué. 

		L'ID de connexion ne peut pas être une adresse de messagerie, un compte d'utilisateur Windows ou un Windows Live ID. L'authentification Windows et l'authentification basée sur les revendications ne sont pas prises en charge sur la base de données SQL. 
	3. Fournissez un mot de passe fort de plus de huit caractères, combinant des majuscules et des minuscules, et comportant un chiffre ou un symbole.

	


9. Une fois que vous avez terminé, cliquez sur la coche **CRÉATION D'UNE BASE DE DONNÉES SQL** en bas de la page.

### Nom du serveur généré automatiquement

Notez que si vous avez créé un nouveau serveur, vous n'avez spécifié aucun nom de serveur. La base de données SQL génère automatiquement le nom du serveur pour garantir l'absence d'entrées DNS en double. Le nom du serveur se présente sous la forme d'une chaîne alphanumérique de dix caractères. Vous ne pouvez pas changer le nom de votre serveur de base de données SQL.

À l'étape suivante, vous allez configurer le pare-feu de telle sorte que les connexions des applications exécutées sur votre réseau soient autorisées à y accéder.

<a id="configFWLogical"></a>

##  Configuration du pare-feu pour le serveur logique

1. Dans le [portail de gestion](http://manage.windowsazure.com), cliquez sur **bases de données SQL**, puis cliquez sur **Serveurs**

	![Click Servers][4]
2. Dans la liste, cliquez sur le serveur que vous venez de créer.

2. Cliquez sur **Configurer**.

	![Click Configure][5]

3. Dans la section **Adresses IP autorisées**, cliquez sur **AJOUTER AUX ADRESSES IP AUTORISÉES**. Il s'agit de l'adresse IP sur laquelle votre routeur ou serveur proxy est actuellement à l'écoute. La base de données SQL détecte l'adresse IP utilisée par la connexion actuelle et crée une règle de pare-feu pour accepter les demandes de connexion provenant de cet appareil. 
	![Click Add to the allowed IP addresses][6]

	Un nom par défaut est généré pour la règle. Modifiez le nom selon vos souhaits. 
	

4. Lorsque vous vous connectez à la base de données à partir d'un autre ordinateur, vous devez créer une nouvelle règle pour permettre à l'adresse IP de se connecter. Utilisez les zones de début et de fin pour créer une plage d'adresses IP.

	Si les ordinateurs clients utilisent des adresses IP attribuées dynamiquement, vous pouvez indiquer une plage suffisamment large afin d'inclure les adresses IP attribuées aux ordinateurs de votre réseau. Optez pour une plage restreinte au départ, puis étendez-la selon vos besoins.

7. Cliquez sur **Enregistrer** en bas de la page pour terminer l'étape. 

Vous disposez à présent d'une base de données SQL, d'un serveur logique, d'une règle de pare-feu qui autorise les connexions entrantes provenant de votre adresse IP et d'une connexion administrateur. 

**Remarque :** le serveur logique que vous venez de créer est virtuel et hébergé dynamiquement sur des serveurs physiques dans un centre de données. La suppression du serveur est une action irréversible. Veillez à sauvegarder les bases de données que vous téléchargez sur le serveur. 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png
[7]: ./media/sql-database-create-configure/quick-create-existing-server.png
[8]: ./media/sql-database-create-configure/quick-create-new-server.png




<!--HONumber=47-->
