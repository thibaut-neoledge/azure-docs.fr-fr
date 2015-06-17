<properties 
	pageTitle="Procédure pas à pas : Activation de la dernière mise à jour V12 de la base de données SQL (version préliminaire)" 
	description="Décrit les étapes nécessaire afin d'essayer la version préliminaire de la base de données SQL Azure V12, à l'aide de la nouvelle interface utilisateur du portail Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard, jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="genemi"/>


# Procédure pas à pas : Activation de la dernière mise à jour V12 de la base de données SQL (version préliminaire)

Cette rubrique décrit la procédure à suivre pour activer à la mise à jour V12 de la base de données SQL Azure (version préliminaire), publiée pour la première fois par Microsoft en décembre 2014.

Pour essayer la dernière version préliminaire V12, vous devez disposer d'un abonnement Microsoft Azure, ou au moins d'un abonnement à la [version d'évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).

Vous pouvez activer la version préliminaire V12 en utilisant la nouvelle version préliminaire du portail de gestion Microsoft Azure, à la page [http://portal.azure.com/](http://portal.azure.com/). Après avoir activé la version préliminaire V12 pour votre abonnement, les options de création et de mise à niveau pour la version préliminaire V12 sont déverrouillées dans le portail Azure. Les utilisateurs peuvent alors lancer le workflow de [création](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) ou de [mise à niveau](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) depuis le volet du serveur ou le volet de la base de données.

> [AZURE.NOTE]
> Les bases de données de test, les copies de base de données ou les nouvelles bases de données sont parfaitement adaptées à la mise à jour vers la version préliminaire. Il est préférable d'attendre la fin de la version préliminaire avant de mettre à jour les bases de données dont dépend votre activité.

Pour plus d'informations sur la version préliminaire, consultez [Planification et préparation de la mise à jour vers la version préliminaire V12 de la base de données Azure SQL](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


## A. Autorisation de sécurité

La première étape consiste à vous assurer que vous disposez des autorisations suffisantes pour activer la version d'évaluation V12 pour votre abonnement. Lorsque vous essayez d'activer l'option de version préliminaire V12, une vérification d'autorisation est effectuée pour vérifier que vous disposez d'autorisations suffisantes dans l'abonnement.

 Pour essayer la version d'évaluation, vous devez disposer d'une des autorisations suivantes :

- Propriétaire de l'abonnement
- Coadministrateur de l'abonnement

Pour plus d'informations sur les comptes Azure, consultez [Gérer des comptes, des abonnements et des rôles d'administrateur](http://msdn.microsoft.com/library/hh531793.aspx).

## B. Étapes dans l'interface utilisateur du portail

Cette section décrit une séquence de clics que vous pouvez effectuer dans l'interface utilisateur du portail Azure pour activer la version préliminaire V12. L'option reste disponible après avoir été activée.

Tous les scénarios d'activation utilisent la même idée fondamentale. Lorsque vous essayez de [créer un nouveau serveur de base de données SQL](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/), un volet intitulé **Dernière mise à jour (version préliminaire)** s'affiche. Il dispose d'une case à cocher que vous pouvez sélectionner afin d'activer votre privilège pour utiliser la version préliminaire V12. Après l'activation du privilège, vous ne verrez plus jamais la case à cocher. Au lieu de cela, vous voyez un contrôle **Oui|Non** qui vous permet de spécifier si vous souhaitez que votre nouveau serveur utilise la version préliminaire V12. Si vous choisissez **Non**, vous créez un serveur V11 (comme indiqué par @@VERSION;).

### B.1 Oui|Aucun contrôle de la version d'évaluation V12

Après avoir activé le privilège de version préliminaire V12, vous verrez le contrôle **Oui|Non**, entouré dans la capture d'écran du portail suivante.

![YesNoOptionForTheV12Preview][Image1]


## C. Étapes suivantes

Puis, les rubriques suivantes expliquent comment utiliser la version préliminaire V12.

- [Création d'une base de données dans la dernière mise à jour V12 de la base de données SQL (version préliminaire)](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)
- [Installation de la dernière mise à jour V12 de la base de données SQL (version préliminaire)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/)

> [AZURE.NOTE]
> Les bases de données de test, les copies de base de données ou les nouvelles bases de données sont parfaitement adaptées à la mise à jour vers la version préliminaire. Il est préférable d'attendre la fin de la version préliminaire avant de mettre à jour les bases de données dont dépend votre activité.


<!-- References, Images. -->
[Image1]: ./media/sql-database-preview-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png


<!-- EOF -->

<!--HONumber=47-->
 