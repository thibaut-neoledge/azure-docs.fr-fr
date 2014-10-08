<properties linkid="mobile-services-dotnet-backend-hybrid-connections-get-started" urlDisplayName="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" pageTitle="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections - Azure Mobile Services" metaKeywords="" description="Learn how to connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" metaCanonical="" services="" documentationCenter="Mobile" title="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Connexion à un serveur SQL Server local à partir d'un service mobile Azure au moyen de connexions hybrides

Lorsqu'une entreprise effectue une transition vers le cloud, il est souvent nécessaire de laisser certaines ressources en local, que ce soit pour des raisons techniques, de conformité ou de sécurité. Mobile Services vous permet de créer facilement une couche de mobilité hébergée sur le cloud par-dessus ces ressources, tout en vous permettant de vous connecter en toute sécurité en local au moyen de connexions hybrides. Les ressources prises en charge incluent toute ressource s'exécutant sur un port TCP statique, y compris Microsoft SQL Server, MySQL, les API web HTTP et la plupart des services web personnalisés.

Au cours de ce didacticiel, vous allez apprendre à modifier un service mobile principal .NET afin d'utiliser une base de données SQL Server locale au lieu de la base de données SQL Azure par défaut approvisionnée avec votre service. Bien que les connexions hybrides soient prises en charge pour les services mobiles principaux JavaScript, cette rubrique traite uniquement des services mobiles principaux .NET.

Cette rubrique vous familiarise avec ces étapes de base :

