<properties title="Creating an Oracle WebLogic Server 12c cluster in Azure" pageTitle="Création d’un cluster Oracle WebLogic Server 12c dans Microsoft Azure" description="Découvrez un exemple de création de cluster Oracle WebLogic Server 12c dans Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Création d’un cluster Oracle WebLogic Server 12c dans Microsoft Azure
L’exemple suivant indique de quelle manière créer un cluster Oracle WebLogic Server 12c dans Microsoft Azure, sur la base d’une image Oracle WebLogic Server 12c fournie par Microsoft et exécutée sur un système Windows Server 2012.

Chaque instance d’un cluster Oracle WebLogic Server doit exécuter la même version de ce logiciel. Cet exemple utilise WebLogic Server 12c Enterprise Edition.

##Créer des machines virtuelles à utiliser dans le cluster
Vous allez créer une machine virtuelle à utiliser en tant que serveur d’administration du cluster, ainsi que des machines virtuelles supplémentaires, à utiliser en tant que composants du cluster.

### Créer une machine virtuelle à utiliser en tant que serveur d’administration

Créez une machine virtuelle à l’aide de l’image **Oracle WebLogic Server 12c Enterprise Edition sur Windows Server 2012**, disponible dans Microsoft Azure. Vous trouverez une description des étapes de création de cette machine virtuelle dans la rubrique [Création d’une machine virtuelle Oracle WebLogic Server 12c dans Azure](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article). Pour les besoins de ce didacticiel, donnez à la machine virtuelle le nom suivant : **MYVM1-ADMIN**. Notez le nom de réseau virtuel de la machine virtuelle, que vous utiliserez lorsque vous créerez les machines virtuelles supplémentaires à ajouter au cluster. (Vous pouvez choisir les noms de machine virtuelle et de réseau virtuel de votre choix, tant qu’ils sont uniques dans Microsoft Azure.)

### Créer des machines virtuelles gérées à utiliser dans le cluster

Créez des machines virtuelles supplémentaires, qui seront gérées par le serveur d’administration. Pour cela, utilisez l’image Oracle WebLogic Server 12c disponible dans Microsoft Azure. Pour les besoins de ce didacticiel, donnez à ces machines supplémentaires les noms suivants : **MYVM2-MANAGED** et **MYVM3-MANAGED**. Vous trouverez une description des étapes de création de ces machines virtuelles dans la rubrique [Création d’une machine virtuelle Oracle WebLogic Server 12c dans Microsoft Azure](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article), *à l’exception* de la modification suivante, qui est requise :

-  Lorsque vous créez les machines virtuelles, ne créez pas de réseau virtuel. En particulier, dans la boîte de dialogue **Configuration facultative > Réseau virtuel**, ne sélectionnez pas la valeur par défaut, à savoir **Créer un réseau virtuel**, mais choisissez le réseau virtuel créé pour la machine virtuelle utilisée en tant que serveur d’administration. Par exemple, si vous avez généré un réseau virtuel appelé **EXAMPLE** lors de la création de votre serveur d’administration, sélectionnez **EXAMPLE** lorsque vous créez les machines virtuelles du cluster géré.

##Créer un domaine

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

2. Cliquez sur **Machines virtuelles**.

3. Cliquez sur le nom de la machine virtuelle que vous avez créée afin qu’elle joue le rôle de serveur d’administration du cluster (par exemple : **MYVM1-ADMIN**).

4. Cliquez sur **Connecter**.

5. Répondez aux invites afin de vous connecter à la machine virtuelle. Lorsque vous y êtes invité, saisissez le nom d’administrateur et le mot de passe fournis lors de la création de la machine virtuelle.

6. Dans la **page 1** de la boîte de dialogue **Fusion Middleware Configuration Wizard**, cliquez sur **Create a new domain**, puis sur **Next**. (Si la boîte de dialogue **Fusion Middleware Configuration Wizard** n’est pas ouverte, lancez-la en cliquant sur **Démarrage Windows**, en saisissant **Assistant Configuration**, plus en cliquant sur l’icône **Assistant Configuration**.)

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image19.png)

