<properties
	pageTitle="Procédure pas à pas : activation de la dernière mise à jour de la base de données SQL (version 12)"
	description="Décrit les étapes à suivre pour pouvoir essayer la version 12 de la base de données SQL Microsoft Azure, à l’aide de la nouvelle interface utilisateur du portail Microsoft Azure."
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
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="genemi"/>


# Procédure pas à pas : activation de la dernière mise à jour de la base de données SQL (version 12)

Cette rubrique décrit la procédure à suivre pour activer la version 12 de la base de données SQL Microsoft Azure, publiée pour la première fois par Microsoft en décembre 2014.

Pour essayer la dernière version 12, vous devez disposer d’un abonnement à Microsoft Azure, ou au moins d’un abonnement à la version d’[essai gratuit](http://azure.microsoft.com/pricing/free-trial/).

Vous pouvez activer la version 12 en utilisant le nouveau portail Microsoft Azure en version préliminaire, à l’adresse [http://portal.azure.com/](http://portal.azure.com/), au lieu du l’[ancien portail Microsoft Azure](http://manage.windowsazure.com/). Une fois que vous avez activé la version 12 pour votre abonnement, les options de création et de mise à niveau de la version 12 sont déverrouillées dans le portail Microsoft Azure. Les utilisateurs peuvent alors lancer le workflow de [création](sql-database-create.md) à partir du panneau du serveur ou de celui de la base de données.

> [AZURE.NOTE]Les bases de données de test, les copies de base de données ou les nouvelles bases de données sont parfaitement adaptées à la mise à jour vers la version préliminaire. Il est préférable d’attendre la fin de la version préliminaire avant de mettre à jour les bases de données dont dépend votre activité.

Pour en savoir plus sur la mise à niveau vers la version 12, voir [Planification et préparation de l’installation de la mise à niveau V12 de la base de données SQL](sql-database-v12-plan-prepare-upgrade.md).


## A. Autorisation de sécurité

La première étape consiste à vous assurer que vous disposez des autorisations suffisantes pour activer la version 12 pour votre abonnement. Lorsque vous tentez d’activer l’option de version 12, une vérification d’autorisation est effectuée, afin de garantir que votre abonnement dispose d’autorisations suffisantes.

 Pour essayer la version 12, vous devez disposer de l’une des autorisations suivantes :

- Propriétaire de l’abonnement
- Coadministrateur de l’abonnement

Pour en savoir plus, voir [Gestion des comptes, des abonnements et des rôles d’administrateur](http://msdn.microsoft.com/library/hh531793.aspx).

## B. Étapes de l’interface utilisateur du portail Microsoft Azure en version préliminaire

Cette section décrit une séquence de clics que vous pouvez effectuer une seule fois dans l’interface utilisateur du portail Microsoft Azure pour activer l’option de la version 12. L’option reste disponible après avoir été activée.

Tous les scénarios d’activation utilisent la même idée fondamentale. Lorsque vous essayez de [créer un serveur de base de données SQL](sql-database-create.md), un panneau intitulé **Dernière mise à jour (version préliminaire)** s’affiche. Il dispose d’une case à cocher que vous pouvez sélectionner afin d’activer votre privilège pour utiliser la version 12. Après l’activation du privilège, vous ne verrez plus jamais la case à cocher. Au lieu de cela, un contrôle **Oui|Non** apparaît et vous permet de spécifier si vous souhaitez que votre nouveau serveur utilise la version 12. Si vous choisissez **Non**, vous créez un serveur version 11 (comme indiqué par SELECT @@VERSION;).

### B.1 Contrôle Oui|Non pour la version 12

Une fois que vous avez activé le privilège de l’option version 12, le contrôle **Oui|Non** s’affiche (voir cercle dans la capture d’écran du portail Microsoft en version préliminaire, ci-après).

![YesNoOptionForTheV12Preview][Image1]


## C. Étapes suivantes

Ensuite, les rubriques suivantes expliquent comment utiliser la base de données SQL version 12.

- [Création d’une base de données dans la mise à jour V12 de la base de données SQL](sql-database-create.md)


<!-- References, Images. -->
[Image1]: ./media/sql-database-v12-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png

<!---HONumber=58--> 