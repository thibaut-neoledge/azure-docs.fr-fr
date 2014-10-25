<properties linkid="manage-services-how-to-configure-a-sqldb" urlDisplayName="How to configure" pageTitle="How to configure a SQL Database - Azure" metaKeywords="Azure creating SQL Server, Azure configuring SQL Server" description="Learn how to create and configure a logical server using SQL Server in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure SQL Database" authors="Lori Clark," solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Lori Clark,"></tags>

# <span id="configLogical"></span></a>Création et configuration d'une base de données SQL

Cette rubrique vous donne des explications sur la création et la configuration d'un serveur logique. La nouvelle version préliminaire du portail de gestion Azure propose une refonte des workflows qui vous permettent de commencer par la création d'une base de données avant de créer un serveur.

Dans cette rubrique, nous allons commencer par la création d'un serveur. Vous pouvez privilégier cette méthode si vous souhaitez télécharger des bases de données SQL Server existantes.

## Sommaire

-   [Création d'un serveur logique][]
-   [Configuration du pare-feu pour le serveur logique][]

## <span id="createLogical"></span></a> Création d'un serveur logique

1.  Connectez-vous au [portail de gestion][].

2.  Cliquez sur **Base de données SQL**, puis sur **SERVEURS** sur la page d'accueil de la base de données SQL.

3.  Cliquez sur **Ajouter** en bas de la page.

4.  Sur la page Paramètres du serveur, entrez un nom d'administrateur en un mot (sans espace).

    La base de données SQL utilise l'authentification SQL sur une connexion chiffrée. Une nouvelle connexion d'authentification SQL Server attribuée au rôle serveur administrateur système fixe est créée avec le nom indiqué.

    L'identifiant de connexion ne peut pas correspondre à une adresse électronique, à un compte d'utilisateur Windows, ni à un Windows Live ID. Les revendications et l'authentification Windows ne sont pas prises en charge sur la base de données SQL.

5.  Fournissez un mot de passe fort de plus de huit caractères, combinant des majuscules et des minuscules, et comportant un chiffre ou un symbole.

6.  Choisissez une région. La région détermine l'emplacement géographique du serveur. Choisissez une région qui est pertinente pour ce serveur, car il n'est pas possible de basculer facilement d'une région à l'autre. Choisissez un emplacement le plus proche possible de vous. Vous économisez sur les coûts de la bande passante de sortie et réduisez la latence des données en maintenant l'application et la base de données Azure dans la même région.

7.  Veillez à ce que l'option **Autoriser les services** reste activée pour pouvoir vous connecter à cette base de données avec le portail de gestion pour la base de données SQL, les services de stockage et d'autres services sur Azure.

8.  Une fois que vous avez terminé, cliquez sur la coche en bas de la page.

Comme vous pouvez le remarquer, vous n'avez spécifié aucun nom de serveur. La base de données SQL génère automatiquement le nom du serveur pour garantir l'absence d'entrées DNS en double. Le nom du serveur se présente sous la forme d'une chaîne alphanumérique de dix caractères. Vous ne pouvez pas changer le nom de votre serveur de base de données SQL.

À l'étape suivante, vous allez configurer le pare-feu de telle sorte que les connexions des applications exécutées sur votre réseau soient autorisées à y accéder.

## <span id="configFWLogical"></span></a> Configuration du pare-feu pour le serveur logique

1.  Dans le [portail de gestion][], cliquez sur **Bases de données SQL**, sur **Serveurs**, puis sur le serveur que vous venez de créer.

2.  Cliquez sur **Configurer**.

3.  Copiez l'adresse IP actuelle du client. Si vous vous connectez depuis un réseau, il s'agit de l'adresse IP sur laquelle votre routeur ou serveur proxy est à l'écoute. La base de données SQL détecte l'adresse IP utilisée par la connexion actuelle afin que vous puissiez créer une règle de pare-feu pour accepter les demandes de connexion provenant de cet appareil.

4.  Collez l'adresse IP dans les plages de début et de fin. Plus tard, si vous êtes confronté à des erreurs de connexion indiquant que la plage est trop étroite, vous pourrez modifier cette règle pour élargir la plage.

    Si les ordinateurs clients utilisent des adresses IP attribuées dynamiquement, vous devez indiquer une plage suffisamment large afin d'inclure les adresses IP attribuées aux ordinateurs de votre réseau. Optez pour une plage restreinte au départ, puis étendez-la selon vos besoins.

5.  Entrez un nom pour la règle de pare-feu, par exemple, le nom de l'ordinateur ou de l'entreprise.

6.  Cliquez sur la coche pour enregistrer la règle.

7.  Cliquez sur **Enregistrer** en bas de la page pour achever cette étape. Si **Enregistrer** n'est pas visible, actualisez la page du navigateur.

Vous disposez à présent d'un serveur logique, d'une règle de pare-feu qui autorise les connexions entrantes provenant de votre adresse IP et d'une connexion administrateur. À l'étape suivante, repassez sur votre ordinateur local afin d'effectuer les étapes de configuration restantes.

**Remarque :** le serveur logique que vous venez de créer est temporaire et hébergé dynamiquement sur plusieurs serveurs physiques d'un centre de données. Si vous supprimez le serveur, sachez qu'il s'agit d'une action irréversible. Veillez à sauvegarder toutes les bases de données que vous téléchargez par la suite sur le serveur.

  [Création d'un serveur logique]: #createLogical
  [Configuration du pare-feu pour le serveur logique]: #configFWLogical
  [portail de gestion]: http://manage.windowsazure.com
