<properties 
	pageTitle="Déplacement de données entre des emplacements locaux et le cloud à l'aide d’Azure Data Factory" 
	description="Découvrez comment déplacer des données entre des emplacements locaux et le cloud à l'aide de la passerelle de gestion des données et d’Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/09/2016" 
	ms.author="spelluru"/>

# Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données
Un des défis de l’intégration de données modernes consiste à déplacer en toute transparence des données vers et depuis un site local et le cloud. Data Factory facilite cette intégration grâce à une passerelle de gestion des données. La passerelle de gestion Data Factory est un agent que vous pouvez installer en local pour créer des pipelines hybrides.

Cet article fournit une vue d’ensemble de l’intégration de magasins de données locaux à des magasins de données cloud et du traitement cloud à l’aide de Data Factory. Cet article s’appuie sur l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) et d’autres articles sur les principaux concepts Data Factory. La vue d’ensemble suivante suppose que vous maîtrisez les concepts Data Factory tels que les pipelines, les activités, les jeux de données et l’activité de copie.

La passerelle de données offre les fonctionnalités suivantes :

1.	Modélisation des sources de données locales et des sources de données sur le cloud au sein de la même fabrique de données, et déplacement des données.
2.	Un point unique de surveillance et de gestion avec une visibilité du statut de la passerelle et un tableau de bord sur un cloud de fabrique de données.
3.	Gestion sécurisée de l’accès aux sources de données locales.
	1. Aucune modification du pare-feu d’entreprise n’est requise. La passerelle établit uniquement des connexions HTTP sortantes pour l’accès à Internet.
	2. Chiffrement des informations d’identification pour vos magasins de données locaux à l’aide de votre certificat.
4.	Déplacer les données efficacement : les données sont transférées en parallèle et résistent aux problèmes intermittents du réseau, grâce à la logique de nouvelle tentative automatique.

## Considérations sur l’utilisation de la passerelle de gestion de données
1.	Une instance unique de la passerelle de gestion de données peut être utilisée pour plusieurs sources de données locales, mais notez qu’une **seule instance de passerelle est liée à une seule fabrique de données Microsoft Azure** et ne peut pas être partagée avec une autre fabrique de données.
2.	Vous ne pouvez installer qu’**une seule instance de la passerelle de gestion de données** sur un même ordinateur. Si deux fabriques de données doivent accéder aux sources de données locales, vous devez installer des passerelles sur deux ordinateurs locaux où chaque passerelle est liée à une fabrique de données distincte.
3.	Il n’est pas nécessaire que la **passerelle soit sur le même ordinateur que la source de données**, mais le fait qu’elle soit proche de cette dernière réduit le temps nécessaire à la passerelle pour se connecter à la source de données. Nous vous recommandons d’installer la passerelle sur un ordinateur différent de celui qui héberge la source de données locale, afin que la passerelle ne soit pas en concurrence avec la source de données pour l’octroi des ressources.
4.	Vous pouvez avoir **plusieurs passerelles sur différents ordinateurs connectés à la même source de données locale**. Par exemple, vous pouvez avoir deux passerelles desservant deux fabriques de données, alors que la même source de données locale est enregistrée auprès des deux fabriques de données.
5.	Si une passerelle est déjà installée sur votre ordinateur desservant un scénario **Power BI**, installez une **passerelle distincte pour Azure Data Factory** sur un autre ordinateur.
6.	Vous devez **utiliser la passerelle même lorsque vous utilisez ExpressRoute**. 
7.	Vous devez considérer votre source de données comme une source de données locale (qui se trouve derrière un pare-feu) même lorsque vous utilisez **ExpressRoute** et **utilisez la passerelle** pour établir la connectivité entre le service et la source de données. 

## Installer une passerelle de gestion des données

### Installation de la passerelle : configuration requise
1.	Les versions de **système d’exploitation** prises en charge sont les suivantes : Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2. L’installation de la passerelle de gestion des données sur un contrôleur de domaine n’est pas prise en charge actuellement.
2.	La **configuration** recommandée pour l’ordinateur de passerelle est la suivante : au moins 2 GHz, 4 cœurs, 8 Go de RAM et 80 Go d’espace disque.
3.	Si l’ordinateur hôte est en veille prolongée, la passerelle n’est pas en mesure de répondre à la demande de données. Vous devez donc configurer un **plan de gestion de l’alimentation** approprié sur l’ordinateur avant d’installer la passerelle. L’installation de la passerelle ouvre une invite si l’ordinateur est configuré pour la mise en veille prolongée.

Étant donné que l’activité de copie s’exécute selon une fréquence spécifique, l’utilisation des ressources (processeur, mémoire) sur l’ordinateur suit également le même modèle avec des pics et des baisses d’inactivité. L'utilisation des ressources dépend également en grande partie de la quantité de données déplacées. Lorsque plusieurs tâches sont en cours, vous constaterez une augmentation des ressources utilisées pendant les heures de pointe. L’exemple ci-dessus représente la configuration minimale, et il est toujours préférable d'avoir une configuration avec plus de ressources que cette configuration minimale en fonction de votre charge spécifique pour le déplacement des données.

### Installation
Vous pouvez installer la passerelle de gestion des données en téléchargeant un package d’installation MSI à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Le fichier MSI peut également servir à mettre à niveau la passerelle de gestion des données existante vers la version la plus récente, en conservant tous les paramètres. Vous trouverez le lien vers le package MSI sur le portail Azure en suivant la procédure pas à pas ci-dessous.


### Meilleures pratiques d’installation :
1.	Définissez un plan d'alimentation sur l'ordinateur hôte de la passerelle afin d’empêcher la mise en veille prolongée. Si l’ordinateur hôte est en veille prolongée, la passerelle n’est pas en mesure de répondre à la demande de données.
2.	Vous devriez sauvegarder le certificat associé à la passerelle.

## Mise à jour de la passerelle de gestion de données
Par défaut, la passerelle de gestion des données est automatiquement mise à jour lorsqu’une version plus récente est disponible. La passerelle n’est pas mise à jour tant que toutes les tâches planifiées ne sont pas terminées. Aucune autre tâche n’est traitée par la passerelle avant la fin de l’opération de mise à jour. Si la mise à jour échoue, la passerelle est restaurée vers son ancienne version.

