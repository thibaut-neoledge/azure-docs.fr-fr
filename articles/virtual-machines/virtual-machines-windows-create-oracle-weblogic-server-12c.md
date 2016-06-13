<properties
	pageTitle="Création d'une machine virtuelle Oracle WebLogic Server 12c | Microsoft Azure"
	description="Créez une machine virtuelle Oracle WebLogic Server 12c exécutant Windows Server 2012 dans Microsoft Azure à l'aide du modèle de déploiement Resource Manager."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rclaus" />

#Création d’une machine virtuelle Oracle WebLogic Server 12c dans Azure

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

L’exemple suivant vous indique comment créer une instance de WebLogic Server 12c exécutée sur une machine virtuelle que vous avez précédent créée et installée avec Oracle WebLogic 12c exécuté sous Windows Server 2012 dans Azure.

##Configurer votre machine virtuelle Oracle WebLogic Server 12c dans Azure

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

2.	Cliquez sur **Machines virtuelles**.

3.	Cliquez sur le nom de la machine virtuelle à laquelle vous souhaitez vous connecter.

4.	Cliquez sur **Connecter**.

5.	Répondez aux invites afin de vous connecter à la machine virtuelle. Lorsque vous y êtes invité, saisissez le nom d’administrateur et le mot de passe fournis lors de la création de la machine virtuelle.

6.	Dans la boîte de dialogue **WebLogic Platform QuickStart**, cliquez sur **Getting Started with WebLogic Server**. (Si la boîte de dialogue **WebLogic Platform QuickStart** n'est pas ouverte, lancez-la en cliquant sur le **menu Démarrer de Windows**, en tapant **Start Admin Server for WebLogic Server Domain**, puis en cliquant sur l'icône **Start Admin Server for WebLogic Server Domain**.)

7.	Dans la boîte de dialogue **Welcome**, sélectionnez **Create a new WebLogic domain**, puis cliquez sur **Next**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image10.png)

8.	Dans la boîte de dialogue **Select Domain Source**, acceptez les valeurs par défaut, puis cliquez sur **Next**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image11.png)

9.	Dans la boîte de dialogue **Specify Domain Name and Location**, acceptez les valeurs par défaut, puis cliquez sur **Next**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image12.png)

10.	Dans la boîte de dialogue **Configure Administrator User Name and Password** :

	1.	[Facultatif] Remplacez le nom d'utilisateur **weblogic** par celui de votre choix.

	2.	Spécifiez un mot de passe pour l’administrateur WebLogic Server et confirmez-le.

	3.	Cliquez sur **Next**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image13.png)

11.	Dans la boîte de dialogue **Configure Server Start Mode and JDK**, sélectionnez **Production Mode**, choisissez le JDK disponible (ou sélectionnez l'emplacement d'un JDK, le cas échéant) et cliquez sur **Next**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image14.png)

12.	Dans la boîte de dialogue **Select Optional Configuration**, ne sélectionnez aucune option, puis cliquez sur **Next**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image15.png)

13.	Dans la boîte de dialogue **Configuration Summary**, cliquez sur **Create**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image16.png)

14.	Dans la boîte de dialogue **Creating Domain**, cochez la case **Start Admin Server**, puis cliquez sur **Done**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image17.png)

15.	Une invite de commandes pour **startWebLogic.cmd** s'affiche. À l’invite, saisissez vos nom d’utilisateur et mot de passe WebLogic.

##Installer une application sur une machine virtuelle Oracle WebLogic Server 12c dans Azure
1.	Restez connecté à votre machine virtuelle et copiez en local l'exemple de fichier shoppingcart.war disponible à l'adresse http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war. Par exemple, créez un dossier nommé **c:\\mywar** et enregistrez le fichier WAR à l'emplacement http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war sur **c:\\mywar**.

2.	Ouvrez la **Console Administration de WebLogic Server**, http://localhost:7001/console. À l’invite, saisissez vos nom d’utilisateur et mot de passe WebLogic.

