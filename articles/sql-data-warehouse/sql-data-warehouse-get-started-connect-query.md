<properties
   pageTitle="Prise en main : connexion à SQL Data Warehouse | Microsoft Azure"
   description="Familiarisez-vous avec la procédure de connexion à SQL Data Warehouse et découvrez comme exécuter certaines requêtes."
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
   
# Prise en main : connexion à SQL Data Warehouse
Cet article vous présente rapidement le processus de connexion et d’interrogation d’une instance provisionnée de SQL Data Warehouse, via deux outils :

**Visual Studio** : l’outil d’édition de code et de débogage intégré de Visual Studio, SQL Server Data Tools (SSDT), est entièrement compatible avec SQLDW, ce qui vous permet de vous connecter à ce dernier, de le soumettre à des requêtes et de le gérer, en toute simplicité.

**sqlcmd** : cet outil de ligne de commande propose des fonctions simples de connexion et d’interrogation des données.

Après la fin de cet article, vous aurez effectué les opérations suivantes :

1. installation et mise à jour de Visual Studio 2013 ;
2. création d’une connexion à SQL Data Warehouse dans SSDT ;
3. exécution de requêtes sur la base de données SQL Data Warehouse.

>[AZURE.NOTE]Nous partons du principe que vous avez suivi les directives du guide d’approvisionnement, ou que le logiciel SQL Data Warehouse est disponible. Si ce dernier n’est pas approvisionné, reportez-vous au [guide de démarrage rapide sur l’approvisionnement].

## Configuration de Visual Studio à des fins de développement##
Pour le développement, l’équipe SQL Data Warehouse recommande l’utilisation de Visual Studio 2013 ou plus, en parallèle avec SSDT. Cette section explique comment télécharger et mettre à jour Visual Studio 2013 lorsqu’aucune version utilisable de ce logiciel n’est installée.