Vous verrez apparaître l’heure de mise à jour planifiée dans le panneau de propriétés de la passerelle du portail, à la page d’accueil du Gestionnaire de configuration de passerelle de gestion des données, et dans le message de notification de la barre d’état système. Vous pouvez installer la mise à jour immédiatement ou attendre que la passerelle soit mise à jour automatiquement à l’heure planifiée. Par exemple, la capture d’écran suivante montre le message de notification affiché dans le Gestionnaire de configuration de passerelle de gestion des données, ainsi que le bouton de mise à jour qui vous permet d’installer cette dernière immédiatement.

![Mise à jour dans DMG Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/gateway-auto-update-config-manager.png)

Le message de notification dans la barre d’état système se présente comme suit :

![Message de barre d’état système](./media/data-factory-move-data-between-onprem-and-cloud/gateway-auto-update-tray-message.png)

Vous verrez s’afficher la progression de l’opération de mise à jour (manuelle ou automatique) dans la barre d’état système. Lors de la prochaine ouverture de Gestionnaire de configuration de passerelle de gestion de données, un message s’affiche dans la barre de notification, selon lequel la passerelle a été mise à jour, et contenant un lien vers la rubrique relative aux [nouveautés](data-factory-gateway-release-notes.md).

L’onglet de mise à jour du Gestionnaire de configuration de passerelle de gestion de données affiche la planification de la mise à jour, ainsi que la dernière fois que la passerelle a été installée ou mise à jour. Si la mise à jour automatique est désactivée, il affiche un message s’y rapportant, mais vous ne pourrez pas activer la fonctionnalité dans l’onglet ; vous devrez utiliser l’applet de commande à cette fin.
  

## Icônes/notification de barre d’état système
L’illustration suivante représente certaines des icônes de barre d’état qui s’affichent.

![Icônes de la barre d’état système](./media/data-factory-move-data-between-onprem-and-cloud/gateway-tray-icons.png)

Si vous déplacez le curseur sur les icônes/messages de notification de la barre d’état système, vous afficherez des informations supplémentaires sur l’état d’opération de la passerelle/la progression de la mise à jour dans une fenêtre contextuelle.

## Pour activer/désactiver une fonctionnalité de mise à jour automatique
Vous pouvez désactiver/activer la fonctionnalité de mise à jour automatique de la manière suivante :

1. Lancez Windows PowerShell sur l’ordinateur de passerelle. 
2. Accédez au dossier C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript.
3. Exécutez la commande suivante pour désactiver la fonctionnalité de mise à jour automatique.   

		.\GatewayAutoUpdateToggle.ps1  -off

4. Pour la réactiver :
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## Considérations liées aux ports et à la sécurité
Vous devez porter votre attention sur deux pare-feu : le **pare-feu d’entreprise** en cours d’exécution sur le routeur central de l’entreprise et le **pare-feu Windows** configuré en tant que démon sur l’ordinateur local sur lequel la passerelle est installée.

![Pare-feu](./media/data-factory-move-data-between-onprem-and-cloud/firewalls.png)

### Passerelle de connexion avec services cloud
Pour maintenir la connectivité de la passerelle avec Azure Data Factory et d’autres services cloud, vous devez vous assurer que la règle de trafic sortant pour les ports **TCP** **80** et **443** est configurée. Vous pouvez éventuellement activer les ports **9350** à **9354**, qui sont utilisés par Microsoft Azure Service Bus pour établir la connexion entre Azure Data Factory et la passerelle de gestion des données. Cela peut améliorer les performances des communications entre ces deux systèmes.

Au niveau du pare-feu d’entreprise, vous devez configurer les domaines et ports de sortie suivants :

| Noms de domaine | Ports | Description |
| ------ | --------- | ------------ |
| **. servicebus.windows.net | 443, 80 | Écouteurs sur Service Bus Relay via TCP (nécessite le port 443 pour l’acquisition du jeton Access Control) || *. servicebus.windows.net | 9350 à 9354 | Système Service Bus Relay facultatif via TCP || *. core.windows.net | 443 | HTTPS || *. clouddatahub.net | 443 | HTTPS || Graph.Windows.NET | 443 | HTTPS || Login.Windows.NET | 443 | HTTPS | 

Au niveau du pare-feu Windows, ces ports de sortie sont normalement activés. Sinon, vous pouvez configurer en conséquence les domaines et les ports sur l’ordinateur de passerelle.

### Définition des informations d’identification
Le port d’entrée **8050** est utilisé par l’application de **configuration des informations d’identification** pour relayer les informations d’identification à la passerelle au moment où vous configurez un service lié local dans le portail Azure (voir détails ci-après). Lors de l’installation de la passerelle, l’installation de la passerelle de gestion des données ouvre cette dernière par défaut sur l’ordinateur de passerelle.
 
