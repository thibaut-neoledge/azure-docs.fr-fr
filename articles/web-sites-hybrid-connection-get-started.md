<properties title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Connexion hybride : Connexion d'un site web Azure à une ressource locale" description="Create a connection between an Azure website and an on-premises resource that uses a static TCP port" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Connexion d'un site web Azure à une ressource locale au moyen de connexions hybrides

Vous pouvez connecter un site web sur Microsoft Azure à n'importe quelle ressource locale qui utilise un port TCP statique, par exemple, SQL Server, MySQL, les API web HTTP, les services mobiles et la plupart des services web personnalisés. Cette section montre comment créer une connexion hybride entre un site web Azure et une base de données SQL Server locale.

> [WACOM.NOTE] La partie Sites web de la fonctionnalité Connexions hybrides n'est disponible que dans le [portail Azure Preview](https://portal.azure.com). Pour créer une connexion dans BizTalk Services, consultez [Connexions hybrides](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

##Configuration requise
- Un abonnement Azure. Pour un abonnement gratuit, consultez [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/fr-fr/pricing/free-trial/). 

- Pour utiliser une base de données SQL Server ou SQL Server Express locale avec une connexion hybride, TCP/IP doit être activé sur un port statique. L'utilisation d'une instance par défaut sur SQL Server est recommandée, car elle utilise le port statique 1433. Pour des informations sur l'installation et la configuration de SQL Server Express pour son utilisation avec des connexions hybrides, consultez [Connexion à une instance SQL Server locale à partir d'un site web Azure au moyen de connexions hybrides](http://go.microsoft.com/fwlink/?LinkID=397979).

- L'ordinateur sur lequel vous installez l'agent Gestionnaire de connexions hybrides local décrit plus loin dans cet article :

	- doit être capable de se connecter à Azure sur le port 5671 ;
	- doit être capable d'accéder au *nom_d'hôte*:*numéro_de_port* de votre ressource locale. 

> [WACOM.NOTE] Les étapes décrites dans cet article partent du principe que vous utilisez le navigateur à partir de l'ordinateur qui hébergera l'agent de connexion hybride local.


##Dans cet article##


[Création d'un site web dans le portail Azure Preview](#CreateSite)

[Création d'une connexion hybride et d'un service BizTalk](#CreateHC)

[Installation du Gestionnaire de connexions hybrides local pour terminer la connexion](#InstallHCM)

[Étapes suivantes](#NextSteps)


## Création d'un site web dans le portail Azure Preview ##

> [WACOM.NOTE] Si vous avez déjà créé dans le portail Azure Preview un site web que vous voulez utiliser pour ce didacticiel, vous pouvez commencer directement à la section [Création d'une connexion hybride et d'un service BizTalk](#CreateHC) .

1. En bas à gauche du [portail Azure Preview](https://portal.azure.com), cliquez sur **Nouveau**, puis choisissez **Site web**.
	
	![New button][New]
	
	![New website][NewWebsite]
	
2. Dans le volet **Site web**, fournissez un nom pour votre site web, puis cliquez sur **Créer**. 
	
	![Website name][WebsiteCreationBlade]
	
3. Après quelques instants, le site web est créé et son volet apparaît. Le volet est un tableau de bord vertical qu'il est possible de faire défiler et qui vous permet de gérer votre site.
	
	![Website running][WebSiteRunningBlade]
	
4. Pour vérifier que le site est en ligne, cliquez sur l'icône **Parcourir** pour afficher la page par défaut.
	
	![Click browse to see your website][Browse]
	
	![Default website page][DefaultWebSitePage]
	
Vous allez ensuite créer une connexion hybride et un service BizTalk pour le site web.

<a name="CreateHC"></a>
## Création d'une connexion hybride et d'un service BizTalk ##

1. Une fois de retour dans le portail Azure Preview, faites défiler jusqu'au volet correspondant à votre site web et cliquez sur **Connexions hybrides**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. Dans le volet Connexions hybrides, cliquez sur **Ajouter**.
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. Le volet **Ajouter une connexion hybride** s'ouvre.  Comme il s'agit de votre première connexion hybride, l'option **Nouvelle connexion hybride** est présélectionnée, et le volet **Créer une connexion hybride** s'ouvre.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	Dans le volet **Créer une connexion hybride** :
	- Dans la zone **Nom**, indiquez un nom pour la connexion.
	- Dans la zone **Nom d'hôte**, entrez le nom de l'ordinateur local qui héberge votre ressource.
	- Dans la zone **Port**, entrez le numéro du port utilisé par votre ressource locale (1433 pour une instance par défaut SQL Server).
	- Cliquez sur **Service Biz Talk**


4. Le volet **Créer un service Biz Talk** s'ouvre. Entrez un nom pour le service BizTalk, puis cliquez sur **OK**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	Le volet **Créer un service Biz Talk** se ferme et vous revenez au volet **Créer une connexion hybride**.
	
5. Dans le volet Créer une connexion hybride, cliquez sur **OK**. 
	
	![Click OK][CreateBTScomplete]
	
6. Lorsque le processus est terminé, la zone des notifications du portail vous informe que la connexion a été créée.
	
	![Success notification][CreateHCSuccessNotification]
	
7. Dans le volet du site web, l'icône**Connexions hybrides** indique à présent que 1 connexion hybride a été créée.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
À ce stade, vous avez terminé une partie importante de l'infrastructure de connexion hybride cloud. Vous allez ensuite créer un élément local.

<a name="InstallHCM"></a>
## Installation du Gestionnaire de connexions hybrides local pour terminer la connexion ##

1. Dans le volet du site web, cliquez sur l'icône Connexions hybrides. 
	
	![Hybrid connections icon][HCIcon]
	
2. Dans le volet **Connexions hybrides**, la colonne **État** correspondant au point de terminaison ajouté récemment indique **Non connecté**. Cliquez sur la connexion pour la configurer.
	
	![Not connected][NotConnected]
	
	Le volet Connexion hybride s'ouvre.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Dans le volet, cliquez sur **Configuration de l'écouteur**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Le volet **Propriétés de la connexion hybride** s'ouvre. Sous **Gestionnaire de connexions hybrides locales**, choisissez **Cliquez ici pour installer**.
	
	![Click here to install][ClickToInstallHCM]
	
5. Dans la boîte de dialogue Exécution de l'application - Avertissement de sécurité, cliquez sur **Exécuter** pour continuer.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	Dans la boîte de dialogue **Contrôle de compte d'utilisateur**, choisissez **Oui**.
	
	![Choose Yes][UAC]
	
7. Le Gestionnaire de connexion hybride est téléchargé et installé pour vous. 
	
	![Installing][HCMInstalling]
	
8. Une fois l'installation terminée, cliquez sur **Fermer**.
	
	![Click Close][HCMInstallComplete]
	
	Dans le volet **Connexions hybrides**, la colonne **État** indique désormais **Connecté**. 
	
	![Connected Status][HCStatusConnected]

Maintenant que l'infrastructure de connexion hybride est terminée, vous pouvez créer une application hybride qui l'utilise. 

<a name="NextSteps"></a>
## Étapes suivantes##

- Pour des informations sur la création d'une application web ASP.NET utilisant une connexion hybride, consultez [Connexion à une instance SQL Server locale à partir d'un site web Azure au moyen de connexions hybrides](http://go.microsoft.com/fwlink/?LinkID=397979).

- Pour des informations sur l'utilisation d'une connexion hybride avec un service mobile, consultez [Connexion à une instance SQL Server locale à partir d'un service mobile Azure au moyen de connexions hybrides](http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/).

###Ressources supplémentaires

[Vue d'ensemble des connexions hybrides](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist présente les connexions hybrides (vidéo Channel 9))](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Site web des connexions hybrides](http://azure.microsoft.com/fr-fr/services/biztalk-services/)

[BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l'échelle, Configurer et Connexion hybride](http://azure.microsoft.com/fr-fr/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Création d'un cloud hybride réel avec la portabilité transparente des applications (vidéo Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Connexion à une instance SQL Server locale à partir d'Azure Mobile Services au moyen de connexions hybrides (vidéo Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

<!-- IMAGES -->
[New] :./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite] :./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade] :./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade] :./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse] :./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage] :./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon] :./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC] :./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades] :./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS] :./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete] :./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification] :./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated] :./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon] :./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected] :./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade] :./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup] :./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM] :./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning] :./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC] :./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling] :./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete] :./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected] :./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

<!--HONumber=35.1-->
