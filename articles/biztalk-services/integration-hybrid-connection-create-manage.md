<properties 
	pageTitle="Création et gestion des connexions hybrides | Microsoft Azure" 
	description="Découvrez comment créer une connexion hybride, gérer la connexion et installer le Gestionnaire de connexions hybrides. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="mandia"/>


# Création et gestion des connexions hybrides


## Vue d’ensemble des étapes
1. Créez une connexion hybride en spécifiant le **nom d’hôte** ou le **nom de domaine complet** de la ressource locale dans votre réseau privé.
2. Liez vos applications web Azure ou vos applications mobiles Azure à la connexion hybride.
3. Installez le Gestionnaire de connexion hybride sur votre ressource locale et établissez une connexion à la connexion hybride spécifique. Le portail Azure offre des possibilités d’installation et de connexion en un seul clic.
4. Gestion des connexions hybrides et de leurs clés de connexion.

Cette rubrique répertorie ces étapes.

> [AZURE.IMPORTANT] Il est possible de définir un point de terminaison de connexion hybride sur une adresse IP. Si vous utilisez une adresse IP, vous pouvez ou non atteindre la ressource locale, en fonction de votre client. La connexion hybride est établie si le client effectue une recherche DNS. Dans la plupart des cas, le __client__ est le code de votre application. Si le client n’effectue pas de recherche DNS (c’est-à-dire qu’il n’essaie pas de résoudre l’adresse IP comme s’il s’agissait d’un nom de domaine (x.x.x.x)), le trafic ne sera pas envoyé via la connexion hybride.
>
> Par exemple (pseudo-code), vous définissez **10.4.5.6** comme hôte local :
> 
> **Scénario possible :** `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **Scénario impossible :** `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`


## <a name="CreateHybridConnection"></a>Création d’une connexion hybride

Vous pouvez créer une connexion hybride dans le portail Azure avec Web Apps **ou** BizTalk Services.

**Pour créer des connexions hybrides à l’aide de Web Apps**, consultez la page [Connexion d’Azure Web Apps à une ressource locale](../app-service-web/web-sites-hybrid-connection-get-started.md). Vous pouvez également installer le Gestionnaire de connexion hybride à partir de votre application web, qui est la méthode recommandée.

**Pour créer des connexions hybrides dans BizTalk Services** :

1. Connectez-vous au [portail Azure Classic](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation sur la gauche, sélectionnez **BizTalk Services**, puis sélectionnez votre service BizTalk.

	Si vous n’avez pas de service BizTalk, vous pouvez en [créer un](biztalk-provision-services.md).
3. Sélectionnez l’onglet **Connexions hybrides** : ![Onglet Connexions hybrides][HybridConnectionTab]

4. Sélectionnez **Créer une connexion hybride** ou cliquez sur le bouton **AJOUTER** dans la barre des tâches. Entrez les informations suivantes :

	Propriété | Description
--- | ---
Nom | Le nom de la connexion hybride doit être unique et ne doit pas être le même que celui du service BizTalk. Vous pouvez entrer n’importe quel nom, mais soyez précis quant à son objet. Exemples :<br/><br/>Payroll*SQLServer*<br/>SupplyList*SharepointServer*<br/>Customers*OracleServer*
Nom d’hôte | Entrez le nom complet de l’hôte, uniquement le nom d’hôte ou l’adresse IPv4 de la ressource locale. Exemples :<br/><br/>mySQLServer<br/>*mySQLServer*.*Domain*.corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*.*yourCompany*.com<br/>10.100.10.10<br/><br/>Si vous utilisez l’adresse IPv4, notez que le code de votre code ou application ne peut pas résoudre l’adresse IP. Consultez la remarque importante figurant en haut de cette rubrique.
Port | Entrez le numéro de port de la ressource locale. Par exemple, si vous utilisez des applications web, entrez le port 80 ou 443. Si vous utilisez SQL Server, entrez le port 1433.

5. Cliquez sur la coche pour terminer la configuration.

#### Informations complémentaires

- Il est possible de créer plusieurs connexions hybrides. Consultez la page [Tableau comparatif des éditions de BizTalk Services](biztalk-editions-feature-chart.md) pour connaître le nombre de connexions autorisées.
- Chaque connexion hybride est créée avec une paire de chaînes de connexion : des clés Application qui envoient (SEND) et des clés locales qui écoutent (LISTEN). Chaque paire possède une clé primaire et une clé secondaire.


## <a name="LinkWebSite"></a>Liaison de votre application Azure Web Apps ou Azure Mobile Apps

Pour lier l’application Azure Web Apps à une connexion hybride existante, sélectionnez **Utiliser une connexion hybride existante** dans le panneau Connexions hybrides. Consultez la page [Connexion d’une application web Azure à une ressource locale](../app-service-web/web-sites-hybrid-connection-get-started.md).

Pour lier l’application Azure Mobile Apps à une connexion hybride existante, sélectionnez **Ajouter une connexion hybride** lors de la modification ou de la création d’un service mobile. Consultez la page [Azure Mobile Services et connexions hybrides](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md).


## <a name="InstallHCM"></a>Installation locale du Gestionnaire de connexions hybrides

Après la création d’une connexion hybride, installez le Gestionnaire de connexions hybrides sur la ressource locale. Vous pouvez le télécharger par le biais de vos applications web Azure ou à partir de votre service BizTalk. Procédure pour BizTalk Services :

1. Connectez-vous au [portail Azure Classic](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation sur la gauche, sélectionnez **BizTalk Services**, puis sélectionnez votre service BizTalk.
3. Sélectionnez l’onglet **Connexions hybrides** : ![Onglet Connexions hybrides][HybridConnectionTab]
4. Dans la barre des tâches, sélectionnez **Installation locale** : ![On-Premises Setup][HCOnPremSetup]
5. Sélectionnez **Installer et configurer** pour exécuter ou télécharger le Gestionnaire de connexions hybrides sur le système local.
6. Cliquez sur la coche pour démarrer l’installation.

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### Informations complémentaires
- Le Gestionnaire de connexions hybrides peut être installé sur les systèmes d’exploitation suivants :

	- Windows Server 2008 R2 (.NET Framework 4.5 et version ultérieures et Windows Management Framework 4.0 et version ultérieures requis)
	- Windows Server 2012 (Windows Management Framework 4.0 et version ultérieures requis)
	- Windows Server 2012 R2


- Une fois que vous avez installé le Gestionnaire de connexions hybrides, les événements suivants ont lieu :

	- La connexion hybride hébergée sur Azure est automatiquement configurée pour utiliser la chaîne de connexion d'application principale.
	- La ressource locale est automatiquement configurée pour utiliser la chaîne de connexion locale principale.

- Le Gestionnaire de connexions hybrides doit utiliser une chaîne de connexion locale valide pour l’autorisation. Les applications web Azure Web Apps ou Mobile Apps doivent utiliser une chaîne de connexion d’application valide pour l’autorisation.
- Vous pouvez mettre à l’échelle les connexions hybrides en installant une autre instance du gestionnaire des connexions hybrides sur un autre serveur. Configurez l'écouteur local pour utiliser la même adresse que le premier écouteur local. Dans ce cas, le trafic est distribuée de manière aléatoire, en tourniquet (round robin), entre les écouteurs actifs locaux.


## <a name="ManageHybridConnection"></a>Gestion des connexions hybrides
Pour gérer vos connexions hybrides, vous pouvez :

- utiliser le portail Azure et accéder à votre service BizTalk ;
- utiliser les [API REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### Copie/régénération des chaînes de connexion hybride

1. Connectez-vous au [portail Azure Classic](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation sur la gauche, sélectionnez **BizTalk Services**, puis sélectionnez votre service BizTalk.
3. Sélectionnez l’onglet **Connexions hybrides** : ![Onglet Connexions hybrides][HybridConnectionTab]
4. Sélectionnez la connexion hybride. Dans la barre des tâches, sélectionnez **Gérer la connexion** : ![Gérer les options][HCManageConnection]

	**Gérer la connexion** répertorie les chaînes de connexion locales et d’application. Vous pouvez copier les chaînes de connexion ou régénérer la clé d’accès utilisée dans la chaîne de connexion.

	**Si vous sélectionnez Régénérer**, la clé d’accès partagé utilisée dans la chaîne de connexion est modifiée. Effectuez les actions suivantes :
	- Dans le portail Azure Classic, sélectionnez **Clés de synchronisation** dans l’application Azure.
	- Réexécutez l’**Installation locale**. Lorsque vous réexécutez l'installation locale, la ressource locale est automatiquement configurée pour utiliser la chaîne de connexion principale mise à jour.


#### Utilisation d’une stratégie de groupe pour contrôler les ressources locales utilisées par une connexion hybride

1. Téléchargez les [modèles d’administration du Gestionnaire de connexion hybride](http://www.microsoft.com/download/details.aspx?id=42963).
2. Procédez à l’extraction des fichiers.
3. Sur l'ordinateur qui modifie la stratégie de groupe, procédez comme suit :

	- Copiez les fichiers .ADMX dans le dossier *%WINROOT%\\PolicyDefinitions*.
	- Copiez les fichiers .ADMX dans le dossier *%WINROOT%\\PolicyDefinitions\\fr-FR*.

Une fois les fichiers copiés, vous pouvez utiliser l'Éditeur de stratégie de groupe pour modifier la stratégie.




## Suivant

[Connexion d’applications web Azure à une ressource locale](../app-service-web/web-sites-hybrid-connection-get-started.md) [Connexion à un serveur SQL Server local à partir d’Azure Web Apps](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) [Azure Mobile Services et connexions hybrides](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md) [Aperçu des connexions hybrides](integration-hybrid-connection-overview.md)


## Voir aussi

[API REST pour la gestion des services BizTalk sur Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx) [Tableau comparatif des éditions de BizTalk Services](biztalk-editions-feature-chart.md) [Créer des services BizTalk Services à l’aide du portail Azure Classic](biztalk-provision-services.md) [Onglets Tableau de bord, Surveiller et Mettre à l’échelle dans BizTalk Services](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png

<!---HONumber=AcomDC_0824_2016-->