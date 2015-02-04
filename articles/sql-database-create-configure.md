<properties urlDisplayName="How to create and configure an Azure SQL DB" pageTitle="Création et configuration d'une base de données SQL Azure - Didacticiel Azure" metaKeywords="Création base de données SQL Azure, configuration de base de données SQL Azure" description="Création et configuration d'une base de données SQL Azure" metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure an Azure SQL Database" authors="sidneyh" solutions="" manager="jhubbard" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

<h1><a id="configLogical"></a>Création et configuration d'une base de données SQL Azure</h1>

Dans cette rubrique, vous allez créer et configurer une base de données SQL Azure à l'aide du portail de gestion Azure. Dans ce workflow, nous allons commencer par la création d'un serveur. Vous pouvez privilégier cette méthode si vous souhaitez télécharger des bases de données SQL Server existantes.

## Sommaire##
* [ Création d'un serveur logique](#createLogical)
* [ Configuration du pare-feu pour le serveur logique](#configFWLogical)

<h2><a id="createLogical"></a> Création d'un serveur logique</h2>

1. Connectez-vous au [portail de gestion](http://manage.windowsazure.com).

2. En bas de la page, cliquez sur **NOUVEAU**.

	![Click SQL Databases][1]

3. Cliquez sur **Data Services**, puis sur **Base de données SQL**, puis sur **Création rapide**.

	![Click New, Data Services, and Quick Create][2]
	 
5. Dans **Paramètres du serveur de base de données SQL**, sélectionnez un abonnement.

	![Select a subscription][3]

6. Dans **Paramètres du serveur de base de données SQL**, entrez un nom de connexion en un seul mot sans espaces. 

	La base de données SQL utilise l'authentification SQL sur une connexion chiffrée. Une nouvelle connexion d'authentification SQL Server attribuée au rôle serveur administrateur système fixe est créée avec le nom indiqué. 

	L'identifiant de connexion ne peut pas correspondre à une adresse électronique, à un compte d'utilisateur Windows, ni à un Windows Live ID. Les revendications et l'authentification Windows ne sont pas prises en charge sur la base de données SQL.
	
7. Fournissez un mot de passe fort de plus de huit caractères, combinant des majuscules et des minuscules, et comportant un chiffre ou un symbole.

7. Choisissez une région. La région détermine l'emplacement géographique du serveur. Choisissez une région qui est pertinente pour ce serveur, car il n'est pas possible de basculer facilement d'une région à l'autre. Choisissez un emplacement le plus proche possible de vous. Vous économisez sur les coûts de la bande passante de sortie et réduisez la latence des données en maintenant l'application et la base de données Azure dans la même région.

8. Veillez à ce que l'option **Autoriser les services Microsoft Azure à accéder au serveur** reste activée pour pouvoir vous connecter à cette base de données avec le portail de gestion, les services de stockage et d'autres services sur Azure. 

9. Une fois que vous avez terminé, cliquez sur la coche en bas de la page.

### Nom du serveur généré automatiquement

Comme vous pouvez le remarquer, vous n'avez spécifié aucun nom de serveur. La base de données SQL génère automatiquement le nom du serveur pour garantir l'absence d'entrées DNS en double. Le nom du serveur se présente sous la forme d'une chaîne alphanumérique de dix caractères. Vous ne pouvez pas changer le nom de votre serveur de base de données SQL.

À l'étape suivante, vous allez configurer le pare-feu de telle sorte que les connexions des applications exécutées sur votre réseau soient autorisées à y accéder.

## Configuration du pare-feu pour le serveur logique

1. Dans le [portail de gestion](http://manage.windowsazure.com), cliquez sur **bases de données SQL**, puis cliquez sur **Serveurs**

	![Click Servers][4]
2. Dans la liste, cliquez sur le serveur que vous venez de créer.

2. Cliquez sur **Configurer**.

	![Click Configure][5]

3. Dans la section **Adresses IP autorisées**, cliquez sur **AJOUTEZ AUX ADRESSES IP AUTORISÉES**. Il s'agit de l'adresse IP sur laquelle votre routeur ou serveur proxy est actuellement à l'écoute. La base de données SQL détecte l'adresse IP utilisée par la connexion actuelle et crée une règle de pare-feu pour accepter les demandes de connexion provenant de cet appareil. 
	![Click Add to the allowed IP addresses][6]

	Un nom par défaut est généré pour la règle. Modifiez le nom selon vos souhaits. 
	

4. Lorsque vous vous connectez à la base de données à partir d'un autre ordinateur, vous devez créer une nouvelle règle pour permettre à l'adresse IP de se connecter. Utilisez les zones de début et de fin pour créer une plage d'adresses IP.

	Si les ordinateurs clients utilisent des adresses IP attribuées dynamiquement, vous pouvez indiquer une plage suffisamment large afin d'inclure les adresses IP attribuées aux ordinateurs de votre réseau. Optez pour une plage restreinte au départ, puis étendez-la selon vos besoins.

7. Cliquez sur **Enregistrer** en bas de la page pour achever cette étape. 

Vous disposez à présent d'un serveur logique, d'une règle de pare-feu qui autorise les connexions entrantes provenant de votre adresse IP et d'une connexion administrateur. 

**Remarque:** le serveur logique que vous venez de créer est virtuel et hébergé dynamiquement sur plusieurs serveurs physiques d'un centre de données. La suppression du serveur est une action irréversible. Veillez à sauvegarder toutes les bases de données que vous téléchargez par la suite sur le serveur. 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png