7. Dans la **page 2** de la boîte de dialogue **Fusion Middleware Configuration Wizard**, sélectionnez le modèle **Basic WebLogic Server Domain**, puis cliquez sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image20.png)

8. Dans la **page 3** de la boîte de dialogue **Fusion Middleware Configuration Wizard**, procédez comme suit :

	1. [Facultatif] Remplacez le nom d’utilisateur **weblogic** par celui de votre choix.
	
	2. Spécifiez un mot de passe pour l’administrateur WebLogic Server et confirmez-le.
	
	3. Cliquez sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image21.png)

9. Dans la **page 4** de la boîte de dialogue **Fusion Middleware Configuration Wizard**, sélectionnez le mode de domaine **Production**, choisissez le JDK disponible (ou accédez au JDK de votre choix, le cas échéant), puis cliquez sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image22.png)

10.  Dans la **page 5** de la boîte de dialogue **Fusion Middleware Configuration Wizard**, ne sélectionnez aucune option et cliquez sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image23.png)

11.  Dans la **page 6** de la boîte de dialogue **Fusion Middleware Configuration Wizard**, cliquez sur **Create**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image24.png)

12.  Dans la **page 7** de la boîte de dialogue **Fusion Middleware Configuration Wizard**, cliquez sur **Next** une fois le domaine créé.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image25.png)

13.  Dans la **page 8** de la boîte de dialogue **Fusion Middleware Configuration Wizard**, cliquez sur Start Admin Server, puis sur **Next**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image26.png)

14.  Une invite de commandes pour **startWebLogic.cmd** s’affiche. À l’invite, saisissez vos nom d’utilisateur et mot de passe WebLogic.

##Configurer le cluster

1. Toujours connecté à la machine virtuelle d’administration, exécutez la **Console Administration de WebLogic Server**, <http://localhost:7001/console>. À l’invite, saisissez vos nom d’utilisateur et mot de passe WebLogic Server.

2. Dans la **Console Administration de WebLogic Server**, cliquez sur **Lock & Edit**.

3. Dans le volet **Domain Structure**, développez la zone **Environment** et cliquez sur **Clusters**.

4. Dans la boîte de dialogue **Summary of Clusters**, cliquez sur **New**, puis sur **Cluster**.

5. Dans la boîte de dialogue **Clusters Property**, procédez comme suit :

	1. Saisissez le nom du cluster.

	2. Choisissez la valeur **Unicast** dans le champ **Messaging Mode**.

		![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image001.png)

	
	3. Cliquez sur **OK**.

6. Dans le volet **Domain Structure**, développez la zone **Environment** et cliquez sur **Servers**.

7. Ajouter votre premier serveur géré au cluster.

	1. Cliquez sur **New**.

	2. Dans la boîte de dialogue **Create a New Server**, procédez comme suit :

		1. Dans le champ **Server Name**, saisissez le nom de votre premier serveur géré. Par exemple :**MYVM2-MANAGED.**

		2. Dans le champ **Server Listen Address**, saisissez à nouveau le nom.

		3. Dans le champ **Listen Port**, indiquez **7008**.

		4. Cliquez sur **Yes, make this server a member of an existing cluster.**

		5. Dans la liste déroulante **Select a cluster**, sélectionnez le cluster créé précédemment.

			34d27e82-bb2e-4f9c-aaad-ca3e28c0f5fc

		6. Cliquez sur **Next**.

		7. Cliquez sur **Finish**.

8. Ajoutez votre deuxième serveur géré au cluster, en suivant la procédure décrite ci-dessus. Dans les champs **Server Name** et **Server Listen Address**, utilisez le nom du deuxième ordinateur géré. Dans le champ **Listen Port**, indiquez **7008.**

9. Dans la Console Administration de WebLogic Server, cliquez sur **Activate Changes.**

