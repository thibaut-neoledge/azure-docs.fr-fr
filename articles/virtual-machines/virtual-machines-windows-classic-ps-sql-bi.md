<properties
	pageTitle="Business Intelligence de SQL Server | Microsoft Azure"
	description="Cette rubrique utilise des ressources créées avec le modèle de déploiement classique et décrit les fonctionnalités de Business Intelligence (BI) disponibles pour SQL Server s’exécutant sur Azure Virtual Machines."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/11/2015"
	ms.author="jroth" />

# Business Intelligence de SQL Server dans les machines virtuelles Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


La galerie de machines virtuelles Microsoft Azure inclut des images qui contiennent des installations de SQL Server. Les éditions de SQL Server prises en charge dans les images de la galerie sont les mêmes fichiers d’installation que vous pouvez installer sur les ordinateurs locaux et sur les machines virtuelles. Cette rubrique résume les fonctionnalités de SQL Server Business Intelligence (BI) installées sur les images, et les étapes de configuration requises après la configuration d’une machine virtuelle. Elle décrit également les topologies de déploiement prises en charge pour les fonctionnalités et les meilleures pratiques en matière de décisionnel (BI).

## Remarques sur la licence

Il existe deux manières de mettre sous licence SQL Server dans Microsoft Azure Virtual Machines :

