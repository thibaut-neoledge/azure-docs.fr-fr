<properties 
	pageTitle="Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio" 
	description="Découvrez comment dépanner une application web Microsoft Azure via des outils de journalisation, de suivi et de débogage distants intégrés dans Visual Studio 2013." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="tdykstra"/>

# Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio

## Vue d'ensemble

Ce didacticiel explique comment utiliser les outils de Visual Studio pour déboguer une application Web pendant son exécution dans [App Service](http://go.microsoft.com/fwlink/?LinkId=529714), en activant le [mode débogage](http://www.visualstudio.com/fr-fr/get-started/debug-your-app-vs.aspx) à distance ou en consultant les journaux des applications et des serveurs Web.

Vous apprendrez ce qui suit :

* quelles sont les fonctions de gestion d’application web Microsoft Azure proposées par Visual Studio ;
* comment utiliser l’affichage à distance de Visual Studio pour apporter des modifications rapides dans une application web à distance ;
* comment exécuter le mode débogage à distance lorsqu’un projet est exécuté dans Microsoft Azure, pour une application web comme pour une tâche web.
* la création de journaux de suivi d'application et leur affichage pendant leur création par l'application ;
* l'affichage des journaux de serveur Web, notamment des messages d'erreur détaillés et le suivi des demandes ayant échoué ;
* l'envoi de journaux de diagnostic à un compte Azure Storage et leur affichage depuis ce compte.

Si vous disposez de Visual Studio Ultimate, vous pouvez également utiliser [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) à des fins de débogage. IntelliTrace n’est pas couvert dans ce didacticiel.

## <a name="prerequisites"></a>Configuration requise

Ce didacticiel fonctionne avec l’environnement de développement, le projet Web et l’application Web Azure que vous avez configurés dans le didacticiel [Prise en main d’Azure et ASP.NET][GetStarted]. Pour les sections WebJobs, vous aurez besoin de l’application créée dans le cadre de la [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure][GetStartedWJ].

Les exemples de code inclus dans ce didacticiel sont destinés à une application Web C# MVC, mais les procédures de résolution de problèmes sont identiques pour les applications Visual Basic et Web Forms.

Le débogage distant nécessite Visual Studio 2013 ou Visual Studio 2012 avec Update 4. Les fonctionnalités de débogage distant et l’**Explorateur de serveurs** pour WebJobs nécessitent [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) ou une version ultérieure. Les autres fonctionnalités illustrées dans ce didacticiel fonctionnent également dans Visual Studio 2013 Express pour le Web et Visual Studio 2012 Express pour le Web.

La fonctionnalité de diffusion de journaux en continu est opérationnelle uniquement pour les applications ciblant .NET Framework 4 ou une version ultérieure.

## <a name="sitemanagement"></a>Gestion et configuration de l’application Web

Visual Studio permet d’accéder à un sous-ensemble des fonctionnalités de gestion des applications Web et des paramètres de configuration disponibles sur le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Cette section présente les éléments disponibles.

1. Si vous n'êtes pas déjà connecté à Azure dans Visual Studio, cliquez sur le bouton **Connect to Azure** dans l'**Explorateur de serveurs**.

	Vous pouvez aussi installer un certificat de gestion permettant d'accéder à votre compte. Si vous choisissez d'installer un certificat, cliquez avec le bouton droit sur le nœud **Azure** dans l'**Explorateur de serveurs**, puis cliquez sur **Gérer les abonnements** dans le menu contextuel. Dans la boîte de dialogue **Manage Azure Subscriptions**, cliquez sur l’onglet **Certificates**, puis sur **Import**. Suivez la procédure pour télécharger et importer un fichier d’abonnement (portant l’extension *.publishsettings*) pour votre compte Azure.

	> [AZURE.NOTE]Si vous téléchargez un fichier d’abonnement, enregistrez-le dans un dossier situé hors de vos répertoires de code source (par exemple, dans le dossier Téléchargements), puis supprimez-le une fois l’importation terminée. Si un utilisateur malveillant accède au fichier d’abonnement, il peut modifier, créer et supprimer vos services Azure.

	Pour plus d'informations sur la connexion aux ressources Azure à partir de Visual Studio, consultez la page [Gérer des comptes, des abonnements et des rôles d'administrateur](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. Dans l’**Explorateur de serveurs**, développez **Azure**, puis **Applications Web**.

3. Cliquez avec le bouton droit sur le nœud de l’application Web créée au cours de la [Prise en main d’Azure et ASP.NET][GetStarted], puis cliquez sur **Afficher les paramètres**.

	![Afficher les paramètres dans l'Explorateur de serveurs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	L’onglet **Application Web Azure** apparaît, affichant les tâches de gestion et de configuration des applications Web disponibles dans Visual Studio.

	![Fenêtre Application web Microsoft Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	Dans ce didacticiel, vous utiliserez les menus déroulants de suivi et de journalisation. Vous utiliserez également le débogage à distance en l'activant d'une façon différente.
   
	Pour plus d’informations sur les cases à cocher Paramètres de l’application et Chaînes de connexion de cette fenêtre, consultez l’article [Fonctionnement des chaînes d’application et de connexion dans les applications Web Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx) (en anglais).

	Pour mener à bien une tâche de gestion d’application Web qui n’est pas disponible dans cette fenêtre, cliquez sur **Paramètres d’application web complets** pour ouvrir une fenêtre de navigateur vers le portail de gestion. Pour plus d’informations, consultez [Configurer des applications Web](/fr-fr/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

## <a name="remoteview"></a>Accès aux fichiers d’applications Web dans l’Explorateur de serveurs

En général, vous déployez un projet Web avec l’indicateur `customErrors` défini sur la valeur `On` ou `RemoteOnly` dans le fichier Web.config, ce qui signifie que vous n’obtenez pas de message d’erreur utile en cas de problème. Généralement, ces messages se présentent sous la forme d'une page comme celles qui suivent.

**Erreur de serveur dans l’application « / » :**

![Page d'erreur inutile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Nous avons rencontré une erreur :**

![Page d'erreur inutile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Le site Web ne peut pas afficher la page**

![Page d’erreur inutile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Généralement, la méthode la plus simple pour rechercher la cause d’une erreur consiste à activer les messages d’erreur détaillés : la première capture d’écran de la série précédente montre comment procéder. Vous devez modifier le fichier Web.config déployé. Vous pouvez modifier le fichier *Web.config* dans le projet et redéployer le projet, ou créer une [transformation Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) et déployer une build de débogage, mais il existe un moyen plus rapide : dans l’**Explorateur de solutions**, vous pouvez directement afficher et modifier des fichiers dans l’application web distante en utilisant la fonctionnalité d’*affichage distant*.

1. Dans l’**Explorateur de serveurs**, développez l’élément **Azure**, **Service d’applications**, puis le groupe de ressources où se trouve votre application web et développez le nœud de votre application web.

	Pour afficher les nœuds qui vous donnent accès aux fichiers de contenu et les fichiers journaux de l’application web.

2. Développez le nœud **Fichiers**, puis double-cliquez sur le fichier *Web.config*.

	![Ouvrir Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	Visual Studio ouvre le fichier Web.config à partir de l’application web, puis affiche l’élément [Distant] en regard du nom du fichier dans la barre de titre.

3. Ajoutez la ligne suivante à l’élément `system.web` :

	`<customErrors mode="off"></customErrors>`

	![Modifier Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Actualisez le navigateur qui affiche le message d'erreur inutile : à présent, vous obtenez un message d'erreur détaillé, comme dans l'exemple suivant :

	![Messages d’erreur détaillés](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	L'erreur affichée est générée par l'ajout de la ligne affichée en rouge à *Views\Home\Index.cshtml*.

La modification du fichier Web.config n’est qu’un exemple des scénarios dans lesquels la capacité de lecture et de modification des fichiers sur votre application web Microsoft Azure simplifie la résolution des problèmes.

## <a name="remotedebug"></a>Débogage à distance des applications Web

Si le message d'erreur détaillé ne fournit pas assez d'informations et que vous ne pouvez pas recréer l'erreur localement, une autre méthode pour résoudre le problème consiste à exécuter le mode de débogage à distance. Vous pouvez définir des points d'arrêt, manipuler directement la mémoire, parcourir le code en détail et même modifier le chemin d'accès du code.

Le débogage à distance ne fonctionne pas avec les éditions Express de Visual Studio.

Cette section vous montre comment procéder au débogage à distance en utilisant le projet que vous créez dans le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted].

1. Ouvrez le projet Web que vous avez créé durant le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted].

1. Ouvrez *Controllers\HomeController.cs*.

2. Supprimez la méthode `About()` et insérez le code suivant à la place.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Définissez un point d’arrêt](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) sur la ligne `ViewBag.Message`.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**.

2. Dans le menu déroulant **Profil**, sélectionnez le même profil que celui utilisé dans [Prise en main d'Azure et d'ASP.NET][GetStarted].

3. Cliquez sur l'onglet **Paramètres**, remplacez **Configuration** par **Debug**, puis cliquez sur **Publier**.

	![Publier en mode débogage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. Une fois le déploiement terminé, lorsque votre navigateur s’ouvre, affichant l’adresse URL Microsoft Azure de votre application web, fermez le navigateur.

5. Pour Visual Studio 2013 : dans l’**Explorateur de serveurs**, développez **Azure**, puis **Applications Web**. Ensuite, cliquez avec le bouton droit sur votre application Web, puis cliquez sur **Attacher le débogueur**.

	![Attacher le débogueur](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	Le navigateur ouvre automatiquement votre page d'accueil exécutée dans Azure. Vous devrez peut-être attendre environ 20 secondes pendant qu'Azure configure le serveur pour le débogage. Ce retard se produit uniquement la première fois que vous exécutez le mode débogage sur une application web. Il ne se reproduira pas lors des redémarrages du mode débogage au cours des 48 prochaines heures.

6. Pour Visual Studio 2012 with Update 4 :<a id="vs2012"></a>

	* Sur le portail de gestion Microsoft Azure, accédez à l’option **Paramètres > Paramètres de l’application** de votre application Web, puis faites défiler la page jusqu’à la section **Débogage**.

	* Définissez l’option **Débogage distant** sur **Activé**, puis l’option **Visual Studio Version** sur **2012**.

	* Dans le menu **Déboguer** de Visual Studio, cliquez sur **Attacher au processus**.

	* Dans la zone **Qualificateur**, saisissez l’URL de votre application Web, sans indiquer le préfixe `http://`.

	* Sélectionnez **Afficher les processus de tous les utilisateurs**.

	* Lorsque vous êtes invité à saisir les informations d’identification, entrez le nom d’utilisateur et le mot de passe d’un compte disposant des autorisations adéquates pour le déploiement d’un contenu sur l’application web. Pour obtenir ces informations d’identification, accédez à l’onglet Tableau de bord associé à votre application Web sur le portail de gestion, puis cliquez sur **Télécharger le profil de publication**. Ouvrez le fichier dans un éditeur de texte : vous trouverez le nom d'utilisateur et le mot de passe après les premières occurrences de **userName=** et **userPWD=**.

	* Lorsque les processus sont affichés dans la table **Processus disponibles**, sélectionnez **w3wp.exe**, puis cliquez sur **Joindre**.

	* Ouvrez un navigateur afin d’accéder à l’URL de votre application web.

	Vous devrez peut-être attendre environ 20 secondes pendant qu'Azure configure le serveur pour le débogage. Ce retard se produit uniquement la première fois que vous exécutez le mode débogage sur une application web. Il ne se reproduira pas lors des redémarrages du mode débogage au cours des 48 prochaines heures.

6. Cliquez sur **À propos de** dans le menu.

	Visual Studio s'arrête sur le point d'arrêt et le code s'exécute dans Azure, et non pas sur votre ordinateur local.

7. Passez la souris sur la variable `currentTime` pour afficher la valeur de temps.

	![Afficher une variable en mode débogage sur Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	Le temps affiché correspond au fuseau horaire du serveur Azure, qui peut différer de celui de votre ordinateur local.

8. Entrez une nouvelle valeur pour la variable `currentTime`, comme « En cours d’exécution dans Azure ».

5. Appuyez sur F5 pour continuer.

	La page « À propos de » exécutée dans Azure affiche la nouvelle valeur entrée dans la variable currentTime.

	![Page « À propos de » avec une nouvelle valeur](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Débogage à distance de WebJobs

Cette section explique comment déboguer un élément à distance en utilisant le projet et l’application Web que vous avez créés avec le didacticiel [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure](websites-dotnet-webjobs-sdk.md). Les fonctionnalités illustrées dans cette section sont uniquement disponibles dans Visual Studio 2013 avec Update 4. Le débogage à distance fonctionne uniquement avec les tâches Web en continu. Les tâches Web planifiées et à la demande ne prennent pas en charge le débogage.

1. Ouvrez le projet Web que vous avez créé dans [Prise en main du Kit de développement logiciel Azure WebJobs][GetStartedWJ].

1. Dans le projet ContosoAdsWebJob, ouvrez *Functions.cs*.

2. [Définissez un point d’arrêt](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) sur la première instruction dans la méthode `GnerateThumbnail`.

	![Définir le point d’arrêt](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet Web (pas le projet WebJobs), puis cliquez sur **Publier**.

2. Dans le menu déroulant **Profil**, sélectionnez le même profil que celui utilisé dans [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure](websites-dotnet-webjobs-sdk.md).

3. Cliquez sur l'onglet **Paramètres**, remplacez **Configuration** par **Debug**, puis cliquez sur **Publier**.

	Visual Studio déploie les projets de tâche web et web et votre navigateur ouvre l’URL Microsoft Azure de votre application web.

5. Dans l’**Explorateur de serveurs**, développez **Azure** > **Applications Web** > votre application Web > **WebJobs** > **Continu**, puis cliquez avec le bouton droit sur **ContosoAdsWebJob**.

7. Cliquez sur **Attacher le débogueur**.

	![Attacher le débogueur](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

	Le navigateur ouvre automatiquement votre page d'accueil exécutée dans Azure. Vous devrez peut-être attendre environ 20 secondes pendant qu'Azure configure le serveur pour le débogage. Ce retard se produit uniquement la première fois que vous exécutez le mode débogage sur une application web. La prochaine fois que vous attacherez le débogueur il n’y aura aucun délai, si vous le faites dans les 48 heures.

6. Dans le navigateur web ouvert à la page d’accueil Contoso Ads, créez une publicité.

	La création d’une publicité provoque la création d’un message de file d’attente, qui sera récupéré par la tâche web et traité. Quand le Kit SDK WebJobs appelle la fonction pour traiter le message de file d’attente, le code atteint votre point d’arrêt.

7. Quand le débogueur s’arrête au point d’arrêt, vous pouvez examiner et modifier les valeurs des variables pendant l’exécution du programme dans le cloud. Dans l’illustration suivante, le débogueur affiche le contenu de l’objet blobInfo qui a été passé à la méthode GenerateThumbnail.

	![Objet blobInfo dans le débogueur](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. Appuyez sur F5 pour continuer.

	La méthode GenerateThumbnail achève la création de la miniature.

6. Dans le navigateur, actualisez la page Index ; la miniature apparaît.

6. Dans Visual Studio, appuyez sur Maj+F5 pour arrêter le débogage.

7. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur le nœud ContosoAdsWebJob et cliquez sur **Afficher le tableau de bord**.

8. Connectez-vous avec vos informations d’identification Azure, puis cliquez sur le nom de la tâche web pour accéder à la page de votre tâche web.

	![Cliquer sur ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	Le tableau de bord indique que la fonction GenerateThumbnail a été exécutée récemment.

	(La prochaine fois que vous cliquez sur **Afficher le tableau de bord**, vous ne serez pas obligé de vous connecter et le navigateur accédera directement à la page de votre tâche Web).

9. Cliquez sur le nom de la fonction pour afficher les détails relatifs à son exécution.

	![Détails de la fonction](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Si votre fonction [a écrit des journaux](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs), vous pouvez cliquer sur **ToggleOutput** pour les afficher.

## Notes à propos du débogage à distance

* Nous vous déconseillons d'exécuter le mode débogage en production. Si votre application web de production n’est pas montée en charge sur plusieurs instances de serveur, la fonction de débogage empêche le serveur web de répondre aux autres demandes. Si vous avez plusieurs instances de serveurs web, le fait de joindre le débogueur génèrera une instance aléatoire et vous empêchera de vérifier que les demandes suivantes de votre navigateur parviendront à cette instance. De même, comme vous ne déployez généralement pas une version de débogage dans un environnement de production, les optimisations du compilateur pour les versions Release peuvent empêcher l'affichage des réactions ligne par ligne dans votre code source. Pour résoudre les problèmes de production, la meilleure ressource est constituée des journaux de suivi d'application et de serveur Web.

* Évitez les arrêts longs aux points d'arrêt avec le débogage à distance. Azure considère qu'un processus arrêté pendant plus de quelques minutes ne répond pas, et l'arrête définitivement.

* Pendant le débogage, le serveur envoie des données à Visual Studio, ce qui peut affecter les frais de bande passante. Pour plus d'informations sur les tarifs de bande passante, consultez les [tarifs Azure](/pricing/calculator/).

* Vérifiez que l’attribut `debug` de l’élément `compilation` du fichier *Web.config* est défini sur true. Il est défini sur true par défaut lorsque vous publiez une configuration de version de débogage.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* Si le débogueur ne parcourt pas le code que vous voulez déboguer, vous devez modifier le paramètre « Uniquement mon code ». Pour plus d'informations, consultez la page [Limiter le pas à pas à Uniquement mon code](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

* Lorsque vous activez la fonctionnalité de débogage à distance, un compteur démarre sur le serveur : après 48 heures, la fonctionnalité est automatiquement désactivée. Cette limite de 48 heures a été définie à des fins de sécurité et de performances. Vous pouvez facilement réactiver la fonctionnalité autant de fois que nécessaire. Nous vous recommandons de la désactiver lorsque vous n'utilisez pas le débogage.

* Vous pouvez manuellement connecter le débogueur à n’importe quel processus, et non au seul processus d’application web (w3wp.exe). Pour plus d'informations sur l'utilisation du mode débogage dans Visual Studio, consultez la page [Débogage dans Visual Studio](http://msdn.microsoft.com/fr-fr/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Présentation des journaux de diagnostic

Une application ASP.NET qui s’exécute dans une application web Microsoft Azure peut créer les types de journaux suivants :

* **Journaux de suivi d'application**<br/> : l'application crée ces journaux en appelant des méthodes de la classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx).
* **Journaux de serveur Web**<br/> : le serveur Web crée une entrée de journal pour chaque requête HTTP vers l’application Web.
* **Journaux de messages d'erreur détaillés**<br/> : le serveur Web crée une page HTML contenant certaines informations supplémentaires pour les requêtes HTTP ayant échoué (celles dont le code d'état est 400 ou plus). 
* **Journaux de suivi des demandes ayant échoué**<br/> : le serveur Web crée un fichier XML avec des informations de suivi détaillées pour les requêtes HTTP ayant échoué. Le serveur Web fournit également un fichier XSL pour mettre en forme le XML dans un navigateur.
  
La fonction de journalisation affecte les performances des applications web. Pour cette raison, Microsoft Azure vous offre la possibilité d’activer ou de désactiver chaque type de journal, selon vos besoins. Vous pouvez définir un niveau minimal de gravité pour l'écriture des journaux d'application. Lorsque vous créez une application web, la fonction de journalisation est désactivée par défaut.

Les journaux sont écrits dans des fichiers issus du dossier *LogFiles*, au sein du système de fichiers de votre application Web, et ils sont accessibles par FTP. Il est également possible d'écrire les journaux de serveur Web et d'application dans un compte Azure Storage. Vous pouvez conserver davantage de journaux sur un compte de stockage que sur le système de fichiers. Le système de fichiers peut conserver jusqu'à 100 Mo de journaux (il ne conserve pas les journaux très longtemps : Azure libère de l'espace pour les nouveaux fichiers journaux en supprimant les anciens une fois la limite atteinte).

## <a name="apptracelogs"></a>Création et affichage des journaux de suivi d’application

Dans cette section, vous effectuerez les tâches suivantes :

* ajout d’instructions de traçage au projet Web que vous avez créé dans [Prise en main d’Azure et ASP.NET][GetStarted] ;
* affichage des journaux lorsque vous exécutez le projet localement ;
* affichage des journaux durant leur génération par l'application exécutée dans Azure. 

Pour plus d’informations sur la création de journaux d’application dans WebJobs, consultez [Utilisation du stockage de file d’attente Microsoft Azure avec le Kit de développement logiciel (SDK) de WebJobs - Écriture de journaux](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs). Les instructions suivantes permettant d'afficher les journaux et de contrôler la façon dont ils sont stockés dans Azure s'appliquent également aux journaux des applications créés par WebJobs.

### Ajout d'instructions de suivi à l'application

1. Ouvrez *Controllers\HomeController.cs*, puis remplacez le contenu du fichier par le code suivant afin d’ajouter les instructions `Trace` ainsi qu’une instruction `using` au `System.Diagnostics` :

		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Diagnostics;
		using System.Linq;
		using System.Web;
		using System.Web.Configuration;
		using System.Web.Mvc;
		namespace MyExample.Controllers
		{
		    public class HomeController : Controller
		    {
		        public ActionResult Index()
		        {
		            Trace.WriteLine("Entering Index method");
		            ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
		            Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Index method");
		            return View();
		        }
		
		        public ActionResult About()
		        {
		            Trace.WriteLine("Entering About method");
		            ViewBag.Message = "Your app description page.";
		            Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
		            Trace.WriteLine("Leaving About method");
		            return View();
		        }
		
		        public ActionResult Contact()
		        {
		            Trace.WriteLine("Entering Contact method");
		            ViewBag.Message = "Your contact page.";
		            Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Contact method");
		            return View();
		        }
		    }
		}
		
				
### Affichage de la sortie de suivi en local

3. Appuyez sur F5 pour exécuter l'application en mode débogage.

	L'écouteur de suivi par défaut écrit toutes les sorties de suivi dans la fenêtre **Sortie**, avec d'autres sorties de débogage. L’image suivante montre la sortie des instructions de suivi ajoutées à la méthode `Index`.

	![Suivi dans la fenêtre Débogage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	La procédure suivante montre comment afficher la sortie de suivi dans une page Web, sans procéder à la compilation en mode de débogage.

2. Ouvrez le fichier Web.config de l'application (celui situé dans le dossier de projet), puis ajoutez un élément `<system.diagnostics>` à la fin du fichier, juste avant l'élément de fermeture `</configuration>` :

  		<system.diagnostics>
		    <trace>
		      <listeners>
		        <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
		      </listeners>
		    </trace>
		  </system.diagnostics>

	L’élément `WebPageTraceListener` vous permet d’afficher la sortie de suivi en accédant à `/trace.axd`.

3. Ajoutez un <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">élément de suivi</a> sous `<system.web>` dans le fichier Web.config, comme dans l’exemple suivant :

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Appuyez sur Ctrl+F5 pour exécuter l’application.

4. Dans la barre d’adresse du navigateur, ajoutez *trace.axd* à l’URL, puis appuyez sur Entrée (l’URL ressemblera à http://localhost:53370/trace.axd)).

5. Sur la page **Suivi d’application**, cliquez sur **Afficher les détails** sur la première ligne (pas la ligne BrowserLink).

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	La page **Détails de la demande** s’affiche et la section **Informations de suivi** affiche la sortie des instructions de suivi ajoutées à la méthode `Index`.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	Par défaut, `trace.axd` est uniquement disponible localement. Si vous voulez le rendre disponible à partir d’une application Web distante, vous pouvez ajouter `localOnly="false"` à l’élément `trace` dans le fichier *Web.config*, comme le montre l’exemple suivant :

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	Cependant, l’activation du fichier `trace.axd` dans une application Web de production n’est généralement pas recommandée, pour des raisons de sécurité. Dans les sections suivantes, vous découvrirez un moyen plus simple de lire les journaux de suivi dans une application Web Microsoft Azure.

### Affichage de la sortie de suivi dans Azure

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet Web, puis cliquez sur **Publier**.

2. Dans la boîte de dialogue **Publier le site Web**, cliquez sur **Publier**.

	Une fois que Visual Studio a publié votre mise à jour, il ouvre une fenêtre de navigateur vers votre page d'accueil (en partant du principe que vous n'avez pas désactivé la case à cocher **URL de destination** sous l'onglet **Connexion**).

3. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur votre application Web et sélectionnez **Afficher les journaux de diffusion en continu**.

	![Afficher la diffusion de journaux en continu dans le menu contextuel](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	La fenêtre **Sortie** indique que vous êtes connecté au service de diffusion de journaux en continu et ajoute une ligne de notification à chaque minute passée sans affichage de journal.

	![Afficher la diffusion de journaux en continu dans le menu contextuel](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. Dans la fenêtre du navigateur qui affiche la page d'accueil de votre application, cliquez sur **Contacter**.

	En quelques secondes, la sortie du suivi de l’erreur que vous avez ajouté à la méthode `Contact` apparaît dans la fenêtre **Sortie**.

	![Suivi d'erreur dans la fenêtre Sortie](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	Visual Studio affiche uniquement le suivi des erreurs, car il s'agit du paramètre par défaut lorsque vous activez le service de surveillance des journaux. Lorsque vous créez une application web Microsoft Azure, la journalisation est désactivée par défaut, comme vous l’avez vu lorsque vous avez ouvert la page Paramètres un peu plus tôt :

	![Journalisation d'application désactivée](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


	Cependant, lorsque vous avez sélectionné **Afficher les journaux de diffusion en continu**, Visual Studio a automatiquement remplacé **Journal des applications (système de fichiers)** par **Erreur**, ce qui signifie que les journaux d’erreur sont signalés. Pour afficher tous vos journaux de suivi, vous pouvez remplacer ce paramètre par **Commentaires**. Lorsque vous sélectionnez un niveau de gravité inférieur à l'erreur, tous les journaux correspondant aux niveaux de gravité supérieurs sont également signalés. Donc, lorsque vous sélectionnez Commentaires, vous pouvez également consulter des informations, des avertissements et des journaux d'erreurs.

4. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur l’application Web, puis cliquez sur **Paramètres d’affichage**, comme vous l’avez fait précédemment.

5. Remplacez **Application Logging (File System)** par **Commentaires**, puis cliquez sur **Enregistrer**.
 
	![Définir le niveau de suivi sur Commentaires](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. Dans la fenêtre du navigateur affichant votre page **Contact**, cliquez sur **Accueil**, sur **À propos de**, puis sur **Contact**.

	Quelques secondes après, la fenêtre **Sortie** affiche toutes vos sorties de suivi.

	![Sortie de suivi des commentaires](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	Dans cette section, vous avez activé et désactivé la journalisation à l’aide des paramètres d’application web Microsoft Azure. Vous pouvez également activer et désactiver les écouteurs de suivi en modifiant le fichier Web.config. Toutefois, le fait de modifier le fichier Web.config entraîne le recyclage du domaine d’application, ce que ne fait pas l’activation de la journalisation via la configuration de l’application web. Si le problème met du temps à se reproduire ou s'il est intermittent, le recyclage du domaine d'application peut le « résoudre » temporairement et vous forcer à attendre qu'il se reproduise. L'activation des diagnostics dans Azure n'entraîne pas cela, vous pouvez donc commencer tout de suite à saisir des informations sur une erreur.

### Fonctionnalités de la fenêtre Sortie

L'onglet **Journaux Azure** de la fenêtre **Sortie** contient plusieurs boutons et une zone de texte :

![Boutons de l'onglet Journaux](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Ces éléments permettent d'effectuer les opérations suivantes :

* Suppression du contenu de la fenêtre **Sortie**
* Activation ou désactivation du retour automatique à la ligne
* Démarrage ou arrêt de la surveillance des journaux
* Sélection des journaux à surveiller
* Téléchargement de journaux
* Filtrage des journaux en fonction d'une chaîne de recherche ou d'une expression régulière
* Fermeture de la fenêtre **Sortie**

Si vous entrez une chaîne de recherche ou une expression régulière, Visual Studio filtre les informations de journalisation au niveau du client. Cela signifie que vous pouvez entrer les critères après l'affichage des journaux dans la fenêtre **Sortie** et que vous pouvez modifier les critères de filtrage sans avoir à régénérer les journaux.

## <a name="webserverlogs"></a>Affichage des journaux de serveur Web

Les journaux de serveur Web enregistrent toutes les activités HTTP de l’application web. Afin de pouvoir afficher ces journaux dans la fenêtre **Sortie**, vous devez les activer pour l’application Web et indiquer à Visual Studio que vous souhaitez les surveiller.

1. Dans l’onglet **Configuration de l’application web Azure** que vous avez ouvert à partir de l’**Explorateur de serveurs**, remplacez la valeur du paramètre Journalisation du serveur Web par **Activé**, puis cliquez sur **Enregistrer**.

	![Activer la journalisation de serveur Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. Dans la fenêtre **Sortie**, cliquez sur le bouton **Sélection des journaux Azure à surveiller**.
	
	![Sélectionner les journaux Azure à surveiller](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. Dans la boîte de dialogue **Options de journalisation Azure**, sélectionnez **Journaux de serveur Web**, puis cliquez sur **OK**.

	![Surveiller les journaux de serveur Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. Dans la fenêtre du navigateur qui affiche l’application Web, cliquez sur **Accueil**, **À propos de**, puis **Contact**.

	Généralement, les journaux d'application s'affichent en premier, suivis par les journaux de serveur Web. Le processus d'affichage peut prendre un certain temps.

	![Journaux de serveur Web dans la fenêtre Sortie](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


Par défaut, Azure écrit les journaux dans le système de fichiers lorsque vous activez les journaux de serveur Web en utilisant Visual Studio. Une autre méthode consiste à utiliser le portail de gestion pour indiquer que les journaux de serveur Web doivent être écrits sur un conteneur d'objets blob dans un compte de stockage. Pour plus d'informations, consultez la section **Diagnostics de site** de la rubrique [Configuration des sites Web](/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

Si vous utilisez le portail de gestion pour activer la journalisation de serveur Web sur un compte de stockage Azure, puis que vous désactivez la journalisation dans Visual Studio, lorsque vous réactivez la journalisation dans Visual Studio, les paramètres de votre compte de stockage sont restaurés.

## <a name="detailederrorlogs"></a>Affichage des journaux de messages d’erreur détaillés

Les journaux d'erreur détaillés fournissent des informations supplémentaires sur les requêtes HTTP ayant pour résultat des codes de réponse d'erreur (400 ou au-delà). Afin de pouvoir afficher ces journaux dans la fenêtre **Sortie**, vous devez les activer pour l’application Web et indiquer à Visual Studio que vous souhaitez les surveiller.

1. Dans l’onglet **Configuration de l’application web Azure** que vous avez ouvert à partir de l’**Explorateur de serveurs**, remplacez la valeur du paramètre **Messages d’erreur détaillés** par **Activé**, puis cliquez sur **Enregistrer**.

	![Activer les messages d'erreur détaillés](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Dans la fenêtre **Sortie**, cliquez sur le bouton **Sélection des journaux Azure à surveiller**.

3. Dans la boîte de dialogue **Options de journalisation Azure**, cliquez sur **Tous les journaux**, puis sur **OK**.

	![Surveiller tous les journaux](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Dans la barre d’adresse de la fenêtre de navigateur, ajoutez un caractère supplémentaire à l’URL pour provoquer une erreur 404 (par exemple, `http://localhost:53370/Home/Contactx`) et appuyez sur Entrée.

	Après quelques secondes, le journal d'erreur détaillé s'affiche dans la fenêtre **Sortie** de Visual Studio.

	![Journal d'erreur détaillé dans la fenêtre Sortie](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Maintenez la touche Ctrl appuyée tout en cliquant sur le lien pour afficher le journal de sortie mis en forme dans un navigateur :

	![Journal d'erreur détaillé dans la fenêtre du navigateur](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Téléchargement de journaux du système de fichiers

Tous les journaux que vous pouvez surveiller dans la fenêtre **Sortie** peuvent aussi être téléchargés en tant que fichiers *.zip*.

1. Dans la fenêtre **Sortie**, cliquez sur **Download Streaming Logs**.

	![Boutons de l'onglet Journaux](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	L'Explorateur de fichiers ouvre votre dossier *Téléchargements* dans lequel le fichier téléchargé est sélectionné.

	![Fichier téléchargé](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Procédez à l'extraction du fichier *.zip* pour afficher la structure de dossiers suivante :

	![Fichier téléchargé](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* Les journaux de suivi d'application sont dans des fichiers *.txt* dans le dossier *LogFiles\Application*.
	* Les journaux de serveur Web sont dans des fichiers *.log* dans le dossier *LogFiles\http\RawLogs*. Vous pouvez utiliser un outil tel que [Log Parser](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) pour afficher et manipuler ces fichiers.
	* Les journaux de messages d'erreur détaillés sont dans des fichiers *.html* dans le dossier *LogFiles\DetailedErrors*.

	Le dossier *deployments* contient les fichiers créés par la publication à partir du contrôle de code source : il n'a rien à voir avec la publication Visual Studio. Le dossier *Git* contient le suivi lié à la publication à partir du contrôle de code source et au service de diffusion de fichier journal en continu.

## <a name="storagelogs"></a>Affichage des journaux de stockage

Les journaux de suivi d'application peuvent également être envoyés vers un compte de stockage Azure et vous pouvez les afficher dans Visual Studio. Pour cela, vous allez créer un compte de stockage, activer les journaux de stockage sur le portail de gestion et les afficher dans l’onglet **Journaux** de la fenêtre **Application web Microsoft Azure**.

Vous pouvez envoyer des journaux à l'une des trois destinations suivantes, ou aux trois :

* le système de fichiers ;
* les tables de compte de stockage ;
* les objets blob de compte de stockage.

Vous pouvez définir un niveau de gravité distinct pour chaque destination.

Les tables facilitent l'affichage des détails des journaux en ligne et elles prennent en charge la diffusion en continu. Vous pouvez demander des journaux dans des tables et afficher les nouveaux journaux durant leur création. Les objets blob facilitent le téléchargement des journaux dans des fichiers et leur analyse en utilisant HDInsight, car HDInsight sait comment utiliser le stockage d'objets blob. Pour plus d'informations, consultez la section **Hadoop et MapReduce** de la page [Options de stockage de données (développement d'applications de cloud concrètes avec Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

Pour le moment, vous avez configuré des journaux de système de fichiers sur un niveau de commentaires : la procédure suivante va vous guider durant la configuration des journaux au niveau de l'information pour accéder aux tables de compte de stockage. « Au niveau de l’information » signifie que tous les journaux créés en appelant `Trace.TraceInformation`, `Trace.TraceWarning` et `Trace.TraceError` seront affichés, mais pas les journaux créés en appelant `Trace.WriteLine`.

Les comptes de stockage offrent un espace de stockage plus important et une conservation des journaux plus longue que le système de fichiers. L'envoi des journaux de suivi d'application au compte de stockage permet également d'obtenir des informations supplémentaires sur chaque journal. Cela n'est pas le cas avec le système de fichiers.

5. Cliquez avec le bouton droit sur **Stockage** sous le nœud Azure, puis cliquez sur **Créer un compte de stockage**.

![Créer un compte de stockage](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. Dans la boîte de dialogue **Créer un compte de stockage**, entrez un nom correspondant au compte de stockage. 

	Le nom doit être unique (aucun autre compte de stockage Azure ne doit avoir le même nom). Si le nom que vous entrez est déjà utilisé, vous aurez la possibilité de le modifier.

	L’URL permettant d’accéder à votre compte de stockage est *{nom}*.core.windows.net.

5. Choisissez la région la proche de vous dans la liste déroulante **Région ou groupe d’affinités**.

	Ce paramètre spécifie le centre de données Azure qui hébergera votre compte de stockage. Pour les besoins de ce didacticiel, votre choix n’aura pas une grande incidence, mais dans le cas d’une application web de production, votre compte de stockage et votre serveur web doivent se trouver dans la même région, afin de réduire les frais d’acheminement des données et de latence. L’application web (que vous créerez ultérieurement) doit s’exécuter dans une région aussi proche que possible pour les navigateurs qui y auront accès, afin de minimiser la latence.

6. Dans la liste déroulante **Réplication**, sélectionnez **Redondant en local**.

	Lorsque la géo-réplication est activée pour un compte de stockage, le contenu stocké est répliqué dans un centre de données secondaire pour activer le basculement vers cet emplacement en cas de sinistre majeur à l'emplacement principal. La géo-réplication peut engendrer des coûts supplémentaires. Dans le cas des comptes test et de développement, vous êtes en général peu enclin à payer pour la géo-réplication. Pour plus d’informations, consultez [Création, gestion ou suppression d’un compte de stockage](../storage-create-storage-account/#replication-options).

5. Cliquez sur **Create**.

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)

1. Dans la fenêtre **Application web Microsoft Azure** de Visual Studio, cliquez sur l’onglet **Journaux**, puis sur **Configurer la journalisation dans le portail de gestion**.

    <!-- todo:screenshot of new portal if the VS page link goes to new portal -->![Configuration de la journalisation](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	Cette opération ouvre l’onglet **Configurer** dans le portail de gestion de votre application Web. Pour accéder à cet emplacement, vous pouvez aussi cliquer sur l’onglet **Applications web**, sur l’application Web, puis sur l’onglet **Configurer**.

2. Sous l'onglet **Configure** du portail de gestion, faites défiler vers le bas jusqu'à la section Diagnostic d'application, puis définissez **Application Logging (Table Storage)** sur **Actif**.

3. Définissez le **niveau de journalisation** sur **Information**.

4. Cliquez sur **Manage Table Storage**.

	![Cliquer sur Manage Table Storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	Dans la zone **Manage table storage for application diagnostics**, vous pouvez sélectionner votre compte de stockage, si vous en avez plusieurs. Vous pouvez créer une table ou utiliser une table existante.

	![Gérer le stockage de tables](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. Dans la zone **Manage table storage for application diagnostics**, cliquez sur la coche pour fermer la zone.

6. Sous l'onglet **Configure** du portail de gestion, cliquez sur **Enregistrer**.

7. Dans la fenêtre de navigateur qui affiche l’application Web, cliquez sur **Accueil**, puis sur **À propos de** et sur **Contact**.

	Les informations de journalisation générées par la navigation sur ces pages Web seront écrites sur le compte de stockage.

8. Dans l’onglet **Journaux** de la fenêtre **Application web Microsoft Azure**, au sein de Visual Studio, cliquez sur **Actualiser** sous **Récapitulatif du diagnostic**.

	![Cliquer sur Actualiser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	La section **Diagnostic Summary** affiche par défaut les journaux des 15 dernières minutes. Vous pouvez modifier ce délai pour afficher plus de journaux.

	Si vous obtenez l'erreur « table introuvable », vérifiez que vous avez bien parcouru les pages sur lesquelles le suivi est activé après avoir activé **Application Logging (Storage)** et cliqué sur **Enregistrer**.

	![Journaux de stockage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	Notez qu'ici, vous pouvez afficher l'**ID de processus** et l'**ID de thread** pour chaque journal, ce qui n'est pas possible dans le système de fichiers. Vous pouvez consulter d'autres champs en affichant directement la table de stockage Azure.

8. Cliquez sur **View all application logs**.

	La table de journal de suivi s'affiche dans la vue de table de stockage Azure.
   
	Si vous obtenez une erreur du type « la séquence ne contient pas d'élément », ouvrez l'**Explorateur de serveurs**, développez le nœud de votre compte de stockage sous le nœud **Azure**, cliquez avec le bouton droit sur **Tables**, puis cliquez sur **Actualiser**.

	![Journaux de stockage dans la vue Table](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	Cette vue affiche des champs supplémentaires que vous ne pouvez pas consulter ailleurs. Elle vous permet également de filtrer les journaux en utilisant l'interface utilisateur du générateur de requêtes pour construire une requête. Pour plus d'informations, consultez les sections « Utilisation des ressources de tables » et « Filtrage d'entités » de la page [Consultation des ressources de stockage avec l'Explorateur de serveurs](http://msdn.microsoft.com/library/ff683677.aspx).

7. Pour consulter les détails d'une ligne unique, double-cliquez sur l'une des lignes.

	![Table de suivi dans l'Explorateur de serveurs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

## <a name="failedrequestlogs"></a>Affichage des journaux de suivi de demandes ayant échoué

Les journaux de suivi de demandes ayant échoué sont utiles pour comprendre les détails de gestion IIS d'une requête HTTP, dans des scénarios tels que des problèmes de réécriture d'URL ou d'authentification.

Les applications web Microsoft Azure utilisent la même fonctionnalité de suivi de demandes ayant échoué que celle que proposait IIS 7.0 et plus. Cependant, vous ne pouvez pas accéder aux paramètres IIS permettant de configurer les erreurs à journaliser. Lorsque vous activez le suivi des demandes ayant échoué, toutes les erreurs sont récupérées.

Vous pouvez activer le suivi des demandes ayant échoué en utilisant Visual Studio, mais vous ne pouvez pas les afficher dans Visual Studio. Ces journaux sont des fichiers XML. Le service de diffusion de journal en continu surveille uniquement les fichiers jugés lisibles en mode texte brut : fichiers *.txt*, *.html* et *.log*.

Vous pouvez afficher les journaux de suivi des demandes ayant échoué directement dans un navigateur via FTP ou en local, après avoir utilisé un outil FTP pour les télécharger sur votre ordinateur local. Dans cette section, nous les afficherons directement dans un navigateur.

1. Dans l’onglet **Configuration** de la fenêtre **Application Web Microsoft Azure** que vous avez ouverte à partir de l’**Explorateur de serveurs**, remplacez la valeur du paramètre **Suivi des demandes ayant échoué** par **Activé**, puis cliquez sur **Enregistrer**.

	![Activer le suivi des demandes ayant échoué](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. Dans la barre d’adresses de la fenêtre de navigateur qui affiche l’application web, ajoutez un caractère supplémentaire à l’URL et appuyez sur Entrée pour provoquer une erreur 404.

	Ceci génère un journal de suivi des demandes ayant échoué. La procédure suivante vous montre comment afficher ou télécharger ce journal.

2. Dans l’onglet **Configuration** de la fenêtre **Application web Microsoft Azure** de Visual Studio, cliquez sur **Ouvrir l’élément dans le portail de gestion**.

3. Dans le panneau du portail de gestion de votre application Web, cliquez sur **Tous les paramètres > Informations d’identification de déploiement**, puis sur **Réinitialisez vos informations d’identification de déploiement**.

4. Entrez un nouveau nom d'utilisateur et un nouveau mot de passe.

	![Nouveaux nom d'utilisateur et mot de passe FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. Sous l'onglet **Tableau de bord** du portail de gestion, appuyez sur F5 pour actualiser la page, puis faites défiler vers le bas jusqu'à voir **Deployment / FTP User**. Vous pouvez constater que le nom d’utilisateur présente un préfixe constitué du nom de l’application web. **Lorsque vous vous connectez, vous devez utiliser ce nom d’utilisateur complet, avec pour préfixe le nom de l’application Web, comme indiqué ici.**

5. Dans une nouvelle fenêtre de navigateur, accédez à l’URL affichée sous **Nom de l’hôte FTP** dans l’onglet **Tableau de bord** de la page du portail de gestion de votre application Web. **FTP Host Name** est situé en regard de **Deployment / FTP User** dans la section **Quick Glance**.

6. Connectez-vous en utilisant les informations d’identification FTP que vous avez créées précédemment (nom d’utilisateur incluant le nom application web en tant que préfixe).

	Le navigateur affiche le dossier racine de l’application web.

6. Ouvrez le fichier *LogFiles*.

	![Ouvrir le dossier LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Ouvrez le dossier nommé W3SVC avec une valeur numérique.

	![Ouvrir le dossier W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	Ce dossier contient des fichiers XML pour plusieurs erreurs journalisées une fois le suivi des demandes ayant échoué activé, ainsi qu'un fichier XSL utilisable par un navigateur pour mettre en forme les fichiers XML.

	![Dossier W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Cliquez sur le fichier XML de la demande ayant échoué dont vous voulez consulter les informations de suivi.

	L'image suivante montre une partie des informations de suivi d'un exemple d'erreur.

	![Suivi d'une demande ayant échoué dans le navigateur](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


## <a name="nextsteps"></a>Étapes suivantes

Vous avez vu en quoi Visual Studio facilite l’affichage de journaux créés par une application web Microsoft Azure. Les sections suivantes fournissent des liens vers des ressources supplémentaires sur des rubriques connexes :

* Dépannage des applications web Microsoft Azure
* Débogage dans Visual Studio 
* Débogage distant dans Azure
* Suivi dans les applications ASP.NET
* Analyse de journaux de serveur Web
* Analyse des journaux de suivi des demandes ayant échoué
* Débogage de Cloud Services

### Dépannage des applications web Microsoft Azure

Pour en savoir plus sur la résolution des applications web dans le Service d’application Microsoft Azure, consultez les ressources suivantes :

* [Surveillance d’applications Web](/manage/services/web-sites/how-to-monitor-websites/)
* [Étude des fuites de mémoire dans les applications Web Microsoft Azure avec Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Article sur le blog ALM de Microsoft concernant les fonctionnalités de Visual Studio prévues pour l'analyse de problèmes de mémoire gérés.
* [Les outils en ligne des applications Web Microsoft Azure que vous devez connaître](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Article de blog par Amit Apple.

Pour obtenir des réponses sur une question relative à la résolution des problèmes, ouvrez un fil de discussion dans l'un des forums suivants :

* [Forum Azure sur le site ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Forum Azure sur MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### Débogage dans Visual Studio 

Pour plus d'informations sur l'utilisation du mode débogage dans Visual Studio, consultez la rubrique MSDN [Débogage dans Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx), puis [Conseils de débogage avec Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### Débogage distant dans Azure

Pour en savoir plus sur le débogage distant des applications web Microsoft Azure et WebJobs, consultez les articles de blog suivants, en anglais :

* [Présentation du débogage à distance d’Azure App Service Web Apps](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/) (en anglais).
* [Présentation du débogage à distance d’Azure App Service Web Apps : 2e partie - au cœur du débogage distant](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/) (en anglais)
* [Présentation du débogage à distance d’Azure App Service Web Apps : 3e partie - environnement multi-instance et GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/) (en anglais)
* [Débogage de WebJobs (vidéo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Si votre application Web utilise une API Web Microsoft Azure ou un système Mobile Services principal qu’il vous faut déboguer, consultez l’article [Débogage du serveur principal .NET dans Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx) (en anglais).

### Suivi dans les applications ASP.NET

Il n'y a pas d'autre présentation du suivi ASP.NET plus détaillée et actualisée disponible sur Internet. Nous vous conseillons de commencer par consulter les anciens documents de présentation rédigés pour Web Forms, car MVC n'existait pas encore, et de compléter cela en consultant les billets de blog les plus récents traitant de problèmes plus précis. Les ressources suivantes constituent un bon début :

* [Surveillance et télémétrie (développement d'applications de cloud plus concrètes avec Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> Chapitre de livre électronique contenant des recommandations pour le suivi dans les applications de cloud Azure.
* [Suivi ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/> Une ressource ancienne mais toujours efficace pour une présentation de base du sujet.
* [Écouteurs de suivi](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/> Contient des informations sur les écouteurs de suivi mais ne mentionne pas l'élément [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Procédure pas à pas : intégration du traçage ASP.NET avec le traçage System.Diagnostics](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/> Cette page est également un peu ancienne, mais vous y trouverez des informations complémentaires que l’article de présentation ne traite pas.
* [Suivi dans les vues d'ASP.NET MVC Razor](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/> En plus du suivi dans les vues Razor, ce billet explique également comment créer un filtre d'erreur pour journaliser toutes les exceptions non gérées dans une application MVC. Pour plus d'informations sur la procédure de journalisation des exceptions non gérées dans une application Web Forms, consultez l'exemple de Global.asax dans [Exemple complet pour les gestionnaires d'erreurs](http://msdn.microsoft.com/library/bb397417.aspx) sur MSDN. Dans MVC ou Web Forms, si vous voulez journaliser certaines exceptions tout en laissant le Framework par défaut les gérer, vous pouvez utiliser l'exemple de code suivant :

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Journalisation de suivi de diagnostics de diffusion à partir de l’outil en ligne de commande Azure (et Glimpse)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/> Apprenez à utiliser l’outil en ligne de commande pour effectuer ce que montre ce didacticiel dans Visual Studio. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) est un outil pour le débogage d'applications ASP.NET.
* Vidéos [Utilisation de la journalisation et des diagnostics d’applications Web Azure, avec David Ebbo](/documentation/videos/azure-web-site-logging-and-diagnostics/) et [Diffusion de journaux à partir d’applications Web Azure, avec David Ebbo](/documentation/videos/log-streaming-with-azure-web-sites/)<br> par Scott Hanselman et David Ebbo.

Pour la journalisation d'erreurs, vous pouvez éviter d'avoir à écrire votre propre code de suivi en utilisant un Framework de journalisation Open Source comme [ELMAH](http://nuget.org/packages/elmah/). Pour plus d'informations, consultez les [billets du blog de Scott Hanselman sur ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Notez également que vous n'avez pas besoin d'utiliser le suivi ASP.NET ou System.Diagnostics si vous voulez activer une diffusion de journaux en continu à partir d'Azure. Le service de diffusion de journaux en continu des applications Web Azure diffusera tous les fichiers *.txt*, *.html* ou *.log* qu’il trouvera dans le dossier *LogFiles*. Par conséquent, vous pouvez créer votre propre système de journalisation, qui écrira des données dans le système de fichiers de l’application web ; votre fichier sera automatiquement diffusé en continu et téléchargé. Tout ce que vous avez à faire est d'écrire un code d'application qui crée les fichiers dans le dossier *d:\home\logfiles*.

### Analyse de journaux de serveur Web

Pour plus d'informations sur l'analyse des journaux de serveur Web, consultez les ressources suivantes :

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/> Outil pour afficher les données des journaux de serveur Web (fichiers *.log*).
* [Résolution des problèmes de performance IIS ou des erreurs d'application à l'aide de LogParser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/> Une introduction à l'outil LogParser que vous pouvez utiliser pour analyser les journaux de serveur Web.
* [Billets du blog de Robert McMurray sur l’utilisation de LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Code d’état HTTP dans IIS 7.0, IIS 7.5 et IIS 8.0](http://support.microsoft.com/kb/943891)

### Analyse des journaux de suivi des demandes ayant échoué

Le site Web Microsoft TechNet comporte une section [Utilisation du suivi des demandes ayant échoué](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) qui permet de comprendre l'utilisation de ces journaux. Toutefois, cette documentation se concentre principalement sur la configuration du suivi des demandes ayant échoué dans IIS, ce que vous ne pouvez pas faire dans les applications web Microsoft Azure.

### Débogage de Cloud Services

Si vous voulez déboguer un service cloud Microsoft Azure plutôt qu’une application Web, consultez [Débogage de services cloud](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee405479.aspx).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l'ancien et le nouveau portail, consultez : [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

[GetStarted]: web-sites-dotnet-get-started.md
[GetStartedWJ]: websites-dotnet-webjobs-sdk.md
 

<!---HONumber=62-->