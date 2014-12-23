<properties title="Troubleshooting Azure Websites in Visual Studio" pageTitle="Dépannage de Sites Web Azure dans Visual Studio" metaKeywords="troubleshoot debug azure web site tracing logging" description="Learn how to troubleshoot an Azure Website by using remote debugging, tracing, and logging tools that are built in to Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter=".NET" authors="tdykstra" manager="wpickett" solutions="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/13/2014" ms.author="tdykstra" />

# Résolution des problèmes de Sites Web Azure dans Visual Studio

Ce didacticiel montre comment utiliser les outils Visual Studio permettant de déboguer une application exécutée dans un site web Azure en utilisant le [mode débogage](http://www.visualstudio.com/fr-fr/get-started/debug-your-app-vs.aspx) à distance ou en consultant les journaux des applications et des serveurs web.

Vous apprendrez ce qui suit :

* les fonctionnalités de gestion de site Azure disponibles dans Visual Studio ;
* l'utilisation de l'affichage distant de Visual Studio pour effectuer des modifications rapides dans un site Web distant ;
* l'exécution distante du mode débogage durant l'exécution d'un projet dans Azure ;
* la création de journaux de suivi d'application et leur affichage pendant leur création par l'application ;
* l'affichage des journaux de serveur Web, notamment des messages d'erreur détaillés et le suivi des demandes ayant échoué ;
* l'envoi de journaux de diagnostic à un compte Azure Storage et leur affichage depuis ce compte.

Si vous disposez de Visual Studio Ultimate, vous pouvez également utiliser [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) à des fins de débogage. IntelliTrace n'est pas couvert dans ce didacticiel.

### Sections du didacticiel

- [Configuration requise](#prerequisites)
- [Configuration et gestion de site](#sitemanagement)
- [Accès aux fichiers de site web dans l'Explorateur de serveurs](#remoteview)
- [Débogage à distance](#remotedebug)
	- Débogage à distance des sites web
	- Débogage à distance de WebJobs
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

Ce didacticiel fonctionne avec l'environnement de développement, le projet web et le site web Azure que vous avez configurés dans le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted]. Pour les sections relatives à WebJobs, vous avez besoin de l'application créée dans [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure][GetStartedWJ].

Les exemples de code inclus dans ce didacticiel sont destinés à une application Web C# MVC, mais les procédures de résolution de problèmes sont identiques pour les applications Visual Basic et Web Forms.

Le débogage distant requiert Visual Studio 2013 ou Visual Studio 2012 Update 4. Le débogage distant et les fonctionnalités de l'**Explorateur de serveurs** pour WebJobs nécessitent [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) ou version ultérieure. Les autres fonctionnalités illustrées dans ce didacticiel fonctionnent également dans Visual Studio 2013 Express pour le Web et Visual Studio 2012 Express pour le Web. 

La fonctionnalité de diffusion de journaux en continu est opérationnelle uniquement pour les applications ciblant .NET Framework 4 ou une version ultérieure.

<h2><a name="sitemanagement"></a>Configuration et gestion de site</h2>

Visual Studio permet d'accéder à un sous-ensemble des fonctionnalités de gestion de site et des paramètres de configuration disponibles dans le portail de gestion. Cette section présente les éléments disponibles.

1. Si vous n'êtes pas déjà connecté à Azure dans Visual Studio, cliquez sur le bouton **Se connecter à Azure** dans l'**Explorateur de serveurs**.

	Vous pouvez aussi installer un certificat de gestion permettant d'accéder à votre compte. Celui-ci permet à l'**Explorateur de serveurs** d'accéder à des services Azure supplémentaires (base de données SQL et Mobile Services). Si vous choisissez d'installer un certificat, cliquez avec le bouton droit sur le nœud **Azure** dans l'**Explorateur de serveurs**, puis cliquez sur **Gérer les abonnements** dans le menu contextuel. Dans la boîte de dialogue **Manage Azure Subscriptions**, cliquez sur l'onglet **Certificates**, puis sur **Import**. Suivez la procédure pour télécharger et importer un fichier d'abonnement (portant l'extension *.publishsettings*) pour votre compte Azure.

	> [WACOM.NOTE]
	> Si vous téléchargez un fichier d'abonnement, enregistrez-le dans un dossier situé hors de vos répertoires de code source (par exemple, dans le dossier Téléchargements), puis supprimez-le une fois l'importation terminée. Si un utilisateur malveillant accède au fichier d'abonnement, il peut modifier, créer et supprimer vos services Azure.

	Pour plus d'informations sur la connexion aux ressources Azure à partir de Visual Studio, consultez la page [Gestion des comptes, des abonnements et des rôles d'administrateur](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. Dans l'**Explorateur de serveurs**, développez **Azure**, puis **Sites Web**.

3. Cliquez avec le bouton droit sur le nœud du site web créé durant le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted], puis cliquez sur **Paramètres d'affichage**.

	![View Settings in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	L'onglet **Site Web Azure** s'affiche et des tâches de gestion et de configuration de site deviennent disponibles dans Visual Studio.

	![Azure Website window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	Dans ce didacticiel, vous utiliserez les menus déroulants de suivi et de journalisation.	Vous utiliserez également le débogage à distance en l'activant d'une façon différente.
   
	Pour plus d'informations sur les cases à cocher Paramètres de l'application et Chaînes de connexion de cette fenêtre, consultez la page [Fonctionnement des chaînes d'application et de connexion dans Sites Web Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

	Pour mener à bien une tâche de gestion de site qui n'est pas disponible dans cette fenêtre, cliquez sur **Paramètres de site Web complets** pour ouvrir une fenêtre de navigateur vers le portail de gestion. Pour plus d'informations, consultez la rubrique [Configuration des sites web](/fr-fr/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

<h2><a name="remoteview"></a>Accès aux fichiers de site web dans l'Explorateur de serveurs</h2>

Vous pouvez généralement déployer un site avec l'indicateur " customErrors " dans le fichier Web.config défini sur " On " ou " RemoteOnly ", ce qui signifie que vous n'obtenez pas de message d'erreur utile lorsqu'une erreur survient. Généralement, ces messages se présentent sous la forme d'une page comme celles qui suivent.

**Erreur de serveur dans l'application " / " :**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Nous avons rencontré une erreur :**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Le site Web ne peut pas afficher la page**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Généralement, la méthode la plus simple pour rechercher la cause d'une erreur consiste à activer les messages d'erreur détaillés : la première capture d'écran de la série précédente montre comment procéder. Vous devez modifier le fichier Web.config déployé. Vous pouvez modifier le fichier *Web.config* dans le projet, puis redéployer le projet, ou créer une [transformation Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations), puis déployer une version de débogage, mais il existe une méthode plus rapide : l'**Explorateur de solutions** permet d'afficher et de modifier directement des fichiers sur un site distant en utilisant la fonctionnalité *affichage distant*.

1. Dans l'**Explorateur de serveurs**, développez **Azure**, **Sites Web**, puis le nœud du site web vers lequel vous procédez au déploiement.

	Les nœuds qui s'affichent permettent d'accéder aux fichiers de contenu du site et aux fichiers journaux.

	![File and log files](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2. Développez le nœud **Fichiers**, puis double-cliquez sur le fichier *Web.config*.

	![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	Visual Studio ouvre le fichier Web.config à partir du site distant, puis affiche [Distant] en regard du nom du fichier dans la barre de titre.

3. Ajoutez la ligne suivante à l'élément " system.web " :

	`<customErrors mode="off"></customErrors>`

	![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Actualisez le navigateur qui affiche le message d'erreur inutile : à présent, vous obtenez un message d'erreur détaillé, comme dans l'exemple suivant :

	![Detailed error messaeg](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	L'erreur affichée est générée par l'ajout de la ligne affichée en rouge à *Views\Home\Index.cshtml*.

La modification du fichier Web.config n'est qu'un exemple des scénarios dans lesquels la capacité d'afficher et de modifier des fichiers sur votre site Web Azure simplifie la résolution des problèmes.

<h2><a name="remotedebug"></a>Débogage à distance</h2>

Si le message d'erreur détaillé ne fournit pas assez d'informations et que vous ne pouvez pas recréer l'erreur localement, une autre méthode pour résoudre le problème consiste à exécuter le mode de débogage à distance. Vous pouvez définir des points d'arrêt, manipuler directement la mémoire, parcourir le code en détail et même modifier le chemin d'accès du code. 

Le débogage à distance ne fonctionne pas avec les éditions Express de Visual Studio.

### Débogage à distance des sites web

Cette section vous montre comment procéder au débogage à distance en utilisant le projet créé dans le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted].

1. Ouvrez le projet web que vous avez créé durant le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted].

1. Ouvrez *Controllers\HomeController.cs*.

2. Supprimez la méthode " About() " et insérez le code suivant à la place.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Définissez un point d'arrêt](http://www.visualstudio.com/fr-fr/get-started/debug-your-app-vs.aspx) sur la ligne " ViewBag.Message ".

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**.

2. Dans le menu déroulant **Profil**, sélectionnez le même profil que celui utilisé dans [Prise en main d'Azure et d'ASP.NET][GetStarted].

3. Cliquez sur l'onglet **Paramètres**, remplacez **Configuration** par **Debug**, puis cliquez sur **Publier**.

	![Publish in debug mode](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. Une fois le déploiement terminé et que votre navigateur ouvre l'URL Azure de votre site, fermez-le.

5. Pour Visual Studio 2013 : dans l'**Explorateur de serveurs**, développez **Azure**, **Sites Web**, cliquez avec le bouton droit sur votre site Web, puis cliquez sur **Attacher le débogueur**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	Le navigateur ouvre automatiquement votre page d'accueil exécutée dans Azure. Vous devrez peut-être attendre environ 20 secondes pendant qu'Azure configure le serveur pour le débogage. Ce délai se produit uniquement lors du premier démarrage en mode débogage sur un site Web. Il ne se reproduira pas lors des redémarrages du mode débogage au cours des 48 prochaines heures.

6. Pour Visual Studio 2012 avec Update 4 :<a id="vs2012"></a>

	* Dans le portail de gestion Azure, accédez à l'onglet **Configurer** pour votre site web, puis faites défiler vers le bas jusqu'à la section **Diagnostics de site**.

	* Définissez **Débogage distant** sur **On**, puis **Débogage distant : Visual Studio Version** sur **2012**.

	![Set remote debugging in management portal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)
   
	* Dans le menu **Déboguer** de Visual Studio, cliquez sur **Attacher au processus**.

	* Dans la zone **Qualificateur**, entrez l'URL de votre site web, sans le préfixe " http:// ". 

	* Sélectionnez **Afficher les processus de tous les utilisateurs**.

	* Lorsque le système vous demande vos informations d'identification, entrez le nom d'utilisateur et le mot de passe du compte ayant les autorisations de publication sur le site web. Pour les obtenir, accédez à l'onglet Tableau de bord de votre site Web dans le portail de gestion, puis cliquez sur **Télécharger le profil de publication**. Ouvrez le fichier dans un éditeur de texte : vous trouverez le nom d'utilisateur et le mot de passe après les premières occurrences de **userName=** et **userPWD=**. 

	* Lorsque les processus sont affichés dans la table **Processus disponibles**, sélectionnez **w3wp.exe**, puis cliquez sur **Joindre**.

	* Ouvrez un navigateur vers l'URL de votre site.

	Vous devrez peut-être attendre environ 20 secondes pendant qu'Azure configure le serveur pour le débogage. Ce délai se produit uniquement lors du premier démarrage en mode débogage sur un site Web. Il ne se reproduira pas lors des redémarrages du mode débogage au cours des 48 prochaines heures.

6. Cliquez sur **À propos de** dans le menu.

	Visual Studio s'arrête sur le point d'arrêt et le code s'exécute dans Azure, et non pas sur votre ordinateur local.

7. Passez la souris sur la variable " currentTime " pour afficher la valeur de temps.

	![View variable in debug mode running in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	Le temps affiché correspond au fuseau horaire du serveur Azure, qui peut différer de celui de votre ordinateur local.

8. Entrez une nouvelle valeur pour la variable " currentTime ", comme " En cours d'exécution dans Azure ".

5. Appuyez sur F5 pour continuer.

	La page " À propos de " exécutée dans Azure affiche la nouvelle valeur entrée dans la variable currentTime.

	![About page with new value](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### <a name="remotedebugwj"></a> Débogage à distance de WebJobs

Cette section vous montre comment procéder au débogage à distance en utilisant le projet créé dans le didacticiel [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure](../websites-dotnet-webjobs-sdk). Les fonctionnalités figurant dans cette section sont uniquement disponibles dans Visual Studio 2013 avec Update 4.

1. Ouvrez le projet web que vous avez créé dans [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure][GetStartedWJ].

1. Dans le projet ContosoAdsWebJob, ouvrez *Functions.cs*.

2. [Définissez un point d'arrêt](http://www.visualstudio.com/fr-fr/get-started/debug-your-app-vs.aspx) sur la première instruction dans la méthode " GnerateThumbnail ".

	![Set breakpoint](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet web (pas le projet WebJobs), puis cliquez sur **Publier**.

2. Dans le menu déroulant **Profil**, sélectionnez le même profil que celui utilisé dans [Prise en main du Kit de développement logiciel (SDK) WebJobs Azure](../websites-dotnet-webjobs-sdk).

3. Cliquez sur l'onglet **Paramètres**, remplacez **Configuration** par **Debug**, puis cliquez sur **Publier**.

	Visual Studio déploie les projets web et WebJob, et votre navigateur ouvre l'URL Azure de votre site.

5. Dans l'**Explorateur de serveurs**, développez **Azure** > **Sites Web** > votre site web > **Tâches web** > **Continu**, puis cliquez avec le bouton droit sur **ContosoAdsWebJob**.

7. Cliquez sur **Attacher le débogueur**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

	Le navigateur ouvre automatiquement votre page d'accueil exécutée dans Azure. Vous devrez peut-être attendre environ 20 secondes pendant qu'Azure configure le serveur pour le débogage. Ce délai se produit uniquement lors du premier démarrage en mode débogage sur un site Web. La prochaine fois que vous attachez le débogueur, aucun délai ne se produira, si vous le faites dans les 48 heures.

6. Dans le navigateur web qui s'ouvre sur la page d'accueil Contoso Ads, créez une nouvelle publicité. 

	La création d'une publicité entraîne la création d'un message de file d'attente, qui sera récupéré par la tâche web WebJob et traité. Lorsque le Kit de développement logiciel (SDK) WebJobs appelle la fonction pour traiter le message de file d'attente, le code atteint votre point d'arrêt.

7. Lorsque le débogueur s'arrête au point d'arrêt, vous pouvez examiner et modifier les valeurs de variable pendant l'exécution du programme dans le cloud. Dans l'illustration suivante, le débogueur affiche le contenu de l'objet blobInfo qui a été passé à la méthode GenerateThumbnail.

	![blobInfo object in debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. Appuyez sur F5 pour continuer.

	La méthode GenerateThumbnail achève la création de la miniature.

6. Dans le navigateur, actualisez la page Index ; la miniature apparaît.

6. Dans Visual Studio, appuyez sur Maj+F5 pour arrêter le débogage.

7. Dans l'**Explorateur de serveurs**, cliquez avec le bouton droit sur le nœud ContosoAdsWebJob et cliquez sur **Afficher le tableau de bord**.

8. Connectez-vous avec vos informations d'identification Azure, puis cliquez sur le nom de la tâche web WebJob pour accéder à la page associée. 

	![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	Le tableau de bord indique que la fonction GenerateThumbnail a été exécutée récemment.

	(La prochaine fois que vous cliquez sur **Afficher le tableau de bord**, vous n'êtes pas obligé de vous connecter et le navigateur accède directement à la page de votre tâche web WebJob).

9. Cliquez sur le nom de la fonction pour afficher les détails relatifs à son exécution.

	![Function details](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Si votre fonction [a écrit des journaux](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs), vous pouvez cliquer sur **ToggleOutput** pour les afficher.

### Notes à propos du débogage à distance

* Nous vous déconseillons d'exécuter le mode débogage en production. Si votre site de production n'est pas réparti sur plusieurs instances de serveur, le débogage va empêcher le serveur web de répondre aux autres demandes. Si vous avez plusieurs instances de serveurs web, le fait de joindre le débogueur génèrera une instance aléatoire et vous empêchera de vérifier que les demandes suivantes de votre navigateur parviendront à cette instance. De même, comme vous ne déployez généralement pas une version de débogage dans un environnement de production, les optimisations du compilateur pour les versions Release peuvent empêcher l'affichage des réactions ligne par ligne dans votre code source. Pour résoudre les problèmes de production, la meilleure ressource est constituée des journaux de suivi d'application et de serveur web.

* Évitez les arrêts longs aux points d'arrêt avec le débogage à distance. Azure considère qu'un processus arrêté pendant plus de quelques minutes ne répond pas, et l'arrête définitivement.

* Pendant le débogage, le serveur envoie des données à Visual Studio, ce qui peut affecter les frais de bande passante. Pour plus d'informations sur les tarifs de bande passante, consultez les [tarifs Azure](/fr-fr/pricing/calculator/).

* Vérifiez que l'attribut " debug " de l'élément " compilation " dans le fichier *Web.config* est défini sur true. Il est défini sur true par défaut lorsque vous publiez une configuration de version de débogage.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* Si le débogueur ne parcourt pas le code que vous voulez déboguer, vous devez modifier le paramètre " Uniquement mon code ".  Pour plus d'informations, consultez la page [Limitation du pas à pas à Uniquement mon code](http://msdn.microsoft.com/fr-fr/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

* Lorsque vous activez la fonctionnalité de débogage à distance, un compteur démarre sur le serveur : après 48 heures, la fonctionnalité est automatiquement désactivée. Cette limite de 48 heures a été définie à des fins de sécurité et de performances. Vous pouvez facilement réactiver la fonctionnalité autant de fois que nécessaire. Nous vous recommandons de la désactiver lorsque vous n'utilisez pas le débogage.

* Vous pouvez attacher manuellement le débogueur à tout processus, et pas uniquement au processus de site web (w3wp.exe). Pour plus d'informations sur l'utilisation du mode débogage dans Visual Studio, consultez la page [Débogage dans Visual Studio](http://msdn.microsoft.com/fr-fr/library/vstudio/sc65sadd.aspx).

<h2><a name="logsoverview"></a>Présentation des journaux de diagnostic</h2>

Une application ASP.NET exécutée dans un site Web Azure peut créer les types de journaux suivants :

* **Journaux de suivi d'application**<br/>
  L'application crée ces journaux en appelant des méthodes de la classe [System.Diagnostics.Trace](http://msdn.microsoft.com/fr-fr/library/system.diagnostics.trace.aspx).
* **Journaux de serveur Web**<br/>
  Le serveur web crée une entrée de journal pour chaque requête HTTP vers le site.
* **Journaux détaillés des messages d'erreur**<br/>
9  Le serveur web crée une page HTML contenant certaines informations supplémentaires pour les requêtes HTTP ayant échoué (celles dont le code d'état est 400 ou plus). 
* **Journaux de suivi de demandes ayant échoué**<br/>
  Le serveur web crée un fichier XML avec des informations de suivi détaillées pour les requêtes HTTP. Il fournit également un fichier XSL pour mettre en forme le XML dans un navigateur.
  
La journalisation affecte les performances du site. Azure vous permet donc d'activer ou de désactiver chaque type de journal selon vos besoins. Vous pouvez définir un niveau minimal de gravité pour l'écriture des journaux d'application. Lorsque vous créez un site web, toutes les journalisations sont désactivées par défaut.

Les journaux sont écrits dans des fichiers dans un dossier *LogFiles* du système de fichiers de votre site web et sont accessibles via FTP. Il est également possible d'écrire les journaux de serveur Web et d'application dans un compte Azure Storage. Vous pouvez conserver davantage de journaux sur un compte de stockage que sur le système de fichiers. Le système de fichiers peut conserver jusqu'à 100 Mo de journaux (il ne conserve pas les journaux très longtemps : Azure libère de l'espace pour les nouveaux fichiers journaux en supprimant les anciens une fois la limite atteinte).  

<h2><a name="apptracelogs"></a>Création et affichage des journaux de suivi d'application</h2>

Dans cette section, vous effectuerez les tâches suivantes :

* Ajoutez des instructions de suivi au projet web que vous avez créé durant le didacticiel [Prise en main d'Azure et d'ASP.NET][GetStarted].
* affichage des journaux lorsque vous exécutez le projet localement ;
* Affichez les journaux tels que générés par l'application exécutée dans Azure. 

Pour plus d'informations sur la création de journaux d'application dans WebJobs, consultez la page [Utilisation de stockage de files d'attente Azure à l'aide du Kit de développement logiciel (SDK) WebJobs - Écriture de journaux](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs). Les instructions suivantes permettant d'afficher les journaux et de contrôler la façon dont ils sont stockés dans Azure s'appliquent également aux journaux des applications créés par WebJobs. 

### Ajout d'instructions de suivi à l'application

1. Ouvrez *Controllers\HomeController.cs*, puis remplacez le contenu du fichier par le code suivant afin d'ajouter les instructions " Trace " et une instruction " using " pour " System.Diagnostics " :

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
		            Trace.WriteLine("Entrée de méthode Index");
		            ViewBag.Message = "Modifiez ce modèle pour accélérer la mise en œuvre de votre application MVC ASP.NET.";
		            Trace.TraceInformation("Affichage de la page d'index à " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Sortie de méthode Index");
		            return View();
		        }
		
		        public ActionResult About()
		        {
		            Trace.WriteLine("Entrée de méthode About");
		            ViewBag.Message = "Page de description de votre application.";
		            Trace.TraceWarning("Erreur temporaire sur la page About à " + DateTime.Now.ToShortTimeString());
		            Trace.WriteLine("Sortie de méthode About");
		            return View();
		        }
		
		        public ActionResult Contact()
		        {
		            Trace.WriteLine("Entrée de méthode Contact");
		            ViewBag.Message = "Votre page de contacts.";
		            Trace.TraceError("Erreur irrécupérable sur la page Contact à " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Sortie de méthode Contact");
		            return View();
		        }
		    }
		}
		
				
### Affichage de la sortie de suivi en local

3. Appuyez sur F5 pour exécuter l'application en mode débogage.

	L'écouteur de suivi par défaut écrit toutes les sorties de suivi dans la fenêtre **Sortie**, avec d'autres sorties de débogage. L'image suivante montre la sortie des instructions de suivi ajoutées à la méthode " Index ".

	![Tracing in Debug window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	La procédure suivante montre comment afficher la sortie de suivi dans une page Web, sans procéder à la compilation en mode de débogage.

2. Ouvrez le fichier Web.config de l'application (celui situé dans le dossier de projet), puis ajoutez un élément " <system.diagnostics> " à la fin du fichier, juste avant l'élément de fermeture " </configuration> " :

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

	L'élément " WebPageTraceListener " vous permet d'afficher la sortie de suivi en accédant à " /trace.axd ".

3. Ajoutez un <a href="http://msdn.microsoft.com/fr-fr/library/vstudio/6915t83k(v=vs.100).aspx">élément de suivi</a> sous " <system.web> " dans le fichier Web.config, comme dans l'exemple suivant :

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Appuyez sur Ctrl+F5 pour exécuter l'application.

4. Dans la barre d'adresse du navigateur, ajoutez *trace.axd* à l'URL, puis appuyez sur Entrée (l'URL ressemblera à http://localhost:53370/trace.axd).

5. Dans la page **Suivi d'application**, cliquez sur **Afficher les détails** sur la première ligne (pas la ligne BrowserLink).

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	La page **Détails de la demande** s'affiche et la section **Informations de suivi** affiche la sortie des instructions de suivi ajoutées à la méthode " Index ".

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	Par défaut, " trace.axd " est uniquement disponible localement. Si vous voulez le rendre disponible à partir d'un site distant, vous pouvez ajouter " localOnly="false" " à l'élément " trace " dans le fichier *Web.config*, comme le montre l'exemple suivant :

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	Cependant, l'activation de " trace.axd " dans un site de production est généralement déconseillée pour des raisons de sécurité. Les sections suivantes montrent une méthode plus simple pour lire les journaux de suivi dans un site web Azure.

### Affichage de la sortie de suivi dans Azure

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet web, puis cliquez sur **Publier**.

2. Dans la boîte de dialogue **Publier le site Web**, cliquez sur **Publier**.

	Une fois que Visual Studio a publié votre mise à jour, il ouvre une fenêtre de navigateur vers votre page d'accueil (en partant du principe que vous n'avez pas désactivé la case à cocher **URL de destination** sous l'onglet **Connexion**).

3. Dans l'**Explorateur de serveurs**, cliquez avec le bouton droit sur votre site web, puis sélectionnez **Afficher les journaux de diffusion en continu**. 

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	La fenêtre **Sortie** indique que vous êtes connecté au service de diffusion de journaux en continu et ajoute une ligne de notification à chaque minute passée sans affichage de journal.

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. Dans la fenêtre du navigateur qui affiche la page d'accueil de votre application, cliquez sur **Contacter**.

	En quelques secondes, la sortie du suivi de l'erreur que vous avez ajouté à la méthode " Contact " apparaît dans la fenêtre **Sortie**.

	![Error trace in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	Visual Studio affiche uniquement le suivi des erreurs, car il s'agit du paramètre par défaut lorsque vous activez le service de surveillance des journaux. Lorsque vous créez un site web Azure, toutes les journalisations sont désactivées par défaut, comme vous l'avez constaté en ouvrant la page de paramètres du site :

	![Application Logging off](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


Cependant, lorsque vous avez sélectionné **Afficher les journaux de diffusion en continu**, Visual Studio a automatiquement remplacé **Journal des applications (système de fichiers)** par **Erreur**, ce qui signifie que les journaux d'erreur sont signalés. Pour afficher tous vos journaux de suivi, vous pouvez remplacer ce paramètre par **Commentaires**. Lorsque vous sélectionnez un niveau de gravité inférieur à l'erreur, tous les journaux correspondant aux niveaux de gravité supérieurs sont également signalés. Donc, lorsque vous sélectionnez Commentaires, vous pouvez également consulter des informations, des avertissements et des journaux d'erreurs.  

4. Dans l'**Explorateur de serveurs**, cliquez avec le bouton droit sur le site web, puis cliquez sur **Afficher les paramètres** comme vous l'avez fait auparavant.

5. Remplacez **Journal des applications (système de fichiers)** par **Commentaires**, puis cliquez sur **Enregistrer**.
 
	![Setting trace level to Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. Dans la fenêtre du navigateur affichant votre page **Contact**, cliquez sur **Accueil**, sur **À propos de**, puis sur **Contact**.

	Quelques secondes après, la fenêtre **Sortie** affiche toutes vos sorties de suivi.

	![Verbose trace output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	Dans cette section, vous avez activé et désactivé la journalisation en utilisant les paramètres de Sites Web Azure. Vous pouvez également activer et désactiver les écouteurs de suivi en modifiant le fichier Web.config. Cependant, la modification du fichier Web.config entraîne le recyclage du domaine d'application, tandis que la journalisation via le site Web ne le fait pas. Si le problème met du temps à se reproduire ou s'il est intermittent, le recyclage du domaine d'application peut le " résoudre " temporairement et vous forcer à attendre qu'il se reproduise. L'activation des diagnostics dans Azure n'entraîne pas cela, vous pouvez donc commencer tout de suite à saisir des informations sur une erreur.

### Fonctionnalités de la fenêtre Sortie

L'onglet **Journaux Azure** de la fenêtre **Sortie** contient plusieurs boutons et une zone de texte :

![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Ces éléments permettent d'effectuer les opérations suivantes :

* Supprimez le contenu de la fenêtre **Sortie**.
* Activation ou désactivation du retour automatique à la ligne
* Démarrage ou arrêt de la surveillance des journaux
* Sélection des journaux à surveiller
* Téléchargement de journaux
* Filtrage des journaux en fonction d'une chaîne de recherche ou d'une expression régulière
* Fermez la fenêtre **Sortie**.

Si vous entrez une chaîne de recherche ou une expression régulière, Visual Studio filtre les informations de journalisation au niveau du client. Cela signifie que vous pouvez entrer les critères après l'affichage des journaux dans la fenêtre **Sortie** et que vous pouvez modifier les critères de filtrage sans avoir à régénérer les journaux.

<h2><a name="webserverlogs"></a>Affichage des journaux de serveur Web</h2>

Les journaux de serveur web enregistrent toutes les activités HTTP sur le site. Pour les afficher dans la fenêtre **Sortie**, vous devez les activer sur le site et indiquer à Visual Studio que vous voulez les surveiller. 

1. Sous l'onglet **Configuration de site web Azure** que vous avez ouvert à partir de l'**Explorateur de serveurs**, définissez la valeur de Journalisation de serveur Web sur **Actif**, puis cliquez sur **Enregistrer**.

	![Enable web server logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. Dans la fenêtre **Sortie**, cliquez sur le bouton **Sélection des journaux Azure à surveiller**.
	
	![Specify which Azure logs to monitor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. Dans la boîte de dialogue **Options de journalisation Azure**, sélectionnez **Journaux de serveur web**, puis cliquez sur **OK**.

	![Monitor web server logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. Dans la fenêtre de navigateur affichant le site web, cliquez sur **Accueil**, sur **À propos de**, puis sur **Contact**.

	Généralement, les journaux des applications s'affichent en premier, suivis par les journaux de serveurs web. Le processus d'affichage peut prendre un certain temps. 

	![Web server logs in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


Par défaut, Azure écrit les journaux dans le système de fichiers lorsque vous activez les journaux de serveurs web en utilisant Visual Studio. Une autre méthode consiste à utiliser le portail de gestion pour indiquer que les journaux de serveur Web doivent être écrits sur un conteneur d'objets blob dans un compte de stockage. Pour plus d'informations, consultez la section **Diagnostics de site** de la rubrique [Configuration des sites web](/fr-fr/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig). .

If you use the management portal to enable web server logging to an Azure storage account, and then disable logging in Visual Studio, when you re-enable logging in Visual Studio your storage account settings are restored. 

<h2><a name="detailederrorlogs"></a>View detailed error message logs</h2>

Detailed error logs provide some additional information about HTTP requests that result in error response codes (400 or above). In order to see them in the **Output** window, you have to enable them on the site and tell Visual Studio that you want to monitor them.

1. In the **Azure Website Configuration** tab that you opened from **Server Explorer**, change **Detailed Error Messages** to **On**, and then click **Save**.

	![Enable detailed error messages](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. In the **Output** Window, click the **Specify which Azure logs to monitor** button.

3. In the **Azure Logging Options** dialog box, click **All logs**, and then click **OK**.

	![Monitor all logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. In the address bar of the browser window, add an extra character to the URL to cause a 404 error (for example, `http://localhost:53370/Home/Contactx`), and press Enter.

	After several seconds the detailed error log appears in the Visual Studio **Output** window.

	![Detailed error log in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Control+click the link to see the log output formatted in a browser:

	![Detailed error log in browser window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a>Download file system logs</h2>

Any logs that you can monitor in the **Output** window can also be downloaded as a *.zip* file. 

1. In the **Output** window, click **Download Streaming Logs**.

	![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	File Explorer opens to your *Downloads* folder with the downloaded file selected.

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Extract the *.zip* file, and you see the following folder structure:

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* Application tracing logs are in *.txt* files in the *LogFiles\Application* folder.
	* Web server logs are in *.log* files in the *LogFiles\http\RawLogs* folder. You can use a tool such as [Log Parser](http://www.microsoft.com/fr-fr/download/details.aspx?displaylang=en&id=24659) to view and manipulate these files.
	* Detailed error message logs are in *.html* files in the *LogFiles\DetailedErrors* folder.

	(The *deployments* folder is for files created by source control publishing; it doesn't have anything related to Visual Studio publishing. The *Git* folder is for traces related to source control publishing and the log file streaming service.)  

<h2><a name="storagelogs"></a>View storage logs</h2>

Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the management portal, and view them in the **Logs** tab of the **Azure Website** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination. 

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

5. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. In the **Create Storage Account** dialog, enter a name for the storage account. 

	The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

	The URL to access your storage account will be *{name}*.core.windows.net. 

5. Set the **Region or Affinity Group** drop-down list to the region closest to you.

	This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production site you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The website (which you'll create later) should be as close as possible to the browsers accessing your site in order to minimize latency.

6. Set the **Replication** drop-down list to **Locally redundant**. 

	When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [How To Manage Storage Accounts](/fr-fr/documentation/articles/storage-manage-storage-account/).

5. Cliquez sur **Créer**. 

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)	

1. Dans la fenêtre **Site Web Azure** de Visual Studio, cliquez sur l'onglet **Journaux**, puis sur **Configurer la journalisation dans le portail de gestion**.

	![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	L'onglet **Configurer** du portail de gestion de votre site web s'ouvre. Pour arriver à cet emplacement, vous pouvez aussi cliquer sur l'onglet **Sites Web** et sur l'onglet **Configurer**.

2. Sous l'onglet **Configurer** du portail de gestion, faites défiler vers le bas jusqu'à la section Diagnostic d'application, puis définissez **Journal des applications (stockage de tables)** sur **Actif**.

3. Définissez **Niveau de journalisation** sur **Information**.

4. Cliquez sur **Gérer le stockage des tables**.

	![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	Dans la zone **Gérer le stockage des tables pour les diagnostics de l'application**, vous pouvez sélectionner votre compte de stockage, si vous en avez plusieurs. Vous pouvez créer une table ou utiliser une table existante.

	![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. Dans la zone **Gérer le stockage des tables pour les diagnostics de l'application**, cliquez sur la coche pour fermer la zone.

6. Sous l'onglet **Configurer** du portail de gestion, cliquez sur **Enregistrer**.

7. Dans la fenêtre de navigateur affichant le site web de l'application, cliquez sur **Accueil**, sur **À propos de**, puis sur **Contact**.

	Les informations de journalisation générées par la navigation sur ces pages Web seront écrites sur le compte de stockage.

8. Sous l'onglet **Journaux** de la fenêtre **Site Web Azure** de Visual Studio, cliquez sur **Actualiser** sous **Résumé du diagnostic**.

	![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

La section **Résumé du diagnostic** affiche par défaut les journaux des 15 dernières minutes. Vous pouvez modifier ce délai pour afficher plus de journaux. 

Si vous obtenez l'erreur " table introuvable ", vérifiez que vous avez bien parcouru les pages sur lesquelles le suivi est activé après avoir activé **Journal des applications (stockage)** et cliqué sur **Enregistrer**.

	![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

Notez qu'ici, vous pouvez afficher l'**ID de processus** et l'**ID de thread** pour chaque journal, ce qui n'est pas possible dans le système de fichiers. Vous pouvez consulter d'autres champs en affichant directement la table de stockage Azure.

8. Cliquez sur **Afficher tous les journaux d'application**.

	La table de journal de suivi s'affiche dans la vue de table de stockage Azure.
   
	Si vous obtenez une erreur du type " la séquence ne contient pas d'élément ", ouvrez l'**Explorateur de serveurs**, développez le nœud de votre compte de stockage sous le nœud **Azure**, cliquez avec le bouton droit sur **Tables**, puis cliquez sur **Actualiser**.

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	Cette vue affiche des champs supplémentaires que vous ne pouvez pas consulter ailleurs. Elle vous permet également de filtrer les journaux en utilisant l'interface utilisateur du générateur de requêtes pour construire une requête. Pour plus d'informations, consultez les sections " Utilisation des ressources de tables " et " Filtrage d'entités " de la page [Consultation des ressources de stockage avec l'Explorateur de serveurs](http://msdn.microsoft.com/fr-fr/library/windowsazure/ff683677.aspx).

7. Pour consulter les détails d'une ligne unique, double-cliquez sur l'une des lignes.

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a>Affichage des journaux de suivi de demandes ayant échoué</h2>

Les journaux de suivi de demandes ayant échoué sont utiles pour comprendre les détails de gestion IIS d'une requête HTTP, dans des scénarios tels que des problèmes de réécriture d'URL ou d'authentification. 

Sites Web Azure utilise la fonctionnalité de suivi des demandes ayant échoué disponible avec IIS 7.0 et versions ultérieures. Cependant, vous ne pouvez pas accéder aux paramètres IIS permettant de configurer les erreurs à journaliser. Lorsque vous activez le suivi des demandes ayant échoué, toutes les erreurs sont récupérées. 

Vous pouvez activer le suivi des demandes ayant échoué en utilisant Visual Studio, mais vous ne pouvez pas les afficher dans Visual Studio. Ces journaux sont des fichiers XML. Le service de diffusion de journal en continu surveille uniquement les fichiers jugés lisibles en mode texte brut :  fichiers *.txt*, *.html* et *.log*.

Vous pouvez afficher les journaux de suivi des demandes ayant échoué directement dans un navigateur via FTP ou en local, après avoir utilisé un outil FTP pour les télécharger sur votre ordinateur local. Dans cette section, nous les afficherons directement dans un navigateur.

1. Sous l'onglet **Configuration** de la fenêtre **Site Web Azure** ouverte à partir de l'**Explorateur de serveurs**, définissez **Suivi des demandes ayant échoué** sur **Actif**, puis cliquez sur **Enregistrer**.

	![Enable failed request tracing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. Dans la barre d'adresse de la fenêtre de navigateur affichant le site Web, ajoutez un caractère supplémentaire à l'URL, puis cliquez sur Entrée pour provoquer une erreur 404.

	Ceci génère un journal de suivi des demandes ayant échoué. La procédure suivante vous montre comment afficher ou télécharger ce journal.

2. Dans Visual Studio, sous l'onglet **Configuration** de la fenêtre **Site Web Azure**, cliquez sur **Ouvrir dans le portail de gestion**.

3. Dans le portail de gestion, cliquez sur **Tableau de bord**, puis sur **Réinitialisez vos informations d'identification de déploiement** dans la section **Aperçu rapide**.

	![Reset FTP credentials link in Dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

4. Entrez un nouveau nom d'utilisateur et un nouveau mot de passe.

	![New FTP user name and password](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. Sous l'onglet **Tableau de bord** du portail de gestion, appuyez sur F5 pour actualiser la page, puis faites défiler vers le bas jusqu'à voir **Utilisateur du déploiement/FTP**. Notez que le nom d'utilisateur est doté d'un préfixe composé du nom du site. **Lorsque vous vous connectez, vous devez utiliser ce nom d'utilisateur complet, comprenant le nom du site en guise de préfixe, comme indiqué ici.**

5. Dans une nouvelle fenêtre de navigateur, accédez à l'URL affichée sous **Nom de l'hôte FTP** sous l'onglet **Tableau de bord** de la page du portail de gestion pour votre site web. **Nom de l'hôte FTP** est situé en regard de **Utilisateur du déploiement/FTP** dans la section **Aperçu rapide**.

6. Connectez-vous en utilisant les informations d'identification FTP créées précédemment (incluant le préfixe du nom d'utilisateur, basé sur le nom du site).

	Le navigateur affiche le dossier racine du site.

6. Ouvrez le dossier *LogFiles*.

	![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Ouvrez le dossier nommé W3SVC avec une valeur numérique.

	![Open W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	Ce dossier contient des fichiers XML pour plusieurs erreurs journalisées une fois le suivi des demandes ayant échoué activé, ainsi qu'un fichier XSL utilisable par un navigateur pour mettre en forme les fichiers XML.

	![W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Cliquez sur le fichier XML de la demande ayant échoué dont vous voulez consulter les informations de suivi.

	L'image suivante montre une partie des informations de suivi d'un exemple d'erreur.

	![Failed request tracing in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


<h2><a name="nextsteps"></a>Étapes suivantes</h2>

Vous avez vu comment Visual Studio facilite l'affichage de journaux créés par un site web Azure. Les sections suivantes fournissent des liens vers des ressources supplémentaires pour des rubriques connexes :

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
* [Étude des fuites de mémoire dans Sites Web Azure au sein de Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Article du blog ALM de Microsoft concernant les fonctionnalités de Visual Studio prévues pour l'analyse de problèmes de mémoire managée.
* [Outils en ligne de Sites Web Azure que vous devez connaître](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Billet de blog par Amit Apple.

Pour obtenir des réponses sur une question relative à la résolution des problèmes, ouvrez un fil de discussion dans l'un des forums suivants :

* [Forum Azure sur le site ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Forum Azure sur MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### Débogage dans Visual Studio 

Pour plus d'informations sur l'utilisation du mode débogage dans Visual Studio, consultez la rubrique MSDN [Débogage dans Visual Studio](http://msdn.microsoft.com/fr-fr/library/vstudio/sc65sadd.aspx), puis [Conseils de débogage avec Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### Débogage distant dans Azure

Pour plus d'informations sur le débogage à distance de Sites Web Azure et WebJobs, consultez les ressources suivantes :

* [Présentation du débogage à distance de Sites Web Azure](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Deuxième partie de la présentation du débogage à distance de Sites Web Azure : au cœur du débogage à distance](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Troisième partie de la présentation du débogage à distance de Sites Web Azure : environnement multi-instance et GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Débogage de WebJobs (vidéo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Si votre site web utilise une API web Azure ou les composants principaux Mobile Services et que vous voulez les déboguer, consultez la page [Débogage du serveur principal .NET dans Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### Suivi dans les applications ASP.NET

Il n'y a pas d'autre présentation du suivi ASP.NET plus détaillée et actualisée disponible sur Internet. Nous vous conseillons de commencer par consulter les anciens documents de présentation rédigés pour Web Forms, car MVC n'existait pas encore, et de compléter cela en consultant les billets de blog les plus récents traitant de problèmes plus précis. Les ressources suivantes constituent un bon début :

* [Surveillance et télémétrie (développement d'applications de cloud plus concrètes avec Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> 
  Chapitre de livre électronique contenant des recommandations pour le suivi dans les applications de cloud Azure.
* [Suivi ASP.NET](http://msdn.microsoft.com/fr-fr/library/ms972204.aspx)<br/>
  Une ressource ancienne mais toujours efficace pour une présentation de base du sujet.
* [Écouteurs de suivi](http://msdn.microsoft.com/fr-fr/library/4y5y10s7.aspx)<br/>
  Contient des informations sur les écouteurs de suivi mais ne mentionne pas l'élément [WebPageTraceListener](http://msdn.microsoft.com/fr-fr/library/system.web.webpagetracelistener.aspx).
* [Procédure pas à pas : Intégration du suivi ASP.NET au suivi System.Diagnostics](http://msdn.microsoft.com/fr-fr/library/b0ectfxd.aspx)<br/>
  Cette page également est un peu ancienne, mais vous y trouverez des informations complémentaires que l'article de présentation ne traite pas.
* [Suivi dans les vues d'ASP.NET MVC Razor](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  En plus du suivi dans les vues Razor, ce billet explique également comment créer un filtre d'erreur pour journaliser toutes les exceptions non gérées dans une application MVC. Pour plus d'informations sur la procédure de journalisation des exceptions non gérées dans une application Web Forms, consultez l'exemple de Global.asax dans [Exemple complet pour les gestionnaires d'erreurs](http://msdn.microsoft.com/fr-fr/library/bb397417.aspx) sur MSDN. Dans MVC ou Web Forms, si vous voulez journaliser certaines exceptions tout en laissant l'infrastructure par défaut les gérer, vous pouvez utiliser l'exemple de code suivant :

        try
        {
           // Votre code pouvant provoquer la levée d'une exception.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Journalisation de suivi de diagnostics de diffusion à partir de l'outil en ligne de commande Azure (et Glimpse)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
 Apprenez à utiliser l'outil en ligne de commande pour effectuer ce que montre ce didacticiel dans Visual Studio. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) est un outil pour le débogage d'applications ASP.NET. 
* [Utilisation de la journalisation et des diagnostics de site web Azure, avec David Ebbo](http://www.windowsazure.com/fr-fr/documentation/videos/azure-web-site-logging-and-diagnostics/) et [Diffusion de journaux à partir de Sites Web Azure, avec David Ebbo](http://www.windowsazure.com/fr-fr/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Vidéos par Scott Hanselman et David Ebbo.

Pour la journalisation d'erreurs, vous pouvez éviter d'avoir à écrire votre propre code de suivi en utilisant une infrastructure de journalisation open source comme [ELMAH](http://nuget.org/packages/elmah/). Pour plus d'informations, consultez les [billets du blog de Scott Hanselman sur ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Notez également que vous n'avez pas besoin d'utiliser le suivi ASP.NET ou System.Diagnostics si vous voulez activer une diffusion de journaux en continu à partir d'Azure. Le service de diffusion de journaux en continu des sites web Azure diffusera tous les fichiers *.txt*, *.html* ou *.log* qu'il trouvera dans le dossier *LogFiles*. C'est pourquoi vous devez créer votre propre système de journalisation, qui écrira dans le système de fichiers du site Web. Ainsi, vos fichiers seront automatiquement diffusés et téléchargés. Tout ce que vous avez à faire est d'écrire un code d'application qui crée les fichiers dans le dossier *d:\home\logfiles*. 

### Analyse de journaux de serveur Web

Pour plus d'informations sur l'analyse des journaux de serveur Web, consultez les ressources suivantes :

* [LogParser](http://www.microsoft.com/fr-fr/download/details.aspx?id=24659)<br/>
  Outil permettant d'afficher les données des journaux de serveur web (fichiers *.log*).
* [Dépannage des problèmes de performances IIS ou des erreurs d'application à l'aide de LogParser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Présentation de l'outil Analyseur de journal que vous pouvez utiliser pour analyser les journaux de serveur web.
* [Billets du blog de Robert McMurray sur l'utilisation de LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Code d'état HTTP dans IIS 7.0, IIS 7.5 et IIS 8.0](http://support.microsoft.com/kb/943891)

### Analyse des journaux de suivi des demandes ayant échoué

Le site web Microsoft TechNet comporte une section [Utilisation du suivi des demandes ayant échoué](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) qui permet de comprendre l'utilisation de ces journaux. Cependant, cette documentation traite principalement du suivi des demandes ayant échoué dans IIS, ce qui ne s'applique pas à Sites Web Azure.

### Débogage de Cloud Services

Si vous voulez déboguer un service cloud Azure plutôt qu'un site web, consultez la page [Débogage de Cloud Services](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee405479.aspx).




[GetStarted]: ../web-sites-dotnet-get-started/
[GetStartedWJ]: ../websites-dotnet-webjobs-sdk/

