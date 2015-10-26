<properties
	pageTitle="Machine virtuelle Oracle WebLogic Server et Oracle Database | Microsoft Azure"
	description="Créez une image Oracle WebLogic Server 12c et Oracle Database 12c s’exécutant sur Windows Server 2012, à l’aide du modèle de déploiement Resource Manager."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Création d’une machine virtuelle Oracle WebLogic Server 12c et Oracle Database 12c dans Azure

Cet article indique comment créer une machine virtuelle basée sur une image Oracle WebLogic Server 12c et Oracle Database 12c fournie par Microsoft et exécutée sur Windows Server 2012 dans Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


##Pour créer une machine virtuelle Oracle WebLogic Server 12c et Oracle Database 12c dans Azure

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

2.	Cliquez successivement sur **Marketplace**, sur **Calculer**, puis tapez **Oracle** dans la zone de recherche.

3.	Sélectionnez l’image **Oracle Database 12c et WebLogic Server 12c Standard Edition sur Windows Server 2012** ou **Oracle Database 12c et WebLogic Server 12c Enterprise Edition sur Windows Server 2012**. Passez en revue les informations concernant cette image (par exemple, la taille minimale recommandée), puis cliquez sur **Suivant**.

4.	Spécifiez un **nom d’hôte** pour la machine virtuelle.

5.	Spécifiez un **nom d’utilisateur** pour la machine virtuelle. Notez que ce nom d’utilisateur vise à se connecter à distance à la machine virtuelle ; il ne s’agit pas du nom d’utilisateur de la base de données Oracle.

6.	Spécifiez un mot de passe pour la machine virtuelle et confirmez-le, ou fournissez une clé publique SSH (Secure Shell).

7.	Sélectionnez un **niveau tarifaire**. Notez que les niveaux de tarification recommandés sont affichés par défaut. Pour afficher toutes les options de configuration, cliquez sur **Afficher tout** dans le coin supérieur droit.

8. Définissez les configurations facultatives selon vos besoins. Tenez compte des remarques suivantes :

	a. Laissez le champ **Compte de stockage** tel quel pour créer un compte de stockage portant le nom de la machine virtuelle.

	b. Laissez le champ **Groupe à haute disponibilité** sur la valeur **Non configuré**.

	c. N’ajoutez aucun point de terminaison pour l’instant.

9.	Choisissez ou créez un groupe de ressources. Pour plus d’informations, consultez la page [Utilisation du portail Azure en version préliminaire pour gérer vos ressources Azure](resource-group-portal.md).

10. Choisissez un **Abonnement**.

11. Choisissez un **Emplacement**.


##Pour créer votre base de données hébergée sur cette machine virtuelle

Suivez les instructions dans la page [Création d’une machine virtuelle Oracle Database 12c dans Azure](virtual-machines-creating-oracle-database-virtual-machine.md), en commençant par la section **Pour créer votre base de données à l’aide de la machine virtuelle Oracle Database 12c dans Azure**.

##Pour configurer votre système Oracle WebLogic Server 12c hébergé dans cette machine virtuelle
Suivez les instructions dans la page [Création d’une machine virtuelle Oracle WebLogic Server 12c dans Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md), en commençant par la section **Pour configurer votre machine virtuelle Oracle WebLogic Server 12c dans Azure**. Si vous souhaitez configurer un cluster WebLogic Server, consultez aussi la section [Création d’un cluster Oracle WebLogic Server 12c dans Azure](virtual-machines-creating-oracle-webLogic-server-12c-cluster.md).

##Ressources supplémentaires
[Remarques diverses concernant les images de machines virtuelles Oracle](miscellaneous-considerations-for-oracle-virtual-machine-images-new-article.md)

[Liste d’images de machines virtuelles Oracle](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

[Connexion à une base de données Oracle à partir d’une application Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Oracle WebLogic Server 12c en utilisant Linux sur Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=Oct15_HO3-->