10. Sur la machine virtuelle d’administration, créez une variable d’environnement appelée **SERVER\_HOME** et définissez sa valeur sur **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver.** Vous pouvez créer cette variable d’environnement en procédant comme suit :

	1. Cliquez sur **Démarrage Windows**, saisissez **Panneau de configuration**, cliquez sur l’icône **Panneau de configuration**, et sélectionnez ** Système et sécurité**, puis **Système,** et enfin cliquez sur **Paramètres système avancés.**

	2. Sélectionnez l’onglet **Avancé**, puis cliquez sur **Variables d’environnement.**

	3. Sous la section **Variables système**, cliquez sur **Nouveau** afin de créer la variable.

	4. Dans la boîte de dialogue **Nouvelle variable système**, saisissez le nom de variable **SERVER\_HOME**, puis choisissez la valeur **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver**.

	5. Cliquez sur **OK** pour enregistrer la nouvelle variable d’environnement et fermer la boîte de dialogue **Nouvelle variable système**.

	6. Fermez les autres boîtes de dialogue ouvertes par le panneau de configuration.

11. Ouvrez une nouvelle invite de commande (afin que la variable d’environnement **SERVER\_HOME** soit appliquée).

	>[AZURE.NOTE]Certaines des étapes restantes requièrent l’utilisation d’une invite de commandes une fois que vous êtes connecté à vos machines virtuelles. Afin de déterminer plus facilement à quelle machine vous êtes connecté, ouvrez l’invite de commandes et exécutez la commande **title %COMPUTERNAME%.**
	>
	>**(La valeur %COMPUTERNAME%** est une variable d’environnement définie par le système, qui est automatiquement définie sur le nom de l’ordinateur. L’exécution de la commande **title** **%COMPUTERNAME%** entraîne l’affichage du nom de l’ordinateur dans la barre de titre de l’invite de commandes).

12. Exécutez la commande suivante :

		%SERVER\_HOME%\\common\\bin\\pack.cmd -managed=true -domain=C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain -template=c:\\mytestdomain.jar -template\_name="mytestdomain" 

	Cette commande permet de créer un fichier .jar appelé **c:\\mytestdomain.jar.** Vous le copierez plus tard dans les machines virtuelles gérées dans votre cluster.

13. Autorisez une connexion entrante au port 7001 via le pare-feu.

	1. Tout en restant connecté à votre machine virtuelle, cliquez sur **Démarrage Windows**, saisissez **Pare-feu Windows avec sécurité avancée**, puis cliquez sur l’icône **Pare-feu Windows avec sécurité avancée**. Cette opération ouvre la console de gestion **Pare-feu Windows avec sécurité avancée**.

	2. Dans la console de gestion du pare-feu, cliquez sur **Règles de trafic entrant** dans le volet de gauche (si l’option **Règles de trafic entrant** ne s’affiche pas, développez le nœud supérieur dans le volet de gauche), puis cliquez sur **Nouvelle règle** dans le volet de droite.

	3. Dans le champ **Type de règle**, sélectionnez **Port** et cliquez sur **Suivant.**

	4. Dans le champ **Protocole et port**, choisissez **TCP**, sélectionnez **Ports locaux spécifiques**, attribuez au port la valeur **7001** et cliquez sur **Suivant.**

	5. Sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

	6. Acceptez les valeurs par défaut pour les profils concernés par la règle et cliquez sur **Suivant.**

	7. Spécifiez un nom pour la règle, ainsi qu’une description, le cas échéant, puis cliquez sur **Terminer.**

