<properties
	pageTitle="Création d’une machine virtuelle Oracle WebLogic Server 12c dans Azure" 
	description="Parcourez un exemple vous indiquant comment créer une machine virtuelle Oracle WebLogic Server 12c exécutant Windows Server 2012 dans Microsoft Azure." 
	services="virtual-machines" 
	authors="bbenz" 
	documentationCenter=""/>
<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="infrastructure-services" 
	ms.date="06/22/2015" 
	ms.author="bbenz" />
#Création d’une machine virtuelle Oracle WebLogic Server 12c dans Azure
L’exemple suivant vous indique comment créer une machine virtuelle sur la base d’une image Oracle WebLogic Server 12c fournie par Microsoft et exécutée sur Windows Server 2012 dans Azure.

##Créer une machine virtuelle Oracle WebLogic Server 12c dans Azure

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

2. Cliquez sur **Marketplace**, puis sur **Compute**, puis saisissez **Oracle** dans la zone de recherche.

3.	Sélectionnez l’image **Oracle WebLogic Server 12c Standard Edition sur Windows Server 2012** ou **Oracle WebLogic Server 12c Enterprise Edition sur Windows Server 2012**. Passez en revue les informations concernant cette image (par exemple, la taille minimale recommandée), puis cliquez sur **Suivant**.

4.	Spécifiez un **nom d’hôte** pour la machine virtuelle.

5.	Spécifiez un **nom d’utilisateur** pour la machine virtuelle. Remarquez que cet utilisateur convient pour la connexion à distance à la machine virtuelle ; il ne s’agit pas du nom d’utilisateur de la base de données Oracle.

6.	Spécifiez un mot de passe pour la machine virtuelle et confirmez-le, ou fournissez une clé publique SSH.

7.	Sélectionnez un **niveau de tarification**. Vous pouvez constater que les niveaux de tarification recommandés sont affichés par défaut ; pour afficher toutes les options de configuration, cliquez sur l’option **Afficher tout** qui figure dans l’angle supérieur droit de la fenêtre.

8.	Définissez l’élément [Configuration facultative](https://msdn.microsoft.com/library/azure/dn763935.aspx) si nécessaire, en tenant compte de ces remarques :
	1. Laissez le champ **Compte de stockage** tel quel pour créer un compte de stockage portant le nom de la machine virtuelle.
	2. Laissez le champ **Groupe à haute disponibilité** sur la valeur « Non configuré ».
	3. N’ajoutez aucun **point de terminaison** pour l’instant.

9.	Choisissez ou créez un [groupe de ressources](resource-group-portal.md).

10. Choisissez un **abonnement**.

11. Choisissez un **emplacement**.


##Configurer votre machine virtuelle Oracle WebLogic Server 12c dans Azure

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

2.	Cliquez sur **Machines virtuelles**.

3.	Cliquez sur le nom de la machine virtuelle à laquelle vous souhaitez vous connecter.

4.	Cliquez sur **Connecter**.

5.	Répondez aux invites afin de vous connecter à la machine virtuelle. Lorsque vous y êtes invité, saisissez le nom d’administrateur et le mot de passe fournis lors de la création de la machine virtuelle.

6.	Dans la boîte de dialogue **WebLogic Platform QuickStart**, cliquez sur **Getting Started with WebLogic Server**. (Si la boîte de dialogue **WebLogic Platform QuickStart** n’est pas ouverte, lancez-la en cliquant sur **Démarrage Windows**, en saisissant **Start Admin Server for WebLogic Server Domain**, puis en cliquant sur l’icône **Start Admin Server for WebLogic Server Domain**.)

7.	Dans la boîte de dialogue **Welcome**, sélectionnez **Create a new WebLogic domain**, puis cliquez sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image10.png)

8.	Dans la boîte de dialogue **Select Domain Source**, acceptez les valeurs par défaut, puis cliquez sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image11.png)

9.	Dans la boîte de dialogue **Specify Domain Name and Location**, acceptez les valeurs par défaut, puis cliquez sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image12.png)

10.	Dans la boîte de dialogue **Configure Administrator User Name and Password**, procédez comme suit :

	1.	[Facultatif] Remplacez le nom d’utilisateur **weblogic** par celui de votre choix.

	2.	Spécifiez un mot de passe pour l’administrateur WebLogic Server et confirmez-le.

	3.	Cliquez sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image13.png)

11.	Dans la boîte de dialogue **Configure Server Start Mode and JDK**, sélectionnez **Production Mode**, choisissez le JDK disponible (ou sélectionnez l’emplacement d’un JDK, le cas échéant) et cliquez sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image14.png)

12.	Dans la boîte de dialogue **Select Optional Configuration**, ne sélectionnez aucune option, puis cliquez sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image15.png)