Pour toute question d’ordre général sur SSDT ou pour en savoir plus, reportez-vous à la [documentation complète sur SSDT](https://msdn.microsoft.com/library/azure/hh272686.aspx).

### Obtenir Visual Studio 2013 ###
Accédez au site web de Visual Studio 2013 pour télécharger et installer une version de Visual Studio. N’oubliez pas que vous pouvez utiliser n’importe quelle version gratuite avec SQL Data Warehouse. Choisissez celle qui répond le mieux à vos besoins.

<a href="https://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5" target="blank">Obtenir Visual Studio 2013</a>

### Mettre à jour Visual Studio 2013 ###
Vous avez déjà installé Visual Studio 2013 ? Parfait ! Pour vous assurer que ce logiciel est à jour, procédez comme suit. Vous pouvez passer à l’étape suivante.

1. Ouvrez Visual Studio 2013.
2. Dans le menu Outils, cliquez sur Extensions et mises à jour...
3. Parcourez l’arborescence afin d’accéder à Mises à jour et Mises à jour de produits.
4. Si aucune mise à jour n’est disponible, vous pouvez fermer la fenêtre Extensions et mises à jour et passer à l’étape suivante de cette procédure de démarrage rapide.

Si une mise à jour est disponible pour Visual Studio, cliquez sur le bouton Mettre à jour. Le téléchargement de la dernière mise à jour de Visual Studio 2013 se lance.

Fermez la fenêtre Extensions et mises à jour, ainsi que Visual Studio 2013, avant de continuer.

5. Cliquez sur le bouton Exécuter pour installer la dernière mise à jour de Visual Studio 2013.

6. Acceptez les termes du contrat de licence, cliquez sur le bouton Installer et acceptez les invites du Contrôle de compte d’utilisateur (UAC).

7. Cliquez sur le bouton Démarrer pour terminer l’installation.

La mise à jour de Visual Studio 2013 est terminée.

### Mettre SSDT à jour 
Peut-être devrez-vous également mettre SSDT à jour ; c’est parfaitement normal. Les ingénieurs SSDT mettent à jour leur plug-in très régulièrement, en ajoutant de nouvelles fonctions. Vous aurez donc besoin de mettre ce logiciel à jour de temps en temps. Là encore, la procédure à suivre est très simple. Pour vérifier si vous devez mettre SSDT à jour, procédez comme suit :

1. Ouvrez Visual Studio 2013.  
2. Dans le menu Outils, cliquez sur Extensions et mises à jour...
3. Parcourez l’arborescence afin d’accéder à Mises à jour et Mises à jour de produits.
4. Si aucune mise à jour n’est disponible, vous pouvez fermer la fenêtre Extensions et mises à jour et passer à l’étape suivante de cette procédure de démarrage rapide.

Toutefois, s’il existe une mise à jour Microsoft SQL Server pour les outils de la base de données, cliquez sur le bouton Mettre à jour.

Le téléchargement de la dernière version de SSDT démarre. L’image ci-dessous représente le fichier SSDTSetup.exe, dans le Gestionnaire de téléchargement de Microsoft Internet Explorer.

5. Cliquez sur le bouton Exécuter pour installer la dernière version du logiciel SSDT.

6. Acceptez les termes du contrat de licence, puis cliquez sur le bouton Installer.

7. Cliquez sur le bouton Fermer pour terminer la mise à jour du logiciel SSDT.

8. Fermez la fenêtre Extensions et mises à jour.

La version de Visual Studio 2013 apparaissant sur votre bureau est désormais à jour, tout comme l’extension SSDT.

> [AZURE.NOTE]Actuellement, nous recommandons l’utilisation de la [version préliminaire de SSDT pour Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=616714&clcid=0x409).

## Se connecter avec Visual Studio 2013
Si vous exécutez la version souhaitée de Visual Studio, vous pourrez vous connecter à l’instance SQL Data Warehouse de deux manières différentes :

### Dans Visual Studio
Pour établir la connexion, il nous suffit d’ouvrir l’Explorateur d’objets SQL Server et de transmettre les informations de connexion.

1. Ouvrez Visual Studio.
2. Cliquez sur le menu Affichage et sélectionnez Explorateur d’objets SQL Server dans la liste déroulante.

> [AZURE.NOTE]Vérifiez que vous avez bien choisi l’Explorateur d’objets SQL Server, et ***non*** l’Explorateur de serveurs. Ces deux noms sont très similaires, mais ils correspondent à des contrôles très différents. De plus, ils sont placés côte à côte. Vérifiez que vous avez bien choisi le bon !

Vous pouvez maintenant voir apparaître l’Explorateur d’objets SQL Server :


3. Dans cet Explorateur, cliquez sur le bouton Ajouter un serveur. Ce dernier a été mis en surbrillance dans l’image ci-dessous.

4. Renseignez la boîte de dialogue Se connecter au serveur.

Utilisez les valeurs que vous avez sélectionnées lors de la création du serveur logique.

Vous pouvez cocher la case Mémoriser le mot de passe, si vous le souhaitez. Cela peut vous permettre de gagner de temps. Toutefois, n’oubliez pas que, si vous cochez cette case, vous permettez à n’importe quel utilisateur ayant physiquement accès à votre profil d’exécuter des requêtes via ce compte.

> [AZURE.NOTE]Par ailleurs, rappelez-vous que le nom du serveur doit être complet. Par conséquent, le nom de votre serveur doit respecter la convention suivante : ***nom_serveur***. database.windows.net, où ***nom_serveur*** correspond au nom attribué à votre serveur logique.

Cliquez sur Se connecter une fois que vous avez saisi toutes les informations d’identification.

Vous avez maintenant établi la connexion et enregistré votre serveur logique SQLDW dans l’Explorateur d’objets SQL Server.
    
### Dans le portail Azure
Accédez à Visual Studio directement depuis le portail Azure.

1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).
2. Sélectionnez l’instance SQLDW souhaitée dans le panneau Parcourir. 
3. Dans la partie supérieure du panneau SQLDW, cliquez sur Ouvrir dans Visual Studio...
4. Si vous n’avez pas configuré votre pare-feu en indiquant l’adresse IP de votre ordinateur client, sélectionnez Configurer le Pare-feu Windows.
	1. Remplissez les champs Nom de la règle, Adresse IP de début et Adresse IP de fin. 
	2. Cliquez sur l’option Enregistrer figurant sur la partie supérieure du panneau.   