14. Pour chacune des machines virtuelles gérées, procédez comme suit :

	1. Connectez-vous à la machine virtuelle.

	2. Créez une variable d’environnement appelée **SERVER\_HOME** et définissez sa valeur sur **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver.**

	3. Copiez le fichier c:\\mytestdomain.jar à partir de la machine virtuelle d’administration sur le fichier c:\\mytestdomain.jar, sur la machine virtuelle gérée.

	4. Ouvrez une invite de commandes, sans oublier d’exécuter la commande **title %COMPUTERNAME%** à l’invite de commande, afin de déterminer précisément à quel ordinateur vous accédez.

	5. Exécutez la commande suivante :

			%SERVER\_HOME%\\common\\bin\\unpack.cmd -domain=C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain -template=c:\\mytestdomain.jar

	6. Remplacez le répertoire actuel de l’invite de commandes par le répertoire **C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain\\bin.**

	7. Exécutez la commande suivante : start<<*NOM\_MACHINE*>>.cmd, la valeur <<*NOM\_MACHINE*>> correspondant au nom de l’ordinateur géré. Exemple : **startMYVM2-MANAGED**.

	8. À l’invite, saisissez les nom d’utilisateur et mot de passe WebLogic Server.

	9. Autorisez une connexion entrante au port 7008 via le pare-feu. (Suivez les étapes permettant d’ouvrir le port 7001 sur le serveur d’administration, en utilisant la valeur 7008 pour les serveurs gérés.)

15. Sur la machine virtuelle d’administration, ouvrez la **Console Administration de WebLogic Server**, <http://localhost:7001/console>, et affichez les serveurs en cours d’exécution.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image003.png)

16. Créez un point de terminaison avec équilibrage de charge défini pour vos machines virtuelles gérées :

	1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à la section **Machines virtuelles**, choisissez la première machine virtuelle gérée (par exemple, **MYVM2-MANAGED**).

	2. Cliquez sur **Paramètres**, sur **Points de terminaison**, puis sur **Ajouter.**

	3. Indiquez un nom pour le point de terminaison, spécifiez le protocole **TCP**, ainsi que le port public **80** et le port privé **7008.** Ne modifiez aucune des options restantes.

	4. Sélectionnez **Créer un jeu d’équilibrage de la charge**, puis cliquez sur **Terminer.**

	5. Spécifiez un nom pour le jeu d’équilibrage de la charge, acceptez les valeurs par défaut des autres paramètres, puis cliquez sur **Terminer.**

17. Créez un point de terminaison pour votre machine virtuelle :

	1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).

	2. Cliquez sur **Parcourir**.

	3. Cliquez sur **Machines virtuelles**.

	4. Sélectionnez la machine virtuelle.

	5. Cliquez sur **Paramètres**.

	6. Cliquez sur **Jeux d’équilibrage de la charge**.

	7. Cliquez sur **Joindre**.

	8. Définissez le type de jeu d’équilibrage de la charge sur **Interne**.

	9. Saisissez le nom du point de terminaison.

		1. Indiquez **TCP** pour le protocole.

		2. Indiquez **80** pour le port public.

		3. Pour le port de sonde, utilisez la valeur **7008**.

	10. Ne modifiez aucune des options restantes.

	11. Cliquez sur **OK**

	12. Attendez que la machine virtuelle ait rejoint le jeu d’équilibrage de charge avant de passer à l’étape suivante.

18. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à la section **Machines virtuelles**, choisissez la deuxième machine virtuelle gérée (par exemple, **MYVM3-MANAGED**). Suivez les étapes ci-dessus pour rejoindre le jeu d’équilibrage de la charge que vous avez créé pour la première machine virtuelle.

##Déploiement d’une application dans le cluster

À ce stade, vous pouvez déployer votre application à l’aide de la procédure suivante. Partons du principe que vous déployez l’application Oracle shoppingcart, que vous pouvez charger à l’emplacement suivant : <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war>.

1. Connectez-vous à votre machine virtuelle, qui joue le rôle d’administrateur pour le cluster WebLogic Server (par exemple, **MYVM1-ADMIN**). 

2. Copiez l’élément shoppingcart.war en local. Par exemple, créez un dossier nommé **c:\\mywar**, puis enregistrez le fichier WAR à l’emplacement <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war> sur **c:\\mywar**.