1.  [Configuration requise][]
2.  [Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local][]
3.  [Création d'une connexion hybride][]
4.  [Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion][]
5.  [Modification d'un service mobile pour pouvoir utiliser la connexion][]

<a name="Prerequisites"></a>

## Configuration requise

Pour réaliser ce didacticiel, vous avez besoin des produits suivants. Tous sont disponibles gratuitement, de sorte que vous pouvez commencer à développer gratuitement pour Azure.

-   **Visual Studio 2013** - Pour télécharger une version d'évaluation gratuite de Visual Studio 2013, consultez la page [Téléchargements Visual Studio][]. Installez ceci avant de poursuivre.

-   **SQL Server 2014 Express with Tools** - Téléchargez gratuitement Microsoft SQL Server Express sur la [page des bases de données Microsoft Web Platform][]. Choisissez la version **Express** (pas LocalDB). La version **Express with Tools** inclut SQL Server Management Studio, que vous utiliserez dans ce didacticiel.

Vous aurez également besoin d'un ordinateur local se connectant à Azure au moyen de connexions hybrides. Celui-ci doit répondre aux critères suivants :

-   Capacité à se connecter à Azure au moyen du port 5671
-   Capacité à atteindre le *nom\_hôte*:*numéro\_port* de la ressource locale à laquelle vous voulez vous connecter. La ressource peut ou non être hébergée sur le même ordinateur.

<a name="InstallSQL"></a>

## Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local

Pour utiliser une base de données SQL Server ou SQL Server Express locale avec une connexion hybride, TCP/IP doit être activé sur un port statique. Les instances par défaut dans SQL Server utilisent le port statique 1433, mais pas les instances nommées.

Pour plus d'informations sur la façon de configurer SQL Server pour qu'il réponde aux conditions décrites ci-dessus, consultez [Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local][1]. Si vous avez déjà installé SQL Server dans une configuration et dans un environnement qui répondent aux conditions décrites ci-dessus, vous pouvez passer directement à [Création d'une base de données SQL Server en local][].

Pour les besoins de ce didacticiel, nous supposerons que le nom de la base de données est **OnPremisesDB**, qu'elle s'exécute sur le port **1433** et que le nom d'hôte de l'ordinateur est **onPremisesServer**.

<a name="CreateHC"></a>

## Création d'une connexion hybride

1.  Sur l'ordinateur local, connectez-vous au [portail de gestion Azure][].

2.  En bas du volet de navigation, sélectionnez **+NOUVEAU**, **App Services**, **Service BizTalk**, puis **Création personnalisée**

    ![Create BizTalk Service][]

3.  Fournissez un **Nom du service BizTalk** et sélectionnez une **Édition**.

    ![Configure new BizTalk Service][]

    Ce didacticiel utilise **mobile1**. Vous aurez besoin d'un nom unique pour votre nouveau service BizTalk.

4.  Une fois que le service BizTalk a été créé, sélectionnez l'onglet **Connexions hybrides**, puis cliquez sur **Ajouter**.

    ![Add Hybrid Connection][]

    Cette action crée une connexion hybride.

5.  Fournissez un **Nom** et un **Nom d'hôte** pour votre connexion hybride et définissez **Port** sur `1433`.

    ![Configure Hybrid Connection][]

    Le nom d'hôte correspond au nom du serveur local. Cette action configure la connexion hybride pour pouvoir accéder à SQL Server s'exécutant sur le port 1433.

6.  Une fois que la nouvelle connexion a été créée, elle apparaît dans le tableau suivant.

    ![Hybrid Connection successfully created][]

    Le statut de la nouvelle connexion indique **Installation locale incomplète**.

À présent, nous devons installer le Gestionnaire de connexions hybrides sur l'ordinateur local.

<a name="InstallHCM"></a>

## Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion

Le Gestionnaire de connexions hybrides permet à votre ordinateur local de se connecter à Azure et de relayer le trafic TCP. Vous devez installer ce logiciel sur l'ordinateur local auquel vous essayez d'accéder à partir d'Azure.

1.  Sélectionnez la connexion que vous venez de créer, puis dans la barre inférieure, cliquez sur **Installation locale**.

    ![On-Premises Setup][]

2.  Cliquez sur **Installer et configurer**.

    ![Install Hybrid Connection Manager][]

    Cette option installe une instance personnalisée du Gestionnaire de connexions, qui est déjà préconfiguré pour utiliser la connexion hybride que vous venez de créer.

3.  Effectuez les étapes restantes de l'installation du Gestionnaire de connexions.

    ![Hybrid Connection Manager setup][]

    Au terme de l'installation, le statut de la connexion hybride passe à **1 instance connectée**. Il vous faudra peut-être actualiser le navigateur et patienter quelques minutes. L'installation locale est à présent terminée.

    ![Hybrid Connection connected][]

<a name="CreateService"></a>

## Modification d'un service mobile pour pouvoir utiliser la connexion

### Association d'une connexion hybride à un service

1.  Sous l'onglet **Services mobiles** du portail, sélectionnez un service mobile existant ou créez-en un.

    > [WACOM.NOTE]Veillez à sélectionner un service qui a été créé au moyen de .NET Backend ou créez un service mobile principal .NET. Pour apprendre à créer un service mobile principal .NET, consultez la page [Prise en main de Mobile Services][]

2.  Sous l'onglet **Configurer** de votre service mobile, accédez à la section **Connexions hybrides** et sélectionnez **Ajouter une connexion hybride**.

    ![Associate Hybrid Connection][]

3.  Sélectionnez la connexion hybride que nous venons de créer sous l'onglet Services BizTalk, puis appuyez sur **OK**.

    ![Pick associated Hybrid Connection][]

Le service mobile est à présent associé à la nouvelle connexion hybride.

### Mise à jour du service pour utiliser la chaîne de connexion en local

Pour finir, nous devons créer un paramètre d'application permettant de stocker la valeur de la chaîne de connexion sur notre serveur SQL Server local. Nous devons ensuite modifier le service mobile pour pouvoir utiliser la nouvelle chaîne de connexion.

1.  Sous l'onglet **Configurer** dans **Paramètres d'application**, ajoutez un nouveau paramètre d'application intitulé `onPremisesDatabase` avec une valeur comme `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}`.

    ![App setting for connection string][]

    Remplacez `{password}` par le mot de passe sécurisé de votre base de données locale.

2.  Appuyez sur **Enregistrer** pour enregistrer la connexion hybride et le paramètre d'application que nous venons de créer.

3.  Dans Visual Studio 2013, ouvrez le projet qui définit votre service mobile basé sur .NET.

    Pour apprendre à télécharger votre projet principal .NET, consultez la page [Prise en main de Mobile Services][] .

4.  Dans l'Explorateur de solutions, développez le dossier **Modèles** et ouvrez le fichier de modèle de données, se terminant par *Context.cs*.

5.  Modifiez le constructeur d'instance **DbContext** pour qu'il soit similaire à l'extrait de code suivant :

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base(ConfigurationManager.AppSettings["onPremisesDatabase"])
            {
            }

            // snipped
        }

    Le service va à présent utiliser la nouvelle chaîne de connexion hybride dans les paramètres d'application dans Azure.

6.  Publiez vos modifications et utilisez une application cliente connectée à votre service mobile pour appeler certaines opérations permettant de générer des modifications dans la base de données.

7.  Ouvrez SQL Management Studio sur l'ordinateur local sur lequel SQL Server s'exécute, puis dans l'Explorateur d'objets, développez la base de données **OnPremisesDB** et développez **Tables**.

8.  Cliquez avec le bouton droit sur la table **hybridService1.TodoItems** et choisissez **Sélectionner les 1 000 premières lignes** pour afficher les résultats.

    ![SQL Management Studio][]

Les modifications générées dans votre application ont été transmises par notification push par votre service mobile à votre base de données locale.

## Voir aussi

-   [Site web des connexions hybrides][]
-   [Aperçu des connexions hybrides][]
-   [BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l'échelle, Configurer et Connexion hybride][]

<!-- IMAGES -->

  [Configuration requise]: #Prerequisites
  [Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local]: #InstallSQL
  [Création d'une connexion hybride]: #CreateHC
  [Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion]: #InstallHCM
  [Modification d'un service mobile pour pouvoir utiliser la connexion]: #CreateService
  [Téléchargements Visual Studio]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [page des bases de données Microsoft Web Platform]: http://www.microsoft.com/web/platform/database.aspx
  [1]: /en-us/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL
  [Création d'une base de données SQL Server en local]: /en-us/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB
  [portail de gestion Azure]: http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409
  [Create BizTalk Service]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
  [Configure new BizTalk Service]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
  [Add Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
  [Configure Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
  [Hybrid Connection successfully created]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
  [On-Premises Setup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png
  [Install Hybrid Connection Manager]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
  [Hybrid Connection Manager setup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
  [Hybrid Connection connected]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
  [Prise en main de Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Associate Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
  [Pick associated Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
  [App setting for connection string]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
  [SQL Management Studio]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
  [Site web des connexions hybrides]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [Aperçu des connexions hybrides]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l'échelle, Configurer et Connexion hybride]: http://azure.microsoft.com/en-us/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
