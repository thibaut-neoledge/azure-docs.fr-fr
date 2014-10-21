<properties linkid="web-sites-hybrid-connection" title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" description="Create a connection between an Azure website and an on-premises resource that uses a static TCP port" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm"></tags>

# Connexion d'un site web Azure à une ressource locale au moyen de connexions hybrides

Vous pouvez connecter un site web sur Microsoft Azure à n'importe quelle ressource locale qui utilise un port TCP statique, par exemple, SQL Server, MySQL, les API web HTTP, les services mobiles et la plupart des services web personnalisés. Cette section montre comment créer une connexion hybride entre un site web Azure et une base de données SQL Server locale.

> [WACOM.NOTE] La partie Sites web de la fonctionnalité Connexions hybrides n'est disponible que dans la [version préliminaire du portail Azure][]. Pour créer une connexion dans BizTalk Services, consultez la page [Connexions hybrides][].

## Configuration requise

-   Un abonnement Azure. Pour un abonnement gratuit, consultez la page [Version d'évaluation gratuite d'Azure][].

-   Pour utiliser une base de données SQL Server ou SQL Server Express locale avec une connexion hybride, TCP/IP doit être activé sur un port statique. L'utilisation d'une instance par défaut sur SQL Server est recommandée, car elle utilise le port statique 1433. Pour des informations sur l'installation et la configuration de SQL Server Express en vue de l'utiliser avec des connexions hybrides, consultez la page [Connexion à une instance SQL Server locale à partir d'un site web Azure au moyen de connexions hybrides][].

-   L'ordinateur sur lequel vous installez l'agent Gestionnaire de connexions hybrides local décrit plus loin dans cet article :

    -   doit être capable de se connecter à Azure sur le port 5671 ;
    -   doit être capable d'accéder au *nom\_d'hôte*:*numéro\_de\_port* de votre ressource locale.

> [WACOM.NOTE] Les étapes décrites dans cet article partent du principe que vous utilisez le navigateur à partir de l'ordinateur qui hébergera l'agent de connexion hybride local.

## Dans cet article

[Création d'un site web dans la version préliminaire du portail Azure][]

[Création d'une connexion hybride et d'un service BizTalk][]

[Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion][]

[Étapes suivantes][]

## Création d'un site web dans la version préliminaire du portail Azure

> [WACOM.NOTE] Si vous avez déjà créé dans la version préliminaire du portail Azure un site web que vous voulez utiliser pour ce didacticiel, vous pouvez commencer directement à la section [Création d'une connexion hybride et d'un service BizTalk][].

1.  Dans le coin inférieur gauche de la [version préliminaire du portail Azure][], cliquez sur **Nouveau**, puis choisissez **Site Web**.

    ![New button][]

    ![New website][]

2.  Dans le volet **Site Web**, fournissez un nom pour votre site web, puis cliquez sur **Créer**.

    ![Website name][]

3.  Après quelques instants, le site web est créé et son volet apparaît. Le volet est un tableau de bord vertical qu'il est possible de faire défiler et qui vous permet de gérer votre site.

    ![Website running][]

4.  Pour vérifier que le site est actif, cliquez sur l'icône **Parcourir** afin d'afficher la page par défaut.

    ![Click browse to see your website][]

    ![Default website page][]

Vous allez ensuite créer une connexion hybride et un service BizTalk pour le site web.

<a name="CreateHC"></a>

## Création d'une connexion hybride et d'un service BizTalk

1.  Une fois de retour dans la version préliminaire du portail, faites défiler jusqu'au volet correspondant à votre site web et cliquez sur **Connexions hybrides**.

    ![Hybrid connections][]

2.  Dans le volet Connexions hybrides, cliquez sur **Ajouter**.

    ![Add a hybrid connnection][]

3.  Le volet **Ajouter une connexion hybride** s'ouvre. Comme il s'agit de votre première connexion hybride, l'option **Nouvelle connexion hybride** est présélectionnée, et le volet **Créer une connexion hybride** s'ouvre.

    ![Create a hybrid connection][]

    Dans le volet **Créer une connexion hybride** :

    -   Sous **Nom**, entrez un nom pour la connexion.
    -   Sous **Nom d'hôte**, entrez le nom de l'ordinateur local qui héberge votre ressource.
    -   Sous **Port**, entrez le numéro du port utilisé par votre ressource locale (1433 pour une instance par défaut SQL Server).
    -   Cliquez sur **Service BizTalk**.

4.  Le volet **Créer un service BizTalk** s'ouvre. Entrez un nom pour le service BizTalk, puis cliquez sur **OK**.

    ![Create BizTalk service][]

    Le volet **Créer un service BizTalk** se ferme et vous revenez au volet **Créer une connexion hybride**.

5.  Dans le volet Créer une connexion hybride, cliquez sur **OK**.

    ![Click OK][]

6.  Lorsque le processus est terminé, la zone des notifications du portail vous informe que la connexion a été créée.

    ![Success notification][]

7.  Dans le volet du site web, l'icône **Connexions hybrides** indique à présent que 1 connexion hybride a été créée.

    ![One hybrid connection created][]

À ce stade, vous avez terminé une partie importante de l'infrastructure de connexion hybride cloud. Vous allez ensuite créer un élément local.

<a name="InstallHCM"></a>

## Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion

1.  Dans le volet du site web, cliquez sur l'icône Connexions hybrides.

    ![Hybrid connections icon][]

2.  Dans le volet **Connexions hybrides**, la colonne **Statut** correspondant au point de terminaison ajouté récemment indique **Non connecté**. Cliquez sur la connexion pour la configurer.

    ![Not connected][]

    Le volet Connexion hybride s'ouvre.

    ![NotConnectedBlade][]

3.  Dans le volet, cliquez sur **Configuration de l'écouteur**.

    ![Click Listener Setup][]

4.  Le volet **Propriétés de la connexion hybride** s'ouvre. Sous **Gestionnaire de connexions hybrides local**, cliquez sur **Cliquez ici pour l'installer**.

    ![Click here to install][]

5.  Dans la boîte de dialogue Exécution de l'application - Avertissement de sécurité, cliquez sur **Exécuter** pour continuer.

    ![Choose Run to continue][]

6.  Dans la boîte de dialogue **Contrôle de compte d'utilisateur**, cliquez sur **Oui**.

    ![Choose Yes][]

7.  Le Gestionnaire de connexion hybride est téléchargé et installé pour vous.

    ![Installation][]

8.  Une fois l'installation terminée, cliquez sur **Fermer**.

    ![Click Close][]

    Dans le volet **Connexions hybrides**, la colonne **Statut** indique à présent **Connecté**.

    ![Connected Status][]

Maintenant que l'infrastructure de connexion hybride est terminée, vous pouvez créer une application hybride qui l'utilise.

<a name="NextSteps"></a>

## Étapes suivantes

-   Pour des informations sur la création d'une application web ASP.NET utilisant une connexion hybride, consultez la page [Connexion à une instance SQL Server locale à partir d'un site web Azure au moyen de connexions hybrides][].

-   Pour des informations sur l'utilisation d'une connexion hybride avec un service mobile, consultez la page [Connexion à une instance SQL Server locale à partir d'un service mobile Azure au moyen de connexions hybrides][].

### Ressources supplémentaires

[Aperçu des connexions hybrides][Connexions hybrides]

[Josh Twist présente les connexions hybrides (vidéo Channel 9)][]

[Site web des connexions hybrides][]

[BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l'échelle, Configurer et Connexion hybride][]

[Création d'un cloud hybride réel avec la portabilité transparente des applications (vidéo Channel 9)][]

[Connexion à une instance SQL Server locale à partir d'Azure Mobile Services au moyen de connexions hybrides (vidéo Channel 9)][]

<!-- IMAGES -->

  [version préliminaire du portail Azure]: https://portal.azure.com
  [Connexions hybrides]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Connexion à une instance SQL Server locale à partir d'un site web Azure au moyen de connexions hybrides]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Création d'un site web dans la version préliminaire du portail Azure]: #CreateSite
  [Création d'une connexion hybride et d'un service BizTalk]: #CreateHC
  [Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion]: #InstallHCM
  [Étapes suivantes]: #NextSteps
  [New button]: ./media/web-sites-hybrid-connection-get-started/B01New.png
  [New website]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
  [Website name]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
  [Website running]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
  [Click browse to see your website]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
  [Default website page]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
  [Hybrid connections]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
  [Add a hybrid connnection]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
  [Create a hybrid connection]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
  [Create BizTalk service]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
  [Click OK]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
  [Success notification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
  [One hybrid connection created]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
  [Hybrid connections icon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
  [Not connected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
  [Click Listener Setup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
  [Click here to install]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
  [Choose Run to continue]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
  [Choose Yes]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
  [Installation]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
  [Click Close]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
  [Connected Status]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
  [Connexion à une instance SQL Server locale à partir d'un service mobile Azure au moyen de connexions hybrides]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Josh Twist présente les connexions hybrides (vidéo Channel 9)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [Site web des connexions hybrides]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l'échelle, Configurer et Connexion hybride]: http://azure.microsoft.com/fr-fr/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Création d'un cloud hybride réel avec la portabilité transparente des applications (vidéo Channel 9)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [Connexion à une instance SQL Server locale à partir d'Azure Mobile Services au moyen de connexions hybrides (vidéo Channel 9)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
