<properties 
	pageTitle="Dépannage des sites Web Azure dans Visual Studio" 
	description="Découvrez comment résoudre les problèmes d'un site Web Azure à l'aide des outils de débogage distant, de suivi et de journalisation qui sont intégrés à Visual Studio 2013." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/13/2014" 
	ms.author="tdykstra"/>

# Dépannage des sites Web Azure dans Visual Studio

Ce didacticiel montre comment utiliser les outils Visual Studio permettant de déboguer une application exécutée dans un site Web Azure en utilisant le [mode débogage](http://www.visualstudio.com/fr-fr/get-started/debug-your-app-vs.aspx) à distance ou en consultant les journaux d'application et de serveur Web.

Vous allez découvrir :

* les fonctionnalités de gestion de site Azure disponibles dans Visual Studio ;
* l'utilisation de l'affichage distant de Visual Studio pour effectuer des modifications rapides dans un site Web distant ;
* l'exécution distante du mode débogage durant l'exécution d'un projet dans Azure ;
* la création de journaux de suivi d'application et leur affichage pendant leur création par l'application ;
* l'affichage des journaux de serveur Web, notamment des messages d'erreur détaillés et le suivi des demandes ayant échoué ;
* l'envoi de journaux de diagnostic à un compte Azure Storage et leur affichage depuis ce compte.

Si vous avez Visual Studio Ultimate, vous pouvez également utiliser [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) pour le débogage. IntelliTrace n'est pas couvert dans ce didacticiel.

### Sections du didacticiel

- [Conditions préalables](#prerequisites)
- [Configuration et gestion de site](#sitemanagement)
- [Accès aux fichiers de site Web dans l'Explorateur de serveurs](#remoteview)
- [Débogage distant](#remotedebug)
	- Débogage distant des sites Web
	- Débogage distant des tâches Web
	- Notes à propos du débogage à distance 
- [Présentation des journaux de diagnostic](#logsoverview)
- [Création et affichage des journaux de suivi d'application](#apptracelogs)
- [Affichage des journaux de serveur Web](#webserverlogs)
- [Affichage des journaux de messages d'erreur détaillés](#detailederrorlogs)
- [Téléchargement de journaux du système de fichiers](#downloadlogs)
- [Affichage des journaux de stockage](#storagelogs)
- [Affichage des journaux de demandes ayant échoué](#failedrequestlogs)
- [Étapes suivantes](#nextsteps)

<h2><a name="prerequisites"></a>Configuration requise</h2>

Ce didacticiel fonctionne avec l'environnement de développement, le projet Web et le site Web Azure que vous avez configurés dans le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted]. Pour les sections relatives à WebJobs, vous avez besoin de l'application créée dans [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure][GetStartedWJ].

Les exemples de code inclus dans ce didacticiel sont destinés à une application Web C# MVC, mais les procédures de résolution de problèmes sont identiques pour les applications Visual Basic et Web Forms.

Le débogage distant nécessite Visual Studio 2013 ou Visual Studio 2012 avec Update 4. Les fonctionnalités de débogage distant et l'**Explorateur de serveurs** pour WebJobs nécessitent [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) ou version ultérieure. Les autres fonctionnalités illustrées dans ce didacticiel fonctionnent également dans Visual Studio 2013 Express pour le Web et Visual Studio 2012 Express pour le Web. 

La fonctionnalité de diffusion de journaux en continu est opérationnelle uniquement pour les applications ciblant .NET Framework 4 ou une version ultérieure.

<h2><a name="sitemanagement"></a>Configuration et gestion de site</h2>

Visual Studio permet d'accéder à un sous-ensemble des fonctionnalités de gestion de site et des paramètres de configuration disponibles dans le portail de gestion. Cette section présente les éléments disponibles.

1. Si vous n'êtes pas déjà connecté à Azure dans Visual Studio, cliquez sur le bouton **Se connecter à Azure** dans l'**Explorateur de serveurs**.

	Vous pouvez aussi installer un certificat de gestion permettant d'accéder à votre compte. Celui-ci permet à l'**Explorateur de serveurs** d'accéder à des services Azure supplémentaires (QL Database et Mobile Services). Si vous choisissez d'installer un certificat, cliquez avec le bouton droit sur le nœud **Azure** dans l'**Explorateur de serveurs**, puis cliquez sur **Gérer les abonnements** dans le menu contextuel. Dans la boîte de dialogue **Gérer les abonnements Azure**, cliquez sur l'onglet **Certificats**, puis sur **Importer**. Suivez la procédure pour télécharger et importer un fichier d'abonnement (portant l'extension *.publishsettings*) pour votre compte Azure.

	> [AZURE.NOTE]
	> Si vous téléchargez un fichier d'abonnement, enregistrez-le dans un dossier situé hors de vos répertoires de code source (par exemple dans le dossier Téléchargements), puis supprimez-le une fois l'importation terminée. Si un utilisateur malveillant accède au fichier d'abonnement, il peut modifier, créer et supprimer vos services Azure.

	Pour plus d'informations sur la connexion aux ressources Azure à partir de Visual Studio, consultez la page [Gérer des comptes, des abonnements et des rôles d'administrateur](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. Dans l'**Explorateur de serveurs**, développez **Azure**, puis **Sites Web**.

3. Cliquez avec le bouton droit sur le nœud du site Web créé durant le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted], puis cliquez sur **Paramètres d'affichage**.

	![View Settings in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	L'onglet **Site Web Azure** s'affiche et des tâches de gestion et de configuration de site deviennent disponibles dans Visual Studio.

	![Azure Website window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	Dans ce didacticiel, vous utiliserez les menus déroulants de suivi et de journalisation.	Vous utiliserez également le débogage à distance en l'activant d'une façon différente.
   
	Pour obtenir des informations sur les zones Paramètres de l'application et Chaînes de connexion de cette fenêtre, consultez la page [Fonctionnement des chaînes d'application et de connexion dans Sites Web Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

	Pour mener à bien une tâche de gestion de site qui n'est pas disponible dans cette fenêtre, cliquez sur **Paramètres de site Web complets** pour ouvrir une fenêtre de navigateur vers le portail de gestion. Pour plus d'informations, consultez la rubrique [Configuration des sites Web](/fr-fr/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

<h2><a name="remoteview"></a>Accès aux fichiers de site Web dans l'Explorateur de serveurs</h2>

Vous pouvez généralement déployer un site avec l'indicateur  `customErrors` dans le fichier Web.config défini sur  `On` ou  `RemoteOnly`, ce qui signifie que vous n'obtenez pas de message d'erreur utile lorsqu'une erreur survient. Généralement, ces messages se présentent sous la forme d'une page comme celles qui suivent.

**Erreur de serveur dans l'application " / " :**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Nous avons rencontré une erreur :**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Le site Web ne peut pas afficher la page**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Généralement, la méthode la plus simple pour rechercher la cause d'une erreur consiste à activer les messages d'erreur détaillés : la première capture d'écran de la série précédente montre comment procéder. Vous devez modifier le fichier Web.config déployé. Vous pouvez modifier le fichier  *Web.config* dans le projet et redéployer le projet, ou créer une [transformation Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) et déployer une build de débogage, mais il existe un moyen plus rapide : dans l'**Explorateur de solutions**, vous pouvez directement afficher et modifier des fichiers sur le site distant en utilisant la fonctionnalité d' *remote view*.

1. Dans l'**Explorateur de serveurs**, développez **Azure**, **Sites Web**, puis le nœud du site Web vers lequel vous procédez au déploiement.

	Les nœuds qui s'affichent permettent d'accéder aux fichiers de contenu du site et aux fichiers journaux.

	![File and log files](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2. Développez le nœud **Fichiers**, puis double-cliquez sur le fichier  *Web.config*.

	![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	Visual Studio ouvre le fichier Web.config à partir du site distant, puis affiche [Distant] en regard du nom du fichier dans la barre de titre.

3. Ajoutez la ligne suivante à l'élément  `system.web` :

	`<customErrors mode="off"></customErrors>`

	![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Actualisez le navigateur qui affiche le message d'erreur inutile : à présent, vous obtenez un message d'erreur détaillé, comme dans l'exemple suivant :

	![Detailed error messaeg](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	(L'erreur affichée est générée par l'ajout de la ligne affichée en rouge à  *Views\Home\Index.cshtml*).

La modification du fichier Web.config n'est qu'un exemple des scénarios dans lesquels la capacité d'afficher et de modifier des fichiers sur votre site Web Azure simplifie la résolution des problèmes.

<h2><a name="remotedebug"></a>Débogage à distance</h2>

Si le message d'erreur détaillé ne fournit pas assez d'informations et que vous ne pouvez pas recréer l'erreur localement, une autre méthode pour résoudre le problème consiste à exécuter le mode de débogage à distance. Vous pouvez définir des points d'arrêt, manipuler directement la mémoire, parcourir le code en détail et même modifier le chemin d'accès du code. 

Le débogage à distance ne fonctionne pas avec les éditions Express de Visual Studio.

### Débogage distant des sites Web

Cette section vous montre comment procéder au débogage à distance en utilisant le projet créé dans le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted].

1. Ouvrez le projet Web que vous avez créé durant le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted].

1. Ouvrir  *Controllers\HomeController.cs*.

2. Supprimez la méthode  `About()` et insérez le code suivant à la place.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Définissez un point d'arrêt](http://www.visualstudio.com/fr-fr/get-started/debug-your-app-vs.aspx) sur la ligne  `ViewBag.Message`.

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**.

2. Dans le menu déroulant **Profil**, sélectionnez le même profil que celui utilisé dans [Prise en main d'Azure et d'ASP.NET][GetStarted].

3. Cliquez sur l'onglet **Paramètres**, remplacez **Configuration** par **Débogage**, puis cliquez sur **Publier**.

	![Publish in debug mode](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. Une fois le déploiement terminé et que votre navigateur ouvre l'URL Azure de votre site, fermez-le.

5. Pour Visual Studio 2013 : dans l'**Explorateur de serveurs**, développez **Azure**, **Sites Web**, cliquez avec le bouton droit sur votre site Web, puis cliquez sur **Attacher le débogueur**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	Le navigateur ouvre automatiquement votre page d'accueil exécutée dans Azure. Vous devrez peut-être attendre environ 20 secondes pendant qu'Azure configure le serveur pour le débogage. Ce délai se produit uniquement lors du premier démarrage en mode débogage sur un site Web. Il ne se reproduira pas lors des redémarrages du mode débogage au cours des 48 prochaines heures.

6. Pour Visual Studio 2012 avec Update 4 : <a id="vs2012"></a>

	* Dans le portail de gestion Azure, accédez à l'onglet **Configurer** pour votre site Web, puis faites défiler vers le bas jusqu'à la section **Diagnostics de site**.

	* Définissez **Débogage distant** sur **On**, puis **Remote Debugging Visual Studio Version** sur **2012**.

	![Set remote debugging in management portal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)
   
	* Dans le menu **Déboguer** de Visual Studio, cliquez sur **Attacher au processus**.

	* Dans la zone **Qualificateur**, entrez l'URL de votre site Web, sans le préfixe `http://`. 

	* Sélectionnez **Afficher les processus de tous les utilisateurs**.

	* Lorsque le système vous demande vos informations d'identification, entrez le nom d'utilisateur et le mot de passe du compte ayant les autorisations de publication sur le site Web. Pour les obtenir, accédez à l'onglet Tableau de bord de votre site Web dans le portail de gestion, puis cliquez sur **Télécharger le profil de publication**. Ouvrez le fichier dans un éditeur de texte : vous trouverez le nom d'utilisateur et le mot de passe après les premières occurrences de **userName=** et **userPWD=**. 

	* Lorsque les processus sont affichés dans la table **Processus disponibles**, sélectionnez **w3wp.exe**, puis cliquez sur **Joindre**.

	* Ouvrez un navigateur vers l'URL de votre site.

	Vous devrez peut-être attendre environ 20 secondes pendant qu'Azure configure le serveur pour le débogage. Ce délai se produit uniquement lors du premier démarrage en mode débogage sur un site Web. Il ne se reproduira pas lors des redémarrages du mode débogage au cours des 48 prochaines heures.

6. Cliquez sur **À propos de** dans le menu.

	Visual Studio s'arrête sur le point d'arrêt et le code s'exécute dans Azure, et non pas sur votre ordinateur local.

7. Passez la souris sur la variable  `currentTime` pour afficher la valeur de temps.

	![View variable in debug mode running in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	Le temps affiché correspond au fuseau horaire du serveur Azure, qui peut différer de celui de votre ordinateur local.

8. Entrez une nouvelle valeur pour la variable  `currentTime`, comme " En cours d'exécution dans Azure ".

5. Appuyez sur F5 pour continuer.

	La page " À propos de " exécutée dans Azure affiche la nouvelle valeur entrée dans la variable currentTime.

	![About page with new value](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### <a name="remotedebugwj"></a>Débogage à distance de WebJobs

Cette section explique comment déboguer à distance en utilisant le projet et le site Web que vous créez dans [Prise en main du Kit de développement logiciel Azure WebJobs](../websites-dotnet-webjobs-sdk). Les fonctionnalités illustrées dans cette section sont uniquement disponibles dans Visual Studio 2013 avec Update 4.

1. Ouvrez le projet Web que vous avez créé dans [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure][GetStartedWJ].

1. Dans le projet ContosoAdsWebJob, ouvrez  *Functions.cs*.

2. [Définissez un point d'arrêt](http://www.visualstudio.com/fr-fr/get-started/debug-your-app-vs.aspx) sur la première instruction dans la méthode  `GnerateThumbnail`.

	![Set breakpoint](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. Dans l'**Explorateur de solutions**, cliquez sur le projet Web (et non le projet WebJob), puis cliquez sur **Publier**.

2. Dans le menu déroulant **Profil**, sélectionnez le même profil que celui utilisé dans [Prise en main du Kit de développement logiciel Azure WebJobs](../websites-dotnet-webjobs-sdk).

3. Cliquez sur l'onglet **Paramètres**, remplacez **Configuration** par **Débogage**, puis cliquez sur **Publier**.

	Visual Studio déploie les projets Web et WebJob et votre navigateur ouvre l'URL Azure de votre site.

5. Dans l'**Explorateur de serveurs** développez **Azure** > **sites Web** > votre site Web > **WebJobs** > **Continu**, puis cliquez avec le bouton droit sur **ContosoAdsWebJob**.

7. Cliquez sur **Attacher le débogueur**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

	Le navigateur ouvre automatiquement votre page d'accueil exécutée dans Azure. Vous devrez peut-être attendre environ 20 secondes pendant qu'Azure configure le serveur pour le débogage. Ce délai se produit uniquement lors du premier démarrage en mode débogage sur un site Web. La prochaine fois que vous attacherez le débogueur, il n'y aura aucun délai, si vous le faites dans les 48 heures.

6. Dans le navigateur Web ouvert à la page d'accueil Contoso Ads, créez une publicité. 

	La création d'une publicité provoque la création d'un message de file d'attente, qui sera récupéré par la tâche Web et traité. Quand le Kit SDK WebJobs appelle la fonction pour traiter le message de file d'attente, le code atteint votre point d'arrêt.

7. Quand le débogueur s'arrête au point d'arrêt, vous pouvez examiner et modifier les valeurs des variables pendant l'exécution du programme dans le cloud. Dans l'illustration suivante, le débogueur affiche le contenu de l'objet blobInfo qui a été passé à la méthode GenerateThumbnail.

	![blobInfo object in debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. Appuyez sur F5 pour continuer.

	La méthode GenerateThumbnail achève la création de la miniature.

6. Dans le navigateur, actualisez la page Index. La miniature est visible.

6. Dans Visual Studio, appuyez sur Maj+F5 pour arrêter le débogage.

7. Dans l'**Explorateur de serveurs**, cliquez sur le noeud ContosoAdsWebJob et cliquez avec le bouton droit sur **Afficher le tableau de bord**.

8. Connectez-vous avec vos informations d'identification Azure, puis cliquez sur le nom de la tâche Web pour accéder à la page de votre tâche Web. 

	![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	Le tableau de bord montre que la fonction GenerateThumbnail s'est exécutée récemment.

	(La prochaine fois que vous cliquerez sur **Afficher le tableau de bord**, vous ne serez pas obligé de vous connecter et le navigateur accèdera directement à la page de votre tâche Web.)

9. Cliquez sur le nom de la fonction pour afficher les détails sur l'exécution de la fonction.

	![Function details](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Si votre fonction [a écrit des journaux](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs), vous pouvez cliquer sur **ToggleOutput** pour les afficher.

### Notes à propos du débogage à distance

* Nous vous déconseillons d'exécuter le mode débogage en production. Si votre site de production n'est pas réparti sur plusieurs instances de serveur, le débogage empêche le serveur Web de répondre à d'autres demandes. Si vous avez plusieurs instances de serveurs Web, le fait de joindre le débogueur génèrera une instance aléatoire et vous empêchera de vérifier que les demandes de navigateur ultérieures parviendront à cette instance. De même, comme vous ne déployez généralement pas une version de débogage dans un environnement de production, les optimisations du compilateur pour les versions Release peuvent empêcher l'affichage des réactions ligne par ligne dans votre code source. Pour résoudre les problèmes de production, la meilleure ressource est constituée des journaux de suivi d'application et de serveur Web.

* Évitez les arrêts longs aux points d'arrêt avec le débogage à distance. Azure considère qu'un processus arrêté pendant plus de quelques minutes ne répond pas, et l'arrête définitivement.

* Pendant le débogage, le serveur envoie des données à Visual Studio, ce qui peut affecter les frais de bande passante. Pour plus d'informations sur les tarifs de bande passante, consultez les [tarifs Azure](/fr-fr/pricing/calculator/).

* Vérifiez que l'attribut  `debug` de l'élément  `compilation` du fichier  *Web.config* est défini sur true. Il est défini sur true par défaut lorsque vous publiez une configuration de version de débogage.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* Si le débogueur ne parcourt pas le code que vous voulez déboguer, vous devez modifier le paramètre " Uniquement mon code ".  Pour plus d'informations, consultez la page [Limiter le pas à pas à Uniquement mon code](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

* Lorsque vous activez la fonctionnalité de débogage à distance, un compteur démarre sur le serveur : après 48 heures, la fonctionnalité est automatiquement désactivée. Cette limite de 48 heures a été définie à des fins de sécurité et de performances. Vous pouvez facilement réactiver la fonctionnalité autant de fois que nécessaire. Nous vous recommandons de la désactiver lorsque vous n'utilisez pas le débogage.

* Vous pouvez manuellement attacher le débogueur à n'importe quel processus, et non uniquement au processus de site Web (w3wp.exe). Pour plus d'informations sur l'utilisation du mode débogage dans Visual Studio, consultez la page [Débogage dans Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

<h2><a name="logsoverview"></a> Présentation des journaux de diagnostic</h2>

Une application ASP.NET exécutée dans un site Web Azure peut créer les types de journaux suivants :

* **Journaux de suivi d'application**<br/>
  L'application crée ces journaux en appelant des méthodes de la classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx).
* **Journaux de serveur Web**<br/>
  Le serveur Web crée une entrée de journal pour chaque requête HTTP vers le site.
* **Journaux de messages d'erreur détaillés**<br/>
  Le serveur Web crée une page HTML contenant certaines informations supplémentaires pour les requêtes HTTP ayant échoué (celles dont le code d'état est 400 ou plus). 
* **Journaux de suivi des demandes ayant échoué**<br/>
  Le serveur Web crée un fichier XML avec des informations de suivi détaillées pour les requêtes HTTP ayant échoué. Le serveur Web fournit également un fichier XSL pour mettre en forme le XML dans un navigateur.
  
La journalisation affecte les performances du site. Azure vous permet donc d'activer ou de désactiver chaque type de journal selon vos besoins. Vous pouvez définir un niveau minimal de gravité pour l'écriture des journaux d'application. Lorsque vous créez un site Web, toutes les journalisations sont désactivées par défaut.

Les journaux sont écrits dans des fichiers dans un dossier  *LogFiles* du système de fichiers de votre site Web et sont accessibles via FTP. Il est également possible d'écrire les journaux de serveur Web et d'application dans un compte Azure Storage. Vous pouvez conserver davantage de journaux sur un compte de stockage que sur le système de fichiers. Le système de fichiers peut conserver jusqu'à 100 Mo de journaux (il ne conserve pas les journaux très longtemps : Azure libère de l'espace pour les nouveaux fichiers journaux en supprimant les anciens une fois la limite atteinte).  

<h2><a name="apptracelogs"></a>Création et affichage des journaux de suivi d'application</h2>

Dans cette section, vous effectuerez les tâches suivantes :

* Ajoutez des instructions de suivi au projet Web que vous avez créé durant le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted].
* affichage des journaux lorsque vous exécutez le projet localement ;
* affichage des journaux durant leur génération par l'application exécutée dans Azure. 

Pour plus d'informations sur la façon de créer des journaux d'applications dans WebJobs, consultez [Comment utiliser le stockage de file d'attente Azure avec le Kit SDK WebJobs - Comment écrire des journaux](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs). Les instructions suivantes concernant l'affichage des journaux et le contrôle de leur stockage dans Azure s'appliquent également aux journaux d'applications créés par WebJobs. 

### Ajout d'instructions de suivi à l'application

1. Ouvrez  *Controllers\HomeController.cs*, puis remplacez le contenu du fichier par le code suivant afin d'ajouter les instructions  `Trace` ainsi qu'une instruction  `using` au  `System.Diagnostics`:

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

	L'écouteur de suivi par défaut écrit toutes les sorties de suivi dans la fenêtre **Sortie**, avec d'autres sorties de débogage. L'image suivante montre la sortie des instructions de suivi ajoutées à la méthode  `Index`.

	![Tracing in Debug window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	La procédure suivante montre comment afficher la sortie de suivi dans une page Web, sans procéder à la compilation en mode de débogage.

2. Ouvrez le fichier Web.config de l'application (celui situé dans le dossier de projet), puis ajoutez un élément `<system.diagnostics>` à la fin du fichier, juste avant l'élément de fermeture `</configuration>` :

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

	L'élément  `WebPageTraceListener` vous permet d'afficher la sortie de suivi en accédant à  `/trace.axd`.

3. Ajoutez un <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">élément de suivi</a> sous " <system.web> " dans le fichier Web.config, comme dans l'exemple suivant :

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Appuyez sur Ctrl+F5 pour exécuter l'application.

4. Dans la barre d'adresses du navigateur, ajoutez  *trace.axd* à l'URL et appuyez sur Entrée (l'URL ressemblera à http://localhost:53370/trace.axd).

5. Dans la page **Suivi d'application**, cliquez sur **Afficher les détails** sur la première ligne (et non sur la ligne BrowserLink).

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	La page **Détails de la demande** s'affiche et la section **Informations de suivi** affiche la sortie des instructions de suivi ajoutées à la méthode  `Index`.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	Par défaut,  `trace.axd` est uniquement disponible localement. Si vous voulez le rendre disponible à partir d'un site distant, vous pouvez ajouter `localOnly="false"` à l'élément  `trace` dans le fichier  *Web.config*, comme le montre l'exemple suivant :

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	Cependant, l'activation de  `trace.axd` dans un site de production est généralement déconseillée pour des raisons de sécurité. Les sections suivantes montrent une méthode plus simple pour lire les journaux de suivi dans un site Web Azure.

### Affichage de la sortie de suivi dans Azure

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet Web, puis cliquez sur **Publier**.

2. Dans la boîte de dialogue **Publier le site Web**, cliquez sur **Publier**.

	Une fois que Visual Studio a publié votre mise à jour, il ouvre une fenêtre de navigateur vers votre page d'accueil (en partant du principe que vous n'avez pas décoché la case **URL de destination** sous l'onglet **Connexion**).

3. Dans l'**Explorateur de serveurs**, cliquez avec le bouton droit sur votre site Web et sélectionnez **Afficher la diffusion des journaux**. 

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	La fenêtre **Sortie** indique que vous êtes connecté au service de diffusion de journaux en continu et ajoute une ligne de notification à chaque minute passée sans affichage de journal.

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. Dans la fenêtre du navigateur qui affiche la page d'accueil de votre application, cliquez sur **Contact**.

	En quelques secondes, la sortie du suivi d'erreur que vous avez ajouté à la méthode  `Contact` apparaît dans la fenêtre **Sortie**.

	![Error trace in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	Visual Studio affiche uniquement le suivi des erreurs, car il s'agit du paramètre par défaut lorsque vous activez le service de surveillance des journaux. Lorsque vous créez un site Web Azure, toutes les journalisations sont désactivées par défaut, comme vous l'avez constaté en ouvrant la page de paramètres du site :

	![Application Logging off](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


	Cependant, lorsque vous avez sélectionné **Afficher la diffusion des journaux**, Visual Studio a automatiquement remplacé **Journalisation d'application (système de fichiers)** par **Erreur**, ce qui signifie que les journaux d'erreur sont signalés. Pour afficher tous vos journaux de suivi, vous pouvez remplacer ce paramètre par **Commentaires**. Lorsque vous sélectionnez un niveau de gravité inférieur à l'erreur, tous les journaux correspondant aux niveaux de gravité supérieurs sont également signalés. Donc, lorsque vous sélectionnez Commentaires, vous pouvez également consulter des informations, des avertissements et des journaux d'erreurs.  

4. Dans l'**Explorateur de serveurs**, cliquez avec le bouton droit sur le site Web, puis cliquez sur **Afficher les paramètres** comme vous l'avez fait auparavant.

5. Remplacez **Journalisation d'application (système de fichiers)** par **Commentaires**, puis cliquez sur **Enregistrer**.
 
	![Setting trace level to Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. Dans la fenêtre du navigateur affichant votre page **Contact**, cliquez sur **Home**, sur **About**, puis sur **Contact**.

	Quelques secondes après, la fenêtre **Sortie** affiche toutes vos sorties de suivi.

	![Verbose trace output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	Dans cette section, vous avez activé et désactivé la journalisation en utilisant les paramètres de Sites Web Azure. Vous pouvez également activer et désactiver les écouteurs de suivi en modifiant le fichier Web.config. Cependant, la modification du fichier Web.config entraîne le recyclage du domaine d'application, tandis que la journalisation via le site Web ne le fait pas. Si le problème met du temps à se reproduire ou s'il est intermittent, le recyclage du domaine d'application peut le " résoudre " temporairement et vous forcer à attendre qu'il se reproduise. L'activation des diagnostics dans Azure n'entraîne pas cela, vous pouvez donc commencer tout de suite à saisir des informations sur une erreur.

### Fonctionnalités de la fenêtre Sortie

L'onglet **Journaux Azure** de la fenêtre **Sortie** contient plusieurs boutons et une zone de texte :

![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Ces éléments permettent d'effectuer les opérations suivantes :

* suppression du contenu de la fenêtre **Sortie** ;
* activation ou désactivation du retour automatique à la ligne ;
* démarrage ou arrêt de la surveillance des journaux ;
* sélection des journaux à surveiller ;
* téléchargement de journaux ;
* filtrage des journaux en fonction d'une chaîne de recherche ou d'une expression régulière ;
* fermeture de la fenêtre **Sortie**.

Si vous entrez une chaîne de recherche ou une expression régulière, Visual Studio filtre les informations de journalisation au niveau du client. Cela signifie que vous pouvez entrer les critères après l'affichage des journaux dans la fenêtre **Sortie** et que vous pouvez modifier les critères de filtrage sans avoir à régénérer les journaux.

<h2><a name="webserverlogs"></a>Affichage des journaux de serveur Web</h2>

Les journaux de serveur Web enregistrent toutes les activités HTTP sur le site. Pour les afficher dans la fenêtre **Sortie**, vous devez les activer sur le site et indiquer à Visual Studio que vous voulez les surveiller. 

1. Sous l'onglet **Configuration de site Web Azure** que vous avez ouvert à partir de l'**Explorateur de serveurs**, définissez la valeur de Journalisation de serveur Web sur **Actif**, puis cliquez sur **Enregistrer**.

	![Enable web server logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. Dans la fenêtre **Sortie**, cliquez sur le bouton **Sélection des journaux Azure à surveiller**.
	
	![Specify which Azure logs to monitor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. Dans la boîte de dialogue **Options de journalisation Azure**, sélectionnez **Journaux de serveur Web**, puis cliquez sur **OK**.

	![Monitor web server logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. Dans la fenêtre de navigateur affichant le site Web, cliquez sur **Home**, sur **About**, puis sur **Contact**.

	Généralement, les journaux d'application s'affichent en premier, suivis par les journaux de serveur Web. Le processus d'affichage peut prendre un certain temps. 

	![Web server logs in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


Par défaut, Azure écrit les journaux dans le système de fichiers lorsque vous activez les journaux de serveur Web en utilisant Visual Studio. Une autre méthode consiste à utiliser le portail de gestion pour indiquer que les journaux de serveur Web doivent être écrits sur un conteneur d'objets blob dans un compte de stockage. Pour plus d'informations, consultez la section **Diagnostics de site** de la rubrique [Configuration des sites Web](/fr-fr/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig). 

Si vous utilisez le portail de gestion pour activer la journalisation de serveur Web sur un compte de stockage Azure, puis que vous désactivez la journalisation dans Visual Studio, lorsque vous réactivez la journalisation dans Visual Studio, les paramètres de votre compte de stockage sont restaurés. 

<h2><a name="detailederrorlogs"></a>Affichage des journaux de messages d'erreur détaillés</h2>

Les journaux d'erreur détaillés fournissent des informations supplémentaires sur les requêtes HTTP ayant pour résultat des codes de réponse d'erreur (400 ou au-delà). Pour les afficher dans la fenêtre **Sortie**, vous devez les activer sur le site et indiquer à Visual Studio que vous voulez les surveiller.

1. Sous l'onglet **Configuration de site Web Azure** ouvert depuis l'**Explorateur de serveurs**, définissez la valeur de **Messages d'erreur détaillés** sur **Actif**, puis cliquez sur **Enregistrer**.

	![Enable detailed error messages](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Dans la fenêtre **Sortie**, cliquez sur le bouton **Sélection des journaux Azure à surveiller**.

3. Dans la boîte de dialogue **Options de journalisation Azure**, cliquez sur **Tous les journaux**, puis sur **OK**.

	![Monitor all logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Dans la barre d'adresse de la fenêtre de navigateur, ajoutez un caractère supplémentaire à l'URL pour provoquer une erreur 404 (par exemple,  `http://localhost:53370/Home/Contactx`) et appuyez sur Entrée.

	Après quelques secondes, le journal d'erreur détaillé s'affiche dans la fenêtre **Sortie** de Visual Studio.

	![Detailed error log in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Maintenez la touche Ctrl appuyée tout en cliquant sur le lien pour afficher le journal de sortie mis en forme dans un navigateur :

	![Detailed error log in browser window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a>Téléchargement de journaux du système de fichiers</h2>

Tous les journaux que vous pouvez surveiller dans la fenêtre **Sortie** peuvent aussi être téléchargés en tant que fichiers *.zip*. 

1. Dans la fenêtre **Sortie**, cliquez sur **Download Streaming Logs**.

	![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	L'Explorateur de fichiers ouvre votre dossier  *Downloads* dans lequel le fichier téléchargé est sélectionné.

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Procédez à l'extraction du fichier *.zip* pour afficher la structure de dossiers suivante :

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* Les journaux de suivi d'application sont dans des fichiers *.txt* dans le dossier  *LogFiles\Application*.
	* Les journaux de serveur Web sont dans des fichiers *.log* dans le dossier  *LogFiles\http\RawLogs*. Vous pouvez utiliser un outil tel que [Log Parser](http://www.microsoft.com/fr-fr/download/details.aspx?displaylang=en&id=24659) pour afficher et manipuler ces fichiers.
	* Les journaux de messages d'erreur détaillés sont dans des fichiers *.html* dans le dossier  *LogFiles\DetailedErrors*.

	(Le dossier  *deployments* contient les fichiers créés par la publication à partir du contrôle de code source : il n'a rien à voir avec la publication Visual Studio. Le dossier  *Git* contient le suivi lié à la publication à partir du contrôle de code source et au service de diffusion de fichier journal en continu.)  

<h2><a name="storagelogs"></a>Affichage des journaux de stockage</h2>

Les journaux de suivi d'application peuvent également être envoyés vers un compte de stockage Azure et vous pouvez les afficher dans Visual Studio. Pour ce faire, vous allez créer un compte de stockage, activer les journaux de stockage dans le portail de gestion et les afficher sous l'onglet **Journaux** de la fenêtre **Site Web Azure**.

Vous pouvez envoyer des journaux à l'une des trois destinations suivantes, ou aux trois :

* le système de fichiers ;
* les tables de compte de stockage ;
* les objets blob de compte de stockage.

Vous pouvez définir un niveau de gravité distinct pour chaque destination. 

Les tables facilitent l'affichage des détails des journaux en ligne et elles prennent en charge la diffusion en continu. Vous pouvez demander des journaux dans des tables et afficher les nouveaux journaux durant leur création. Les objets blob facilitent le téléchargement des journaux dans des fichiers et leur analyse en utilisant HDInsight, car HDInsight sait comment utiliser le stockage d'objets blob. Pour plus d'informations, consultez la section **Hadoop et MapReduce** de la page [Options de stockage de données (développement d'applications de cloud concrètes avec Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

Pour le moment, vous avez configuré des journaux de système de fichiers sur un niveau de commentaires : la procédure suivante va vous guider durant la configuration des journaux au niveau de l'information pour accéder aux tables de compte de stockage. Au niveau de l'information " signifie que tous les journaux créés en appelant  `Trace.TraceInformation`,  `Trace.TraceWarning` et  `Trace.TraceError` seront affichés, mais pas les journaux créés en appelant  `Trace.WriteLine`.

Les comptes de stockage offrent un espace de stockage plus important et une conservation des journaux plus longue que le système de fichiers. L'envoi des journaux de suivi d'application au compte de stockage permet également d'obtenir des informations supplémentaires sur chaque journal. Cela n'est pas le cas avec le système de fichiers.

5. Cliquez avec le bouton droit sur **Stockage** sous le nœud Azure, puis cliquez sur **Créer un compte de stockage**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. Dans le **créer un compte de stockage** boîte de dialogue, entrez un nom pour le compte de stockage. 

	Le nom doit être unique (aucun autre compte de stockage Azure ne doit avoir le même nom). Si le nom que vous entrez est déjà utilisé, vous aurez la possibilité de le modifier.

	L'URL d'accès à votre compte de stockage sera *{nom}*.core.windows.net. 

5. Dans la liste déroulante **Région ou groupe d'affinités**, sélectionnez la région la plus proche de vous.

	Ce paramètre spécifie le centre de données Azure qui hébergera votre compte de stockage. Pour ce didacticiel, votre choix ne fait pas de différence importante mais, pour un site de production, vous voulez que votre serveur Web et votre compte de stockage se trouvent dans la même région pour minimiser les temps de latence et les charges d'acheminement des données. Le site Web (que vous créez ensuite) doit être aussi proche que possible des navigateurs qui y accèdent afin de minimiser les temps de latence.

6. Dans la liste déroulante **Réplication**, sélectionnez **Localement redondant**. 

	Lorsque la géo-réplication est activée pour un compte de stockage, le contenu stocké est répliqué dans un centre de données secondaire pour activer le basculement vers cet emplacement en cas de sinistre majeur à l'emplacement principal. La géo-réplication peut engendrer des coûts supplémentaires. Dans le cas des comptes test et de développement, vous êtes en général peu enclin à payer pour la géo-réplication. Pour plus d'informations, consultez [Créer, gérer ou supprimer un compte de stockage](../storage-create-storage-account/#replication-options).

5. Cliquez sur **Créer**. 

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)	

1. Dans la fenêtre **Site Web Azure** de Visual Studio, cliquez sur l'onglet **Journaux**, puis sur **Configurer la journalisation dans le portail de gestion**.

	![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	L'onglet **Configurer** du portail de gestion de votre site Web s'ouvre. Pour arriver à cet emplacement, vous pouvez aussi cliquer sur l'onglet **Sites Web** et cliquer sur l'onglet **Configurer**.

2. Sous l'onglet **Configurer** du portail de gestion, faites défiler vers le bas jusqu'à la section Diagnostics d'application, puis affectez la valeur **Actif** à l'option **Journal des applications (Stockage de tables)**.

3. Affectez la valeur **Information** au **Niveau de journalisation**.

4. Cliquez sur **Gérer le stockage des tables**.

	![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	Dans la zone **Gérer le stockage des tables pour les diagnostics de l'application**, vous pouvez sélectionner votre compte de stockage, si vous en avez plusieurs. Vous pouvez créer une table ou utiliser une table existante.

	![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. Dans la zone **Gérer le stockage des tables pour les diagnostics de l'application**, cliquez sur la coche pour fermer la zone.

6. Sous l'onglet **Configurer** du portail de gestion, cliquez sur **Enregistrer**.

7. Dans la fenêtre de navigateur qui affiche le site Web de l'application, cliquez sur **Home**, sur **About**, puis sur **Contact**.

	Les informations de journalisation générées par la navigation sur ces pages Web seront écrites sur le compte de stockage.

8. Sous l'onglet **Journaux** de la fenêtre **Site Web Azure** de Visual Studio, cliquez sur **Actualiser** sous **Résumé du diagnostic**.

	![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	La section **Résumé du diagnostic** affiche par défaut les journaux des 15 dernières minutes. Vous pouvez modifier ce délai pour afficher plus de journaux. 

	Si vous obtenez l'erreur " table introuvable ", vérifiez que vous avez bien parcouru les pages sur lesquelles le suivi est activé après avoir activé **Journal des applications (Stockage)** et cliqué sur **Enregistrer**.)

	![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	Notez qu'ici, vous pouvez afficher l'**ID de processus** et l'**ID de thread** pour chaque journal, ce qui n'est pas possible dans le système de fichiers. Vous pouvez consulter d'autres champs en affichant directement la table de stockage Azure.

8. Cliquez sur **Afficher tous les journaux des applications**.

	La table de journal de suivi s'affiche dans la vue de table de stockage Azure.
   
	(Si vous obtenez une erreur du type " la séquence ne contient pas d'élément ", ouvrez l'**Explorateur de serveurs**, développez le nœud de votre compte de stockage sous le nœud **Azure**, cliquez avec le bouton droit sur **Tables**, puis cliquez sur **Actualiser**.)

	![Tableau de suivi dans l'Explorateur de serveurs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	Cette vue affiche des champs supplémentaires que vous ne pouvez pas consulter ailleurs. Elle vous permet également de filtrer les journaux en utilisant l'interface utilisateur du générateur de requêtes pour construire une requête. Pour plus d'informations, consultez les sections " Utilisation des ressources de tables " et " Filtrage d'entités " de la page [Consultation des ressources de stockage avec l'Explorateur de serveurs](http://msdn.microsoft.com/library/windowsazure/ff683677.aspx).

7. Pour consulter les détails d'une ligne unique, double-cliquez sur l'une des lignes.

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a>Affichage des journaux de suivi des demandes ayant échoué</h2>

Les journaux de suivi de demandes ayant échoué sont utiles pour comprendre les détails de gestion IIS d'une requête HTTP, dans des scénarios tels que des problèmes de réécriture d'URL ou d'authentification. 

Les sites Web Azure utilisent la fonctionnalité de suivi des demandes ayant échoué disponible avec IIS 7.0 et versions ultérieures. Cependant, vous ne pouvez pas accéder aux paramètres IIS permettant de configurer les erreurs à journaliser. Lorsque vous activez le suivi des demandes ayant échoué, toutes les erreurs sont récupérées. 

Vous pouvez activer le suivi des demandes ayant échoué en utilisant Visual Studio, mais vous ne pouvez pas les afficher dans Visual Studio. Ces journaux sont des fichiers XML. Le service de diffusion de journal en continu surveille uniquement les fichiers jugés lisibles en mode texte brut :  les fichiers *.txt*, *.html* et *.log*.

Vous pouvez afficher les journaux de suivi des demandes ayant échoué directement dans un navigateur via FTP ou en local, après avoir utilisé un outil FTP pour les télécharger sur votre ordinateur local. Dans cette section, nous les afficherons directement dans un navigateur.

1. Sous l'onglet **Configuration** de la fenêtre **Site Web Azure** ouverte à partir de l'**Explorateur de serveurs**, affectez la valeur **Actif** à l'option **Suivi des demandes ayant échoué**, puis cliquez sur **Enregistrer**.

	![Enable failed request tracing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. Dans la barre d'adresse de la fenêtre de navigateur affichant le site Web, ajoutez un caractère supplémentaire à l'URL, puis cliquez sur Entrée pour provoquer une erreur 404.

	Ceci génère un journal de suivi des demandes ayant échoué. La procédure suivante vous montre comment afficher ou télécharger ce journal.

2. Dans Visual Studio, sous l'onglet **Configuration** de la fenêtre **Site Web Azure**, cliquez sur **Ouvrir dans le portail de gestion**.

3. Dans le portail de gestion, cliquez sur **Tableau de bord**, puis sur **Réinitialisez vos informations d'identification de déploiement** dans la section **Aperçu rapide**.

	![Reset FTP credentials link in Dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

4. Entrez un nouveau nom d'utilisateur et un nouveau mot de passe.

	![New FTP user name and password](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. Sous l'onglet **Tableau de bord** du portail de gestion, appuyez sur F5 pour actualiser la page, puis faites défiler vers le bas jusqu'à voir **Utilisateur du déploiement/FTP**. Notez que le nom d'utilisateur est doté d'un préfixe composé du nom du site. **Lorsque vous vous connectez, vous devez utiliser ce nom d'utilisateur complet, comprenant le nom du site en guise de préfixe, comme indiqué ici.**

5. Dans une nouvelle fenêtre de navigateur, accédez à l'URL affichée sous **Nom de l'hôte FTP** sous l'onglet **Tableau de bord** de la page du portail de gestion pour votre site Web. **Nom de l'hôte FTP** est situé en regard de **Utilisateur du déploiement/FTP** dans la section **Aperçu rapide**.

6. Connectez-vous en utilisant les informations d'identification FTP créées précédemment (incluant le préfixe du nom d'utilisateur, basé sur le nom du site).

	Le navigateur affiche le dossier racine du site.

6. Ouvrir le dossier  *LogFiles*.

	![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Ouvrez le dossier nommé W3SVC avec une valeur numérique.

	![Open W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	Ce dossier contient des fichiers XML pour plusieurs erreurs journalisées une fois le suivi des demandes ayant échoué activé, ainsi qu'un fichier XSL utilisable par un navigateur pour mettre en forme les fichiers XML.

	![W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Cliquez sur le fichier XML de la demande ayant échoué dont vous voulez consulter les informations de suivi.

	L'image suivante montre une partie des informations de suivi d'un exemple d'erreur.

	![Failed request tracing in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


<h2><a name="nextsteps"></a>Étapes suivantes</h2>

Vous avez vu comment Visual Studio facilite l'affichage de journaux créés par un site Web Azure. Les sections suivantes fournissent des liens vers des ressources supplémentaires sur des rubriques connexes :

* Résolution des problèmes de sites Web Azure
* Débogage dans Visual Studio 
* Débogage distant dans Azure
* Suivi dans les applications ASP.NET
* Analyse de journaux de serveur Web
* Analyse des journaux de suivi des demandes ayant échoué
* Débogage de Cloud Services

### Résolution des problèmes de sites Web Azure

Pour plus d'informations sur la résolution des problèmes de Sites Web Azure (WAWS), consultez les ressources suivantes :

* [Surveillance de sites Web](/fr-fr/manage/services/web-sites/how-to-monitor-websites/)
* [Étude des fuites de mémoire dans Sites Web Azure avec Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Article sur le blog ALM de Microsoft concernant les fonctionnalités de Visual Studio prévues pour l'analyse de problèmes de mémoire gérés.
* [Outils en ligne de Sites Web Azure que vous devez connaître](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Article de blog par Amit Apple.

Pour obtenir des réponses sur une question relative à la résolution des problèmes, ouvrez un fil de discussion dans l'un des forums suivants :

* [Forum Azure sur le site ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Forum Azure sur MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### Débogage dans Visual Studio 

Pour plus d'informations sur l'utilisation du mode débogage dans Visual Studio, consultez la rubrique MSDN [Débogage dans Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) et [Conseils de débogage avec Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### Débogage distant dans Azure

Pour plus d'informations sur le débogage distant de Sites Web Azure et WebJobs, consultez les ressources suivantes :

* [Introduction au débogage à distance sur des Sites Web Azure](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Deuxième partie de la présentation du débogage distant de Sites Web Azure : au cœur du débogage distant](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Troisième partie de la présentation du débogage distant de Sites Web Azure : environnement multi-instance et GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Débogage WebJobs (vidéo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Si votre site Web utilise une API Web Azure ou les composants principaux Mobile Services et que vous voulez les déboguer, consultez la page [Débogage du .NET principal dans Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### Suivi dans les applications ASP.NET

Il n'y a pas d'autre présentation du suivi ASP.NET plus détaillée et actualisée disponible sur Internet. Nous vous conseillons de commencer par consulter les anciens documents de présentation rédigés pour Web Forms, car MVC n'existait pas encore, et de compléter cela en consultant les billets de blog les plus récents traitant de problèmes plus précis. Les ressources suivantes constituent un bon début :

* [Surveillance et télémétrie (développement d'applications de cloud plus concrètes avec Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> 
  Chapitre de livre électronique contenant des recommandations pour le suivi dans les applications de cloud Azure.
* [Suivi ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Une ressource ancienne mais toujours efficace pour une présentation de base du sujet.
* [Écouteurs de suivi](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Contient des informations sur les écouteurs de suivi mais ne mentionne pas l'élément [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Procédure pas à pas : intégration du suivi ASP.NET avec le suivi System.Diagnostics](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Cette page également est un peu ancienne, mais vous y trouverez des informations complémentaires que l'article de présentation ne traite pas.
* [Suivi dans les vues d'ASP.NET MVC Razor](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  En plus du suivi dans les vues Razor, ce billet explique également comment créer un filtre d'erreur pour journaliser toutes les exceptions non gérées dans une application MVC. Pour plus d'informations sur la procédure de journalisation des exceptions non gérées dans une application Web Forms, consultez l'exemple de Global.asax dans [Exemple complet pour les gestionnaires d'erreurs](http://msdn.microsoft.com/library/bb397417.aspx) sur MSDN. Dans MVC ou Web Forms, si vous voulez journaliser certaines exceptions tout en laissant le Framework par défaut les gérer, vous pouvez utiliser l'exemple de code suivant :

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Journalisation de suivi de diagnostics de diffusion à partir de l'outil en ligne de commande Azure (et Glimpse)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Apprenez à utiliser l'outil en ligne de commande pour effectuer ce que montre ce didacticiel dans Visual Studio. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) est un outil pour le débogage d'applications ASP.NET. 
* [Utilisation de la journalisation et des diagnostics de site Web Azure - avec David Ebbo](http://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) et [Diffusion de journaux à partir de Sites Web Azure - avec David Ebbo](http://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Vidéos par Scott Hanselman et David Ebbo.

Pour la journalisation d'erreurs, vous pouvez éviter d'avoir à écrire votre propre code de suivi en utilisant un Framework de journalisation Open Source comme [ELMAH](http://nuget.org/packages/elmah/). Pour plus d'informations, consultez les [billets du blog de Scott Hanselman sur ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Notez également que vous n'avez pas besoin d'utiliser le suivi ASP.NET ou System.Diagnostics si vous voulez activer une diffusion de journaux en continu à partir d'Azure. Le service de diffusion de journaux en continu des sites Web Azure diffusera tous les fichiers *.txt*, *.html* ou *.log* qu'il trouvera dans le dossier  *LogFiles*. C'est pourquoi vous devez créer votre propre système de journalisation, qui écrira dans le système de fichiers du site Web. Ainsi, vos fichiers seront automatiquement diffusés et téléchargés. Tout ce que vous avez à faire est d'écrire un code d'application qui crée les fichiers dans le dossier  *d:\home\logfiles*. 

### Analyse de journaux de serveur Web

Pour plus d'informations sur l'analyse des journaux de serveur Web, consultez les ressources suivantes :

* [LogParser](http://www.microsoft.com/fr-fr/download/details.aspx?id=24659)<br/>
  Un outil pour afficher les données des journaux de serveur Web (fichiers *.log*).
* [Dépannage des problèmes de performances IIS ou des erreurs d'application à l'aide de LogParser](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Présentation de l'outil Log Parser que vous pouvez utiliser pour analyser les journaux de serveur Web.
* [Billets du blog de Robert McMurray sur l'utilisation de LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Code d'état HTTP dans IIS 7.0, IIS 7.5 et IIS 8.0](http://support.microsoft.com/kb/943891)

### Analyse des journaux de suivi des demandes ayant échoué

Le site Web Microsoft TechNet comporte une section [Utilisation du suivi des demandes ayant échoué](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) qui permet de comprendre l'utilisation de ces journaux. Cependant, cette documentation traite principalement du suivi des demandes ayant échoué dans IIS, ce qui ne s'applique pas à Sites Web Azure.

### Débogage de Cloud Services

Si vous voulez déboguer un service cloud Azure plutôt qu'un site Web, consultez la page [Débogage de services cloud](http://msdn.microsoft.com/library/windowsazure/ee405479.aspx).




[GetStarted]: ../web-sites-dotnet-get-started/
[GetStartedWJ]: ../websites-dotnet-webjobs-sdk/



<!--HONumber=42-->