En cas d’utilisation d’un pare-feu tiers, vous pouvez ouvrir manuellement le port 8050. Si vous rencontrez des problèmes de pare-feu lors de l’installation de la passerelle, vous pouvez essayer d’utiliser la commande suivante pour installer la passerelle sans configurer le pare-feu.

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Si vous préférez ne pas ouvrir le port 8050 sur l’ordinateur de passerelle et que vous souhaitez configurer un service lié local, vous devez utiliser d’autres mécanismes que l’application de **configuration des informations d’identification** pour pouvoir configurer les informations d’identification du magasin de données. Vous pouvez par exemple utiliser l’applet de commande PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Consultez la section [Configuration des informations d’identification et de la sécurité](#set-credentials-and-securityy) pour savoir comment configurer les informations d’identification du magasin de données.

**Pour copier des données d’un magasin de données source vers un magasin de données récepteur :**

Vous devez vous assurer que les règles de pare-feu sont correctement activées sur le pare-feu d’entreprise, sur le pare-feu Windows de l’ordinateur de passerelle, ainsi que sur le magasin de données lui-même. Cela permet à la passerelle de se connecter correctement à la source et au récepteur. Vous devez activer les règles pour chaque magasin de données impliqué dans l’opération de copie.

Par exemple, pour effectuer une copie entre **un magasin de données local et un récepteur de base de données SQL Azure ou un récepteur Azure SQL Data Warehouse**, vous devez autoriser le trafic **TCP** sortant sur le port **1433** pour le pare-feu Windows et le pare-feu d’entreprise. Vous devez aussi configurer les paramètres de pare-feu du serveur SQL Azure pour ajouter l’adresse IP de l’ordinateur de passerelle à la liste des adresses IP autorisées.

### Considérations relatives aux serveurs proxy
Par défaut, la passerelle de gestion des données utilisera les paramètres de proxy d’Internet Explorer et utilisera les informations d’identification par défaut pour y accéder. Si vous avez besoin de modifier ces paramètres, vous pouvez configurer les **paramètres du serveur proxy** comme indiqué ci-dessous pour vous assurer que la passerelle sera en mesure de se connecter à Azure Data Factory :

1.	Après avoir installé la passerelle de gestion des données, dans l’Explorateur de fichiers, effectuez une copie de sauvegarde de « C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config » pour sauvegarder le fichier d’origine.
2.	Lancez Notepad.exe en tant qu’administrateur, puis ouvrez le fichier texte « C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config ». La balise par défaut pour system.net apparaît comme suit :

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	Vous pouvez ensuite ajouter les détails du serveur proxy (par exemple, l’adresse du proxy) à l’intérieur de cette balise parent. Par exemple :

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	Vous pouvez ajouter des propriétés supplémentaires à l’intérieur de la balise de proxy pour spécifier les paramètres requis comme scriptLocation. Reportez-vous à la page de syntaxe [<proxy>, élément (paramètres réseau)](https://msdn.microsoft.com/library/sa91de1e.aspx).

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. Enregistrez le fichier de configuration à l’emplacement d’origine, puis redémarrez le service de passerelle de gestion des données pour relever les modifications. Pour cela, utilisez le menu **Démarrer** > **Services.msc**. Sinon, à partir du **Gestionnaire de configuration de la passerelle de gestion des données**, cliquez sur le bouton **Arrêter le service**, puis sur **Démarrer le service**. Si le service ne démarre pas, il est probable qu’une syntaxe de balise XML incorrecte ait été ajoutée dans le fichier de configuration d’application que vous avez modifié.

Outre les points ci-dessus, vous devez également vous assurer que Microsoft Azure figure dans la liste d’autorisation de votre entreprise. Vous pouvez télécharger la liste des adresses IP Microsoft Azure valides à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### Symptômes possibles des erreurs liées au pare-feu et au serveur proxy :
Si vous rencontrez l’une des erreurs suivantes, cela signifie que vous avez probablement mal configuré le serveur proxy ou le pare-feu, et que la passerelle de gestion des données ne peut pas se connecter à Azure Data Factory pour s’authentifier. Reportez-vous à la section ci-dessus pour vous assurer que votre pare-feu et votre serveur proxy sont correctement configurés.

1.	Lorsque vous tentez d’inscrire la passerelle, vous recevez le message d’erreur suivant : « Nous n’avons pas pu enregistrer la clé de passerelle. Avant de réessayer d’enregistrer la clé de passerelle, vérifiez que la passerelle de gestion des données est connectée et que le service d’hébergement de la passerelle de gestion des données est en cours d’exécution. »
2.	Lorsque vous ouvrez le Gestionnaire de configuration, l’état indiqué est « Déconnecté » ou « En cours de connexion ». Lors de l’affichage de journaux d’événements Windows, sous « Observateur d’événements » > « Journaux d’applications et de services » > « Passerelle de gestion des données », des messages d’erreur apparaissent, tels que « Impossible de se connecter au serveur distant » ou « Un composant de la passerelle de gestion des données ne répond plus et va redémarrer automatiquement. Nom du composant : Passerelle. »

## Résolution des problèmes de passerelle


- Pour plus d’informations, vous pouvez consulter les journaux de la passerelle contenus dans les journaux des événements Windows. Vous les trouverez à l'aide de **l'Observateur d'événements** Windows sous **Journaux des applications et des services** > **Passerelle de gestion des données**. Lors de la résolution des problèmes liés à la passerelle, recherchez des événements de type erreur dans l’Observateur d’événements.
- Si la passerelle cesse de fonctionner lorsque vous **modifiez le certificat**, redémarrez (arrêtez et démarrez) le **service de passerelle de gestion des données** à l’aide de l’outil Gestionnaire de configuration de passerelle de gestion de données Microsoft ou l’applet Services du Panneau de configuration. Si l’erreur persiste, vous devrez peut-être attribuer des autorisations explicites à l’utilisateur du service de passerelle de gestion des données pour lui donner accès au certificat dans le Gestionnaire de certificats (certmgr.msc). Le compte d’utilisateur par défaut du service est **NT Service\\DIAHostService**. 
- Si vous rencontrez des problèmes de connexion au magasin de données ou des erreurs liées au pilote, lancez le **Gestionnaire de configuration de la passerelle de gestion de données** sur l’ordinateur de passerelle, basculez vers l’onglet **Diagnostics**, sélectionnez/saisissez les valeurs appropriées dans les champs du groupe **Tester la connexion à une source de données locale à l’aide de cette passerelle**, puis cliquez sur **Tester la connexion** pour vérifier si vous pouvez vous connecter à la source de données locale à partir de l’ordinateur de passerelle en utilisant les informations de connexion et d’identification. Si le test de connexion échoue encore après l'installation d'un pilote, redémarrez la passerelle pour récupérer les dernières modifications.  

	![Tester la connexion](./media/data-factory-move-data-between-onprem-and-cloud/TestConnection.png)
		
## Procédure pas à pas : Utilisation de la passerelle de gestion des données 
Dans cette procédure pas à pas, vous créez une fabrique de données avec un pipeline qui déplace les données d’une base de données SQL Server locale vers un objet blob Azure.

### Étape 1 : Création d’une fabrique de données Microsoft Azure
Dans cette étape, vous allez utiliser le portail Azure pour créer une instance Azure Data Factory nommée **ADFTutorialOnPremDF**. Vous pouvez également créer une fabrique de données à l’aide des applets de commande Azure Data Factory.

1.	Une fois que vous êtes connecté au [portail Azure](https://portal.azure.com), cliquez dans le coin inférieur gauche sur **NOUVEAU**, sélectionnez **Analyse de données** dans le panneau **Créer**, puis cliquez sur **Fabrique de données** dans le panneau **Analyse de données**.

	![Nouveau -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. Dans le panneau **Nouvelle fabrique de données** :
	1. Saisissez **ADFTutorialOnPremDF** dans le champ **Nom**.
	2. Cliquez sur **RESOURCE GROUP NAME** et sélectionnez **ADFTutorialResourceGroup**. Vous pouvez sélectionner un groupe de ressources existant ou en créer un. Pour créer un groupe de ressources :
		1. Cliquez sur **Créer un groupe de ressources**.
		2. Dans le panneau **Créer un groupe de ressources**, saisissez un **nom** pour le groupe de ressources et cliquez sur **OK**.

7. Notez que l’option **Ajouter au tableau d’accueil** est activée sur le panneau **Nouvelle fabrique de données**.

	![Ajouter au Tableau d’accueil](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. Dans le panneau **Nouvelle fabrique de données**, cliquez sur **Créer**.

	Le nom de la fabrique de données Azure doit être un nom global unique. Si l'erreur suivante s'affiche : **Le nom de la fabrique de données « ADFTutorialOnPremDF » n'est pas disponible**, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialOnPremDF), puis essayez de la recréer. Utilisez ce nom à la place d'ADFTutorialOnPremDF quand vous effectuez les étapes restantes de ce didacticiel.

9. Recherchez des notifications à partir du processus de création en cliquant sur le bouton **Notifications** sur la barre de titre, comme indiqué dans l’image suivante. Cliquez dessus de nouveau pour fermer la fenêtre de notifications.

	![Hub NOTIFICATIONS](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. Une fois la création terminée, vous verrez apparaître le panneau **Data Factory** comme indiqué ci-dessous :

	![Page d’accueil Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### Étape 2 : création d’un portail de gestion des données
5. Dans le panneau **DATA FACTORY**, cliquez sur la vignette **Créer et déployer** pour lancer l’**éditeur** de la fabrique de données.

	![Vignette Créer et déployer](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.	Dans Data Factory Editor, cliquez sur **... (points de suspension)** sur la barre d’outils, puis cliquez sur **Nouvelle passerelle de données**. 

	![Nouvelle passerelle de données sur la barre d’outils](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. Dans le panneau **Créer**, saisissez **adftutorialgateway** dans le champ **Nom**, puis cliquez sur **OK**. 	

	![Panneau Créer une passerelle](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. Dans le panneau **Configurer**, cliquez sur **Installer directement sur cet ordinateur**. Cette opération télécharge le package d’installation de la passerelle, installe, configure et inscrit la passerelle sur l’ordinateur.

	> [AZURE.NOTE] 
	Utilisez Internet Explorer ou un navigateur web compatible Microsoft ClickOnce.
	> 
	> Si vous utilisez Chrome, accédez à [Chrome Web Store](https://chrome.google.com/webstore/), faites une recherche sur le mot-clé « ClickOnce», choisissez l’une des extensions ClickOnce, puis installez-la.
	>  
	> Vous devez en faire autant pour Firefox (installez un complément). Cliquez sur le bouton du **menu** dans la barre d’outils (**trois lignes horizontales** en haut à droite), cliquez sur **Modules complémentaires**, effectuez une recherche avec le mot-clé « ClickOnce », choisissez l’une des extensions de ClickOnce et installez le programme.

	![Passerelle - Panneau Configurer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	Il s’agit de la méthode la plus simple (un clic) pour télécharger, installer, configurer et inscrire la passerelle en une seule étape. Vous pouvez voir que l’application **Gestionnaire de configuration de la passerelle de gestion de données Microsoft** est installée sur votre ordinateur. Vous pouvez également trouver l’exécutable **ConfigManager.exe** dans le dossier suivant : **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.

	Vous pouvez également télécharger et installer manuellement la passerelle en utilisant les liens de ce panneau et l’enregistrer à l’aide de la clé indiquée dans la zone de texte **ENREGISTRER AVEC LA CLÉ**.
	
	Pour en savoir plus sur la passerelle, y compris les meilleures pratiques et des remarques importantes, reportez-vous au sections du début de cet article.

	>[AZURE.NOTE] Vous devez être administrateur sur l’ordinateur local pour pouvoir installer et configurer la passerelle de gestion des données avec succès. Vous pouvez ajouter des utilisateurs supplémentaires au groupe Windows local d’utilisateurs de la passerelle de gestion des données. Les membres de ce groupe sont en mesure d’utiliser l’outil Gestionnaire de configuration de la passerelle de gestion de données pour configurer la passerelle.

5. Patientez deux minutes, puis lancez l’application **Gestionnaire de configuration de la passerelle de gestion de données** sur votre ordinateur. Dans la fenêtre **Rechercher**, saisissez **passerelle de gestion de données** pour accéder à cet utilitaire. Vous pouvez également trouver l’exécutable **ConfigManager.exe** dans le dossier suivant : **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.

	![Gestionnaire de configuration de la passerelle](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. Attendez que les valeurs soient définies comme suit :
	1. **État** est défini sur **Démarré**.
	2. Le **nom de la passerelle** est défini sur **adftutorialgateway**.
	3. Le **nom de l’instance** est défini sur **adftutorialgateway**.
	4. **Inscription** est défini sur **Inscrit**.
	5. La barre d’état située au bas de l’écran affiche le message **Connecté au service de cloud de la passerelle de gestion de données** accompagné d’une **coche verte**.

8. Basculez sur l’onglet **Certificats**. Le certificat spécifié dans cet onglet sert à chiffrer/déchiffrer les informations d'identification pour le magasin de données local que vous spécifiez dans le portail. Cliquez sur **Modifier** pour utiliser votre propre certificat à la place. Par défaut, la passerelle utilise le certificat généré automatiquement par le service Data Factory.

	![Configuration de certificat de la passerelle](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)
9. (Facultatif) Basculez sur l’onglet **Diagnostics**, cochez l’option **Activer l’enregistrement des informations détaillées pour résoudre des problèmes** si vous souhaitez activer la journalisation détaillée à utiliser pour résoudre les problèmes de passerelle. Vous trouverez les informations de journalisation dans l’**Observateur d’événements** sous le nœud **Journaux des applications et des services** -> **Passerelle de gestion des données**. 

	![Onglet Diagnostic](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	Vous pouvez également utiliser cette page pour **tester la connexion** à une source de données locale à l’aide de la passerelle.
10. Dans le portail Azure, cliquez sur **OK** dans le panneau **Configurer**, puis dans le panneau **Nouvelle passerelle de données**.
6. Vous devez voir **adftutorialgateway** sous **Passerelles de données** dans l’arborescence de gauche. Si vous cliquez dessus, vous devez voir le code JSON associé. 
	

### Étape 3 : créer des services liés 
Dans cette étape, vous allez créer deux services liés, **AzureStorageLinkedService** et **SqlServerLinkedService**. Le service **SqlServerLinkedService** associe une base de données SQL Server locale, et le service lié **AzureStorageLinkedService** associe un magasin d’objets blob Azure à la fabrique de données. Plus loin dans cette procédure pas à pas, vous allez créer un pipeline qui copie les données de la base de données SQL Server locale vers le magasin d’objets blob Azure.

#### Ajout d’un service lié à une base de données SQL Server locale
1.	Dans **Data Factory Editor**, cliquez sur **Nouvelle banque de données** sur la barre d’outils, puis sélectionnez **SQL Server**. 

	![Nouveau service lié SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.	Dans l’**éditeur JSON**, procédez comme suit : 
	1. Pour **gatewayName**, spécifiez **adftutorialgateway**.	
	2. Si vous utilisez l’authentification Windows, procédez comme suit :
		1. Pour **connectionString** : 
			1. Définissez le paramètre **Sécurité intégrée** sur **true**.
			2. Spécifiez le **nom du serveur** et le **nom de la base de données**. 
			2. Supprimez **ID utilisateur** et **Mot de passe**. 
		3. Spécifiez le nom d’utilisateur et le mot de passe pour les propriétés **userName** et **password**.
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication>",
            		"password": "<Specify password for the user account>"
        		}

	4. Si vous utilisez l’authentification SQL, procédez comme suit :
		1. Spécifiez le **nom du serveur**, le **nom de la base de données**, ainsi que les paramètres **User ID** et **Password** de la base de données dans **connectionString**.       
		2. Supprimez les deux dernières propriétés JSON (**userName** et **password**) du code JSON.
		3. Supprimez le caractère de fin **, (virgule)** à la fin de la ligne spécifiant la valeur de la propriété **gatewayName** 

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	
		Les informations d’identification sont **chiffrées** à l’aide d’un certificat détenu par le service Data Factory. Si vous voulez plutôt utiliser le certificat qui est associé à la passerelle de gestion des données, consultez [Définir les informations d’identification de manière sécurisée](#set-credentials-and-security).
    
2.	Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié SQL Server.

#### Ajout d’un service lié pour un compte de stockage Azure
 
1. Dans **Data Factory Editor**, dans la barre de commandes, cliquez sur **Nouvelle banque de données**, puis sur **Azure Storage**.
2. Entrez le nom de votre compte de stockage Azure dans le champ **Nom du compte**.
3. Entrez la clé de votre compte de stockage Azure dans le champ **Clé du compte**.
4. Cliquez sur l’option **Déployer** pour déployer le service lié **AzureStorageLinkedService**.
   
 
### Étape 4 : créer des jeux de données d’entrée et sortie
Dans cette étape, vous allez créer des jeux de données d’entrée et de sortie qui représentent les données d’entrée et de sortie pour l’opération de copie (base de données SQL Server locale = > stockage d’objets blob Azure). Avant de créer des jeux de données ou des tables (jeux de données rectangulaires), vous devez effectuer les opérations suivantes (les étapes sont détaillées après la liste) :

- Créez une table nommée **emp** dans la base de données SQL Server que vous avez ajoutée en tant que service lié à la fabrique de données et insérez quelques exemples d’entrées dans la table.
- Créez un conteneur d’objets blobs nommé **adftutorial** dans le compte de stockage d’objets blobs Azure que vous avez ajouté en tant que service associé à la fabrique de données.

### Préparation du serveur SQL Server local pour le didacticiel

1. Dans la base de données que vous avez spécifiée pour le service lié SQL Server local (**SqlServerLinkedService**), utilisez le script SQL suivant pour créer la table **emp** dans la base de données.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. Insérer des exemples dans la table :


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### Créer une table d’entrée

1. Dans **Data Factory Editor**, dans la barre de commande, cliquez sur **Nouveau jeu de données**,puis sur **Table SQL Server**. 
2.	Remplacez le code JSON du volet droit par le texte suivant :    

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	Notez les points suivants :
	
	- Le **type** est défini sur **SqlServerTable**.
	- Le paramètre **tablename** est défini sur **emp**.
	- Le paramètre **linkedServiceName** est défini sur **SqlServerLinkedService** (vous avez créé ce service lié à l’étape 2).
	- Dans le cas d’une table d’entrée qui n’est pas générée par un autre pipeline dans Azure Data Factory, vous devez définir **external** sur **true**. Cela signifie que les données d’entrée sont produites à l’extérieur du service Azure Data Factory. Vous pouvez éventuellement spécifier des stratégies de données externes à l’aide de l’élément **externalData** dans la section **Policy**.    

	Pour plus d'informations sur les propriétés JSON, consultez la rubrique [Référence de script JSON][json-script-reference].

2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le jeu de données (la table est un jeu de données rectangulaire). Vérifiez que le message **TABLE DÉPLOYÉE AVEC SUCCÈS** s’affiche dans la barre de titre.


### Créer la table de sortie

1.	Dans **Data Factory Editor**, cliquez sur **Nouveau jeu de données** dans la barre de commandes, puis sur **Stockage d’objets blob Azure**.
2.	Remplacez le code JSON du volet droit par le texte suivant : 

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	Notez les points suivants :
	
	- **type** est défini sur **AzureBlob**.
	- Le paramètre **linkedServiceName** est défini sur **AzureStorageLinkedService** (vous avez créé ce service lié à l’étape 2).
	- Le paramètre **folderPath** est défini sur **adftutorial/outfromonpremdf**, où « outfromonpremdf » est le dossier dans le conteneur adftutorial. Vous devez simplement créer le conteneur **adftutorial**.
	- **availability** est défini sur **hourly** (**frequency** a la valeur **hour** et **interval** est défini sur **1**). Le service Data Factory génère une tranche de données de sortie toutes les heures dans la table **emp** de la base de données SQL Microsoft Azure. 

	Si vous ne spécifiez pas de nom **fileName** pour une table d’**entrée**, tous les fichiers/objets blobs du dossier d’entrée (**folderPath**) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. Pour en savoir plus, consultez les fichiers d'exemple du [didacticiel][adf-tutorial].
 
	Si vous ne spécifiez pas de **fileName** pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

	Pour affecter une valeur aux paramètres **folderPath** et **fileName** de manière dynamique, en fonction de l’heure de **SliceStart**, utilisez la propriété partitionedBy. Dans l’exemple suivant, folderPath utilise les valeurs Year, Month et Day à partir de SliceStart (heure de début de la partie en cours de traitement), alors que fileName utilise la valeur Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2014-10-20T08:00:00, la valeur folderName est wikidatagateway/wikisampledataout/2014/10/20, alors que la valeur de fileName est 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Pour plus d'informations sur les propriétés JSON, consultez la rubrique [Référence de script JSON][json-script-reference].

2.	Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le jeu de données (la table est un jeu de données rectangulaire). Vérifiez que le message **TABLE DÉPLOYÉE AVEC SUCCÈS** s’affiche dans la barre de titre.
  

### Étape 5 : créer et exécuter un pipeline
Dans cette étape, vous créez un **pipeline** avec une **activité Copier l’activité** qui utilise **EmpOnPremSQLTable** en tant qu’entrée et **OutputBlobTable** en tant que sortie.

1.	Dans le panneau **DATA FACTORY**, cliquez sur la vignette **Créer et déployer** pour lancer l’**éditeur** de la fabrique de données.

	![Vignette Créer et déployer](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
2.	Cliquez sur **Nouveau pipeline** dans la barre de commandes. Si le bouton ne s’affiche pas, cliquez sur **... (points de suspension)** pour développer la barre de commandes.
2.	Remplacez le code JSON du volet droit par le texte suivant :   


		{
		  "name": "ADFTutorialPipelineOnPrem",
		  "properties": {
		    "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    "activities": [
		      {
		        "name": "CopyFromSQLtoBlob",
		        "description": "Copy data from on-prem SQL server to blob",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpOnPremSQLTable"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "OutputBlobTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "SqlSource",
		            "sqlReaderQuery": "select * from emp"
		          },
		          "sink": {
		            "type": "BlobSink"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	Notez les points suivants :
 
	- Dans la section des activités, toutes les activités ont le **type** **Copy**.
	- L’**entrée** de l’activité est définie sur **EmpOnPremSQLTable** et la **sortie** de l’activité, sur **OutputBlobTable**.
	- Dans la section **Transformation**, le paramètre **SqlSource** est spécifié en tant que **type de source**, et **BlobSink** en tant que **type sink**.
- La requête SQL **select * from emp** est spécifiée pour la propriété **sqlReaderQuery** de **SqlSource**.

	Remplacez la valeur de la propriété **start** par le jour actuel et la valeur **end**, par le jour suivant. Les dates/heures de début et de fin doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de fin (**end**) est facultative, mais nous allons l’utiliser dans ce didacticiel.
	
	Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « **start + 48 heures** ». Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** comme valeur pour la propriété **end**.
	
	En spécifiant la période active pour un pipeline, vous définissez la durée pendant laquelle les tranches de données seront traitées, selon les propriétés de **disponibilité** qui ont été définies pour chaque table Azure Data Factory.
	
	Dans l'exemple ci-dessus, 24 tranches de données existent, car une tranche de données est générée toutes les heures.
	
2. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le jeu de données (la table est un jeu de données rectangulaire). Vérifiez que le message **PIPELINE DÉPLOYÉ AVEC SUCCÈS** s’affiche dans la barre de titre.
5. Maintenant, fermez le panneau **Éditeur** en cliquant sur **X**. Cliquez de nouveau sur **X** pour fermer le panneau ADFTutorialDataFactory avec la vue d’arborescence et la barre d’outils. Si le message **Vos modifications non enregistrées seront ignorées** s’affiche, cliquez sur **OK**.
6. Vous devez être revenu au panneau **DATA FACTORY** pour **ADFTutorialOnPremDF**.

**Félicitations !** Vous avez correctement créé une fabrique de données Azure, les services liés, les tables et un pipeline et avez planifié le pipeline.

#### Affichage d’une vue schématique d’une fabrique de données 
1. Dans le **portail Azure**, cliquez sur la vignette **Diagramme** sur la page d’accueil de la fabrique de données **ADFTutorialOnPremDF**.

	![Lien Diagramme](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Le diagramme ressemble à ce qui suit :

	![Vue schématique](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	Vous pouvez faire un zoom avant, un zoom arrière, un zoom à 100 %, un zoom pour ajuster, positionner automatiquement les pipelines et les tables, et afficher les informations de lignage (mise en surbrillance des éléments en amont et en aval des éléments sélectionnés). Vous pouvez double-cliquer sur un objet (table ou pipeline d'entrée/de sortie) pour afficher les propriétés associées.

### Étape 6 : surveiller les jeux de données et les pipelines
Dans cette étape, vous allez utiliser le portail Azure pour surveiller ce qui se passe dans une fabrique de données Azure. Vous pouvez également utiliser les applets de commande PowerShell pour surveiller les jeux de données et les pipelines. Pour plus de détails sur la surveillance, consultez [Surveillance et gestion des pipelines](data-factory-monitor-manage-pipelines.md).

1. Ouvrez le **portail Azure** (si vous l’avez fermé).
2. Si le panneau **ADFTutorialOnPremDF** n’est pas ouvert, ouvrez-le en cliquant sur **ADFTutorialOnPremDF** dans le **tableau d’accueil**.
3. Vous devez voir le **nombre** et les **noms** des tables et du pipeline que vous avez créés dans ce panneau.

	![Page d’accueil Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. Cliquez maintenant sur la vignette **Jeux de données**.
5. Dans le panneau **Jeux de données**, cliquez sur **EmpOnPremSQLTable**.

	![Tranches EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Notez que les tranches de données jusqu’à l’heure actuelle ont déjà été produites et ont l’état **Prêt**. Cela est dû au fait que vous avez inséré les données dans la base de données SQL Server et qu’elles y sont tout le temps. Vérifiez qu’aucune tranche n’apparaît dans la section **Tranches problématiques**, sur la partie inférieure de la fenêtre.


	Les listes **Tranches récemment mises à jour** et **Tranches ayant échoué récemment** sont triées en fonction de l’**HEURE DE LA DERNIÈRE MISE À JOUR**. L’heure de mise à jour d’une tranche est modifiée dans les situations suivantes.
    

	-  Vous mettez à jour l’état de la tranche manuellement, par exemple à l’aide de **Set-AzureRmDataFactorySliceStatus** (ou) en cliquant sur **EXÉCUTER** dans le panneau **TRANCHE** de la tranche.
	-  La tranche change d’état en raison d’une exécution (par exemple, une exécution a démarré, une exécution s’est terminée et a échoué, une exécution s’est terminée et a réussi, etc.).
 
	Cliquez sur le titre des listes ou sur **... (points de suspension)** pour consulter la liste plus complète des tranches. Cliquez sur l’option **Filtre** de la barre d’outils pour filtrer les tranches.
	
	Pour afficher les tranches de données triées en fonction des heures de début et de fin de la tranche, cliquez sur la vignette **Tranches de données (par heure de tranche)**.

7. Dans le panneau **Jeux de données**, cliquez sur **OutputBlobTable**.

	![Tranches OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Vérifiez que les tranches jusqu’à l’heure actuelle sont produites et à l’état **Prêt**. Attendez que les états des tranches jusqu’à l’heure actuelle soient définis sur **Prêt**.
9. Cliquez sur une tranche de données dans la liste pour afficher le panneau **TRANCHE DE DONNÉES**.

	![Panneau Tranche de données](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	Si la tranche n’a pas l’état **Prêt**, vous pouvez afficher les tranches en amont qui ne sont pas prêtes et qui empêchent l’exécution de la tranche actuelle dans la liste **Tranches en amont qui ne sont pas prêtes**.

10. Cliquez sur l’**exécution d’activité** dans la liste de la partie inférieure de la fenêtre pour afficher les **détails sur l’exécution d’activité**.

	![Panneau Détails sur l’exécution d’activité][image-data-factory-activity-run-details]

11. Cliquez sur **X** pour fermer tous les panneaux jusqu’à ce que vous
12. reveniez au panneau d’accueil de l’élément **ADFTutorialOnPremDF**.
14. (facultatif) Cliquez sur **Pipelines**, sur **ADFTutorialOnPremDF**, puis accédez aux tables d’entrée (**Consommé**) ou aux tables de sortie (**Produit**).
15. Utilisez des outils tels que l’**Explorateur de stockage Azure** pour contrôler la sortie.

	![Azure Storage Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## Déplacement d’une passerelle d’une machine vers une autre
Cette section décrit les opérations pour déplacer une passerelle client d’une machine vers une autre.

2. Dans le portail, accédez à la **page d’accueil de Data Factory**, puis cliquez sur la vignette **Services liés**. 

	![Lien de passerelles de données](./media/data-factory-move-data-between-onprem-and-cloud/DataGatewaysLink.png) 
3. Sélectionnez votre passerelle dans la section **PASSERELLES DONNÉES** du panneau **Services liés**.
	
	![Panneau Services liés avec passerelle sélectionnée](./media/data-factory-move-data-between-onprem-and-cloud/LinkedServiceBladeWithGateway.png)
4. Dans le panneau **Passerelle de données**, cliquez sur **Télécharger et installer la passerelle de données**.
	
	![Télécharger le lien de passerelle](./media/data-factory-move-data-between-onprem-and-cloud/DownloadGatewayLink.png) 
5. Dans le panneau **Configurer**, cliquez sur **Télécharger et installer la passerelle de données**, puis suivez les instructions pour installer la passerelle de données sur la machine. 

	![Panneau Configurer](./media/data-factory-move-data-between-onprem-and-cloud/ConfigureBlade.png)
6. Laissez le **Gestionnaire de configuration de la passerelle de gestion des données** ouvert. 
 
	![Gestionnaire de configuration](./media/data-factory-move-data-between-onprem-and-cloud/ConfigurationManager.png)	
7. Dans le panneau **Configurer** du portail, cliquez sur **Recréer une clé** dans la barre de commandes, puis, au message d’avertissement, cliquez sur **Oui**. Cliquez sur le **bouton de copie** en regard du texte de la clé pour copier la clé dans le presse-papiers. Notez que la passerelle de l’ancienne machine cesse de fonctionner dès que vous recréez la clé.  
	
	![Recréer la clé](./media/data-factory-move-data-between-onprem-and-cloud/RecreateKey.png)
	 
8. Collez la **clé** dans la zone de texte de la page d’**enregistrement de passerelle** du **Gestionnaire de configuration de la passerelle de gestion de données** sur votre ordinateur. (Facultatif) Cochez la case **Afficher la clé de passerelle** pour afficher le texte de la clé.
 
	![Copier la clé et s’inscrire](./media/data-factory-move-data-between-onprem-and-cloud/CopyKeyAndRegister.png)
9. Cliquez sur **S’inscrire** pour enregistrer la passerelle auprès du service cloud.
10. Dans la page **Spécifier le certificat**, cliquez sur **Parcourir** pour sélectionner le certificat utilisé avec l’ancienne passerelle, saisissez le **mot de passe**, puis cliquez sur **Terminer**. 
 
	![Spécifier le certificat](./media/data-factory-move-data-between-onprem-and-cloud/SpecifyCertificate.png)

	Vous pouvez exporter un certificat à partir de l’ancienne passerelle en procédant comme suit : lancez le Gestionnaire de configuration de passerelle de gestion des données sur l’ancien panneau, basculez vers l’onglet **certificat** cliquez sur le bouton **Exporter** et suivez les instructions. 
10. Une fois l’inscription de la passerelle terminée, vous devez voir **Inscription** définie sur **Inscrit** et **État** sur la valeur **Démarré** de la page d’accueil du Gestionnaire de configuration de passerelle. 

## Configuration des informations d’identification et de la sécurité
Pour chiffrer les informations d’identification dans Data Factory Editor, procédez comme suit :

1. Cliquez sur un **service lié** existant dans l’arborescence pour afficher sa définition JSON ou créez un autre service lié qui nécessite une passerelle de gestion des données (par exemple, SQL Server ou Oracle). 
2. Dans l’éditeur JSON, entrez le nom de la passerelle pour la propriété **gatewayName**. 
3. Entrez le nom du serveur pour la propriété **Data Source** dans **connectionString**.
4. Entrez le nom de la base de données pour la propriété **Initial Catalog** dans **connectionString**.    
5. Cliquez sur le bouton **Chiffrer** dans la barre de commandes. La boîte de dialogue **Configuration des informations d’identification** doit s’afficher. ![Boîte de dialogue des paramètres d’informations d'identification](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png)
6. Dans la boîte de dialogue **Configuration des informations d’identification**, procédez comme suit :  
	1.	Sélectionnez l’**authentification** que le service de Data Factory doit utiliser pour se connecter à la base de données. 
	2.	Entrez le nom de l’utilisateur ayant accès à la base de données dans le paramètre **USERNAME**. 
	3.	Entrez le mot de passe de l’utilisateur dans le paramètre **PASSWORD**.  
	4.	Cliquez sur **OK** pour chiffrer les informations d’identification et fermer la boîte de dialogue. 
5.	Vous devez maintenant voir une propriété **encryptedCredential** dans **connectionString**.		
		
			{
	    		"name": "SqlServerLinkedService",
		    	"properties": {
		        	"type": "OnPremisesSqlServer",
			        "description": "",
		    	    "typeProperties": {
		    	        "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
		            	"gatewayName": "adftutorialgateway"
		        	}
		    	}
			}

Si vous accédez au portail à partir d’un ordinateur différent de l’ordinateur de passerelle, vous devrez peut-être vous assurer que l’application Gestionnaire d’informations d’identification peut se connecter à l’ordinateur de passerelle. Sinon, vous ne pourrez pas définir les informations d’identification de la source de données, ni tester la connexion à la source de données.

Quand vous utilisez l’application **Configuration des informations d’identification** lancée à partir du portail Azure pour définir les informations d’identification d’une source de données locale, le portail chiffre les informations d’identification avec le certificat que vous avez spécifié sous l’onglet **Certificat** du **Gestionnaire de configuration de la passerelle de gestion des données** sur l’ordinateur de la passerelle.

Si vous recherchez une approche basée sur une API pour chiffrer les informations d’identification, vous pouvez utiliser l’applet de commande PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) pour chiffrer les informations d’identification. L'applet de commande utilise le certificat qui a servi à configurer la passerelle pour chiffrer les informations d'identification. Vous pouvez alors chiffrer les informations d’identification retournées par cette applet de commande et les ajouter à l’élément **EncryptedCredential** de **connectionString** dans le fichier JSON que vous utiliserez avec l’applet de commande [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) ou dans l’extrait de code JSON dans Data Factory Editor dans le portail.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

Il existe une autre approche pour définir les informations d’identification à l’aide de Data Factory Editor. Si vous créez un service SQL Server lié à l’aide de l’éditeur et entrez les informations d’identification en texte brut, ces informations d’identification sont chiffrées à l’aide d’un certificat appartenant au service Data Factory et NON le certificat qui a servi à configurer la passerelle. Bien que cette approche puisse être un peu plus rapide dans certains cas, elle reste moins sécurisée. Par conséquent, nous vous recommandons de suivre cette approche uniquement à des fins de développement/test.


## Créer et inscrire la passerelle de gestion des données à l’aide d’Azure PowerShell 
Cette section décrit comment créer et enregistrer une passerelle à l’aide des applets de commande Azure PowerShell.

1. Lancez **Azure PowerShell** en mode administrateur. 
2. Connectez-vous à votre compte Azure en exécutant la commande suivante et en entrant vos informations d’identification Azure. 

	Login-AzureRmAccount
2. Utilisez l’applet de commande **New-AzureRmDataFactoryGateway** pour créer une passerelle logique, comme suit :

		$MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Exemple de commande et de sortie** :


		PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded
		Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI

	
4. Dans Azure PowerShell, accédez au dossier **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** et exécutez le script **RegisterGateway.ps1** associé à la variable locale **$Key**, comme indiqué dans la commande suivante pour enregistrer l’agent client installé sur votre ordinateur avec la passerelle logique que vous avez créée précédemment.

		PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
		
		Agent registration is successful!

	Vous pouvez inscrire la passerelle sur un ordinateur distant en utilisant le paramètre IsRegisterOnRemoteMachine. Exemple :
		
		.\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true

5. Vous pouvez utiliser l’applet de commande **Get-AzureRmDataFactoryGateway** pour obtenir la liste des passerelles dans votre fabrique de données. Lorsque l’**état** est **online**, cela signifie que votre passerelle est prête.

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Vous pouvez supprimer une passerelle à l’aide de l’applet de commande **Remove-AzureRmDataFactoryGateway** et mettre à jour la description de la passerelle en utilisant les applets de commande **Set-AzureRmDataFactoryGateway**. Pour obtenir la syntaxe et d’autres détails sur ces applets de commande, consultez la rubrique Référence des applets de commande Azure Data Factory.

## Répertorier les passerelles à l’aide de PowerShell

	Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup

## Supprimer une passerelle à l’aide de PowerShell
	
	Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force 


## Flux de données pour la copie à l’aide de la passerelle de gestion des données
Lorsque vous utilisez une activité de copie dans un pipeline de données pour recevoir des données locales vers le cloud en vue d’un traitement ultérieur, ou exportez les données de résultat du cloud vers un magasin de données local, l'activité de copie utilise en interne une passerelle pour transférer les données de la source de données locale vers le cloud et vice versa.

Voici un flux de données global et un résumé des étapes pour la copie à l’aide de la passerelle de données : ![Flux de données à l'aide de la passerelle](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	Le développeur des données crée une passerelle pour une fabrique de données Azure à l’aide du [portail Azure](https://portal.azure.com)ou d’une [applet de commande PowerShell](https://msdn.microsoft.com/library/dn820234.aspx). 
2.	Le développeur des données utilise le panneau « Services liés » afin de définir un nouveau service lié pour un magasin de données local à l’aide de la passerelle. Dans le cadre de la configuration du service lié, le développeur des données utilise l'application de configuration des informations d’identification comme indiqué dans la procédure étape par étape pour spécifier les types d'authentification et les informations d’identification. La boîte de dialogue de l’application de configuration des informations d'identification communiquera avec le magasin de données pour tester la connexion et la passerelle afin d’enregistrer les informations d'identification.
3.	La passerelle chiffrera les informations d'identification avec le certificat associé à la passerelle (fourni par le développeur des données) avant d'enregistrer les informations d'identification dans le cloud.
4.	Le service de déplacement de Data Factory communique avec la passerelle pour la planification et la gestion des tâches via un canal de contrôle qui utilise une file d'attente de bus de service Azure partagé. Lorsque la tâche de l’activité de copie doit être lancée, Data Factory place en file d'attente la requête ainsi que les informations d'identification. La passerelle lance la tâche après avoir interrogé la file d'attente.
5.	La passerelle déchiffre les informations d'identification avec le même certificat puis se connecte au magasin de données local avec le type d'authentification approprié.
6.	La passerelle copie les données du magasin local vers un stockage cloud, ou d'un stockage cloud vers un magasin de données local selon la configuration de l'activité de copie dans le pipeline de données. Remarque : pour cette étape, la passerelle communique directement avec le service de stockage basé sur le cloud (par exemple, Azure Blob, SQL Azure) via un canal sécurisé (HTTPS).

<!---HONumber=AcomDC_0316_2016-->