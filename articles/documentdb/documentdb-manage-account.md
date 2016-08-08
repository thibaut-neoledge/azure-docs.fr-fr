<properties
	pageTitle="Gestion d’un compte DocumentDB via le portail Azure | Microsoft Azure"
	description="Apprenez à gérer un compte DocumentDB via le portail Azure. Trouvez un guide vous expliquant comment utiliser le portail Azure pour afficher, copier, supprimer et accéder aux comptes."
	keywords="Portail Azure, documentdb, azure, Microsoft azure"
	services="documentdb"
	documentationCenter=""
	authors="AndrewHoh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2016"
	ms.author="anhoh"/>

# Gestion d'un compte DocumentDB

Découvrez comment configurer la cohérence globale et gérer plusieurs régions pour une disponibilité globale des données dans Azure DocumentDB. Découvrez également comment utiliser les clés et supprimer un compte dans le Portail Azure.

## <a id="consistency"></a>Gestion des paramètres de cohérence DocumentDB

La sélection du niveau de cohérence adéquat dépend de la sémantique de votre application. Vous devez vous familiariser avec les niveaux de cohérence disponibles dans DocumentDB : [Utilisation des niveaux de cohérence pour optimiser la disponibilité et les performances dans DocumentDB][consistency]. DocumentDB fournit les garanties de cohérence, de disponibilité et de performance, à tous les niveaux de cohérence disponibles pour votre compte de base de données. La configuration de votre compte de base de données avec un niveau de cohérence Fort nécessite que vos données se limitent à une seule région Azure et ne soient pas disponibles mondialement. En revanche, les niveaux de cohérence souples (Obsolescence limitée, Session ou Éventuel) vous permettent d’associer autant de régions Azure que vous le souhaitez à votre compte de base de données. Les étapes simples suivantes vous montrent comment sélectionner le niveau de cohérence par défaut pour votre compte de base de données.

### Pour spécifier le niveau de cohérence par défaut d’un compte DocumentDB

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte DocumentDB.
2. Dans le panneau du compte, si le panneau **Paramètres** n’est pas déjà ouvert, cliquez sur l’icône **Tous les paramètres**. ![Cohérence par défaut Session][5]

3. Dans le panneau **Tous les paramètres**, cliquez sur l’entrée **Cohérence par défaut** sous **Fonctionnalité**. ![Cohérence par défaut Session][6]
4. Dans le panneau **Cohérence par défaut**, sélectionnez le nouveau niveau de cohérence et cliquez sur **Enregistrer**.
5. La progression de l’opération peut être contrôlée via le hub de notifications du portail Azure.

> [AZURE.NOTE] L’application d’une modification du paramètre de cohérence par défaut dans votre compte DocumentDB peut prendre plusieurs minutes.

## <a id="addregion"></a>Ajouter des régions

DocumentDB est disponible dans la plupart des [régions Azure][azureregions]. Après avoir sélectionné le niveau de cohérence par défaut pour votre compte de base de données, vous pouvez associer une ou plusieurs régions (en fonction de votre choix de niveau de cohérence par défaut et de vos besoins de distribution mondiale).

> [AZURE.NOTE] À ce stade, vous pouvez ajouter de nouvelles régions aux nouveaux comptes DocumentDB créés le 13 juin 2016 ou ultérieurement. Sélectionnez « Azure DocumentDB - Multi-region database Account » (Azure DocumentDB - Compte de base de données dans plusieurs régions) dans le Marketplace pour créer un compte à plusieurs régions. Les comptes créés avant le 13 juin seront activés pour la disponibilité globale dans un avenir proche.

