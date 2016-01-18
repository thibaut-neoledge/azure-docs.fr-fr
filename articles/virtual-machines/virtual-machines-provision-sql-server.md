<properties
	pageTitle="Approvisionnement d’une machine virtuelle SQL Server | Microsoft Azure"
	description="Ce didacticiel explique la création et la configuration d'une machine virtuelle SQL Server dans Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"
	/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="jroth"/>

# Approvisionnement d’une machine virtuelle SQL Server dans Azure

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)

## Vue d'ensemble

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager.

La galerie de machines virtuelles Azure inclut différentes images contenant Microsoft SQL Server. Vous pouvez sélectionner l'une des images de machine virtuelle de cette galerie et la configurer en quelques clics pour votre environnement Azure.

Ce didacticiel présente les procédures suivantes :

* [Se connecter au portail Azure Classic et approvisionner une machine virtuelle à partir de la galerie](#Provision)
* [Ouvrir la machine virtuelle à l’aide du Bureau à distance et achever la configuration](#RemoteDesktop)
* [Suivre la procédure de configuration pour se connecter à la machine virtuelle en utilisant SQL Server Management Studio sur un autre ordinateur](#SSMS)
* [Étapes suivantes](#Optional)

>[AZURE.NOTE]Cet article décrit la configuration d'une machine virtuelle SQL Server avec le portail existant. Toutefois, il est également possible de créer et gérer des machines virtuelles SQL Server dans le [nouveau portail](https://manage.windowsazure.com). Le nouveau portail comporte plusieurs avantages, comment l’utilisation par défaut du stockage Premium, ainsi que d’autres options, comme l’application automatique de correctifs, la sauvegarde automatisée et les configurations AlwaysOn. Le contenu à venir fournira des instructions détaillées.

##<a id="Provision">Approvisionner une machine virtuelle SQL Server depuis la galerie</a>

1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com) avec votre compte. Si vous n'avez pas de compte Azure, visitez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

2. Dans le portail Azure Classic, en bas à gauche de la page Web, cliquez sur **+NOUVEAU**, sur **CALCUL**, sur **MACHINE VIRTUELLE**, puis sur **À PARTIR DE LA GALERIE**.

3. Sur la page **Choisir une image**, cliquez sur **SQL SERVER**. Sélectionnez ensuite une image SQL Server. Cliquez sur la flèche suivante en bas à droite de la page.

	![Choisir une image](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

Pour obtenir les informations les plus récentes concernant les images SQL Server prises en charge sur Azure, consultez la rubrique [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-infrastructure-services.md).

>[AZURE.NOTE]Si vous avez créé une machine virtuelle à l’aide de l’image de plateforme de la version d’évaluation de SQL Server, vous ne pouvez pas la mettre à jour vers une image de la version payante à la minute dans la galerie. Vous pouvez choisir l'une des deux options suivantes :
>
> - Vous pouvez créer une machine virtuelle en utilisant la version de SQL Server payante à la minute à partir de la galerie et faire migrer vos fichiers de base de données vers cette nouvelle machine virtuelle en suivant la procédure de la page [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-migrate-onpremises-database).
> - Vous pouvez également mettre à niveau une instance de la version d’évaluation de SQL Server vers une autre version de SQL Server selon le contrat de [mobilité de licence via l’assurance logicielle sur Azure](http://azure.microsoft.com/pricing/license-mobility/) en suivant la procédure de la page [Mise à niveau vers une autre version de SQL Server](https://msdn.microsoft.com/library/cc707783.aspx). Pour plus d'informations sur l'achat d'une copie sous licence de SQL Server, consultez la page [Comment acheter SQL Server ?](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).

4. Dans la première page **Configuration de la machine virtuelle**, entrez les informations suivantes :
	- Une **DATE DE SORTIE DE LA VERSION**. Si plusieurs images sont disponibles, sélectionnez la dernière.
	- Un **NOM DE MACHINE VIRTUELLE** unique.
	- Un nom d’utilisateur unique pour le compte administrateur local de la machine dans la zone **NOUVEAU NOM D’UTILISATEUR**.
	- Dans la zone **NEW PASSWORD**, entrez un mot de passe fort.
	- Dans la zone **CONFIRM PASSWORD** entrez de nouveau le mot de passe.
	- Sélectionnez la **taille** adéquate dans le menu déroulant.

	![Configuration de machine virtuelle](./media/virtual-machines-provision-sql-server/4VM-Config.png)

	>[AZURE.NOTE]La taille de la machine virtuelle est indiquée durant la configuration :
 	>
	> - Pour les charges de travail de production, nous recommandons l’utilisation de stockage Premium avec les tailles minimum recommandées : **DS3** pour l’édition SQL Server Enterprise et **DS2** pour l’édition SQL Server Standard. Pour plus d’informations, consultez [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-performance-best-practices.md).
	> - La taille sélectionnée limite le nombre de disques de données que vous pouvez configurer. Pour obtenir les informations les plus récentes sur les tailles de machines virtuelles disponibles et le nombre de disques de données que vous pouvez attribuer à une machine virtuelle, consultez la page [Tailles de machines virtuelles pour Azure](virtual-machines-size-specs.md).

5. Après avoir entré les détails de configuration de votre machine virtuelle, cliquez sur la flèche suivant en bas à droite pour continuer.

5. Dans la deuxième page **Configuration de la machine virtuelle**, configurez les ressources pour la mise en réseau, le stockage et la disponibilité :
	- Dans la zone **Service cloud**, sélectionnez **Créer un nouveau service de cloud computing**.
	- Dans la zone **Nom du cloud Service DNS**, entrez la première partie d'un nom DNS de votre choix, pour qu'il complète un nom au format **TESTNAME.cloudapp.net**
	- Sélectionnez un **ABONNEMENT** s’il en existe plusieurs. Ce choix détermine les **comptes de stockage **disponibles.
- Dans la zone **REGION/AFFINITY GROUP/VIRTUAL NETWORK**, sélectionnez une région d'hébergement pour cette image virtuelle.
	- Dans la zone **Compte de stockage**, générez automatiquement un compte ou sélectionnez-en un dans la liste. Modifiez l’**ABONNEMENT** pour voir plus de comptes.
	- Dans la zone **AVAILABILITY SET**, sélectionnez **(none)**.
	- Lisez et acceptez les conditions juridiques.


6. Cliquez sur la flèche Suivant pour continuer.


7. Pour continuer, cliquez sur la coche dans le coin en inférieur droit.

8. Patientez pendant qu'Azure prépare votre machine virtuelle. L'état de la machine virtuelle doit normalement passer par les phases suivantes :

	- **Démarrage (configuration)**
	- **Arrêté**
	- **Démarrage (configuration)**
	- **Exécution (configuration)**
	- **Exécution**


##<a id="RemoteDesktop">Ouverture de la machine virtuelle à l’aide du Bureau à distance pour achever la configuration</a>

1. Une fois l’approvisionnement terminé, cliquez sur le nom de votre machine virtuelle pour accéder à la page TABLEAU DE BORD. En bas de la page, cliquez sur **Connect**.

2. Cliquer sur le bouton **Ouvrir**.

	![Cliquer sur le bouton Ouvrir](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. Dans la boîte de dialogue **Sécurité de Windows**, cliquez sur **Utiliser un autre compte**.

	![Cliquer sur Utiliser un autre compte](./media/virtual-machines-provision-sql-server/credentials.png)

4. Utilisez le nom de la machine en tant que nom de domaine, suivi de votre nom d’administrateur au format suivant : `machinename\username`. Saisissez votre mot de passe et connectez-vous à la machine.

4. Lors de la première connexion, plusieurs processus doivent s’effectuer, notamment la configuration de votre bureau, les mises à jour de Windows et l’achèvement des tâches de configuration initiales de Windows (sysprep). Une fois Windows sysprep terminé, le programme d’installation de SQL Server effectue les tâches de configuration. L’exécution de ces tâches peut nécessiter quelques minutes. `SELECT @@SERVERNAME` peut ne pas renvoyer le nom correct tant que la configuration de SQL Server n’est pas terminée, et SQL Server Management Studio peut ne pas être visible sur la page de démarrage.

Une fois que vous êtes connecté à la machine virtuelle avec le Bureau à distance Windows, celle-ci fonctionne comme un autre ordinateur. Connectez-vous normalement à l’instance par défaut de SQL Server avec SQL Server Management Studio (exécuté sur la machine virtuelle).

##<a id="SSMS">Connexion à l’instance de machine virtuelle SQL Server en utilisant SQL Server Management Studio (SSMS) sur un autre ordinateur</a>

Les étapes suivantes montrent comment se connecter à l'instance SQL Server sur Internet à l'aide de SQL Server Management Studio (SSMS). Toutefois, les mêmes étapes s'appliquent pour rendre votre machine virtuelle SQL Server accessible pour vos applications exécutées en local et dans le modèle de déploiement classique Azure. Si votre machine virtuelle est déployée dans le modèle Resource Manager, consultez [Se connecter à une machine virtuelle SQL Server sur Azure (Resource Manager)](virtual-machines-sql-server-connectivity-resource-manager.md)

Avant de pouvoir vous connecter à l’instance de SQL Server à partir d’une autre machine virtuelle ou d’Internet, vous devez effectuer les tâches suivantes, comme indiqué dans les sections ci-dessous :

- [Créer un point de terminaison TCP pour la machine virtuelle](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Ouvrir des ports TCP dans le pare-feu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurer SQL Server pour l’écoute du protocole TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurer SQL Server pour l’authentification en mode mixte](#configure-sql-server-for-mixed-mode-authentication)
- [Créer des connexions d’authentification SQL Server](#create-sql-server-authentication-logins)
- [Déterminer le nom DNS de la machine virtuelle](#determine-the-dns-name-of-the-virtual-machine)
- [Se connecter au moteur de base de données à partir d’un autre ordinateur](#connect-to-the-database-engine-from-another-computer)

Le chemin de connexion est résumé dans le schéma suivant :

![Connexion à une machine virtuelle SQL Server](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Se connecter à SQL Server sur un point de terminaison TCP classique de machine virtuelle](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Se connecter à SQL Server dans une machine virtuelle](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Se connecter à SQL Server dans une procédure classique de machine virtuelle](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a id="cdea">Connexion au moteur de base de données à partir de votre application</a>

Si vous pouvez vous connecter à une instance de SQL Server exécutée sur une machine virtuelle Azure en utilisant Management Studio, vous devriez pouvoir vous connecter en utilisant une chaîne de connexion semblable à la suivante :

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Pour plus d'informations, consultez la page [Résolution des problèmes de connexion au moteur de base de données SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Étapes suivantes</a>

Vous avez vu comment créer et configurer une instance de SQL Server sur une machine virtuelle Azure en utilisant une image de plateforme. Dans de nombreux cas, l’étape suivante consiste à migrer vos bases de données vers cette nouvelle machine virtuelle SQL Server. Pour obtenir de l’aide sur la migration des bases de données, consultez [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-migrate-onpremises-database.md).

La liste suivante fournit des ressources supplémentaires pour SQL Server dans des machines virtuelles Azure.

### Ressources recommandées pour SQL Server sur des machines virtuelles Azure :
- [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-infrastructure-services.md)

- [Se connecter à une machine virtuelle SQL Server sur Azure](virtual-machines-sql-server-connectivity.md)

- [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-performance-best-practices.md)

- [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-security-considerations.md)

### Haute disponibilité et récupération d’urgence :
- [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)

- [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-sql-server-backup-and-restore.md)

### Charges de travail SQL Server dans Azure :
- [Business Intelligence de SQL Server dans les machines virtuelles Azure](virtual-machines-sql-server-business-intelligence.md)

### Livres blancs :
- [Présentation de Base de données SQL Azure et de SQL Server dans les machines virtuelles Azure](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Modèles d'application et stratégies de développement pour SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-application-patterns-and-development-strategies.md)

<!---HONumber=AcomDC_0107_2016-->