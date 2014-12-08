<properties urlDisplayName="Create and Manage Hybrid Connections - BizTalk Services" pageTitle="Création et gestion des connexions hybrides | Azure" metaKeywords="BizTalk Services, BizTalk, web sites, website, hybrid connections, Azure" description="Learn how to create a hybrid connection, manage the connection, and install the Hybrid Connection Manager." metaCanonical="" services="integration-services" documentationCenter="" title="Create and Manage Hybrid Connections" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />


#Création et gestion des connexions hybrides

Cette rubrique répertorie les étapes permettant de créer et de gérer des connexions hybrides Azure. 

La procédure de connexion à une ressource locale comporte quatre étapes :

1. [Création d'une connexion hybride](#CreateHybridConnection) avec spécification du nom d'hôte ou de l'adresse IP de la ressource locale dans votre réseau privé.

2.	[Liaison de votre site web Azure ou de votre service mobile Azure](#LinkWebSite) à la connexion hybride.

3. [Installation du Gestionnaire de connexions hybrides](#InstallHCM) sur votre ressource locale et connexion à la connexion hybride spécifique. Le portail Azure offre des possibilités d'installation et de connexion en un seul clic.

4. [Gestion des connexions hybrides](#ManageHybridConnection) et de leurs clés de connexion.


##<a name="CreateHybridConnection"></a>Création d'une connexion hybride

Vous pouvez créer une connexion hybride dans le portail de gestion Azure avec Sites Web **ou** BizTalk Services. 

**Pour créer des connexions hybrides à l'aide de Sites Web**, consultez la page [Connexion d'un site web Azure à une ressource locale](http://go.microsoft.com/fwlink/p/?LinkId=397538).

**Pour créer des connexions hybrides dans BizTalk Services** :

1. Connectez-vous au [portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation sur la gauche, sélectionnez **BizTalk Services**, puis votre service BizTalk. 
<br/>Si vous n'avez pas de service BizTalk existant, vous pouvez [en créer un](http://go.microsoft.com/fwlink/p/?LinkID=329870).
3. Sélectionnez l'onglet Connexions hybrides :
<br/>
![Hybrid Connections Tab][HybridConnectionTab]

4. Sélectionnez **Créer une connexion hybride** ou sélectionnez le bouton **AJOUTER** dans la barre des tâches. Entrez les informations suivantes :

	<table border="1">
    <tr>
       <td><strong>Nom</strong></td>
        <td>Le nom de la connexion hybride doit être unique et différent de celui du service BizTalk. Vous pouvez entrer n'importe quel nom, mais soyez précis quant à son objet. Voici quelques exemples :<br/><br/>
		FichePaie<em>ServeurSQL</em><br/>
		ListeFournitures<em>ServeurSharepoint</em><br/>
		Clients<em>ServeurOracle</em>
        </td>
    </tr>
    <tr>
        <td><strong>Nom d'hôte</strong></td>
        <td>Entrez le nom complet de l'hôte, uniquement le nom d'hôte ou l'adresse IPv4 de la ressource locale. Voici quelques exemples :
        <br/><br/>
<em>monSQLServer</em>
<br/>
<em>mySQLServer</em>.<em>Domaine</em>.corp.<em>votreSociété</em>.com
<br/>
<em>monHTTPSharePointServer</em>
<br/>
<em>myHTTPSharePointServer</em>.<em>votreSociété</em>.com
<br/>
10.100.10.10
       </td>
    </tr>
	<tr>
        <td><strong>Port</strong></td>
        <td>Entrez le numéro de port de la ressource locale. Par exemple, si vous utilisez un site web, entrez le port 80 ou 443. Si vous utilisez SQL Server, entrez le port 1433.</td>
	</tr>
	</table>


5. Cliquez sur la coche. 

####Informations complémentaires

- Il est possible de créer plusieurs connexions hybrides. Pour connaître le nombre de connexions autorisées, consultez la page [BizTalk Services : tableau comparatif des éditions](http://go.microsoft.com/fwlink/p/?LinkID=302279). 
- Chaque connexion hybride est créée avec une paire de chaînes de connexion : des clés d'application qui ENVOIENT et des clés locales qui ÉCOUTENT. Chaque paire possède une clé primaire et une clé secondaire. 


##<a name="LinkWebSite"></a>Liaison de votre site web Azure ou service mobile Azure

Pour lier le site web Azure à une connexion hybride existante, sélectionnez **Utiliser une connexion hybride existante** dans le panneau Connexions hybrides. Consultez la page [Connexion d'un site web Azure à une ressource locale](http://go.microsoft.com/fwlink/p/?LinkId=397538).

Pour lier le service mobile Azure à une connexion hybride existante, sélectionnez **Ajouter une connexion hybride** lors de la modification ou de la création d'un service mobile. Consultez la page [Azure Mobile Services et connexions hybrides](http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started).


##<a name="InstallHCM"></a>Installation locale du Gestionnaire de connexions hybrides

Après la création d'une connexion hybride, installez le Gestionnaire de connexions hybrides sur la ressource locale. Il peut être téléchargé depuis votre site web Azure ou votre service BizTalk. Procédure pour BizTalk Services : 

1. Connectez-vous au [portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation sur la gauche, sélectionnez **BizTalk Services**, puis votre service BizTalk. 
3. Sélectionnez l'onglet **Connexions hybrides** :
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. Dans la barre des tâches, sélectionnez **Installation locale** :
<br/>
![On-Premises Setup][HCOnPremSetup]
5. Sélectionnez **Installer et configurer** pour exécuter ou télécharger le Gestionnaire de connexions hybrides sur le système local. 
6. Cliquez sur la coche pour démarrer l'installation. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString â€œ*Your On-Premises Connection String that you copied*â€ 
--> 

#### Informations complémentaires
- Les connexions hybrides prennent en charge les ressources locales installées sur les systèmes d'exploitation suivants :

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- Une fois que vous avez installé le Gestionnaire de connexions hybrides, les événements suivants ont lieu : 

	- La connexion hybride hébergée sur Azure est automatiquement configurée pour utiliser la chaîne de connexion d'application principale. 
	- La ressource locale est automatiquement configurée pour utiliser la chaîne de connexion locale principale.

- Le Gestionnaire de connexions hybrides doit utiliser une chaîne de connexion locale valide pour l'autorisation. Le site web ou le service mobile Azure doit utiliser une chaîne de connexion d'application valide pour l'autorisation.


##<a name="ManageHybridConnection"></a>Gestion des connexions hybrides
Pour gérer vos connexions hybrides, vous pouvez :

- utiliser le portail Azure et accéder à votre service BizTalk ; 
- utiliser les [API REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

####Copie/régénération des chaînes de connexion hybride

1. Connectez-vous au [portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation sur la gauche, sélectionnez **BizTalk Services**, puis votre service BizTalk. 
3. Sélectionnez l'onglet **Connexions hybrides** :
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. Sélectionnez la connexion hybride. Dans la barre des tâches, sélectionnez **Gérer la connexion** :
<br/>
![Manage Options][HCManageConnection]
<br/>
**Gérer la connexion** répertorie les chaînes de connexion locales et d'application. Vous pouvez copier les chaînes de connexion ou régénérer la clé d'accès utilisée dans la chaîne de connexion. 
<br/>
<br/>
**Si vous sélectionnez Régénérer**, la clé d'accès partagé utilisée dans la chaîne de connexion est modifiée. Effectuez les actions suivantes :
- Dans le portail de gestion Azure, sélectionnez **Clés de synchronisation** dans l'application Azure.
- 	Réexécutez l'**Installation locale**. Lorsque vous réexécutez l'installation locale, la ressource locale est automatiquement configurée pour utiliser la chaîne de connexion principale mise à jour.


####Utilisation d'une stratégie de groupe pour contrôler les ressources locales utilisées par une connexion hybride

1. Téléchargez les modèles d'administration du Gestionnaire de connexions hybrides.
2. Procédez à l'extraction des fichiers.
3. Sur l'ordinateur qui modifie la stratégie de groupe, procédez comme suit : 
- Copiez les fichiers .ADMX dans le dossier *%WINROOT%\PolicyDefinitions*.
- Copiez les fichiers .ADML dans le dossier *%WINROOT%\PolicyDefinitions\fr-fr*.

Une fois les fichiers copiés, vous pouvez utiliser l'Éditeur de stratégie de groupe pour modifier la stratégie.




## Suivant

- [Connexion d'un site web Azure à une ressource locale](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [Les connexions hybrides étape par étape : connexion à un serveur SQL local à partir d'un site web Azure](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Azure Mobile Services et connexions hybrides](http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)
- [Aperçu des connexions hybrides](http://azure.microsoft.com/fr-fr/documentation/articles/integration-hybrid-connection-overview)


## Voir aussi

- [API REST pour gérer BizTalk Services sur Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [BizTalk Services : Tableau comparatif des éditions](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Création d'un service BizTalk avec le portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services : Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
