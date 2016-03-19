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
	ms.date="02/26/2016"
	ms.author="anhoh"/>

# Gestion d'un compte DocumentDB

Découvrez comment utiliser les clés et les niveaux de cohérence. Découvrez également comment supprimer un compte dans le portail Azure.

## <a id="keys"></a>Affichage, copie et régénération des clés d’accès
Lorsque vous créez un compte DocumentDB, le service génère deux clés d'accès maître qui peuvent être utilisées pour l'authentification lors de l'accès au compte DocumentDB. En fournissant deux clés d'accès, DocumentDB vous permet de régénérer les clés sans interruption de votre compte DocumentDB.

Dans le [portail Microsoft Azure](https://portal.azure.com/), accédez au panneau **Clés** à partir de la barre **Essentials** du panneau de votre **Compte DocumentDB** pour afficher, copier et régénérer les clés d’accès à votre compte DocumentDB.

![Capture d’écran du portail Azure, panneau Clés](./media/documentdb-manage-account/keys.png)

Une autre option consiste à accéder à l’entrée **Clés** à partir du panneau **Tous les paramètres**.

![Tous les paramètres, panneau Clés](./media/documentdb-manage-account/allsettingskeys.png)

Notez que le panneau **Clés** inclut également des chaînes de connexion primaire et secondaire qui peuvent être utilisées pour la connexion à votre compte à partir de l’[outil de migration de données](documentdb-import-data.md).

Il inclut également des clés en lecture seule pour permettre aux utilisateurs d’accéder en lecture seule à DocumentDB. Les lectures et les demandes sont des opérations en lecture seule, contrairement aux opérations de création, de suppression et de remplacement.

### Affichage et copie d’une touche d’accès rapide dans le portail Azure

1.      Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte DocumentDB. 

2.      Dans la barre **Essentials** du panneau **Compte DocumentDB**, cliquez sur **Clés**.

3.      Dans le panneau **Clés**, cliquez sur le bouton **Copier** à droite de la clé que vous souhaitez copier.

  ![Affichage et copie d’une touche d’accès rapide dans le portail Azure, panneau Clés](./media/documentdb-manage-account/copykeys.png)

### Régénération de clés d'accès

Vous devez modifier périodiquement les clés d'accès à votre compte DocumentDB pour garantir la sécurité des connexions. Deux clés d'accès vous sont affectées afin de vous permettre de conserver des connexions au compte DocumentDB à l'aide d'une clé d'accès lorsque vous régénérez l'autre clé.

> [AZURE.WARNING] La régénération de vos clés d'accès affecte toutes les applications qui dépendent de la clé actuelle. Tous les clients qui utilisent la clé d'accès pour accéder au compte DocumentDB doivent être mis à jour pour utiliser la nouvelle clé.

Si certains de vos services cloud ou applications utilisent le compte DocumentDB, vous perdrez les connexions en régénérant les clés, sauf si vous remplacez vos clés. Les étapes suivantes décrivent le processus de remplacement de vos clés.

1.      Mettez à jour la clé d'accès dans le code de votre application afin de référencer la clé d'accès secondaire du compte DocumentDB.

2.      Régénérez la clé d’accès principale de votre compte de stockage. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte DocumentDB.

3.      Dans la barre **Essentials** du panneau **Compte DocumentDB**, cliquez sur **Clés**.

4.      Dans le panneau **Clés**, cliquez sur la commande **Régénérer la clé primaire**, puis sur **OK** pour confirmer que vous souhaitez générer une nouvelle clé.

5.      Une fois que vous avez vérifié que la nouvelle clé peut être utilisée(environ 5 minutes après la régénération), mettez à jour la clé d'accès dans le code de votre application afin de référencer la nouvelle clé d'accès primaire.

6.      Régénérez la clé d’accès secondaire.

*Notez qu’il faut parfois attendre plusieurs minutes avant de pouvoir utiliser une clé qui vient d’être générée pour accéder à votre compte DocumentDB.*

## <a id="consistency"></a>Gestion des paramètres de cohérence DocumentDB
DocumentDB prend en charge quatre niveaux de cohérence des données bien définis et configurables par l'utilisateur afin de permettre aux développeurs de créer des compromis prévisibles entre cohérence, disponibilité et latence.

- Le niveau de cohérence **Fort** garantit que les opérations de lecture retournent toujours la dernière valeur écrite.

- Le niveau de cohérence **En fonction de l’obsolescence** garantit que les lectures ne sont pas trop obsolètes. Il garantit en particulier que la version des lectures n’est pas plus ancienne qu’un *certain nombre* de versions par rapport à la dernière version écrite.

- Le niveau de cohérence **Par session** garantit des lectures monotoniques (vous ne lisez jamais d’anciennes données, puis de nouvelles données, puis d’anciennes données à nouveau) et des écritures monotoniques (les écritures sont ordonnées), et que vous lisez les écritures les plus récentes dans n’importe quel point de vue du client.

- Le niveau de cohérence **Éventuel** garantit que les opérations de lecture lisent toujours un sous-ensemble d’écritures valide, qui finiront éventuellement par converger.

*Notez que par défaut, les comptes DocumentDB sont configurés avec une cohérence de niveau Par session. Pour plus d’informations sur les paramètres de cohérence de DocumentDB, consultez la section [Niveau de cohérence](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### Pour spécifier le niveau de cohérence par défaut d’un compte DocumentDB

1.      Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte DocumentDB. 

2.      Dans le panneau du compte, si le panneau **Paramètres** n’est pas déjà ouvert, cliquez sur l’icône **Paramètres** dans la barre de commandes supérieure.

3.      Dans le panneau **Tous les paramètres**, cliquez sur l’entrée **Cohérence par défaut** sous **Fonctionnalité**.

![Cohérence par défaut Session](./media/documentdb-manage-account/chooseandsaveconsistency.png)

4.      Dans le panneau **Cohérence par défaut**, sélectionnez le nouveau niveau de cohérence et cliquez sur **Enregistrer**.

5.      La progression de l’opération peut être contrôlée via le hub de notifications du portail Azure.

*Notez que l’application d’une modification du paramètre de cohérence par défaut dans votre compte DocumentDB peut prendre plusieurs minutes.*

## <a id="delete"></a> Comment supprimer un compte DocumentDB dans le portail Azure
Pour supprimer un compte DocumentDB dont vous ne vous servez plus dans le portail Azure, utilisez la commande **Supprimer** du panneau **Compte DocumentDB**.

![Comment supprimer un compte DocumentDB dans le portail Azure](./media/documentdb-manage-account/deleteaccountconfirmation.png)

1.      Dans le [portail Azure](https://portal.azure.com/), accédez au compte DocumentDB à supprimer. 

2.      Dans le panneau **Compte DocumentDB**, cliquez sur la commande **Supprimer**.

3.      Dans le volet de confirmation qui s'affiche, entrez le nom du compte DocumentDB afin pour confirmer que vous souhaitez le supprimer.

4.      Dans le panneau de confirmation, cliquez sur le bouton **Supprimer**.

## <a id="next"></a>Étapes suivantes

Découvrez comment [prendre en main votre compte DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Pour en savoir plus sur DocumentDB, consultez la documentation Azure DocumentDB sur [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

<!---HONumber=AcomDC_0302_2016-->
