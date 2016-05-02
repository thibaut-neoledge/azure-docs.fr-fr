<properties
	pageTitle="Création d’une machine virtuelle exécutant MySQL | Microsoft Azure"
	description="Créez une machine virtuelle Azure exécutant Windows Server 2012 R2, puis une base de données MySQL avec le modèle de déploiement classique."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="cynthn"/>


# Installation de MongoDB sur une machine virtuelle créée avec le modèle de déploiement classique exécutant Windows Server 2012 R2

[MySQL](http://www.mysql.com) est une base de données SQL open source connue. Ce didacticiel vous montre comment installer et exécuter la version Community de MySQL 5.6.23 comme serveur MySQL sur une machine virtuelle s’exécutant sous Windows Server 2012 R2. Pour obtenir des instructions sur l’installation de MySQL sur Linux, voir [Installation de MySQL sur Azure](virtual-machines-linux-mysql-install.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

## Création d'une machine virtuelle s’exécutant sous Windows Server 2012 R2

Si vous ne disposez pas d'une machine virtuelle s’exécutant sous Windows Server 2012 R2, vous pouvez utiliser ce [didacticiel](virtual-machines-windows-classic-tutorial.md) pour créer la machine virtuelle.

## Association d’un disque de données

Une fois la machine virtuelle créée, vous pouvez éventuellement attacher un disque de données supplémentaire. Cette action est recommandée pour les charges de travail de production et pour éviter de manquer d’espace sur le lecteur incluant le système d’exploitation (C:).

Voir l’article [Association d’un disque de données à une machine virtuelle Windows](virtual-machines-windows-classic-attach-disk.md) et suivez les instructions permettant d’attacher un disque vide. Définissez le paramètre de cache d’hôte sur la valeur **Aucun** ou **Lecture seule**.

## Connexion à la machine virtuelle

[Connectez-vous ensuite à la machine virtuelle](virtual-machines-windows-classic-connect-logon.md) pour installer MySQL.

##Installation et exécution de MySQL Community Server sur la machine virtuelle

Pour installer, configurer et exécuter la version Community de MySQL Server, procédez comme suit :

> [AZURE.NOTE] Ces étapes concernent la version Community 5.6.23.0 de MySQL et Windows Server 2012 R2. Votre expérience peut être différente sur d’autres versions de MySQL ou Windows Server.

1.	Une fois connecté à la machine virtuelle à l’aide du Bureau à distance, cliquez sur **Internet Explorer** dans l’écran d’accueil.
2.	Sélectionnez le bouton **Outils** dans l’angle supérieur droit (icône en forme de roue dentée), puis cliquez sur **Options Internet**. Cliquez successivement sur l’onglet **Sécurité**, sur l’icône **Sites de confiance**, puis sur le bouton **Sites**. Ajoutez http://*.mysql.com à la liste des sites de confiance. Cliquez sur **Fermer**, puis sur **OK**.
3.	Dans la barre d’adresse d’Internet Explorer, tapez http://dev.mysql.com/downloads/mysql/.
4.	Utilisez le site MySQL pour rechercher et télécharger la dernière version du programme d’installation de MySQL pour Windows. Lorsque vous choisissez le programme d’installation de MySQL, téléchargez la version qui comporte le jeu de fichiers complet (par exemple, le fichier mysql-installer-community-5.6.23.0.msi d’une taille de 282,4 Mo), puis enregistrez le fichier d’installation.
5.	Lorsque le programme d’installation est téléchargé, cliquez sur **Exécuter** pour le lancer.
6.	Sur la page **Contrat de licence**, acceptez le contrat de licence, puis cliquez sur **Suivant**.
7.	Sur la page **Choix du type de configuration**, cliquez sur le type souhaité, puis sur **Suivant**. Les étapes suivantes reposent sur l’hypothèse que vous avez sélectionné le type **Serveur uniquement**.
8.	Sur la page **Installation**, cliquez sur **Exécuter**. Une fois l’installation terminée, cliquez sur **Suivant**.
9.	Sur la page **Configuration du produit**, cliquez sur **Suivant**.
10.	Sur la page **Type et réseau**, spécifiez le type de configuration et les options de connectivité requises, notamment le port TCP si nécessaire. Sélectionnez **Afficher les options avancées**, puis cliquez sur **Suivant**.

	![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_TypeNetworking.png)

11.	Sur la page **Comptes et rôles**, spécifiez un mot de passe racine MySQL fort. Ajoutez des comptes d’utilisateurs MySQL si nécessaire, puis cliquez sur **Suivant**.

	![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AccountsRoles_Filled.png)

12.	Sur la page **Windows Service**, spécifiez les modifications à apporter aux paramètres par défaut pour exécuter MySQL Server en tant que service Windows, puis cliquez sur **Suivant**.

	![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_WindowsService.png)

13.	Sur la page **Options avancées**, spécifiez les modifications à apporter aux options de journalisation, puis cliquez sur **Suivant**.

	![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AdvOptions.png)

14.	Sur la page **Appliquer la configuration du serveur**, cliquez sur **Exécuter**. Une fois la configuration terminée, cliquez sur **Terminer**.
15.	Sur la page **Configuration du produit**, cliquez sur **Suivant**.
16.	Sur la page **Installation terminée**, cliquez sur **Copier le journal dans le Presse-papiers** pour l’examiner plus tard, puis cliquez sur **Terminer**.
17.	Sur l’écran d’accueil, tapez **mysql**, puis cliquez sur **Client de ligne de commande MySQL 5.6**.
18.	Entrez le mot de passe racine que vous avez indiqué à l’étape 11 pour ouvrir une invite vous permettant d’émettre des commandes pour configurer MySQL. Pour plus d’informations sur les commandes et sur la syntaxe, voir la page [Manuels de référence de MySQL](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html) (en anglais).

	![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_CommandPrompt.png)

19.	Vous pouvez également configurer les paramètres par défaut de la configuration serveur, tels que les lecteurs et les répertoires de base et de données, avec les entrées du fichier C:\\Program Files (x86) \\MySQL\\MySQL Server 5.6\\my-default.ini. Pour plus d’informations, voir la section [5\.1.2 Paramètres par défaut de la configuration serveur](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html) (en anglais).

## Configuration des points de terminaison

Si vous souhaitez que le service MySQL Server soit disponible pour les ordinateurs du client MySQL sur Internet, vous devez configurer un point de terminaison pour le port TCP sur lequel le service MySQL Server écoute et créer une nouvelle règle de pare-feu Windows. Il s’agit du port TCP 3306, sauf si vous en spécifiez un autre sur la page **Type et réseau** (étape 10 de la procédure précédente).


> [AZURE.NOTE] Envisagez avec soin les implications en matière de sécurité, car le service MySQL Server sera disponible à tous les ordinateurs sur Internet. Vous pouvez définir l’ensemble des adresses IP source autorisées à utiliser le point de terminaison avec une liste de contrôle d’accès (ACL). Pour plus d’informations, voir l’article [Configuration des points de terminaison sur une machine virtuelle](virtual-machines-windows-classic-setup-endpoints.md).


Pour configurer un point de terminaison pour le service MySQL Server :

1.	Dans le portail Azure Classic, cliquez sur **Machines virtuelles**, sur le nom de votre machine virtuelle MySQL, puis sur **Points de terminaison**.
2.	Dans la barre de commandes, cliquez sur **Ajouter**.
3.	Sur la page **Ajouter un point de terminaison à une machine virtuelle**, cliquez sur la flèche droite.
4.	Si vous utilisez le port TCP MySQL 3306 par défaut, cliquez sur **MySQL** dans le champ **Nom**, puis sur la coche.
5.	Si vous utilisez un autre port TCP, tapez un nom unique dans le champ **Nom**. Sélectionnez **TCP** dans le protocole, tapez le numéro du port dans **Port public** et **Port privé**, puis cliquez sur la coche.

## Ajouter une règle de Pare-feu Windows pour autoriser le trafic MySQL

Pour ajouter une règle de pare-feu Windows qui autorise le trafic MySQL à partir d’Internet, exécutez la commande suivante dans une invite de commandes Windows PowerShell avec élévation de privilèges sur la machine virtuelle du serveur MySQL.

	New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


	
## Tester votre connexion à distance


Pour tester votre connexion à distance au service MySQL Server exécuté sur la machine virtuelle Azure, vous devez d’abord déterminer le nom DNS correspondant au service cloud qui contient la machine virtuelle exécutant MySQL Server.

1.	Dans le portail Azure Classic, cliquez sur **Machines virtuelles**, sur le nom de votre machine virtuelle MySQL Server, puis sur **Tableau de bord**.
2.	Dans le tableau de bord de la machine virtuelle, notez la valeur du champ **Nom DNS** sous la section **Aperçu rapide**. Voici un exemple :

	![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_DNSName.png)

3.	Sur un ordinateur local exécutant MySQL ou le client MySQL, exécutez la commande suivante pour vous connecter en tant qu’utilisateur MySQL :

		mysql -u <yourMysqlUsername> -p -h <yourDNSname>

	Par exemple, pour le nom d’utilisateur MySQL dbadmin3 et le nom DNS testmysql.cloudapp.net pour la machine virtuelle, utilisez la commande suivante :

		mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## Étapes suivantes

Pour en savoir plus sur l'exécution de MySQL, consultez la [Documentation de MySQL](http://dev.mysql.com/doc/).

<!---HONumber=AcomDC_0420_2016-->