1. Dans la barre de lancement du [portail Azure](https://portal.azure.com/), cliquez sur **Comptes DocumentDB**.
2. Dans le panneau **Compte DocumentDB**, sélectionnez le compte de base de données à modifier.
3. Dans le panneau du compte, si le panneau **Tous les paramètres** n’est pas déjà ouvert, cliquez sur **Tous les paramètres**.
4. Dans le panneau **Tous les paramètres**, cliquez sur **Ajouter/Supprimer des régions**. ![Ajoutez des régions sous Compte DocumentDB > Paramètres > Ajouter/supprimer des régions][1]
5. Dans le panneau **Ajouter/Supprimer des régions**, sélectionnez les régions à ajouter ou supprimer, puis cliquez sur **OK**. L’ajout de régions a un coût, consultez la page de tarification pour plus d’informations.

    ![Cliquez sur les régions dans la carte pour les ajouter ou les supprimer][2]

### Sélectionner des régions

Lors de la configuration de deux régions ou plus, nous vous recommandons de sélectionner les régions en fonction des paires de régions décrites dans l’article [Continuité des activités et récupération d’urgence (BCDR) : régions jumelées d’Azure][bcdr].

En particulier, lors de la configuration de plusieurs régions, veillez à sélectionner le même nombre de régions (+/-1 pour pair/impair) à partir de chacune des colonnes de la région associée. Par exemple, si vous voulez déployer vers 4 régions des États-Unis, vous sélectionnez 2 régions des États-Unis dans la colonne de gauche et 2 autres dans la colonne de droite. La configuration suivante devrait donc être appropriée : États-Unis de l’Ouest, États-Unis de l’Est, Nord du centre des États-Unis et Sud du centre des États-Unis.

Il est important de suivre ces instructions lorsque 2 régions seulement sont configurées pour les scénarios de récupération d’urgence. Lorsqu’il y a plus de 2 régions, le suivi de ces instructions est une bonne pratique mais n’est pas essentiel dans la mesure où certaines des régions sélectionnées respectent cette association.

## <a id="selectwriteregion"></a>Sélectionner la région d’écriture

Même si toutes les régions associées à votre compte de base de données DocumentDB sont en mesure de traiter les lectures (lectures paginées à un ou plusieurs éléments) et les requêtes, une seule région peut recevoir activement les demandes d’écriture (insertion, upsert, remplacement, suppression). Pour définir la région d’écriture active, procédez comme suit


1. Dans le panneau **Compte DocumentDB**, sélectionnez le compte de base de données à modifier.
2. Dans le panneau du compte, si le panneau **Tous les paramètres** n’est pas déjà ouvert, cliquez sur **Tous les paramètres**.
3. Dans le panneau **Tous les paramètres**, cliquez sur **Write Region Priority** (Priorité de région d’écriture). ![Changez la région d’écriture sous Compte DocumentDB > Paramètres > Ajouter/supprimer des régions][3]
4. Cliquez sur les régions et faites-les glisser pour ordonner la liste des régions. La première région dans la liste des régions est la région d’écriture active. ![Changez la zone d’écriture en réorganisant la liste des régions sous le compte DocumentDB > Paramètres > Changer la région d’écriture][4]

## <a id="keys"></a>Affichage, copie et régénération des clés d’accès
Lorsque vous créez un compte DocumentDB, le service génère deux clés d'accès maître qui peuvent être utilisées pour l'authentification lors de l'accès au compte DocumentDB. En fournissant deux clés d'accès, DocumentDB vous permet de régénérer les clés sans interruption de votre compte DocumentDB.

Dans le [portail Microsoft Azure](https://portal.azure.com/), accédez au panneau **Clés** à partir de la barre **Essentials** du panneau **Compte DocumentDB** pour afficher, copier et régénérer les clés d’accès utilisées pour accéder à votre compte DocumentDB.

![Capture d’écran du portail Azure, panneau Clés](./media/documentdb-manage-account/keys.png)

Une autre option consiste à accéder à l’entrée **Clés** à partir du panneau **Tous les paramètres**.

![Tous les paramètres, panneau Clés](./media/documentdb-manage-account/allsettingskeys.png)

> [AZURE.NOTE] Le panneau **Clés** inclut également des chaînes de connexion primaire et secondaire qui peuvent être utilisées pour la connexion à votre compte à partir de [l’outil de migration de données](documentdb-import-data.md).

Il inclut également des clés en lecture seule pour permettre aux utilisateurs d’accéder en lecture seule à DocumentDB. Les lectures et les demandes sont des opérations en lecture seule, contrairement aux opérations de création, de suppression et de remplacement.

### Affichage et copie d’une touche d’accès rapide dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte DocumentDB.
2. Dans la barre **Essentials** du panneau **Compte DocumentDB**, cliquez sur **Clés**.
3. Dans le panneau **Clés**, cliquez sur le bouton **Copier** à droite de la clé que vous souhaitez copier. ![Affichage et copie d’une touche d’accès rapide dans le portail Azure, panneau Clés](./media/documentdb-manage-account/copykeys.png)

### Régénération de clés d'accès

Vous devez modifier périodiquement les clés d'accès à votre compte DocumentDB pour garantir la sécurité des connexions. Deux clés d'accès vous sont affectées afin de vous permettre de conserver des connexions au compte DocumentDB à l'aide d'une clé d'accès lorsque vous régénérez l'autre clé.

> [AZURE.WARNING] La régénération de vos clés d'accès affecte toutes les applications qui dépendent de la clé actuelle. Tous les clients qui utilisent la clé d'accès pour accéder au compte DocumentDB doivent être mis à jour pour utiliser la nouvelle clé.

Si certains de vos services cloud ou applications utilisent le compte DocumentDB, vous perdrez les connexions en régénérant les clés, sauf si vous remplacez vos clés. Les étapes suivantes décrivent le processus de remplacement de vos clés.

1. Mettez à jour la clé d'accès dans le code de votre application afin de référencer la clé d'accès secondaire du compte DocumentDB.

2. Régénérez la clé d’accès principale de votre compte de stockage. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte DocumentDB.

3. Dans la barre **Essentials** du panneau **Compte DocumentDB**, cliquez sur **Clés**.

4. Dans le panneau **Clés**, cliquez sur la commande **Régénérer la clé primaire**, puis sur **OK** pour confirmer que vous souhaitez générer une nouvelle clé.

5. Une fois que vous avez vérifié que la nouvelle clé peut être utilisée(environ 5 minutes après la régénération), mettez à jour la clé d'accès dans le code de votre application afin de référencer la nouvelle clé d'accès primaire.

6. Régénérez la clé d’accès secondaire.


> [AZURE.NOTE] Il faut parfois attendre plusieurs minutes avant de pouvoir utiliser une clé qui vient d’être générée pour accéder à votre compte DocumentDB.

## <a id="delete"></a> Supprimer un compte DocumentDB
Pour supprimer un compte DocumentDB dont vous ne vous servez plus dans le portail Azure, utilisez la commande **Supprimer** du panneau **Compte DocumentDB**.

![Comment supprimer un compte DocumentDB dans le portail Azure](./media/documentdb-manage-account/deleteaccountconfirmation.png)


1. Dans le [portail Azure](https://portal.azure.com/), accédez au compte DocumentDB à supprimer.
2. Dans le panneau **Compte DocumentDB**, cliquez sur la commande **Supprimer le compte**.
3. Dans le volet de confirmation qui s'affiche, entrez le nom du compte DocumentDB afin pour confirmer que vous souhaitez le supprimer.
4. Dans le panneau de confirmation, cliquez sur le bouton **Supprimer**.

## <a id="next"></a>Étapes suivantes

Découvrez comment [prendre en main votre compte DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Pour en savoir plus sur DocumentDB, consultez la documentation Azure DocumentDB sur [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).


<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/fr-FR/regions/#services
[offers]: https://azure.microsoft.com/fr-FR/pricing/details/documentdb/

<!---HONumber=AcomDC_0727_2016-->