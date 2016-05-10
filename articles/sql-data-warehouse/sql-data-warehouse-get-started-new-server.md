<properties
   pageTitle="Créer une base de données SQL Data Warehouse dans le portail Azure | Microsoft Azure"
   description="Découvrez comment créer une base de données Azure SQL Data Warehouse dans le portail Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="lodipalm;"/>

# Créer un serveur logique

Dans la base de données SQL et SQL Data Warehouse, chaque base de données est affectée à un serveur, et chaque serveur est affecté à un emplacement géographique. Le serveur est appelé serveur SQL logique.

> [AZURE.NOTE] <a name="note"></a>Un serveur logique SQL :
  >
  > + Fournit un moyen cohérent de configurer plusieurs bases de données dans le même emplacement géographique.
  > + N’est pas un matériel physique, contrairement à un serveur local. Il fait partie du logiciel de service. C’est pourquoi nous utilisons le terme *serveur logique*.
  > + Peut héberger plusieurs bases de données sans affecter les performances.
  > + Inclut un *s* minuscule dans son nom. Un **s**erveur SQL est un serveur logique Azure, tandis que SQL **S**erver est un produit de base de données local de Microsoft.

1. Cliquez sur **Serveur** > **Créer un serveur**. Aucuns frais ne s’appliquent pour le serveur. Si vous avez déjà un serveur SQL logique V12 que vous souhaitez utiliser, sélectionnez votre serveur existant et passez à l’étape suivante.

    ![Créer un serveur](./media/sql-data-warehouse-get-started-provision/create-server.png)

2. Renseignez les informations correspondant au **Nouveau serveur**.

	- **Nom du serveur** : entrez un nom pour votre serveur logique. Il est unique pour chaque site géographique.
	- **Nom de l’administrateur de serveur** : entrez un nom d’utilisateur pour le compte d’administrateur de serveur.
	- **Mot de passe** : entrez le mot de passe de l’administrateur de serveur.
	- **Emplacement** : choisissez un emplacement géographique pour votre serveur. Pour réduire le temps de transfert des données, il est préférable de placer votre serveur à proximité des autres ressources de données auxquelles cette base de données accédera.
	- **Créer le serveur V12** : OUI est la seule option disponible pour SQL Data Warehouse.
	- **Autoriser les services Azure à accéder au serveur**: cette option est toujours activée pour SQL Data Warehouse

    >[AZURE.NOTE] Veillez à stocker le nom du serveur, le nom de l’administrateur du serveur et le mot de passe quelque part. Vous aurez besoin de ces informations pour vous connecter au serveur.

3. Cliquez sur **OK** pour enregistrer les paramètres de configuration du serveur SQL logique et revenir au panneau SQL Data Warehouse.

    ![Configurer un nouveau serveur](./media/sql-data-warehouse-get-started-provision/configure-server.png)

<!---HONumber=AcomDC_0504_2016-->