3. Ouvrez la **Console Administration de WebLogic Server**, <http://localhost:7001/console>. À l’invite, saisissez vos nom d’utilisateur et mot de passe WebLogic.

4. Dans la console **WebLogic Server Administration Console**, cliquez sur **Lock & Edit**, sur **Deployments**, puis sur **Install**.

5. Dans la zone **Path**, saisissez **c:\\myway\\shoppingcart.war**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image004.png)

	Cliquez sur **Next**.

6. Sélectionnez **Install this deployment as an application**, puis cliquez sur **Next**.

7. Cliquez sur **Terminer**.

8. Dans **Available Targets**, sélectionnez le cluster créé précédemment et vérifiez que l’option **All servers in the cluster** est sélectionnée, puis cliquez sur **Next**.

9. Sous **Source Accessibility**, sélectionnez **Copy this application onto every target for me** et cliquez sur **Finish**.

10.  Dans la **Console Administration de WebLogic Server**, cliquez sur **Save**, puis sur **Activate Changes**.

11.  Cliquez sur **Deployments**, choisissez **shoppingcart**, puis cliquez sur **Start** et sur **Service All Requests**. À l’invite de confirmation, cliquez sur **Yes**.

12.  Pour observer l’application shoppingcart s’exécuter sur Internet, ouvrez un navigateur et accédez à l’URL, sous la forme `http://<<unique_domain_name>>/shoppingcart`. (Vous pouvez déterminer la valeur de `<<unique_domain_name>>` dans le [portail Azure](https://ms.portal.azure.com/), en cliquant sur Machines Virtuelles, puis en sélectionnant la machine virtuelle que vous utilisez pour exécuter Oracle WebLogic Server).

## Étapes suivantes

Pour vérifier que votre cluster fonctionne comme prévu, vous pouvez également modifier le projet shoppingcart.war, afin qu’il affiche le nom de la machine qui gère la session du navigateur, démarrer une session de navigateur, arrêter la machine ayant géré la session du navigateur, et actualiser cette dernière, afin de vous assurer qu’une autre machine a pris le relais et gère la session de navigateur.

Par exemple :

1. Modifiez le fichier **DWRHeader1.jspf** afin qu’il inclue le code suivant sur sa partie supérieure :

		<table>
		
		<tr><td><CENTER><b><h3><% out.println("Your request is served from " + System.getenv("computername") ); %></h3></b></CENTER></td></tr>
		
		</table>             

2. Modifiez le fichier **weblogic.xml** afin que le code suivant apparaisse après la ligne commentaire `Insert session descriptor element here`.

		<session-descriptor>
			<persistent-store-type>replicated_if_clustered</persistent-store-type>
		</session-descriptor>


3. Recompilez et redéployez le fichier shoppingcart.war mis à jour.

4. Ouvrez une session de navigateur et exécutez l’application shoppingcart. Ajoutez des éléments au panier d’achat et déterminez quelle machine gère la session de navigateur.

5. Dans le portail Azure, accédez à l’interface utilisateur **Machines virtuelles**, puis sélectionnez la machine virtuelle ayant géré la session de navigateur. Cliquez sur **Arrêter**. Avant de poursuivre, attendez que l’état de la machine virtuelle soit **Arrêté (Désalloué)**.

6. Actualisez la session de navigateur qui exécute l’application shoppingcart et vérifiez qu’une autre machine prend le relais et gère la session de navigateur.

7. Cliquez sur le lien du panier d’achat et vérifiez que les éléments précédemment ajoutés figurent toujours dans ce panier.

## Ressources supplémentaires

Maintenant que vous avez configuré votre cluster exécutant Oracle WebLogic Server, consultez les rubriques suivantes pour en savoir plus.

- [Images de machine virtuelle Oracle – Considérations diverses](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Oracle WebLogic Server Product Documentation](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Oracle WebLogic Server 12c using Linux on Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

<!---HONumber=August15_HO6-->