<properties 
	pageTitle="Accéder à des ressources locales à l’aide de connexions hybrides dans Azure App Service" 
	description="Créer une connexion entre une application web dans Azure App Service et une ressource locale utilisant un port TCP statique" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#Accéder à des ressources locales à l’aide de connexions hybrides dans Azure App Service

Vous pouvez connecter une application web d’Azure App Service à n’importe quelle ressource locale utilisant un port TCP statique, par exemple, SQL Server, MySQL, les API web HTTP, les services mobiles et la plupart des services web personnalisés. Cet article vous explique comment créer une connexion hybride entre une application web d’App Service et une base de données SQL Server locale.

> [AZURE.NOTE]La partie Web Apps de la fonctionnalité Connexions hybrides n’est disponible que dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Pour créer une connexion dans BizTalk Services, consultez la page [Connexions hybrides](http://go.microsoft.com/fwlink/p/?LinkID=397274).

## Configuration requise
- Un abonnement Azure. Pour un abonnement gratuit, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/). 

- Pour utiliser une base de données SQL Server ou SQL Server Express locale avec une connexion hybride, TCP/IP doit être activé sur un port statique. L’utilisation d’une instance par défaut sur SQL Server est recommandée, car elle utilise le port statique 1433. Pour plus d’informations sur l’installation et la configuration de SQL Server Express en vue de son utilisation avec des connexions hybrides, consultez la page [Connexion à une instance SQL Server locale à partir d’un site Web Azure au moyen de connexions hybrides](http://go.microsoft.com/fwlink/?LinkID=397979).

- L'ordinateur sur lequel vous installez l'agent Gestionnaire de connexions hybrides local décrit plus loin dans cet article :

	- doit être capable de se connecter à Azure sur le port 5671 ;
	- doit être capable d’accéder au *nom_d’hôte*:*numéro_de_port* de votre ressource locale. 

> [AZURE.NOTE]Les étapes décrites dans cet article partent du principe que vous utilisez le navigateur à partir de l'ordinateur qui hébergera l'agent de connexion hybride local.


## Créer une application web dans le portail Azure ##

> [AZURE.NOTE]Si vous avez déjà créé dans le portail Azure une application web que vous voulez utiliser pour ce didacticiel, passez directement à la section [Création d’une connexion hybride et d’un service BizTalk](#CreateHC).

1. Dans le coin inférieur gauche du [portail Azure](https://portal.azure.com), cliquez sur **Nouveau** > **Web et mobilité** > **Site web**.
	
	![New button][New]
	
	![New web app][NewWebsite]
	
2. Dans le panneau **Application web**, indiquez une URL et cliquez sur **Créer**.
	
	![Website name][WebsiteCreationBlade]
	
3. Après quelques instants, l’application web est créée et son panneau apparaît. Le volet est un tableau de bord vertical qu'il est possible de faire défiler et qui vous permet de gérer votre site.
	
	![Website running][WebSiteRunningBlade]
	
4. Pour vérifier que le site est actif, cliquez sur l'icône **Parcourir** afin d'afficher la page par défaut.
	
	![Click browse to see your web app][Browse]
	
	![Default web app page][DefaultWebSitePage]
	
Vous allez ensuite créer une connexion hybride et un service BizTalk pour l’application web.

<a name="CreateHC"></a>
## Création d'une connexion hybride et d'un service BizTalk ##

1. Faites défiler le panneau de votre application web et choisissez **Connexions hybrides**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. Dans le volet Connexions hybrides, cliquez sur **Ajouter**.
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. Le volet **Ajouter une connexion hybride** s'ouvre. Comme il s'agit de votre première connexion hybride, l'option **Nouvelle connexion hybride** est présélectionnée, et le volet **Créer une connexion hybride** s'ouvre.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	Dans le panneau **Créer une connexion hybride** : - dans **Nom**, indiquez le nom de la connexion. - dans **Nom d’hôte**, entrez le nom de l’ordinateur local qui héberge votre ressource. - Dans **Port**, entrez le numéro de port utilisé par votre ressource locale (1433 pour une instance SQL Server par défaut). - Cliquez sur **Biz Talk Service**


4. Le panneau **Créer un service BizTalk** s’affiche. Entrez un nom pour le service BizTalk, puis cliquez sur **OK**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	Le panneau **Créer un service BizTalk** se ferme et vous revenez au panneau **Créer une connexion hybride**.
	
5. Dans le volet Créer une connexion hybride, cliquez sur **OK**.
	
	![Click OK][CreateBTScomplete]
	
6. Une fois le processus terminé, la zone des notifications du portail vous informe que la connexion a été créée.
	<!-- TODO

Everything fails at this step. I can't create a BizTalk service in the dogfood portal. I switch to the old portal
(full portal) and created the BizTalk service but it doesn't seem to let you connnect them - When you finish the
Create hybrid conn step, you get the following error
Failed to create hybrid connection RelecIoudHC. The 
resource type could not be found in the namespace 
'Microsoft.BizTaIkServices for api version 2014-06-01'.

The error indicates it couldn't find the type, not the instance.
![Success notification][CreateHCSuccessNotification]
-->
7. Dans le panneau de l’application web, l’icône **Connexions hybrides** indique à présent qu’une connexion hybride a été créée.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
À ce stade, vous avez terminé une partie importante de l'infrastructure de connexion hybride cloud. Vous allez ensuite créer un élément local.

<a name="InstallHCM"></a>
## Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion ##

1. Dans le panneau de l’application web, cliquez sur l’icône Connexions hybrides. 
	
	![Hybrid connections icon][HCIcon]
	
2. Dans le volet **Connexions hybrides**, la colonne **Statut** correspondant au point de terminaison ajouté récemment indique **Non connecté**. Cliquez sur la connexion pour la configurer.
	
	![Not connected][NotConnected]
	
	Le volet Connexion hybride s'ouvre.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Dans le volet, cliquez sur **Configuration de l'écouteur**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Le volet **Propriétés de la connexion hybride** s'ouvre. Sous **Gestionnaire de connexions hybrides local**, cliquez sur **Cliquez ici pour l'installer**.
	
	![Click here to install][ClickToInstallHCM]
	
5. Dans la boîte de dialogue Exécution de l'application - Avertissement de sécurité, cliquez sur **Exécuter** pour continuer.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	Dans la boîte de dialogue **Contrôle de compte d'utilisateur**, cliquez sur **Oui**.
	
	![Choose Yes][UAC]
	
7. Le Gestionnaire de connexion hybride est téléchargé et installé pour vous.
	
	![Installation][HCMInstalling]
	
8. Une fois l'installation terminée, cliquez sur **Fermer**.
	
	![Click Close][HCMInstallComplete]
	
	Dans le volet **Connexions hybrides**, la colonne **Statut** indique à présent **Connecté**.
	
	![Connected Status][HCStatusConnected]

Maintenant que l'infrastructure de connexion hybride est terminée, vous pouvez créer une application hybride qui l'utilise.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

<a name="NextSteps"></a>
## Étapes suivantes ##

- Pour des informations sur la création d'une application web ASP.NET utilisant une connexion hybride, consultez la page [Connexion à une instance SQL Server locale à partir d'un site web Azure au moyen de connexions hybrides](http://go.microsoft.com/fwlink/?LinkID=397979).

- Pour des informations sur l'utilisation d'une connexion hybride avec un service mobile, consultez la page [Connexion à une instance SQL Server locale à partir d'un service mobile Azure au moyen de connexions hybrides](../mobile-services-dotnet-backend-hybrid-connections-get-started.md).

### Ressources supplémentaires

[Aperçu des connexions hybrides](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist présente les connexions hybrides (vidéo Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Site web des connexions hybrides](http://azure.microsoft.com/services/biztalk-services/)

[BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l’échelle, Configurer et Connexion hybride](../biztalk-dashboard-monitor-scale-tabs/)

[Création d’un cloud hybride réel avec la portabilité transparente des applications (vidéo Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Connexion à une instance SQL Server locale à partir d’Azure Mobile Services au moyen de connexions hybrides (vidéo Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

<!-- IMAGES -->
[New]: ./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 

<!---HONumber=July15_HO4-->