13.	Dans la boîte de dialogue **Configuration Summary**, cliquez sur **Create**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image16.png)

14.	Dans la boîte de dialogue **Creating Domain**, cochez la case **Start Admin Server**, puis cliquez sur **Done**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image17.png)

15.	Une invite de commandes pour **startWebLogic.cmd** s’affiche. À l’invite, saisissez vos nom d’utilisateur et mot de passe WebLogic.

##Installer une application sur une machine virtuelle Oracle WebLogic Server 12c dans Azure
1.	Restez connecté à votre machine virtuelle et copiez en local l’exemple de fichier shoppingcart.war disponible à l’adresse http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war. Par exemple, créez un dossier nommé **c:\\mywar** et enregistrez le fichier WAR à l’emplacement http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war sur **c:\\mywar**.

2.	Ouvrez la **Console Administration de WebLogic Server**, http://localhost:7001/console. À l’invite, saisissez vos nom d’utilisateur et mot de passe WebLogic.

3.	Dans la **Console Administration de WebLogic Server**, cliquez sur **Lock & Edit**, sur **Deployments**, puis sur **Install**.

4.	Dans la zone **Path**, saisissez **c:\\mywar\\shoppingcart.war.**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image18.png)

	Cliquez sur **Next**.

5.	Sélectionnez \*\*Install this deployment as an application\*\*, puis cliquez sur **Next**.

6.	Cliquez sur **Finish**.

7.	Dans la **Console Administration de WebLogic Server**, cliquez sur **Save**, puis sur **Activate Changes**.

8.	Cliquez sur **Deployments**, choisissez **shoppingcart**, puis cliquez sur **Start** et sur **Service All Requests**. À l’invite de confirmation, cliquez sur **Yes**.

9.	Pour observer l’application shoppingcart s’exécuter en local, ouvrez un navigateur et accédez à l’adresse <http://localhost:7001/shoppingcart>.

10.	Créez un point de terminaison pour votre machine virtuelle :

	1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

	2.	Cliquez sur **Parcourir**.

	3.	Cliquez sur **Machines virtuelles**.

	4.	Sélectionnez la machine virtuelle.

	5.	Cliquez sur **Paramètres**.

	6.	Cliquez sur **Endpoints**.

	7.	Cliquez sur **Add**.

	8.	Saisissez le nom du point de terminaison.

		1. Spécifiez le protocole **TCP**.

		2. Spécifiez le port public **80**.

		3. Pour le port privé, indiquez **7001**.

	9.	Ne modifiez aucune des options restantes.

	10. Cliquez sur **OK**

11.	Autorisez une connexion entrante au port 7001 via le pare-feu.

	1.	Connectez-vous à votre machine virtuelle.

	2.	Cliquez sur **Démarrage Windows**, saisissez **Pare-feu Windows avec sécurité avancée**, puis cliquez sur l’icône **Pare-feu Windows avec sécurité avancée**. Cette opération ouvre la console de gestion **Pare-feu Windows avec sécurité avancée**.

	3.	Dans la console de gestion du pare-feu, cliquez sur **Règles de trafic entrant** dans le volet de gauche (si l’option **Règles de trafic entrant** ne s’affiche pas, développez le nœud supérieur dans le volet de gauche), puis cliquez sur Nouvelle règle dans le volet de droite.

	4.	Dans le champ **Type de règle**, sélectionnez **Port** et cliquez sur **Suivant**.

	5.	Dans le champ **Protocole et port**, choisissez **TCP**, sélectionnez **Ports locaux spécifiques**, attribuez au port la valeur **7001** et cliquez sur **Suivant**.

	6.	Sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

	7.	Acceptez les valeurs par défaut pour les profils concernés par la règle et cliquez sur **Suivant**.

	8.	Spécifiez un nom pour la règle, ainsi qu’une description, le cas échéant, puis cliquez sur **Terminer**.

12.	Pour voir l’application shoppingcart en cours d’exécution sur Internet, ouvrez un navigateur et accédez à l’URL, sous la forme `http://<<unique_domain_name>>/shoppingcart`. (Vous pouvez déterminer la valeur de l’élément <<\*unique\_domain\_name\*>> dans le [portail Azure](https://ms.portal.azure.com/), en cliquant sur **Machines virtuelles**, puis en sélectionnant la machine virtuelle que vous utilisez pour exécuter Oracle WebLogic Server).


##Ressources supplémentaires
Maintenant que vous avez configuré votre machine virtuelle exécutant Oracle WebLogic Server, consultez les rubriques suivantes pour en savoir plus.

-	[Images de machine virtuelle Oracle – Considérations diverses](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Oracle WebLogic Server Product Documentation](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-	[Oracle WebLogic Server 12c using Linux on Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-	[Liste des images de machine virtuelle Oracle](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=July15_HO5-->