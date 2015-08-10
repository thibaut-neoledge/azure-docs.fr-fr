<properties
   pageTitle="Prise en main : approvisionnement d’une instance SQL Data Warehouse | Microsoft Azure"
   description="Suivez ces différentes étapes et instructions pour approvisionner une instance SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Prise en main : approvisionnement d’une instance SQL Data Warehouse #

Cet article, qui constitue un guide rapide, a pour but de vous aider à provisionner une instance SQL Data Warehouse dans Azure. En suivant ce guide, vous allez effectuer les tâches suivantes :

1. création d’une base de données SQL Data Warehouse ;
2. configuration d’un nouveau serveur logique ;
3. définition d’une règle de pare-feu Azure pour activer l’accès de clients externes.

## Version d’évaluation gratuite Azure ##
Vous devez disposer d’un abonnement à Microsoft Azure pour pouvoir effectuer les tâches ci-dessous. Ce n’est pas le cas ? Alors, l’obtention d’un abonnement constitue la première étape à suivre.

Vous pouvez accéder à une [version d’essai gratuit][], qui vous permet d’essayer l’un des services disponibles dans Microsoft Azure. Cela inclut SQL Data Warehouse.


## Se connecter au portail Azure ##

Une fois votre abonnement obtenu, vous pouvez vous connecter au [portail Azure][]. À présent, ouvrez une session.

Au cours de la procédure suivante, nous allons rapidement créer un tout nouveau serveur logique, ainsi qu’une base de données SQL Data Warehouse.

## Localiser le service SQL Data Warehouse

Nous devons avant tout localiser le service SQL Data Warehouse dans le portail Azure.

Dans l’angle inférieur gauche du portail Azure, vous pouvez voir apparaître le bouton Nouveau. Ce bouton est le point de départ de toute création de service dans Microsoft Azure.

- Cliquez sur le bouton Nouveau.

### Stockage + Données

En cliquant sur le bouton Nouveau, vous avez ouvert toutes les catégories de service dans Azure. Le logiciel SQL Data Warehouse se trouve dans la catégorie Stockage + Données.

- Cliquez sur cette dernière pour afficher les détails et faire apparaître les services offerts par Microsoft Azure pour cette catégorie.

### SQL Data Warehouse

Comme vous pouvez le voir, Microsoft Azure propose un grand nombre de moteurs de données et de stockage. Toutefois, ce guide de prise en main est destiné à SQL Data Warehouse.

- À présent, sélectionnez SQL Data Warehouse.

## Configurer SQL Data Warehouse

Pour terminer le processus de déploiement, configurez simplement SQL Data Warehouse.


### Nom de la base de données

La première étape consiste à nommer la base de données.



- Pour ce démarrage rapide, nous l’appellerons « MonSQLDW ».


> [AZURE.NOTE]Bien sûr, lorsque vous créez votre propre base de données, vous pouvez lui attribuer le nom de votre choix. Toutefois, ce nom doit respecter les exigences de dénomination de base d’Azure.

### Performances

L’option relative aux performances est **importante**. L’évolutivité de la puissance de SQL Data Warehouse repose sur ce curseur. Vous pouvez augmenter ou diminuer les performances à tout moment, et pas uniquement lorsque vous configurez l’entrepôt de données. Plus vous faites glisser le curseur vers la droite, plus le nombre de ressources à votre disposition est élevé. Si ces ressources ne sont plus nécessaires, vous pouvez immédiatement le remettre à son emplacement de départ, ce qui vous permet de réduire les coûts. SQL Data Warehouse vous permet de modifier votre profil de performances à la demande sans devoir recréer l’entrepôt de données, ni déplacer des données.

- Vous pouvez voir de quelle manière le nombre d’unités Data Warehouse augmente à mesure que vous faites glisser le curseur vers la droite, et de quelle manière il baisse lorsque vous le déplacez vers la gauche.

- Avant de terminer cette étape, vérifiez que vous avez remis le curseur à son emplacement de départ, vers la gauche. Comme le nouvel entrepôt de données est petit, nous n’avons pas besoin d’un grand nombre de ressources. Réservez-les pour la suite de votre essai !

### Sélectionner une source

Cette option vous donne la possibilité d’utiliser une base de données vide, au départ. Sélectionnez votre nouvelle base de données en tant que point de départ.

> [AZURE.NOTE]Cependant, il existe une deuxième option. Il est possible de créer la base de données à partir d’un point de restauration existant (option de restauration).

### Serveur logique

Votre nouvelle base de données SQL Data Warehouse réside sur un serveur logique. Le serveur logique garantit la cohérence de la configuration d’un certain nombre de bases de données, et localise le service dans un centre de données Azure.

