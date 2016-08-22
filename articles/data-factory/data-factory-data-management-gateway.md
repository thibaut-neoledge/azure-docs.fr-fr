<properties 
	pageTitle="Passerelle de gestion des données pour Data Factory | Microsoft Azure"
	description="Mettez en place une passerelle de données pour déplacer vos données entre un emplacement local et le cloud. Utilisez la passerelle de gestion des données dans Azure Data Factory pour déplacer vos données." 
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
	ms.date="08/05/2016" 
	ms.author="spelluru"/>

# Passerelle de gestion de données
La passerelle de gestion des données est un agent client que vous devez installer dans votre environnement local pour permettre le déplacement des données entre le cloud et les magasins de données locaux qui sont [pris en charge par l’activité de copie](data-factory-data-movement-activities.md##supported-data-stores).

Cet article complète le contenu de l’article [Déplacement des données entre des sources locales et le cloud](data-factory-move-data-between-onprem-and-cloud.md), qui présente la procédure de création d’un pipeline Data Factory utilisant la passerelle pour déplacer les données d’une base de données SQL Server locale vers un objet blob Azure. Cet article fournit des informations détaillées sur la passerelle de gestion des données.

## Vue d'ensemble

### Fonctionnalités de la passerelle de gestion de données
La passerelle de gestion des données offre les fonctionnalités suivantes :

- Modélisation des sources de données locales et des sources de données sur le cloud au sein de la même fabrique de données, et déplacement des données.
- Un point unique de surveillance et de gestion avec une visibilité de l’état de la passerelle à partir du panneau Data Factory.
- Gestion sécurisée de l’accès aux sources de données locales.
	- Aucune modification du pare-feu d’entreprise n’est requise. La passerelle établit uniquement des connexions HTTP sortantes pour l’accès à Internet.
	- Chiffrement des informations d’identification pour vos magasins de données locaux à l’aide de votre certificat.
- Déplacer les données efficacement : les données sont transférées en parallèle et résistent aux problèmes intermittents du réseau, grâce à la logique de nouvelle tentative automatique.

### Flux de commandes et flux de données
Lorsque vous utilisez une activité de copie dans un pipeline de données pour recevoir des données locales vers le cloud en vue d’un traitement ultérieur, ou exportez les données de résultat du cloud vers un magasin de données local, l'activité de copie utilise en interne une passerelle pour transférer les données de la source de données locale vers le cloud et vice versa.

Voici un flux de données global et un résumé des étapes pour la copie à l’aide de la passerelle de données : ![Flux de données à l'aide de la passerelle](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1.	Le développeur des données crée une passerelle pour une fabrique de données Azure à l’aide du [portail Azure](https://portal.azure.com)ou d’une [applet de commande PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2.	Le développeur de données crée un service lié pour un magasin de données local en spécifiant la passerelle. Dans le cadre de la configuration du service lié, le développeur des données utilise l’application de configuration des informations d’identification pour spécifier les types d’authentification et les informations d’identification. La boîte de dialogue de l’application de configuration des informations d’identification communique avec le magasin de données pour tester la connexion et la passerelle afin d’enregistrer les informations d’identification.
3. La passerelle chiffre les informations d’identification avec le certificat associé à la passerelle (fourni par le développeur des données) avant d’enregistrer les informations d’identification dans le cloud.
4. Le service Data Factory communique avec la passerelle pour la planification et la gestion des tâches via un canal de contrôle qui utilise une file d’attente Azure Service Bus partagée. Lorsqu’une tâche de l’activité de copie doit être lancée, Data Factory place en file d’attente la requête ainsi que les informations d’identification. La passerelle lance la tâche après avoir interrogé la file d'attente.
5.	La passerelle déchiffre les informations d’identification avec le même certificat puis se connecte au magasin de données local avec le type d’authentification et les informations d’identification appropriés.
6.	La passerelle copie les données du magasin local vers un stockage cloud, ou d'un stockage cloud vers un magasin de données local selon la configuration de l'activité de copie dans le pipeline de données. Remarque : pour cette étape, la passerelle communique directement avec le service de stockage basé sur le cloud (par exemple, Azure Blob, SQL Azure) via un canal sécurisé (HTTPS).

### Considérations relatives à l’utilisation de la passerelle
- Une instance unique de la passerelle de gestion de données peut être utilisée pour plusieurs sources de données locales, mais notez qu’une **seule instance de passerelle est liée à une seule fabrique de données Microsoft Azure** et ne peut pas être partagée avec une autre fabrique de données.
- Vous ne pouvez installer qu’**une seule instance de la passerelle de gestion de données** sur un même ordinateur. Si deux fabriques de données doivent accéder aux sources de données locales, vous devez installer des passerelles sur deux ordinateurs locaux où chaque passerelle est liée à une fabrique de données distincte.
- Il n’est pas nécessaire que la **passerelle soit sur le même ordinateur que la source de données**, mais le fait qu’elle soit proche de cette dernière réduit le temps nécessaire à la passerelle pour se connecter à la source de données. Nous vous recommandons d’installer la passerelle sur un ordinateur différent de celui qui héberge la source de données locale, afin que la passerelle ne soit pas en concurrence avec la source de données pour l’octroi des ressources.
- Vous pouvez avoir **plusieurs passerelles sur différents ordinateurs connectés à la même source de données locale**. Par exemple, vous pouvez avoir deux passerelles desservant deux fabriques de données, alors que la même source de données locale est enregistrée auprès des deux fabriques de données.
- Si une passerelle est déjà installée sur votre ordinateur desservant un scénario **Power BI**, installez une **passerelle distincte pour Azure Data Factory** sur un autre ordinateur.
- Vous devez **utiliser la passerelle même lorsque vous utilisez ExpressRoute**.
- Vous devez considérer votre source de données comme une source de données locale (qui se trouve derrière un pare-feu) même lorsque vous utilisez **ExpressRoute** et **utiliser la passerelle** pour établir la connectivité entre le service et la source de données.
- Vous devez **utiliser la passerelle** même si la banque de données se trouve dans le cloud sur une **machine virtuelle IaaS Azure**.

## Installation

### Composants requis
- Les versions de **système d’exploitation** prises en charge sont Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2. L’installation de la passerelle de gestion des données sur un contrôleur de domaine n’est pas prise en charge actuellement.
- La **configuration** recommandée pour l’ordinateur de passerelle est la suivante : au moins 2 GHz, 4 cœurs, 8 Go de RAM et 80 Go d’espace disque.
- Si l’ordinateur hôte est en veille prolongée, la passerelle n’est pas en mesure de répondre à la demande de données. Vous devez donc configurer un **plan de gestion de l’alimentation** approprié sur l’ordinateur avant d’installer la passerelle. L’installation de la passerelle ouvre une invite si l’ordinateur est configuré pour la mise en veille prolongée.
- Vous devez être administrateur sur la machine pour installer et configurer la passerelle de gestion des données avec succès. Vous pouvez ajouter des utilisateurs supplémentaires au groupe Windows local **Utilisateurs de la passerelle de gestion des données**. Les membres de ce groupe sont en mesure d’utiliser l’outil Gestionnaire de configuration de la passerelle de gestion de données pour configurer la passerelle.

Étant donné que l’activité de copie s’exécute selon une fréquence spécifique, l’utilisation des ressources (processeur, mémoire) sur l’ordinateur suit également le même modèle avec des pics et des baisses d’inactivité. L'utilisation des ressources dépend également en grande partie de la quantité de données déplacées. Lorsque plusieurs tâches sont en cours, vous constaterez une augmentation des ressources utilisées pendant les heures de pointe. L’exemple ci-dessus représente la configuration minimale, et il est toujours préférable d'avoir une configuration avec plus de ressources que cette configuration minimale en fonction de votre charge spécifique pour le déplacement des données.

### Options d’installation
La passerelle de gestion des données peut être installée comme suit :

- En téléchargeant un package d’installation MSI à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Le fichier MSI peut également servir à mettre à niveau la passerelle de gestion des données existante vers la version la plus récente, en conservant tous les paramètres.
- En cliquant sur le lien **Télécharger et installer la passerelle de données** sous INSTALLATION MANUELLE ou **Installer directement sur cet ordinateur** sous INSTALLATION RAPIDE. Pour des instructions pas à pas sur l’utilisation de l’installation rapide, consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md). Lorsque vous choisissez la procédure manuelle, vous accédez au centre de téléchargement . Les instructions pour télécharger et installer la passerelle à partir du centre de téléchargement se trouvent dans la section suivante.

### Meilleures pratiques d’installation :
1.	Définissez un plan d'alimentation sur l'ordinateur hôte de la passerelle afin d’empêcher la mise en veille prolongée. Si l’ordinateur hôte est en veille prolongée, la passerelle n’est pas en mesure de répondre à la demande de données.
2.	Vous devriez sauvegarder le certificat associé à la passerelle.

### Installer la passerelle à partir du Centre de téléchargement
1. Accédez à la [page de téléchargement de la passerelle de gestion des données Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Cliquez sur **Télécharger**, sélectionnez la version appropriée (**32 bits** ou **64 bits**), puis cliquez sur **Suivant**.
3. Exécutez le **MSI** directement ou enregistrez-le sur votre disque dur avant de l’exécuter.
4. Dans la page **Bienvenue**, sélectionnez une **langue** et cliquez sur **Suivant**.
5. **Acceptez** le Contrat de Licence Utilisateur Final et cliquez sur **Suivant**.
6. Sélectionnez le **dossier** d’installation de la passerelle, puis cliquez sur **Suivant**.
7. Dans la page **Prêt pour l’installation**, cliquez sur **Installer**.
8. Cliquez sur **Terminer** pour terminer l’installation.
9. Obtenez la clé à partir du portail Azure. Pour des instructions pas à pas, consultez la section suivante.
10. Dans la page **Inscrire la passerelle** du **Gestionnaire de configuration de passerelle de gestion des données** exécuté sur votre ordinateur, collez la clé dans le texte, puis, cliquez éventuellement sur **Afficher la clé de passerelle** pour afficher le texte de la clé et cliquez sur **Inscrire**.

### Inscrire la passerelle à l’aide de la clé

#### Si vous n’avez pas déjà créé une passerelle logique dans le portail
Suivez les étapes de la procédure pas à pas de l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour créer une passerelle dans le portail et obtenir la clé à partir du panneau **Configurer**.

#### Si vous avez déjà créé la passerelle logique dans le portail
1. Dans le portail Azure, accédez au panneau **Data Factory**, puis cliquez sur la mosaïque **Services liés**.

	![Panneau Data Factory](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Dans le panneau **Services liés**, sélectionnez la **passerelle** logique que vous avez créée dans le portail.

	![passerelle logique](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
2. Dans le panneau **Passerelle de données**, cliquez sur **Télécharger et installer la passerelle de données**.

	![Lien de téléchargement dans le portail](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
3. Dans le panneau **Configurer**, cliquez sur **Recréer une clé**. Dans le message d’avertissement, cliquez sur Oui après l’avoir lu attentivement.

	![Recréer la clé](media/data-factory-data-management-gateway/recreate-key-button.png)
4. Cliquez sur le bouton Copier en regard de la clé pour la copier dans le Presse-papiers.
	
	![Copier la clé](media/data-factory-data-management-gateway/copy-gateway-key.png)

### Icônes de la barre d’état système/notifications
L’illustration suivante représente certaines des icônes de barre d’état qui s’affichent.

![Icônes de la barre d’état système](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Si vous déplacez le curseur sur les icônes/messages de notification de la barre d’état système, vous afficherez des informations supplémentaires sur l’état d’opération de la passerelle/la progression de la mise à jour dans une fenêtre contextuelle.

### Ports et pare-feu
Vous devez porter votre attention sur deux pare-feu : le **pare-feu d’entreprise**, exécuté sur le routeur central de l’organisation, et le **Pare-feu Windows**, configuré en tant que démon sur l’ordinateur local sur lequel la passerelle est installée.

![Pare-feu](./media/data-factory-data-management-gateway/firewalls.png)

Au niveau du pare-feu d’entreprise, vous devez configurer les domaines et ports de sortie suivants :

| Noms de domaine | Ports | Description |
| ------ | --------- | ------------ |
| *.servicebus.windows.net | 443, 80 | Récepteurs du Service Bus Relay sur TCP (nécessite le port 443 pour l’acquisition du jeton Access Control) | 
| *.servicebus.windows.net | 9350-9354, 5671 | Service Bus Relay facultatif sur TCP | 
| *.core.windows.net | 443 | HTTPS | 
| *.clouddatahub.net | 443 | HTTPS | 
| graph.windows.net | 443 | HTTPS |
| login.windows.net | 443 | HTTPS | 

Au niveau du pare-feu Windows, ces ports de sortie sont normalement activés. Sinon, vous pouvez configurer en conséquence les domaines et les ports sur l’ordinateur de passerelle.

#### Pour copier des données d’une banque de données source vers une banque de données récepteur

Vous devez vous assurer que les règles de pare-feu sont correctement activées sur le pare-feu d’entreprise, sur le pare-feu Windows de l’ordinateur de passerelle, ainsi que sur le magasin de données lui-même. Cela permet à la passerelle de se connecter correctement à la source et au récepteur. Vous devez activer les règles pour chaque magasin de données impliqué dans l’opération de copie.

Par exemple, pour effectuer une copie entre **une banque de données locale et un récepteur de base de données SQL Azure ou un récepteur Azure SQL Data Warehouse**, vous devez autoriser le trafic **TCP** sortant sur le port **1433** pour le Pare-feu Windows et le pare-feu d’entreprise. Vous devez aussi configurer les paramètres de pare-feu du serveur SQL Azure pour ajouter l’adresse IP de l’ordinateur passerelle à la liste des adresses IP autorisées.


### Paramètres du serveur proxy
Par défaut, la passerelle de gestion des données utilisera les paramètres de proxy d’Internet Explorer et utilisera les informations d’identification par défaut pour y accéder. Si vous devez modifier ces paramètres, vous pouvez configurer les **paramètres du serveur proxy** comme indiqué ci-dessous pour vous assurer que la passerelle sera en mesure de se connecter à Azure Data Factory :

1.	Après avoir installé la passerelle de gestion des données, dans l’Explorateur de fichiers, effectuez une copie de sauvegarde de « C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config » pour sauvegarder le fichier d’origine.
2.	Lancez Notepad.exe en tant qu’administrateur, puis ouvrez le fichier texte « C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config ». La balise par défaut pour system.net apparaît comme suit :

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	Vous pouvez ensuite ajouter les détails du serveur proxy (par exemple, l’adresse du proxy) à l’intérieur de cette balise parent. Par exemple :

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	Vous pouvez ajouter des propriétés supplémentaires à l’intérieur de la balise de proxy pour spécifier les paramètres requis comme scriptLocation. Reportez-vous à la page de syntaxe [<proxy>, élément (paramètres réseau)](https://msdn.microsoft.com/library/sa91de1e.aspx).

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. Enregistrez le fichier de configuration à l’emplacement d’origine, puis redémarrez le service de passerelle de gestion des données pour relever les modifications. Pour ce faire, utilisez le menu **Démarrer** > **Services.msc**. Sinon, dans le **Gestionnaire de configuration de passerelle de gestion des données**, cliquez sur le bouton **Arrêter le service**, puis sur **Démarrer le service**. Si le service ne démarre pas, il est probable qu’une syntaxe de balise XML incorrecte ait été ajoutée dans le fichier de configuration d’application que vous avez modifié.

Outre les points ci-dessus, vous devez également vous assurer que Microsoft Azure figure dans la liste d’autorisation de votre entreprise. Vous pouvez télécharger la liste des adresses IP Microsoft Azure valides à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

#### Symptômes possibles des erreurs liées au pare-feu et au serveur proxy :
Si vous rencontrez l’une des erreurs suivantes, cela signifie que vous avez probablement mal configuré le serveur proxy ou le pare-feu, et que la passerelle de gestion des données ne peut pas se connecter à Azure Data Factory pour s’authentifier. Reportez-vous à la section ci-dessus pour vous assurer que votre pare-feu et votre serveur proxy sont correctement configurés.

1.	Lorsque vous tentez d’inscrire la passerelle, vous recevez le message d’erreur suivant : « Nous n’avons pas pu enregistrer la clé de passerelle. Avant de réessayer d’enregistrer la clé de passerelle, vérifiez que la passerelle de gestion des données est connectée et que le service d’hébergement de la passerelle de gestion des données est en cours d’exécution. »
2.	Lorsque vous ouvrez le Gestionnaire de configuration, l’état indiqué est « Déconnecté » ou « En cours de connexion ». Lors de l’affichage de journaux d’événements Windows, sous « Observateur d’événements » > « Journaux d’applications et de services » > « Passerelle de gestion des données », des messages d’erreur apparaissent, tels que « Impossible de se connecter au serveur distant » ou « Un composant de la passerelle de gestion des données ne répond plus et va redémarrer automatiquement. Nom du composant : Passerelle. »

### Ouvrir le port 8050 pour le chiffrement des informations d’identification 
Le port de trafic entrant **8050** est utilisé par l’application **Définition des informations d’identification** pour relayer les informations d’identification à la passerelle lorsque vous configurez un service lié local dans le portail Azure (voir détails ci-après). Lors de l’installation de la passerelle, l’installation de la passerelle de gestion des données ouvre cette dernière par défaut sur l’ordinateur de passerelle.
 
En cas d’utilisation d’un pare-feu tiers, vous pouvez ouvrir manuellement le port 8050. Si vous rencontrez des problèmes de pare-feu lors de l’installation de la passerelle, vous pouvez essayer d’utiliser la commande suivante pour installer la passerelle sans configurer le pare-feu.

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Si vous préférez ne pas ouvrir le port 8050 sur l’ordinateur passerelle et souhaitez configurer un service lié local, vous devez utiliser d’autres mécanismes que l’application **Définition des informations d’identification** pour configurer les informations d’identification de la banque de données. Vous pouvez par exemple utiliser l’applet de commande PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Consultez la section [Configuration des informations d’identification et de la sécurité](#set-credentials-and-securityy) pour savoir comment configurer les informations d’identification de la banque de données.

## Mettre à jour 
Par défaut, la passerelle de gestion des données est automatiquement mise à jour lorsqu’une version plus récente est disponible. La passerelle n’est pas mise à jour tant que toutes les tâches planifiées ne sont pas terminées. Aucune autre tâche n’est traitée par la passerelle avant la fin de l’opération de mise à jour. Si la mise à jour échoue, la passerelle est restaurée vers son ancienne version.

Vous verrez apparaître l’heure de mise à jour planifiée dans le panneau de propriétés de la passerelle du portail, à la page d’accueil du Gestionnaire de configuration de passerelle de gestion des données, et dans le message de notification de la barre d’état système.

L’onglet Accueil du Gestionnaire de configuration de passerelle de gestion des données affiche la planification de la mise à jour, ainsi que la date de la dernière mise à jour/installation de la passerelle.

![Planification de mises à jour](media/data-factory-data-management-gateway/UpdateSection.png)

Vous pouvez installer la mise à jour immédiatement ou attendre que la passerelle soit mise à jour automatiquement à l’heure planifiée. Par exemple, la capture d’écran suivante montre le message de notification affiché dans le Gestionnaire de configuration de passerelle de gestion des données, ainsi que le bouton de mise à jour qui vous permet d’installer cette dernière immédiatement.

![Mise à jour dans DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Le message de notification dans la barre d’état système se présente comme suit :

![Message de barre d’état système](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Vous verrez s’afficher la progression de l’opération de mise à jour (manuelle ou automatique) dans la barre d’état système. À la prochaine ouverture du Gestionnaire de configuration de passerelle de gestion des données, un message s’affiche dans la barre de notification, vous indiquant que la passerelle a été mise à jour, et contenant un lien vers la rubrique relative aux [nouveautés](data-factory-gateway-release-notes.md).

### Pour activer/désactiver une fonctionnalité de mise à jour automatique
Vous pouvez désactiver/activer la fonctionnalité de mise à jour automatique de la manière suivante :

1. Lancez Windows PowerShell sur l’ordinateur de passerelle.
2. Accédez au dossier C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript.
3. Exécutez la commande suivante pour désactiver la fonctionnalité de mise à jour automatique.

		.\GatewayAutoUpdateToggle.ps1  -off

4. Pour la réactiver :
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## Gestionnaire de configuration 
Une fois la passerelle installée, vous pouvez lancer le Gestionnaire de configuration de passerelle de gestion des données de l’une des manières suivantes :

- Dans la fenêtre **Rechercher**, saisissez **passerelle de gestion de données** pour accéder à cet utilitaire.
- Exécutez l’exécutable **ConfigManager.exe** dans le dossier : **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**.
 
### page d'accueil
La page d’accueil permet d’effectuer les opérations suivantes :

- Afficher l’état de la passerelle, c’est-à-dire si la passerelle est connectée au service cloud ou non.
- **Inscrire** à l’aide d’une clé à partir du portail.
- **Arrêter** et démarrer le **service hôte de passerelle de gestion des données** sur l’ordinateur passerelle.
- **Planifier les mises à jour** à un moment spécifique de la journée.
- Afficher la date de la **dernière mise à jour** de la passerelle.

### Page Paramètres
La page Paramètres permet d’effectuer les opérations suivantes :

- Afficher, modifier et exporter le **certificat** utilisé par la passerelle. Ce certificat est utilisé pour chiffrer les informations d’identification de la source de données.
- Modifier le **port HTTPS** du point de terminaison. La passerelle ouvre un port pour définir les informations d’identification de la source de données.
- Afficher **l’état** du point de terminaison.
- Afficher le **Certificat SSL** qui est utilisé pour la communication SSL entre le portail et la passerelle pour définir les informations d’identification pour les sources de données.

### Page Diagnostics
La page Diagnostics permet d’effectuer les opérations suivantes :

- Activer la **journalisation** détaillée, afficher les journaux dans l’observateur d’événements et envoyer des journaux à Microsoft en cas de défaillance.
- **Tester la connexion** à une source de données.

### Help page
Cette page d’aide affiche les informations suivantes :

- Brève description de la passerelle
- Numéro de version
- Liens vers l’aide en ligne, la déclaration de confidentialité et le contrat de licence.

## Résolution de problèmes

- Pour plus d’informations, vous pouvez consulter les journaux de la passerelle contenus dans les journaux des événements Windows. Vous les trouverez à l'aide de **l'Observateur d'événements** Windows sous **Journaux des applications et des services** > **Passerelle de gestion des données**. Lors de la résolution des problèmes liés à la passerelle, recherchez des événements de type erreur dans l’Observateur d’événements.
- Si la passerelle cesse de fonctionner lorsque vous **modifiez le certificat**, redémarrez (arrêtez et démarrez) le **service de passerelle de gestion des données** à l’aide de l’outil Gestionnaire de configuration de passerelle de gestion des données Microsoft ou de l’applet du Panneau de configuration Services. Si l’erreur persiste, vous devrez peut-être attribuer des autorisations explicites à l’utilisateur du service de passerelle de gestion des données pour lui donner accès au certificat dans le Gestionnaire de certificats (certmgr.msc). Le compte d’utilisateur par défaut du service est **NT Service\\DIAHostService**.
- Si l’application **Gestionnaire d’informations d’identification** ne parvient pas à **chiffrer** les informations d’identification lorsque vous cliquez sur le bouton Chiffrer dans Data Factory Editor, vérifiez que vous exécutez cette application sur l’ordinateur sur lequel la passerelle est installée. Si ce n’est pas le cas, exécutez l’application sur l’ordinateur passerelle et essayez de chiffrer les informations d’identification.
- Si vous rencontrez des problèmes de connexion à la banque de données ou des erreurs liées au pilote, lancez le **Gestionnaire de configuration de passerelle de gestion des données** sur l’ordinateur passerelle, basculez vers l’onglet **Diagnostics**, sélectionnez/entrez les valeurs appropriées dans les champs du groupe **Tester la connexion à une source de données locale à l’aide de cette passerelle**, puis cliquez sur **Tester la connexion** pour vérifier si vous pouvez vous connecter à la source de données locale à partir de l’ordinateur passerelle en utilisant les informations de connexion et d’identification. Si le test de connexion échoue encore après l'installation d'un pilote, redémarrez la passerelle pour récupérer les dernières modifications.

	![Tester la connexion](./media/data-factory-data-management-gateway/TestConnection.png)

### Envoyer les journaux de la passerelle à Microsoft
Si vous rencontrez des problèmes avec la passerelle et que vous devez contacter le Support Microsoft, vous aurez peut-être besoin de fournir les journaux de la passerelle. La version de la passerelle vous permet de partager facilement les journaux de la passerelle requis via deux clics de bouton dans le Gestionnaire de configuration de passerelle.

1. Basculez vers l’onglet **Diagnostics** du Gestionnaire de configuration de passerelle.
 
	![Passerelle de gestion des données - Onglet Diagnostics](media/data-factory-data-management-gateway/data-management-gateway-diagnostics-tab.png)
2. Cliquez sur le lien **Envoyer des journaux** pour afficher la boîte de dialogue suivante.

	![Passerelle de gestion de données - Envoyer des journaux](media/data-factory-data-management-gateway/data-management-gateway-send-logs-dialog.png)
3. (facultatif) Cliquez sur **Afficher les journaux** pour consulter les journaux dans l’observateur d’événements.
4. (facultatif) Cliquez sur **Confidentialité** pour consulter la déclaration de confidentialité des services en ligne Microsoft.
3. Une fois que vous êtes satisfait de ce que vous allez charger, cliquez sur **Envoyer des journaux** pour envoyer les journaux des 7 derniers jours à Microsoft en vue de la résolution des problèmes. L’état de l’opération d’envoi des journaux devrait s’afficher comme indiqué ci-dessous.

	![Passerelle de gestion de données - État de l’opération Envoyer des journaux](media/data-factory-data-management-gateway/data-management-gateway-send-logs-status.png)
4. Une fois l’opération terminée, la boîte de dialogue ci-dessous apparaît.
	
	![Passerelle de gestion de données - État de l’opération Envoyer des journaux](media/data-factory-data-management-gateway/data-management-gateway-send-logs-result.png)
5. Notez **l’ID du rapport** et partagez-le avec le Support Microsoft. L’ID du rapport permet de localiser les journaux de la passerelle que vous avez téléchargés pour la résolution des problèmes. L’ID du rapport est également enregistré dans l’Observateur d’événements pour référence. Vous pouvez le trouver en recherchant l’ID d’événement « 25 » et en vérifiant la date et l’heure.
	
	![Passerelle de gestion de données - ID du rapport de l’opération Envoyer des journaux](media/data-factory-data-management-gateway/data-management-gateway-send-logs-report-id.png)

### La passerelle d’archive ouvre une session sur l’ordinateur hôte de la passerelle
Dans certains cas, il se peut que vous ayez des problèmes avec la passerelle et que vous ne puissiez pas partager directement les journaux de la passerelle :

- lorsque les utilisateurs installent manuellement la passerelle et l’inscrivent ;
- lorsque les utilisateurs tentent d’inscrire la passerelle avec une clé régénérée sur le gestionnaire de configuration ;
- lorsque les utilisateurs tentent d’envoyer des journaux mais que le service hôte de la passerelle ne peut pas être connecté.

Dans ces situations, vous pouvez enregistrer les journaux de la passerelle dans un fichier zip que vous partagez ensuite avec le Support Microsoft contacté. Par exemple, si vous recevez une erreur lors de l’inscription de la passerelle comme indiqué ci-dessous :

![Passerelle de gestion des données - Erreur d’inscription](media/data-factory-data-management-gateway/data-management-gateway-registration-error.png)

Cliquez sur le lien **Archiver les journaux de la passerelle** pour archiver et enregistrer les journaux, puis partagez le fichier zip avec le Support Microsoft.

![Passerelle de gestion de données - Archiver des journaux](media/data-factory-data-management-gateway/data-management-gateway-archive-logs.png)

 
## Déplacement d’une passerelle d’une machine vers une autre
Cette section décrit les opérations pour déplacer une passerelle client d’une machine vers une autre.

2. Dans le portail, accédez à la **page d’accueil de Data Factory**, puis cliquez sur la vignette **Services liés**.

	![Lien de passerelles de données](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
3. Sélectionnez votre passerelle dans la section **PASSERELLES DONNÉES** du panneau **Services liés**.
	
	![Panneau Services liés avec passerelle sélectionnée](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
4. Dans le panneau **Passerelle de données**, cliquez sur **Télécharger et installer la passerelle de données**.
	
	![Télécharger le lien de passerelle](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
5. Dans le panneau **Configurer**, cliquez sur **Télécharger et installer la passerelle de données**, puis suivez les instructions pour installer la passerelle de données sur la machine.

	![Panneau Configurer](./media/data-factory-data-management-gateway/ConfigureBlade.png)
6. Laissez le **Gestionnaire de configuration de la passerelle de gestion des données** ouvert.
 
	![Gestionnaire de configuration](./media/data-factory-data-management-gateway/ConfigurationManager.png)
7. Dans le panneau **Configurer** du portail, cliquez sur **Recréer une clé** dans la barre de commandes, puis, au message d’avertissement, cliquez sur **Oui**. Cliquez sur le **bouton de copie** en regard du texte de la clé pour copier la clé dans le presse-papiers. Notez que la passerelle de l’ancienne machine cesse de fonctionner dès que vous recréez la clé.
	
	![Recréer la clé](./media/data-factory-data-management-gateway/RecreateKey.png)
	 
8. Collez la **clé** dans la zone de texte de la page d’**enregistrement de passerelle** du **Gestionnaire de configuration de la passerelle de gestion de données** sur votre ordinateur. (Facultatif) Cochez la case **Afficher la clé de passerelle** pour afficher le texte de la clé.
 
	![Copier la clé et s’inscrire](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
9. Cliquez sur **S’inscrire** pour enregistrer la passerelle auprès du service cloud.
10. Dans l’onglet **Paramètres**, cliquez sur **Modifier** pour sélectionner le certificat utilisé avec l’ancienne passerelle, entrez le **mot de passe**, puis cliquez sur **Terminer**.
 
	![Spécifier le certificat](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

	Vous pouvez exporter un certificat à partir de l’ancienne passerelle en procédant comme suit : lancez le Gestionnaire de configuration de passerelle de gestion des données sur l’ancien panneau, basculez vers l’onglet **certificat** cliquez sur le bouton **Exporter** et suivez les instructions.
10. Une fois l’inscription de la passerelle terminée, vous devez voir **Inscription** définie sur **Inscrit** et **État** sur la valeur **Démarré** de la page d’accueil du Gestionnaire de configuration de passerelle.

## Chiffrement des informations d’identification 
Pour chiffrer les informations d’identification dans Data Factory Editor, procédez comme suit :

1. Lancez le navigateur web sur **l’ordinateur passerelle**, accédez au [portail Azure](http://portal.azure.com), recherchez votre fabrique de données si nécessaire, ouvrez-la dans le panneau **DATA FACTORY**, puis cliquez sur **Créer et déployer** pour démarrer Data Factory Editor.
1. Cliquez sur un **service lié** existant dans l’arborescence pour afficher sa définition JSON ou créez un autre service lié qui nécessite une passerelle de gestion des données (par exemple, SQL Server ou Oracle).
2. Dans l’éditeur JSON, entrez le nom de la passerelle pour la propriété **gatewayName**.
3. Entrez le nom du serveur pour la propriété **Data Source** dans **connectionString**.
4. Entrez le nom de la base de données pour la propriété **Initial Catalog** dans **connectionString**.
5. Cliquez sur le bouton **Chiffrer** dans la barre de commandes pour lancer l’application ClickOnce **Gestionnaire d’informations d’identification**. La boîte de dialogue **Définition des informations d’identification** doit s’afficher. ![Boîte de dialogue des paramètres d’informations d'identification](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
6. Dans la boîte de dialogue **Configuration des informations d’identification**, procédez comme suit :
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

Quand vous utilisez l’application **Définition des informations d’identification** lancée à partir du portail Azure pour définir les informations d’identification d’une source de données locale, le portail chiffre les informations d’identification avec le certificat que vous avez spécifié dans l’onglet **Certificat** du **Gestionnaire de configuration de passerelle de gestion des données** sur l’ordinateur passerelle.

Si vous recherchez une approche basée sur une API pour chiffrer les informations d’identification, vous pouvez utiliser l’applet de commande PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) pour chiffrer les informations d’identification. L'applet de commande utilise le certificat qui a servi à configurer la passerelle pour chiffrer les informations d'identification. Vous pouvez alors voir les informations d’identification chiffrées retournées par cette applet de commande et les ajouter à l’élément **EncryptedCredential** de **connectionString** dans le fichier JSON que vous utiliserez avec l’applet de commande [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) ou dans l’extrait de code JSON dans Data Factory Editor dans le portail.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

Il existe une autre approche pour définir les informations d’identification à l’aide de Data Factory Editor. Si vous créez un service SQL Server lié à l’aide de l’éditeur et entrez les informations d’identification en texte brut, ces informations d’identification sont chiffrées à l’aide d’un certificat appartenant au service Data Factory et NON le certificat qui a servi à configurer la passerelle. Bien que cette approche puisse être un peu plus rapide dans certains cas, elle reste moins sécurisée. Par conséquent, nous vous recommandons de suivre cette approche uniquement à des fins de développement/test.


## Applets de commande PowerShell 
Cette section décrit comment créer et enregistrer une passerelle à l’aide des applets de commande Azure PowerShell.

1. Lancez **Azure PowerShell** en mode administrateur.
2. Connectez-vous à votre compte Azure en exécutant la commande suivante et en entrant vos informations d’identification Azure.

	Login-AzureRmAccount
2. Utilisez l’applet de commande **New-AzureRmDataFactoryGateway** pour créer une passerelle logique, comme suit :

		$MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Exemple de commande et de sortie** :


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

	
4. Dans Azure PowerShell, accédez au dossier **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** et exécutez le script **RegisterGateway.ps1** associé à la variable locale **$Key**, comme indiqué dans la commande suivante pour inscrire l’agent client installé sur votre ordinateur avec la passerelle logique que vous avez créée précédemment.

		PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
		
		Agent registration is successful!

	Vous pouvez inscrire la passerelle sur un ordinateur distant en utilisant le paramètre IsRegisterOnRemoteMachine. Exemple :
		
		.\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true

5. Vous pouvez utiliser l’applet de commande **Get-AzureRmDataFactoryGateway** pour obtenir la liste des passerelles dans votre fabrique de données. Lorsque l’**état** est **online**, cela signifie que votre passerelle est prête.

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Vous pouvez supprimer une passerelle à l’aide de l’applet de commande **Remove-AzureRmDataFactoryGateway** et mettre à jour la description de la passerelle en utilisant les applets de commande **Set-AzureRmDataFactoryGateway**. Pour obtenir la syntaxe et d’autres détails sur ces applets de commande, consultez la rubrique Référence des applets de commande Azure Data Factory.

### Répertorier les passerelles à l’aide de PowerShell

	Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup

### Supprimer une passerelle à l’aide de PowerShell
	
	Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force 


## Étapes suivantes
- Pour obtenir des informations détaillées sur la passerelle, consultez l’article [Passerelle de gestion de données](data-factory-data-management-gateway.md).

<!---HONumber=AcomDC_0810_2016-->