3.	Dans la console **WebLogic Server Administration Console**, cliquez sur **Lock & Edit**, sur **Deployments**, puis sur **Install**.

4.	Dans la zone **Path**, tapez **c:\\mywar\\shoppingcart.war.**.

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image18.png)

	Cliquez sur **Next**.

5.	Sélectionnez **Install this deployment as an application**, puis cliquez sur **Suivant**.

6.	Cliquez sur **Terminer**.

7.	Dans la **Console Administration de WebLogic Server**, cliquez sur **Save**, puis sur **Activate Changes**.

8.	Cliquez sur **Deployments**, choisissez **shoppingcart**, cliquez sur **Start** et sur **Service All Requests**. À l’invite de confirmation, cliquez sur **Yes**.

9.	Pour observer l’exécution de l’application shoppingcart en local, ouvrez un navigateur et accédez à l’adresse <http://localhost:7001/shoppingcart>.

10.	Créez un point de terminaison pour votre machine virtuelle :

	1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

	2.	Cliquez sur **Parcourir**.

	3.	Cliquez sur **Machines virtuelles**.

	4.	Sélectionnez la machine virtuelle.

	5.	Cliquez sur **Paramètres**

	6.	Cliquez sur **Endpoints**.

	7.	Cliquez sur **Add**.

	8.	Saisissez le nom du point de terminaison.

		1. Indiquez **TCP** pour le protocole.

		2. Spécifiez le port public **80**.

		3. Spécifiez le port privé **7001**.

	9.	Ne modifiez aucune des options restantes.

	10. Cliquez sur **OK**

11.	Autorisez une connexion entrante au port 7001 via le pare-feu.

	1.	Connectez-vous à votre machine virtuelle.

	2.	Cliquez sur le **menu Démarrer de Windows**, tapez **Pare-feu Windows avec sécurité avancée**, puis cliquez sur l'icône **Pare-feu Windows avec sécurité avancée**. Cette opération ouvre la console de gestion **Pare-feu Windows avec fonctions avancées de sécurité**.

	3.	Dans la console de gestion du pare-feu, cliquez sur **Règles de trafic entrant** dans le volet de gauche (si l'option **Règles de trafic entrant** ne s'affiche pas, développez le nœud supérieur dans le volet de gauche), puis cliquez sur Nouvelle règle dans le volet de droite.

	4.	Dans le champ **Type de règle**, sélectionnez **Port** et cliquez sur **Suivant**.

	5.	Dans le champ **Protocole et port**, choisissez **TCP**, sélectionnez **Ports locaux spécifiques**, attribuez au port la valeur **7001**, puis cliquez sur **Suivant**.

	6.	Sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

	7.	Acceptez les valeurs par défaut pour les profils concernés par la règle et cliquez sur **Suivant**.

	8.	Spécifiez un nom pour la règle, ainsi qu’une description, le cas échéant, puis cliquez sur **Terminer**.

12.	Pour observer l'exécution de l'application shoppingcart sur Internet, ouvrez un navigateur et accédez à l'URL, sous la forme `http://<<unique_domain_name>>/shoppingcart`. (Vous pouvez déterminer la valeur de l’élément <<*unique\_domain\_name*>> dans le [portail Azure](https://ms.portal.azure.com/), en cliquant sur **Machines virtuelles**, puis en sélectionnant la machine virtuelle que vous utilisez pour exécuter Oracle WebLogic Server).


##Ressources supplémentaires
Maintenant que vous avez configuré votre machine virtuelle exécutant Oracle WebLogic Server, consultez les rubriques suivantes pour en savoir plus.

-	[Images de machine virtuelle Oracle – Considérations diverses](virtual-machines-windows-classic-oracle-considerations.md)

-	[Oracle WebLogic Server Product Documentation](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-	[Oracle WebLogic Server 12c using Linux on Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-	[Liste des images de machine virtuelle Oracle](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0601_2016-->