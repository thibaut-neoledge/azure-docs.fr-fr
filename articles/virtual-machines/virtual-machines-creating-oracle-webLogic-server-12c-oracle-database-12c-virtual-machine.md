<properties title="Creating an Oracle WebLogic Server 12c and Oracle Database 12c Virtual Machine in Azure" pageTitle="Création d’une machine virtuelle Oracle WebLogic Server 12c et Oracle Database 12c dans Azure" description="Parcourez un exemple vous indiquant comment créer une image Oracle WebLogic Server 12c et Oracle Database 12c exécutée sur Windows Server 2012 dans Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Création d’une machine virtuelle Oracle WebLogic Server 12c et Oracle Database 12c dans Azure
L’exemple suivant vous indique comment créer une machine virtuelle sur la base d’une image Oracle WebLogic Server 12c et Oracle Database 12c fournie par Microsoft et exécutée sur Windows Server 2012 dans Azure.

##Créer une machine virtuelle Oracle WebLogic Server 12c et Oracle Database 12c dans Azure

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

2.	Cliquez sur **Marketplace**, puis sur **Compute**, puis saisissez **Oracle** dans la zone de recherche.

3.	Sélectionnez l’image **Oracle Database 12c et WebLogic Server 12c Standard Edition sur Windows Server 2012** ou **Oracle Database 12c et WebLogic Server 12c Enterprise Edition sur Windows Server 2012**. Passez en revue les informations concernant cette image (par exemple, la taille minimale recommandée), puis cliquez sur **Suivant**.

4.	Spécifiez un **nom d’hôte** pour la machine virtuelle.

5.	Spécifiez un **nom d’utilisateur** pour la machine virtuelle. Remarquez que cet utilisateur convient pour la connexion à distance à la machine virtuelle ; il ne s’agit pas du nom d’utilisateur de la base de données Oracle.

6.	Spécifiez un mot de passe pour la machine virtuelle et confirmez-le, ou fournissez une clé publique SSH.

7.	Sélectionnez un **niveau de tarification**. Vous pouvez constater que les niveaux de tarification recommandés sont affichés par défaut ; pour afficher toutes les options de configuration, cliquez sur l’option Afficher tout qui figure dans l’angle supérieur droit de la fenêtre.

8. Définissez l’élément Configuration facultative si nécessaire, en tenant compte de ces remarques :

	1. Laissez le champ **Compte de stockage** tel quel pour créer un compte de stockage portant le nom de la machine virtuelle.

	2. Laissez le champ **Groupe à haute disponibilité** sur la valeur « Non configuré ».

	3. N’ajoutez aucun **point de terminaison** pour l’instant.

9.	Choisissez ou créez un [groupe de ressources](resource-group-portal.md).

10. Choisissez un **abonnement**.

11. Choisissez un **emplacement**.


##Créer votre base de données hébergée sur cette machine virtuelle
Suivez les instructions de la section [Création d’une machine virtuelle Oracle Database 12c dans Azure](virtual-machines-creating-oracle-database-virtual-machine.md), en commençant par la section **Créer votre base de données via la machine virtuelle Oracle Database 12c dans Azure**.

##Configurer votre système Oracle WebLogic Server 12c hébergé sur cette machine virtuelle
Suivez les instructions de la section [Création d’une machine virtuelle Oracle WebLogic Server 12c dans Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md), en commençant par la section **Configurer votre machine virtuelle Oracle WebLogic Server 12c dans Azure**. Si vous souhaitez configurer un cluster WebLogic Server, consultez également la section [Création d’un cluster Oracle WebLogic Server 12c dans Azure](virtual-machines-creating-oracle-webLogic-server-12c-cluster.md).

##Ressources supplémentaires
[Images de machine virtuelle Oracle – Considérations diverses](miscellaneous-considerations-for-oracle-virtual-machine-images-new-article.md)

[Liste des images de machine virtuelle Oracle](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

[Connecting to Oracle Database from a Java Application](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Oracle WebLogic Server 12c using Linux on Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=July15_HO2-->