1. Via les avantages License Mobility inclus dans la Software Assurance. Pour plus d’informations, consultez [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. Payez un tarif horaire pour Azure Virtual Machines avec SQL Server installé. Consultez la section « SQL Server » dans [Tarification de Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)

Pour plus d’informations sur les licences et les tarifs actuellement pratiqués, consultez [FAQ concernant les licences Virtual Machines](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## Images de SQL Server disponibles dans la galerie de machines virtuelles Azure

La galerie de machines virtuelles Microsoft Azure inclut plusieurs images qui contiennent Microsoft SQL Server. Le logiciel installé sur les images de machine virtuelle varie selon la version du système d’exploitation et la version de SQL Server. La liste des images disponibles dans la galerie de machines virtuelles Azure change souvent.

![Image SQL dans la galerie de machines virtuelles Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Le script PowerShell suivant renvoie la liste des images Azure dont le nom ImageName contient « SQL Server » :

	# assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.

	$subscriptionID = ""    # REQUIRED: Provide your subscription ID.
	$subscriptionName = "" # REQUIRED: Provide your subscription name.
	$thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
	$certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

	Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

	Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
	Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
	get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

	Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2012"
	Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
	get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2012*"} | select imagename,category, location, label, description

Pour plus d’informations sur les éditions et les fonctionnalités prises en charge par SQL Server, consultez les rubriques suivantes :

- [Éditions de SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [Fonctionnalités prises en charge par les éditions de SQL Server 2014](https://msdn.microsoft.com/library/cc645993.aspx)

### Fonctionnalités d’aide à la décision installées sur les images de la galerie de machines virtuelles SQL Server

Le tableau suivant récapitule les fonctionnalités Business Intelligence installées sur les images de la galerie de machines virtuelles Microsoft Azure pour SQL Server.

- SQL Server 2014 RTM Enterprise

- SQL Server 2014 Standard

- SQL Server 2012 SP2 Enterprise

- SQL Server 2012 SP2 Standard

|Fonctionnalité BI de SQL Server|Installé sur l’image de la galerie|Remarques|
|---|---|---|
|**Mode natif de Reporting Services**|Oui|Installé, mais nécessite une configuration, notamment de l’URL du Gestionnaire de rapports. Consultez la section [Configurer Reporting Services](#configure-reporting-services).|
|**Mode SharePoint de Reporting Services**|Non|L’image de la galerie de machines virtuelles Microsoft Azure ne comprend pas les fichiers SharePoint ou d’installation SharePoint. <sup>1</sup>|
|**Mode multidimensionnel et d’exploration de données Analysis Services (OLAP)**|Oui|Installé et configuré comme instance par défaut de Analysis Services|
|**Mode tabulaire Analysis Services**|Non|Pris en charge dans les images SQL Server 2012 et 2014, mais pas installé par défaut. Installez une autre instance d’Analysis Services. Consultez la section Installation d’autres services et fonctionnalités SQL Server dans cette rubrique.|
|**Analysis Services Power Pivot pour SharePoint**|Non|L’image de la galerie de machines virtuelles Microsoft Azure ne comprend pas les fichiers SharePoint ou d’installation SharePoint. <sup>1</sup>|

<sup>1</sup> Pour plus d’informations sur SharePoint et les machines virtuelles Azure, consultez [Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) et [Déploiement de SharePoint dans Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Exécutez la commande PowerShell suivante pour obtenir la liste des services installés dont le nom contient « SQL ».

	get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## Instructions générales et meilleures pratiques

- La taille minimale recommandée d’une machine virtuelle est la taille **A3** quand vous utilisez SQL Server Enterprise Edition. La taille de machine virtuelle **A4** est recommandée pour les déploiements SQL Server BI d’Analysis Services et de Reporting Services.

	Pour plus d’informations sur les tailles des machines virtuelles actuelles, consultez [Tailles de machines virtuelles pour Azure](virtual-machines-linux-sizes.md).

- La meilleure pratique de gestion du disque consiste à stocker les fichiers de données, de journal et de sauvegarde sur des lecteurs autres que **C**: et **D**:. Par exemple, créez des disques de données **E**: et **F**:.

	- La stratégie de mise en cache du lecteur **C**: par défaut n’est pas optimale pour l’utilisation de données.

	- Nous vous déconseillons d’utiliser le lecteur **D**: qui est un disque temporaire principalement utilisé pour le fichier d’échange. Nous vous déconseillons d’utiliser le lecteur **D**: qui n’est pas persistant et n’est pas stocké dans le stockage d’objets blob. Les tâches de gestion telles qu’une simple modification de la taille d’une machine virtuelle réinitialisent le lecteur **D**:. Nous vous **DÉCONSEILLONS** d’utiliser le lecteur **D**: pour les fichiers de base de données, y compris tempdb.

	Pour plus d’informations sur la création et l’attachement de disques, consultez la page [Procédure d’attachement d’un disque de données à une machine virtuelle](virtual-machines-windows-classic-attach-disk.md).

- Arrêtez ou désinstallez les services que vous ne prévoyez pas d’utiliser. Par exemple, si la machine virtuelle est uniquement utilisée pour Reporting Services, arrêtez ou désinstallez Analysis Services et SQL Server Integration Services. L’image suivante est un exemple de services qui sont démarrés par défaut.

	![Services SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)

	>[AZURE.NOTE] Le moteur de base de données SQL Server est requis dans les scénarios BI pris en charge. Dans une topologie de machine virtuelle avec un seul serveur, le moteur de base de données doit être exécuté sur la même machine virtuelle.

	Pour plus d’informations, consultez les rubriques suivantes : [Désinstaller Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) et [Procédure : désinstaller une instance d’Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).

- Vérifiez les nouvelles mises à jour importantes sur **Windows Update**. Les images de machines virtuelles Microsoft Azure sont souvent actualisées ; toutefois, des mises à jour importantes peuvent être disponibles sur **Windows Update** après la dernière actualisation de l’image de machine virtuelle.

## Exemples de topologies de déploiement

Voici des exemples de déploiements qui utilisent Microsoft Azure Virtual Machines. Les topologies décrites dans ces schémas ne représentent qu’une partie des topologies possibles avec les fonctionnalités BI de SQL Server et avec Microsoft Azure Virtual Machines.

### Une seule machine virtuelle

Analysis Services, Reporting Services, le moteur de base de données SQL Server et les sources de données sont sur une seule machine virtuelle.

![scénario BI iass avec 1 machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### Deux machines virtuelles

- Analysis Services, Reporting Services et le moteur de base de données SQL Server sont sur une seule machine virtuelle. Ce déploiement inclut les bases de données du serveur de rapports.

- Les sources de données sont sur une deuxième machine virtuelle. La deuxième machine virtuelle inclut le moteur de base de données SQL Server comme source de données.

![scénario BI iaas avec 2 machines virtuelles](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### Topologie mixte Azure - données sur Base de données SQL Azure

- Analysis Services, Reporting Services et le moteur de base de données SQL Server sont sur une seule machine virtuelle. Ce déploiement inclut les bases de données du serveur de rapports.

- La source de données est la Base de données SQL Azure.

![machine virtuelle de scénarios BI iaas et AzureSQL comme source de données](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### Topologie hybride – données locales

- Dans cet exemple de déploiement, Analysis Services, Reporting Services et le moteur de base de données SQL Server s’exécutent sur une seule machine virtuelle. La machine virtuelle héberge les bases de données du serveur de rapports. La machine virtuelle est jointe à un domaine local via le réseau virtuel Azure ou une autre solution de tunneling VPN.

- La source de données est locale.

![machine virtuelle de scénarios BI iaas et sources de données locales](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## Configuration du mode natif de Reporting Services

L’image de la galerie de machines virtuelles pour SQL Server inclut l’installation du mode natif de Reporting Services, mais le serveur de rapports n’est pas configuré. Les étapes de cette section permettent de configurer le serveur de rapports Reporting Services. Pour plus d’informations sur la configuration du mode natif de Reporting Services, consultez la page [Installer le serveur de rapports Reporting Services en mode natif](https://msdn.microsoft.com/library/ms143711.aspx).

>[AZURE.NOTE] Pour obtenir un contenu semblable qui utilise des scripts Windows PowerShell pour configurer le serveur de rapports, consultez la page [Utilisation de PowerShell pour créer une machine virtuelle Azure avec un serveur de rapports en mode natif](virtual-machines-windows-classic-ps-sql-report.md).

### Se connecter à la machine virtuelle et démarrer le Gestionnaire de configuration Reporting Services

Il existe deux flux de travail courants pour la connexion à une machine virtuelle Azure :

- Pour vous connecter, cliquez sur le nom de la machine virtuelle, puis cliquez sur **Connecter**. Une connexion Bureau à distance s’ouvre et le nom de l’ordinateur est automatiquement renseigné.

	![se connecter à une machine virtuelle azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)

- Connectez-vous à la machine virtuelle avec une connexion Bureau à distance Windows. Dans l’interface utilisateur du Bureau à distance :

	1. Tapez le **nom du service cloud** comme nom d’ordinateur.

	1. Appuyez sur la touche deux-points (:), puis entrez le numéro de port public qui est configuré pour le point de terminaison Bureau à distance TCP.

		Myservice.cloudapp.net:63133

		Pour plus d’informations, consultez [Présentation d’un service cloud](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).

**Démarrez le Gestionnaire de configuration Reporting Services.**

1. Dans **Windows Server 2012** :

1. Dans l’écran de **démarrage**, tapez **Reporting Services** pour afficher une liste d’applications.

1. Cliquez avec le bouton droit sur **Gestionnaire de configuration de Reporting Services**, puis cliquez sur **Exécuter en tant qu’administrateur**.

1. Dans **Windows Server 2008 R2** :

1. Cliquez sur **Démarrer**, puis sur **Tous les programmes**.

1. Cliquez sur **Microsoft SQL Server 2012**.

1. Cliquez sur **Outils de configuration**.

1. Cliquez avec le bouton droit sur **Gestionnaire de configuration de Reporting Services**, puis cliquez sur **Exécuter en tant qu’administrateur**.

Ou

1. Cliquez sur **Start**.

1. Dans la boîte de dialogue **Rechercher les programmes et fichiers**, tapez **reporting services**. Si la machine virtuelle exécute Windows Server 2012, tapez **reporting services** à l’écran de démarrage de Windows Server 2012.

1. Cliquez avec le bouton droit sur **Gestionnaire de configuration de Reporting Services**, puis cliquez sur **Exécuter en tant qu’administrateur**.

	![recherche du Gestionnaire de configuration de ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### Configurer Reporting Services

**Compte de service et URL du service Web :**

1. Vérifiez que **Nom du serveur** est le nom du serveur local et cliquez sur **Connecter**.

1. Notez que **Nom de la base de données du serveur de rapports** est vide. La base de données est créée une fois la configuration terminée.

1. Vérifiez que l’**État de Report Server** est **Démarré**. Pour vérifier le service dans le Gestionnaire de Windows Server, consultez le Service Windows **SQL Server Reporting Services**.

1. Cliquez sur **Compte de service** et modifiez le compte si nécessaire. Si la machine virtuelle est utilisée dans un environnement non joint à un domaine, le compte **ReportServer** intégré est suffisant. Pour plus d’informations sur le compte de service, consultez la page [Compte de service](https://msdn.microsoft.com/library/ms189964.aspx).

1. Cliquez sur l’**URL du service Web** dans le volet gauche.

1. Cliquez sur **Appliquer** pour configurer les valeurs par défaut.

1. Notez les **URL du service Web Report Server**. Le port TCP par défaut est 80 et fait partie de l’URL. Dans une étape ultérieure, vous créerez un point de terminaison de machine virtuelle Microsoft Azure pour le port.

1. Dans le volet **Résultats**, vérifiez que les actions ont été effectuées.

**Base de données :**

1. Dans le volet gauche, cliquez sur **Base de données**.

1. Cliquez sur **Changer la base de données**.

1. Vérifiez que **Créer une nouvelle base de données de serveur de rapports** est sélectionné, puis cliquez sur Suivant.

1. Vérifiez le **Nom du serveur** et cliquez sur **Tester la connexion**.

1. Si le résultat est **Le test de la connexion a réussi**, cliquez sur **OK**, puis sur **Suivant**.

1. Notez que le nom de la base de données est **ReportServer** et que le **Mode du serveur de rapports** est **Natif**, puis cliquez sur **Suivant**.

1. Dans la page **Informations d’identification**, cliquez sur **Suivant**.

1. Dans la page **Résumé**, cliquez sur **Suivant**.

1. Dans la page **État d’avancement et fin**, cliquez sur **Suivant**.

**URL du Gestionnaire de rapports :**

1. Dans le volet gauche, cliquez sur **URL du Gestionnaire de rapports**.

1. Cliquez sur **Apply**.

1. Dans le volet **Résultats**, vérifiez que les actions ont été effectuées.

1. Cliquez sur **Quitter**.

Pour plus d’informations sur les autorisations du serveur de rapports, consultez [Octroi d’autorisations sur un serveur de rapports en mode natif](https://msdn.microsoft.com/library/ms156014.aspx).

### Accéder au Gestionnaire de rapports local

Pour vérifier la configuration, accédez au Gestionnaire de rapports sur la machine virtuelle.

1. Sur la machine virtuelle, démarrez Internet Explorer avec des privilèges d’administrateur.

1. Accédez à http://localhost/reports sur la machine virtuelle.

### Pour se connecter au Gestionnaire de rapports distant

Si vous souhaitez vous connecter au Gestionnaire de rapports sur la machine virtuelle à partir d’un ordinateur distant, créez un nouveau point de terminaison TCP de machine virtuelle. Par défaut, le serveur de rapports écoute les requêtes HTTP sur le **port 80**. Si vous configurez les URL du serveur de rapports pour utiliser un autre port, vous devez spécifier ce numéro de port dans les instructions ci-après.

1. Créez un point de terminaison pour la machine virtuelle avec le port TCP 80. Pour plus d’informations, consultez la section [Points de terminaison de la machine virtuelle et ports de pare-feu](#virtual-machine-endpoints-and-firewall-ports) dans ce document.

1. Ouvrez le port 80 dans le pare-feu de la machine virtuelle.

1. Accédez au Gestionnaire de rapports en utilisant le **Nom DNS** de la machine virtuelle Azure comme nom de serveur dans l’URL. Par exemple :

	**Gestionnaire de rapports** : http://uebi.cloudapp.net/reportserver **Serveur de rapports** : http://uebi.cloudapp.net/reports

	[Configurer un pare-feu pour accéder au serveur de rapports](https://technet.microsoft.com/library/bb934283.aspx)

### Pour créer et publier des rapports sur la machine virtuelle Azure

Le tableau suivant résume certaines des options disponibles pour publier des rapports existants à partir d’un ordinateur local vers le serveur de rapports hébergé sur la machine virtuelle Microsoft Azure :

- **Générateur de rapports** : la machine virtuelle inclut la version un clic du Générateur de rapports Microsoft SQL Server. Pour démarrer le Générateur de rapports la première fois sur la machine virtuelle:

	1. Démarrez votre navigateur avec des privilèges administratifs.

	1. Accédez au Gestionnaire de rapports sur la machine virtuelle et cliquez sur **Générateur de rapports** dans le ruban.

	Pour plus d’informations, consultez la page [Installer, désinstaller et prendre en charge le Générateur de rapports](https://technet.microsoft.com/library/dd207038.aspx).

- **SQL Server Data Tools : machines virtuelles** : SQL Server Data Tools est installé sur la machine virtuelle et peut être utilisé pour créer des **projets de serveur de rapports** et des rapports sur la machine virtuelle. SQL Server Data Tools peut publier les rapports vers le serveur de rapports sur la machine virtuelle.

- **SQL Server Data Tools : distant** : sur votre ordinateur local, créez un projet Reporting Services contenant des rapports Reporting Services dans SQL Server Data Tools. Configurez le projet pour la connexion à l’URL du service web.

	![propriétés de projet ssdt pour un projet SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)

- Créez un disque dur .VHD qui contient des rapports, puis téléchargez et attachez le lecteur.

	1. Créez un disque dur .VHD sur votre ordinateur local qui contient vos rapports.

	1. Créez et installez un certificat de gestion.

	1. Téléchargez le fichier VHD sur Azure à l’aide de l’applet de commande Add-AzureVHD en procédant de la manière décrite dans [Créer et charger un disque dur virtuel Windows Server dans Azure](virtual-machines-windows-classic-createupload-vhd.md).

	1. Attachez le disque à la machine virtuelle.

## Installation d’autres services et fonctionnalités SQL Server

Pour installer les services supplémentaires de SQL Server, tels que Analysis Services en mode tabulaire, exécutez l’Assistant Installation de SQL Server. Les fichiers d’installation sont sur le disque local de la machine virtuelle.

1. Cliquez sur **Démarrer**, puis sur **Tous les programmes**.

1. Cliquez sur **Microsoft SQL Server 2014** ou sur **Microsoft SQL Server 2012**, puis sur **Outils de configuration**.

1. Cliquez sur **Centre d’installation SQL Server**.

Vous pouvez également exécuter C:\\SQLServer\_12.0\_full\\setup.exe ou C:\\SQLServer\_11.0\_full\\setup.exe

>[AZURE.NOTE] La première fois que vous exécutez l’installation de SQL Server, davantage de fichiers d’installation peuvent être téléchargés et nécessiter un redémarrage de la machine virtuelle et un redémarrage de l’installation de SQL Server.
>
>Si vous devez personnaliser de façon répétée l’image sélectionnée à partir de la machine virtuelle Microsoft Azure, envisagez de créer votre propre image SQL Server. La fonctionnalité d’Analysis Services SysPrep a été activée avec SQL Server 2012 SP1 CU2. Pour plus d’informations, consultez [Considérations relatives à l’installation de SQL Server à l’aide de SysPrep](https://msdn.microsoft.com/library/ee210754.aspx).

### Pour installer Analysis Services en mode tabulaire

Les étapes de cette section **résument** l’installation d’Analysis Services en mode tabulaire. Pour plus d’informations, consultez les liens suivants :

- [Installer Analysis Services en mode tabulaire](https://msdn.microsoft.com/library/hh231722.aspx)

- [Modélisation tabulaire (didacticiel Adventure Works)](https://technet.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Pour installer Analysis Services en mode tabulaire :**

1. Dans l’Assistant Installation de SQL Server, cliquez sur **Installation** dans le volet gauche, puis cliquez sur **Nouvelle installation autonome SQL Server ou ajout de fonctionnalités à une installation existante**.

	- Si vous voyez **Rechercher un dossier**, accédez à c:\\SQLServer\_12.0\_full ou à c:\\SQLServer\_11.0\_full, puis cliquez sur **OK**.

1. Dans la page des mises à jour du produit, cliquez sur **Suivant**.

1. Dans la page **Type d’installation**, sélectionnez **Effectuer une nouvelle installation de SQL Server**, puis cliquez sur **Suivant**.

1. Dans la page **Rôle d’installation**, cliquez sur **Installation de fonctionnalités SQL Server**.

1. Dans la page **Sélection de composant**, cliquez sur **Analysis Services**.

1. Dans la page **Configuration de l’instance**, entrez un nom descriptif, tel que **Tabulaire** dans les zones de texte **Instance nommée** et **ID d’instance**.

1. Dans la page **Configuration d’Analysis Services**, sélectionnez **Mode Tabulaire**. Ajoutez l’utilisateur actuel à la liste des autorisations d’administrateur.

1. Terminez et fermez l’Assistant Installation de SQL Server.

## Configuration d’Analysis Services

### Accès distant au serveur Analysis Services

Le serveur Analysis Services prend en charge uniquement l’authentification Windows. Pour accéder à Analysis Services à distance des applications clientes telles que SQL Server Management Studio ou SQL Server Data Tools, la machine virtuelle doit être jointe à votre domaine local à l’aide d’Azure Virtual Network. Pour plus d’informations, consultez [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Une **instance par défaut** d’Analysis Services écoute sur le port TCP **2383**. Ouvrez le port dans le pare-feu des machines virtuelles. Une instance en cluster nommée d’Analysis Services écoute également sur le port **2383**.

Pour une **instance nommée** d’Analysis Services, le service SQL Server Browser est nécessaire pour gérer l’accès au port. La configuration par défaut de SQL Server Browser est le port **2382**.

Dans le pare-feu des machines virtuelles, ouvrez le port **2382** et créez un port d’instance nommée Analysis Services statique.

1. Pour vérifier les ports qui sont déjà utilisés sur la machine virtuelle et connaître le processus qui les utilise, exécutez la commande suivante avec des privilèges d’administration :

		netstat /ao

1. Utilisez SQL Server Management Studio pour créer un port d’instance nommée Analysis Services statique en mettant à jour la valeur « Port » dans les propriétés générales de l’instance AS tabulaire. Pour plus d’informations, consultez la section « Utiliser un port fixe pour une instance par défaut ou nommée d’Analysis Services » dans [Configurer le pare-feu Windows pour autoriser l’accès à Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).

1. Redémarrez l’instance tabulaire du service Analysis Services.

Pour plus d’informations, consultez la section **Points de terminaison de la machine virtuelle et ports de pare-feu** dans ce document.

## Points de terminaison de la machine virtuelle et ports de pare-feu

Cette section résume les points de terminaison de machine virtuelle Microsoft Azure à créer et les ports à ouvrir dans les pare-feu de machine virtuelle. Le tableau suivant récapitule les ports **TCP** pour lesquels créer des points de terminaison et les ports à ouvrir dans le pare-feu des machines virtuelles.

- Si vous utilisez une seule machine virtuelle et si les deux affirmations suivantes sont vraies, vous n’avez pas besoin de créer des points de terminaison de machine virtuelle, ni d’ouvrir les ports dans le pare-feu sur la machine virtuelle.

	- Vous ne vous connectez pas à distance aux fonctionnalités SQL Server sur la machine virtuelle. L’établissement d’une connexion Bureau à distance vers la machine virtuelle et l’accès aux fonctionnalités de SQL Server localement sur la machine virtuelle ne sont pas considérés comme une connexion distante aux fonctionnalités de SQL Server.

	- Vous ne joignez pas la machine virtuelle à un domaine local via le réseau virtuel Azure ou via une autre solution de tunneling VPN.

- Si la machine virtuelle n’est pas jointe à un domaine mais que vous souhaitez vous connecter à distance aux fonctionnalités SQL Server sur la machine virtuelle :

	- Ouvrez les ports dans le pare-feu sur la machine virtuelle.

	- Créez des points de terminaison de machine virtuelle pour les ports notés (*).

- Si la machine virtuelle est jointe à un domaine avec un tunnel VPN tel que le réseau virtuel Azure, les points de terminaison ne sont pas requis. Ouvrez toutefois les ports dans le pare-feu sur la machine virtuelle.

	|Port|Type|Description|
|---|---|---|
|**80**|TCP|Accès distant au serveur de rapports (*).| |**1433**|TCP|SQL Server Management Studio (*).| |**1434**|UDP|SQL Server Browser. Nécessaire lorsque la machine virtuelle est jointe à un domaine.|
|**2382**|TCP|SQL Server Browser.|
|**2383**|TCP|Instance par défaut et instance en cluster nommée de SQL Server Analysis Services.|
|**Définie par l’utilisateur**|TCP|Créez une instance nommée Analysis Services statique pour un numéro de port que vous choisissez, puis débloquez le numéro de port dans le pare-feu.|

Pour plus d’informations sur la création de points de terminaison, consultez les rubriques suivantes :

- Créer des points de terminaison : [Comment configurer des points de terminaison sur une machine virtuelle](virtual-machines-windows-classic-setup-endpoints.md).

- SQL Server : Consultez la section « Procédure de configuration complète pour la connexion à la machine virtuelle en utilisant SSMS (SQL Server Management Studio) sur un autre ordinateur » dans [Approvisionnement d’une machine virtuelle SQL Server sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

Le schéma suivant montre les ports à ouvrir dans le pare-feu de la machine virtuelle pour autoriser l’accès à distance aux fonctionnalités et aux composants de la machine virtuelle.

![ports à ouvrir pour des applications BI dans des machines virtuelles Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## Ressources

- Consultez la stratégie de prise en charge des logiciels serveurs Microsoft utilisée dans l’environnement de la machine virtuelle Azure. La rubrique suivante résume la prise en charge de fonctionnalités telles que BitLocker, le clustering de basculement et l’équilibrage de la charge réseau. [Prise en charge des logiciels serveur Microsoft pour Azure Virtual Machines](http://support.microsoft.com/kb/2721672).

- [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-classic-sql-overview.md)

- [Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/)

- [Configuration d'une machine virtuelle SQL Server sur Azure](virtual-machines-windows-portal-sql-server-provision.md)

- [Association d’un disque de données à une machine virtuelle](virtual-machines-windows-classic-attach-disk.md)

- [Migrating a Database to SQL Server on an Azure VM](virtual-machines-windows-classic-migrate-sql.md)

- [Déterminer le mode serveur d’une instance Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)

- [Modélisation multidimensionnelle (didacticiel Adventure Works)](https://technet.microsoft.com/library/ms170208.aspx)

- [Centre de documentation](https://azure.microsoft.com/documentation/)

- [Utilisation de Power BI dans un environnement hybride](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [Submit feedback and contact information through Microsoft SQL Server Connect](https://connect.microsoft.com/SQLServer/Feedback)

### Contenu de la communauté

- [Gestion de base de données SQL Azure avec PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)

<!---HONumber=AcomDC_0330_2016-->