Les options que vous devez configurer sont les suivantes : 1) Nom du serveur. 2) Nom de l’administrateur du serveur. 3) Mot de passe. 4) Emplacement du centre de données. 5) Autorisation d’accès au serveur pour les services Microsoft Azure.

Définissez ces valeurs comme vous l’entendez. Le nom du serveur doit être unique. Nous vous recommandons de choisir un centre de données proche de vous, afin de réduire la latence du réseau. SQL Data Warehouse contient également des fonctionnalités puissantes, qui tirent parti d’autres services Microsoft Azure. Pour cette raison, il peut être judicieux de ne pas désélectionner la case de l’accès des services Microsoft Azure.

> [AZURE.NOTE]SQL Data Warehouse doit utiliser un serveur de version 12. Vérifiez que cette option est définie sur OUI. Par ailleurs, le serveur logique peut être partagé par les bases de données SQL Azure et les bases de données SQL Data Warehouse. Toutefois, il doit présenter la version 12.

> [AZURE.NOTE]Indiquez le nom du serveur, le nom de l’administrateur du serveur et le mot de passe, en les protégeant contre tout accès malveillant. Vous aurez besoin de ces informations pour vous connecter à la base de données SQL Data Warehouse.

### Groupe de ressources
Un groupe de ressources est un conteneur, conçu pour vous aider à gérer un ensemble de ressources Microsoft Azure.

Pour les besoins de ce guide de démarrage rapide, vous pouvez conserver les valeurs par défaut du groupe de ressources.

Voici quelques informations supplémentaires sur les [groupes de ressources](../azure-portal/resource-group-portal.md).

### Abonnement
Un utilisateur unique peut être associé à un ou plusieurs abonnements Microsoft Azure. Si votre identifiant de connexion est associé à plusieurs abonnements, vous pouvez choisir celui qui vous convient.

Toutefois, pour les besoins du présent guide, vous pouvez utiliser l’abonnement par défaut.

À présent, créons l’entrepôt de données SQL Data Warehouse.

## Créer l’entrepôt de données ##
Pour créer votre entrepôt de données, il vous suffit à présent de cliquer sur le bouton Créer.

Félicitations ! Vous avez créé votre première base de données SQL Data Warehouse.

Vous devriez être redirigé vers la page d’accueil du [portail Azure][]. Comme vous pouvez le constater, votre base de données SQL Data Warehouse a été ajoutée sur la page.


Cependant, à ce stade, personne ne peut y accéder. Pour éviter tout risque, par défaut, le système n’a pas configuré l’accès à cette base de données pour les clients.

Ainsi, la dernière étape du processus d’approvisionnement consiste à configurer le service pour accorder l’accès à des systèmes externes.

## Configurer le pare-feu Microsoft Azure ##

Pour configurer le pare-feu Microsoft Azure pour la première fois, procédez comme suit :

1. Cliquez sur le bouton Parcourir, dans le volet de navigation de gauche.

2. Choisissez Serveurs SQL.

3. Sélectionnez votre serveur SQL Server logique.

4. Choisissez les paramètres adéquats.

5. Cliquez sur le pare-feu.

6. Définissez votre règle de pare-feu.

    À présent, vous devez effectuer différentes opérations : - Attribuer un nom à votre règle de pare-feu ; - Fournir une plage d’adresses IP, si vous ne disposez pas d’une adresse IP statique.

    > [AZURE.NOTE]La plage d’adresses IP du client que vous devez inclure correspond à votre adresse IP externe ou publique. Pour connaître votre adresse IP externe, vous pouvez accéder à différents sites web, comme <a href="http://www.whatismyip.com" target="\_blank">www.whatismyip.com</a>.

7. Enregistrez votre règle de pare-feu.


Maintenant que vous avez configuré le pare-feu, vous devriez être en mesure d’établir des connexions entre votre ordinateur de bureau et l’entrepôt de données Azure SQL Data Warehouse que vous venez de créer.

## Étapes suivantes

Le service SQLDW est correctement approvisionné. Nous pouvons désormais découvrir comment l’utiliser.

Les étapes suivantes ont donc pour objectif de vous aider à savoir comment : [Connecter et interroger][] l’entrepôt de données. 2. Exporter les données de l’entrepôt de données vers le stockage d’objets blob Azure. 3. Charger d’autres données dans l’entrepôt de données.


<!--Image references-->


<!-- Articles -->
[Connecter et interroger]: sql-data-warehouse-get-started-connect-query.md

<!--External links-->
[version d’essai gratuit]: https://azure.microsoft.com/fr-fr/pricing/free-trial/
[portail Azure]: https://portal.azure.com/

<!---HONumber=July15_HO5-->