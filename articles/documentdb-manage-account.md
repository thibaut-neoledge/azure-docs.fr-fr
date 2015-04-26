<properties 
	pageTitle="Gestion d'un compte DocumentDB | Azure" 
	description="Découvrez comment gérer votre compte DocumentDB." 
	services="documentdb" 
	documentationCenter="" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="stbaro"/>

#Gestion d'un compte DocumentDB

Apprenez à travailler avec des clés, des paramètres de cohérence et des paramètres de capacité, et découvrez comment supprimer un compte.

## <a id="keys"></a>Affichage, copie et régénération de clés d'accès
Lorsque vous créez un compte DocumentDB, le service génère deux clés d'accès maître qui peuvent être utilisées pour l'authentification lors de l'accès au compte DocumentDB. En fournissant deux clés d'accès, DocumentDB vous permet de régénérer les clés sans interruption de votre compte DocumentDB.

Dans le [portail Azure en version préliminaire](https://portal.azure.com/), accédez à la partie **Clés** du volet de votre **Compte DocumentDB** pour afficher, copier et régénérer les clés d'accès à votre compte DocumentDB.

![](../includes/media/documentdb-keys/keys.png)

### Affichage et copie d'une clé d'accès

1.      Dans le [portail Azure en version préliminaire](https://portal.azure.com/), accédez à votre compte DocumentDB.

2.      Dans le filtre **Résumé**, cliquez sur **Clés**.

3.      Dans le volet **Clés**, cliquez sur le bouton **Copier** à droite de la clé que vous souhaitez copier.

  ![](./media/documentdb-manage-account/image004.jpg)

### Régénération de clés d'accès

Vous devez modifier périodiquement les clés d'accès à votre compte DocumentDB pour garantir la sécurité des connexions. Deux clés d'accès vous sont affectées afin de vous permettre de conserver des connexions au compte DocumentDB à l'aide d'une clé d'accès lorsque vous régénérez l'autre clé.

> [AZURE.WARNING] La régénération de vos clés d'accès affecte toutes les applications qui dépendent de la clé actuelle. Tous les clients qui utilisent la clé d'accès pour accéder au compte DocumentDB doivent être mis à jour pour utiliser la nouvelle clé.

Si certains de vos services cloud ou applications utilisent le compte DocumentDB, vous perdrez les connexions en régénérant les clés, sauf si vous remplacez vos clés. Les étapes suivantes décrivent le processus de remplacement de vos clés.

1.      Mettez à jour la clé d'accès dans le code de votre application afin de référencer la clé d'accès secondaire du compte DocumentDB.

2.      Régénérez la clé d'accès principale de votre compte de stockage. Dans le [portail Azure en version préliminaire](https://portal.azure.com/), accédez à votre compte DocumentDB.

3.      Dans le filtre Résumé, cliquez sur **Clés**.

4.      Dans le volet **Clés**, cliquez sur la commande **Régénérer la clé primaire**, puis cliquez sur **Ok** pour confirmer que vous souhaitez générer une nouvelle clé.

5.      Une fois que vous avez vérifié que la nouvelle clé peut être utilisée(environ 5 minutes après la régénération), mettez à jour la clé d'accès dans le code de votre application afin de référencer la nouvelle clé d'accès primaire.

6.      Régénérez la clé d'accès secondaire.

*Notez qu'il faut parfois attendre plusieurs minutes avant de pouvoir utiliser une clé qui vient d'être générée pour accéder à votre compte DocumentDB.*

## <a id="consistency"></a>Gestion des paramètres de cohérence DocumentDB
DocumentDB prend en charge quatre niveaux de cohérence des données bien définis et configurables par l'utilisateur afin de permettre aux développeurs de faire des compromis prévisibles entre cohérence, disponibilité et latence.

- **Strong** (Fort) : niveau de cohérence qui garantit que les opérations de lecture retournent toujours la dernière valeur écrite.

- **Bounded Staleness** (En fonction de l'obsolescence) : niveau de cohérence qui garantit que les lectures ne sont pas trop obsolètes. Il garantit en particulier que la version des lectures n'est pas plus ancienne qu'un certain nombre de versions par rapport à la dernière version écrite. 

- **Session** (Par session) : niveau de cohérence qui garantit des lectures monotoniques (vous ne lisez jamais d'anciennes données, puis de nouvelles données, puis d'anciennes données à nouveau) et des écritures monotoniques (les écritures sont ordonnées), et que vous lisez les écritures les plus récentes dans n'importe quel point de vue du client.

- **Eventual** (Éventuel) : niveau de cohérence qui garantit que les opérations de lecture lisent toujours un sous-ensemble d'écritures valides, qui finiront éventuellement par converger.

*Notez que par défaut, les comptes DocumentDB sont configurés avec une cohérence de niveau Session. Pour plus d'informations sur les paramètres de cohérence de DocumentDB, consultez la section [Niveau de cohérence](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### Pour spécifier le niveau de cohérence par défaut d'un compte DocumentDB

1.      Dans le [portail Azure en version préliminaire](https://portal.azure.com/), accédez à votre compte DocumentDB.

2.      Dans le filtre **Configuration**, cliquez sur **Cohérence par défaut**.

3.      Dans le volet **Cohérence par défaut**, sélectionnez le niveau de cohérence par défaut souhaité pour votre compte DocumentDB.

![](./media/documentdb-manage-account/image005.png)

![](./media/documentdb-manage-account/image006.png)

4.      Cliquez sur **Enregistrer**.

5.      La progression de l'opération peut être contrôlée via le hub de notifications du portail Azure en version préliminaire.

*Notez que l'application d'une modification du paramètre de cohérence par défaut dans votre compte DocumentDB peut prendre plusieurs minutes.*

## <a id="capacity"></a>Gestion des paramètres de capacité de DocumentDB
Microsoft Azure DocumentDB vous permet d'effectuer une mise à l'échelle avec une grande flexibilité, en fonction de l'évolution des demandes de votre application au cours de son cycle de vie. La mise à l'échelle de DocumentDB s'effectue en augmentant la capacité de votre compte de base de données DocumentDB via le portail Azure en version préliminaire.

Lorsque vous créez un compte de base de données, il est approvisionné avec un stockage de base de données et un débit réservé. Vous pouvez à tout moment modifier le stockage et le débit approvisionnés pour votre compte de base de données en ajoutant ou en supprimant des unités de capacité via le portail Azure en version préliminaire.

### Pour ajouter ou supprimer des unités de capacité

1.      Dans le [portail Azure en version préliminaire](https://portal.azure.com/), accédez à votre compte DocumentDB.

2.      Dans le filtre **Utilisation**, cliquez sur **Échelle**.

3.      Dans le volet **Échelle**, spécifiez le nombre d'unités de capacité désiré pour votre compte DocumentDB.


![](./media/documentdb-manage-account/image007.png)

4.      Cliquez sur **Enregistrer** (notez que l'opération de mise à l'échelle peut prendre plusieurs minutes et que sa progression peut être contrôlée via le hub de notifications du portail Azure en version préliminaire).

 *Notez que la version préliminaire de DocumentDB prend en charge un maximum de 5 unités de capacité
par compte DocumentDB.*


## <a id="delete"></a> Suppression d'un compte DocumentDB
Pour supprimer un compte DocumentDB dont vous ne vous servez plus, utilisez la commande **Supprimer** du volet **Compte DocumentDB**.

> [AZURE.WARNING] Dans la version préliminaire, il n'existe aucun moyen de restaurer le contenu d'un compte DocumentDB supprimé. La suppression d'un compte DocumentDB supprimera toutes les ressources du compte, y compris les bases de données, les collections, les documents et les pièces jointes.

![](./media/documentdb-manage-account/image009.png)

1.      Dans le [portail Azure en version préliminaire](https://portal.azure.com/), accédez au compte DocumentDB à supprimer.

2.      Dans le volet **Compte DocumentDB**, cliquez sur la commande **Supprimer**.

3.      Dans le volet de confirmation qui s'affiche, entrez le nom du compte DocumentDB afin pour confirmer que vous souhaitez le supprimer.

4.      Cliquez sur le bouton **Supprimer** dans le volet de confirmation.

## <a id="next"></a>Étapes suivantes

Découvrez comment [prendre en main votre compte DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Pour en savoir plus sur DocumentDB, consultez la documentation Azure DocumentDB disponible sur

[azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

 

<!--HONumber=49-->