5. Cliquez sur Ouvrir dans Visual Studio. 
6. Visual Studio s’ouvre ; vous êtes invité à saisir vos informations d’identification. 
7. Une fois ces informations saisies et la connexion, établie, les instances DW et de votre serveur s’affichent dans le volet Explorateur d’objets SQL Server.  

## Se connecter à SQL Data Warehouse avec sqlcmd

Vous pouvez également vous connecter à SQLDW en utilisant l’utilitaire d’invite de commande [sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx) inclus avec SQL Server, ou via la version 11 des [utilitaires de ligne de commande Microsoft 11 pour SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501). L’utilitaire sqlcmd vous permet de saisir des procédures système, des instructions Transact-SQL et des fichiers de script à l’invite de commandes.

Pour vous connecter à une instance spécifique de SQLDW en utilisant sqlcmd, vous devez ouvrir la fenêtre d’invite de commandes et saisir la chaîne *sqlcmd*, suivie de la chaîne de connexion de votre base de données SQLDW. La chaîne de connexion doit contenir les paramètres suivants :

+ **Utilisateur (-U) :** utilisateur du serveur, sous la forme `<`Utilisateur`>`@`<`Nom_serveur`>`.database.windows.net.
+ **Mot de passe (-P) :** mot de passe associé à l’utilisateur.
+ **Serveur (-S) :** nom du serveur, sous la forme `<`Nom_serveur`>`.database.windows.net.
+ **Base de données (-D) :** nom de la base de données. 
+ **Activer les identificateurs marqués (-I) :** les identificateurs marqués doivent être activés pour que la connexion à une instance SQLDW soit possible. 

Par conséquent, pour vous connecter à une instance SQLDW, saisissez ce qui suit :

```
C:>sqlcmd -U <User>@<Server Name>.database.windows.net -P <Password> -S <Server Name>.database.windows.net -d <Database> -I
```

Une fois la connexion établie, vous pouvez envoyer des instructions Transact-SQL prises en charge à l’instance. Par exemple, l’instruction ci-dessous tire parti de [CREATE TABLE](https://msdn.microsoft.com/library/azure/dn268335.aspx) pour créer une table :

```
C:>sqlcmd -U <User>@<Server Name>.database.windows.net -P <Password> -S <Server Name>.database.windows.net -d <Database> -I
1> CREATE TABLE table1 (Col1 int, Col2 varchar(20));
2> GO
3> QUIT
```
	
Pour en savoir plus sur sqlcmd, consultez la [documentation sur sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx).

<!--NOTE: SQL DB does not support the -z/-Z parameters for changing users password with SQLCMD.  Do we support this? -->

## Exécution d’exemples de requêtes ##

Maintenant que nous avons enregistré notre serveur, passons à l’écriture des requêtes.

1. Dans l’outil SSDT, cliquez sur la base de données utilisateur.

2. Cliquez sur le bouton Nouvelle requête.

   Une nouvelle fenêtre s’ouvre.

3. Écrivez votre requête.

	Saisissez le code suivant dans la fenêtre de la requête :

	```
	SELECT  COUNT(*)
	FROM    sys.tables;
	```

4. Exécutez la requête.

	Pour exécuter la requête, cliquez sur la flèche verte ci-dessous, ou utilisez le raccourci `CTRL`+`SHIFT`+`F5` :

## Étapes suivantes ##
[Start developing code]: ./articles/sql-data-warehouse-overview-develop/

<!---HONumber=July15_HO3-->