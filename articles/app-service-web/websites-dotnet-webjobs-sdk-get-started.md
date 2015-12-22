<properties
	pageTitle="Création d'une tâche web .NET dans Azure App Service | Microsoft Azure"
	description="Créez une application multiniveau avec ASP.NET MVC et Azure. Le serveur frontal s'exécute dans une application web d'Azure App Service, tandis que le serveur principal s'exécute sous la forme d'une tâche web. L’application utilise Entity Framework, la base de données SQL, ainsi que les files d’attente et objets blobs du stockage Azure."
	services="app-service"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="12/14/2015"
	ms.author="tdykstra"/>

# Créer une tâche web .NET dans Azure App Service

Ce didacticiel montre comment écrire du code pour une simple application ASP.NET MVC 5 à plusieurs niveaux utilisant le [kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk.md).

L’objectif du [kit de développement logiciel (SDK) WebJobs](websites-webjobs-resources.md) consiste à simplifier le code que vous écrivez pour les tâches web courantes, telles que le traitement d’image, le traitement de la file d’attente, l’agrégation RSS, la maintenance des fichiers et l’envoi des messages électroniques. Le kit de développement logiciel (SDK) WebJobs dispose de fonctionnalités intégrées fonctionnant avec le stockage Azure et Service Bus et servant à planifier des tâches, à gérer des erreurs et à nombreux autres scénarios courants. En outre, il est évolutif et il existe un [référentiel open source contenant les extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

L'exemple d'application concerne un panneau d'affichage publicitaire. Les utilisateurs peuvent télécharger des images pour les annonces ; un processus principal convertit les images en miniatures. La page de liste des annonces affiche des miniatures et la page de détails des annonces affiche les images en taille réelle. Voici une capture d'écran :

![Ad list](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

Cet exemple d’application fonctionne avec des [files d’attente Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) et [objets blob Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage). Ce didacticiel montre comment déployer l’application sur [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) et sur la [base de données SQL Azure](http://msdn.microsoft.com/library/azure/ee336279).

## <a id="prerequisites"></a>Configuration requise

Ce didacticiel suppose que vous savez utiliser les projets [ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) dans Visual Studio.

Ce didacticiel a été écrit pour Visual Studio 2013. Si vous ne disposez pas de Visual Studio, l’installation sera lancée pour vous automatiquement, lors de l’installation du Kit de développement logiciel (SDK) Azure pour .NET.

Le didacticiel peut être utilisé avec Visual Studio 2015. Toutefois, avant d’exécuter l’application localement, vous devez modifier la partie `Data Source` de la chaîne de connexion SQL Server LocalDB dans les fichiers Web.config et App.config de `Data Source=(localdb)\v11.0` à `Data Source=(LocalDb)\MSSQLLocalDB`.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note2.md)]

## <a id="learn"></a>Contenu

Ce didacticiel explique comment effectuer les tâches suivantes :

* configuration de votre ordinateur pour le développement Azure en installant le Kit de développement logiciel (SDK) Azure ;
* création du projet d'application console qui se déploie automatiquement comme une tâche web Azure lorsque vous déployez le projet web associé ;
* test d'un serveur principal de Kit de développement logiciel (SDK) WebJobs localement sur l'ordinateur de développement ;
* publication d’une application avec un serveur principal de tâches web dans une application web d’App Service ;
* téléchargement et enregistrement de fichiers dans le service Blob Azure ;
* utilisation du Kit de développement logiciel (SDK) Azure WebJobs avec des files d'attente et des objets blob Azure Storage.

## <a id="contosoads"></a>Architecture de l’application

L'exemple d'application utilise le [modèle de travail centré sur les files d'attente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) pour décharger le travail de création de vignettes exigeant en ressources vers un processus principal.

L'application stocke les publicités dans une base de données SQL et utilise Entity Framework Code First pour créer les tables et accéder aux données. Pour chaque publicité, la base de données stocke deux URL, une pour l’image à taille réelle et l’autre pour la miniature.

![Ad table](./media/websites-dotnet-webjobs-sdk-get-started/adtable.png)

Lorsqu’un utilisateur charge une image, l’application web la stocke dans un [objet blob Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) et stocke les informations publicitaires dans la base de données avec une URL pointant vers l’objet blob. En même temps, il écrit un message dans une file d'attente Azure. Dans un processus principal s’exécutant en tant qu’Azure WebJob, le Kit de développement logiciel (SDK) WebJobs interroge la file d’attente sur la présence de nouveaux messages. Lorsqu'un nouveau message arrive, la tâche web crée une vignette pour cette image et met à jour le champ de la base de données des URL des vignettes pour cette publicité. Le schéma suivant montre l'interaction des parties de l'application :

![Contoso Ads architecture](./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png)

[AZURE.INCLUDE [install-sdk](../../includes/install-sdk-2015-2013.md)]

Les instructions du didacticiel s’appliquent au Kit de développement logiciel (SDK) Azure pour .NET 2.7.1 ou pour une version ultérieure.

## <a id="storage"></a>Création d'un compte de stockage Azure

Un compte de stockage Azure fournit des ressources pour stocker les données de file d'attente et d'objet blob dans le cloud. Le Kit de développement logiciel (SDK) WebJobs l'utilise également pour enregistrer les données de journalisation du tableau de bord.

Dans une application réelle, on crée généralement des comptes distincts pour les données d'application et de journalisation, et des comptes distincts pour les données de test et de production. Pour ce didacticiel, vous allez utiliser un seul compte.

1. Ouvrez la fenêtre **Explorateur de serveurs** dans Visual Studio.

2. Cliquez avec le bouton droit sur le nœud **Azure**, puis cliquez sur **Connexion à Microsoft Azure**. ![Connexion à Azure](./media/websites-dotnet-webjobs-sdk-get-started/connaz.png)

3. Connectez-vous à l'aide de vos informations d'identification Azure.

5. Cliquez avec le bouton droit sur **Stockage** sous le nœud Azure, puis cliquez sur **Créer un compte de stockage**. ![Créer un compte de stockage](./media/websites-dotnet-webjobs-sdk-get-started/createstor.png)

3. Dans la boîte de dialogue **Créer un compte de stockage**, entrez un nom correspondant au compte de stockage.

	Le nom doit être unique (aucun autre compte de stockage Azure ne doit avoir le même nom). Si le nom que vous entrez est déjà utilisé, vous aurez la possibilité de le modifier.

	L’URL permettant d’accéder à votre compte de stockage est *{nom}*.core.windows.net.

5. Choisissez la région la proche de vous dans la liste déroulante **Région ou groupe d’affinités**.

	Ce paramètre spécifie le centre de données Azure qui hébergera votre compte de stockage. Pour ce didacticiel, votre choix ne fera pas une grande différence. Toutefois, dans le cas d’une application web de production, vous souhaitez que votre serveur web et votre compte de stockage soient situés dans la même région afin de minimiser la latence et les frais d’acheminement des données. Le centre de données de l’application web (que vous créerez par la suite) doit être aussi proche que possible des navigateurs qui accèdent à l’application afin de réduire la latence.

6. Dans la liste déroulante **Réplication**, sélectionnez **Redondant en local**.

	Lorsque la géo-réplication est activée pour un compte de stockage, le contenu stocké est répliqué dans un centre de données secondaire pour activer le basculement vers cet emplacement en cas de sinistre majeur à l'emplacement principal. La géo-réplication peut engendrer des coûts supplémentaires. Dans le cas des comptes test et de développement, vous êtes en général peu enclin à payer pour la géo-réplication. Pour plus d’informations, consultez [Création, gestion ou suppression d’un compte de stockage](../storage-create-storage-account/#replication-options).

5. Cliquez sur **Create**.

	![New storage account](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)

## <a id="download"></a>Télécharger l’application

1. Téléchargez et décompressez la [solution terminée](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb).

2. Démarrez Visual Studio.

3. Dans le menu **Fichier**, sélectionnez **Ouvrir > Projet/Solution**, accédez à l’emplacement où vous avez téléchargé la solution, puis ouvrez le fichier de la solution.

4. Appuyez sur Ctrl+Maj+B pour générer la solution.

	Par défaut, Visual Studio restaure automatiquement le contenu du package NuGet, qui n'était pas inclus dans le fichier *.zip*. Si les packages ne sont pas restaurés, installez-les manuellement en ouvrant la boîte de dialogue **Gérer les packages NuGet pour la solution** et en cliquant sur le bouton **Restaurer** en haut à droite.

5. Dans l'**Explorateur de solutions**, vérifiez que **ContosoAdsWeb** est sélectionné comme projet de démarrage.

## <a id="configurestorage"></a>Configurer l’application pour utiliser votre compte de stockage

1. Ouvrez le fichier d'application *Web.config* dans le projet ContosoAdsWeb.

	Ce fichier contient des chaînes de connexion SQL et de stockage Azure pour utiliser des objets blob et des files d'attente.

	La chaîne de connexion SQL pointe vers une base de données [SQL Server Express LocalDB](http://msdn.microsoft.com/library/hh510202.aspx).

	La chaîne de connexion de stockage est un exemple qui comporte des espaces réservés pour la clé d’accès et le nom du compte stockage. Vous allez le remplacer par une chaîne de connexion qui a le nom et la clé de votre compte de stockage.

	<pre class="prettyprint">&lt;connectionStrings>
  &lt;add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
  &lt;add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/>
&lt;/connectionStrings></pre>La chaîne de connexion de stockage est nommée AzureWebJobsStorage, car il s'agit du nom que le Kit de développement logiciel (SDK) WebJobs utilise par défaut. Nous utilisons ce nom ici pour qu’il ne vous reste plus qu’à définir une seule valeur de chaîne de connexion dans l’environnement Azure.

2. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur votre compte de stockage sous le nœud **Stockage**, puis cliquez sur **Propriétés**.

	![Click Storage Account Properties](./media/websites-dotnet-webjobs-sdk-get-started/storppty.png)

3. Dans la fenêtre **Propriétés**, cliquez sur **Clés de compte de stockage**, puis cliquez sur le bouton de sélection.

	![New storage account](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)

4. Copiez la **chaîne de connexion**.

	![Storage Account Keys dialog](./media/websites-dotnet-webjobs-sdk-get-started/cpak.png)

5. Remplacez la chaîne de connexion de stockage dans le fichier *Web.config* par celle que vous venez de copier. Veillez à sélectionner tout ce qui se trouve entre les guillemets, mais sans inclure les guillemets, avant le collage.

6. Ouvrez le fichier *App.config* dans le projet ContosoAdsWebJob.

	Ce fichier comporte deux chaînes de connexion : une pour les données de l'application et une pour la journalisation. Vous pouvez utiliser des comptes de stockage distincts pour les données et la journalisation de l’application, ainsi qu’utiliser [plusieurs comptes de stockage pour les données](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs). Pour ce didacticiel, vous allez utiliser un seul compte de stockage. Les chaînes de connexion utilisent des espaces réservés pour les clés de compte de stockage. <pre class="prettyprint">&lt;configuration&gt; &lt;connectionStrings&gt; &lt;add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt; &lt;add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt; &lt;add name="ContosoAdsContext" connectionString="Data Source=(localdb)\\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;"/&gt; &lt;/connectionStrings&gt; &lt;startup&gt; &lt;supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" /&gt; &lt;/startup&gt; &lt;/configuration&gt;</pre>

	Par défaut, le Kit de développement logiciel (SDK) WebJobs recherche les chaînes de connexion AzureWebJobsStorage et AzureWebJobsDashboard. Vous pouvez également stocker la [chaîne de connexion comme vous le souhaitez et la transmettre explicitement à l’objet `JobHost`](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#config).

7. Remplacez les chaînes de connexion de stockage par la chaîne de connexion que vous avez copiée précédemment.

8. Enregistrez vos modifications.

## <a id="run"></a>Exécuter l’application localement

1. Pour démarrer le programme web frontal de l'application, appuyez sur Ctrl+F5.

	Le navigateur par défaut ouvre la page d'accueil. Le projet web s'exécute, car vous l'avez défini comme projet de démarrage.

	![Contoso Ads home page](./media/websites-dotnet-webjobs-sdk-get-started/home.png)

2. Pour démarrer la tâche web principale de l'application, cliquez avec le bouton droit sur le projet ContosoAdsWebJob dans l'**Explorateur de solutions**, puis sur **Débogage** > **Démarrer une nouvelle instance**.

	Une fenêtre d'application console s'ouvre et affiche des messages de journalisation indiquant que l'objet JobHost du Kit de développement logiciel (SDK) WebJobs a commencé à s'exécuter.

	![Console application window showing that the backend is running](./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png)

3. Dans votre navigateur, cliquez sur **Créer une publicité**.

4. Entrez des données de test et sélectionnez une image à télécharger, puis cliquez sur **Créer**.

	![Create page](./media/websites-dotnet-webjobs-sdk-get-started/create.png)

	L'application ouvre la page Index, mais n'affiche pas de vignette pour la nouvelle publicité, car le processus n'a pas encore eu lieu.

	Entre-temps, après une brève attente, un message dans la fenêtre d'application console indique qu'un message en file d'attente a été reçu et traité.

	![Console application window showing that a queue message has been processed](./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png)

5. Lorsque ces messages s'affichent dans la fenêtre d'application console, actualisez la page Index pour afficher la vignette.

	![Page d'index](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

6. Cliquez sur l'option **Détails** de votre publicité pour afficher l'image intégrale.

	![Details page](./media/websites-dotnet-webjobs-sdk-get-started/details.png)

Vous avez exécuté l'application sur votre ordinateur local. Elle utilise une base de données SQL Server sur votre ordinateur, mais travaille sur des files d'attente et des objets blob dans le cloud. Dans la section suivante, vous allez exécuter l'application dans le cloud en utilisant une base de données du cloud ainsi que des objets blob et des files d'attente du cloud.

## <a id="runincloud"></a>Exécuter l’application dans le cloud

Pour exécuter l'application dans le cloud, procédez comme suit :

* Procédez au déploiement dans Web Apps. Visual Studio crée automatiquement une application web dans App Service et une instance Base de données SQL.
* Configurez l’application pour l’utilisation de votre base de données SQL et de votre compte de stockage Azure.

Après avoir créé quelques publicités dans le cloud, vous afficherez le tableau de bord du Kit de développement logiciel (SDK) WebJobs pour voir les fonctions de surveillance enrichies qu'il offre.

### Déployer dans Web Apps

1. Fermez le navigateur et la fenêtre d'application console.

2. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ContosoAdsWeb, puis cliquez sur **Publier**.

3. À l’étape **Profil** de l’assistant **Publier le site Web**, cliquez sur **Applications web Microsoft Azure**.

	![Select Azure web app publish target](./media/websites-dotnet-webjobs-sdk-get-started/pubweb.png)

4. Connectez-vous à Azure si vous n’êtes pas déjà connecté.

5. Cliquez sur **Nouveau**.

	La boîte de dialogue peut différer légèrement selon la version du Kit de développement logiciel (SDK) Azure pour .NET que vous avez installée.

	![Click New](./media/websites-dotnet-webjobs-sdk-get-started/clicknew.png)

6. Dans la boîte de dialogue **Créer une application web dans Microsoft Azure**, entrez un nom unique dans la zone **Nom de l’application web**.

	L’URL complète se compose de ce que vous avez entré ici et de .azurewebsites.net (comme indiqué en regard de la zone de texte **Nom de l’application web**). Par exemple, si l’application web porte le nom ContosoAds, l’URL sera ContosoAds.azurewebsites.net.

7. Dans la liste déroulante [Plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), sélectionnez **Créer un plan App Service**. Entrez un nom pour le plan App Service, par exemple : ContosoAdsPlan.

8. Dans la liste déroulante [Groupe de ressources](../resource-group-overview.md), sélectionnez **Créer un groupe de ressources**.

9. Entrez un nom pour le groupe de ressources, par exemple : ContosoAdsGroup.

10. Dans la liste déroulante **Région**, sélectionnez la région que vous avez choisie pour votre compte de stockage.

	Ce paramètre indique le centre de données Azure dans lequel votre application Web sera exécutée. Conserver l’application web et le compte de stockage dans le même centre de données réduit la latence et les frais d’acheminement des données.

11. Dans la liste déroulante **Serveur de bases de données**, sélectionnez **Créer un serveur**.

12. Entrez un nom pour le serveur de base de données, par exemple contosoadsserver + un nombre ou votre nom pour que le nom de serveur soit unique.

	Le nom de serveur doit être unique. Il peut contenir des lettres minuscules, des chiffres et des traits d’union. Il ne peut pas se terminer par un trait d’union.

	Si votre abonnement a déjà un serveur, vous pouvez sélectionner ce serveur dans la liste déroulante.

12. Entrez un **Nom d'utilisateur de la base de données** et un **Mot de passe de la base de données**.

	Si vous avez sélectionné **New SQL Database server**, vous ne devez pas entrer un nom et un mot de passe existant ici, mais une nouvelle paire nom/mot de passe que vous allez choisir maintenant et utiliser ultérieurement lorsque vous accèderez à la base de données. Si vous avez sélectionné un serveur créé auparavant, vous devez entrer le mot de passe du compte d’utilisateur administratif déjà créé.

13. Cliquez sur **Create**.

	![Create web app on Microsoft Azure dialog](./media/websites-dotnet-webjobs-sdk-get-started/newdb.png)

	Visual Studio crée la solution, le projet web, l’application web dans Azure, ainsi que l’instance Azure SQL Database.

14. À l'étape **Connexion** de l'Assistant **Publier le site web**, cliquez sur **Suivant**.

	![Connection step](./media/websites-dotnet-webjobs-sdk-get-started/connstep.png)

15. À l'étape **Paramètres**, sélectionnez **Utiliser cette chaîne de connexion au moment de l'exécution**, puis cliquez sur **Suivant**.

	![Settings step](./media/websites-dotnet-webjobs-sdk-get-started/settingsstep.png)

	Vous n'avez pas besoin d'utiliser la boîte de dialogue de connexion pour définir la chaîne de connexion SQL, car vous allez la définir plus tard dans l'environnement Azure.

	Vous pouvez ignorer les avertissements dans cette page.

	* Normalement, le compte de stockage que vous utilisez lors de l'exécution dans Azure est différent de celui que vous utilisez localement. Néanmoins, pour ce didacticiel, vous utilisez le même dans les deux environnements. Il n'est donc pas nécessaire de transformer la chaîne de connexion AzureWebJobsStorage. Même si vous voulez vraiment utiliser un autre compte de stockage dans le cloud, vous n'avez pas besoin de transformer la chaîne de connexion du fait que l'application utilise un paramètre de l'environnement Azure lorsqu'elle s'exécute dans Azure. Nous aborderons cela plus tard dans ce didacticiel.

	* Dans ce didacticiel, vous n'allez pas modifier le modèle de données utilisé pour la base de données ContosoAdsContext ; il n'est donc pas nécessaire d'utiliser Migrations Entity Framework Code First pour le déploiement. Code First crée automatiquement une base de données la première fois que l'application essaie d'accéder aux données SQL.

	Dans ce didacticiel, les valeurs par défaut des options sous **Options de publication des fichiers** sont correctes.

16. À l'étape **Aperçu**, cliquez sur **Démarrer l'aperçu**.

	![Click Start Preview](./media/websites-dotnet-webjobs-sdk-get-started/previewstep.png)

	Vous pouvez ignorer l'avertissement indiquant qu'aucune base de données n'est en cours de publication. Entity Framework Code First crée la base de données ; il n'est pas nécessaire de la publier.

	La fenêtre d’aperçu indique que les fichiers binaires et de configuration du projet WebJob sont copiés dans le dossier *app\_data\\jobs\\continuous* de l’application web.

	![WebJobs files in preview window](./media/websites-dotnet-webjobs-sdk-get-started/previewwjfiles.png)

17. Cliquez sur **Publier**.

	Visual Studio déploie l'application et ouvre l'URL de la page d'accueil dans le navigateur.

	Vous ne pouvez pas utiliser l’application web tant que vous n’avez pas défini les chaînes de connexion dans l’environnement Azure à la section suivante. Une page d’erreur ou la page d’accueil s’affiche en fonction des options de création de l’application web et de la base de données que vous avez sélectionnées auparavant.

### Configurez l’application pour l’utilisation de votre base de données SQL et de votre compte de stockage Azure.

Par sécurité, il est conseillé [d'éviter de placer des informations sensibles (par exemple, des chaînes de connexion) dans des fichiers stockés dans des référentiels de code source](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets). Vous pouvez définir une chaîne de connexion et d’autres paramètres dans l’environnement Azure. Les API de configuration ASP.NET sélectionnent automatiquement ces valeurs quand l’application s’exécute dans Azure. Vous pouvez définir ces valeurs dans Azure à l’aide de l’**Explorateur de serveurs**, du portail Azure, de Windows PowerShell ou de l’interface de ligne de commande interplateforme. Pour plus d’informations, consultez [Fonctionnement des chaînes d’application et des chaînes de connexion](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

Dans cette section, vous utilisez l’**Explorateur de serveurs** pour définir des valeurs de chaînes de connexion dans Azure.

7. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur votre application web sous **Azure > {votre groupe de ressources}**, puis cliquez sur **Afficher les paramètres**.

	La fenêtre **Application web Azure** s’ouvre dans l’onglet **Configuration**.

9. Remplacez le nom de la chaîne de connexion DefaultConnection par ContosoAdsContext.

	Azure a automatiquement créé cette chaîne de connexion lorsque vous avez créé l’application web avec une base de données associée ; il présente donc déjà la valeur de chaîne de connexion adéquate. Vous remplacez simplement le nom par celui que votre code recherche.

9. Ajoutez deux chaînes de connexion nommées AzureWebJobsStorage et AzureWebJobsDashboard. Définissez le type sur Personnalisé et configurez la valeur de la chaîne de connexion avec celle que vous avez utilisée auparavant pour les fichiers *Web.config* et *App.config*. Vérifiez que vous incluez la chaîne de connexion complète, et pas uniquement la clé d'accès, sans guillemets.

	Le Kit de développement logiciel (SDK) WebJobs utilise ces chaînes de connexion : une pour les données de l'application et une pour la journalisation. Comme nous l'avons vu précédemment, le code du programme web frontal utilise aussi la chaîne pour les données de l'application.

9. Cliquez sur **Enregistrer**.

	![Chaînes de connexion dans le portail Azure](./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png)

10. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur l’application web, puis cliquez sur **Arrêter**.

12. Une fois l’application web arrêtée, cliquez de nouveau sur cette dernière avec le bouton droit, puis cliquez sur **Démarrer**.

	La tâche web démarre automatiquement lorsque vous publiez, mais elle s'arrête lorsque vous modifiez la configuration. Pour la redémarrer, vous pouvez soit redémarrer l’application web, soit redémarrer la tâche web dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Il est généralement recommandé de redémarrer l’application web après une modification de la configuration.

9. Actualisez la fenêtre du navigateur contenant l’URL de l’application web dans sa barre d’adresse.

	La page d'accueil s'affiche.

10. Créez une publicité comme vous l'avez fait lorsque vous avez exécuté l'application localement.

	La page Index s'affiche d'abord sans vignette.

11.	Actualisez la page après quelques secondes : la vignette s'affiche.

	Si la miniature n’apparaît pas, vous devrez patienter environ une minute pour que WebJob redémarre. Si après quelques instants vous ne voyez toujours pas la miniature lorsque vous actualisez la page, WebJob n’a peut-être pas démarré automatiquement. Dans ce cas, accédez à l’onglet WebJobs sur la page [portail Classic](https://manage.windowsazure.com) de votre application web, puis cliquez sur **Démarrer**.

### Affichage du tableau de bord du Kit de développement logiciel (SDK) WebJobs

1. Dans le [portail Classic](https://manage.windowsazure.com), sélectionnez votre application web.

2. Cliquez sur l'onglet **Tâches web**.

3. Cliquez sur l'URL dans la colonne Journaux de votre tâche web.

	![WebJobs tab](./media/websites-dotnet-webjobs-sdk-get-started/wjtab.png)

	Un nouvel onglet ouvre le tableau de bord du Kit de développement logiciel (SDK) WebJobs dans le navigateur. Le tableau de bord indique que la tâche web est en cours d'exécution et affiche la liste des fonctions de votre code que le SDK a déclenchées.

4. Cliquez sur une des fonctions pour afficher des informations sur son exécution.

	![WebJobs SDK dashboard](./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png)

	![WebJobs SDK dashboard](./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png)

	Le bouton **Rappeler la fonction** de cette page commande à l'infrastructure du SDK de rappeler la fonction et vous permet de modifier les données déjà transmises à la fonction.

>[AZURE.NOTE]Lorsque le test est terminé, supprimez l’application web et l’instance Base de données SQL. L’application web est gratuite, mais l’instance Base de données SQL et le compte de stockage engendrent des frais (minimaux du fait de la petite taille). De même, si vous laissez l’application web s’exécuter, toute personne trouvant votre URL peut créer et afficher des publicités. Dans le portail Classic, accédez à l’onglet **Tableau de bord** de votre application web, puis cliquez sur le bouton **Supprimer** au bas de la page. Vous pouvez en même temps cocher une case pour supprimer l'instance Base de données SQL. Si vous souhaitez empêcher temporairement l’accès à l’application web, cliquez plutôt sur **Arrêter**. Dans ce cas, les frais continuent à s'accumuler pour la base de données SQL et le compte de stockage. Vous pouvez suivre une procédure similaire pour supprimer la base de données SQL et le compte de stockage lorsque vous n'en avez plus besoin.

## <a id="create"></a>Créer l’application intégralement

Dans cette section, vous effectuerez les tâches suivantes :

* création d'une solution Visual Studio avec un projet web ;
* ajout d'un projet de bibliothèque de classes pour la couche d'accès aux données partagée par le programme frontal et le programme principal ;
* ajout d'un projet d'application console pour le programme principal, le déploiement de tâches web étant activé ;
* ajout de packages NuGet ;
* définition des références d'un projet ;
* copie des fichiers de code et de configuration de l'application téléchargée que vous avez utilisée dans la section précédente de ce didacticiel ;
* examen des parties du code qui fonctionnent avec les objets blob, les files d'attente et le Kit de développement logiciel (SDK) WebJobs Azure.

### Création d'une solution Visual Studio avec un projet web et un projet de bibliothèque de classes

1. Dans Visual Studio, sélectionnez **Nouveau**, **Projet** dans le menu **Fichier**.

2. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#** > **Web** > **Application Web ASP.NET**.

3. Nommez le projet ContosoAdsWeb, la solution ContosoAdsWebJobsSDK (modifiez le nom de la solution si vous la placez dans le même dossier que la solution téléchargée) et cliquez sur **OK**.

	![Nouveau projet](./media/websites-dotnet-webjobs-sdk-get-started/newproject.png)

5. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle MVC et désactivez la case à cocher **Héberger dans le cloud** sous **Microsoft Azure**.

	Si vous sélectionnez l’option **Héberger dans le cloud**, Visual Studio crée automatiquement une application web Azure et une base de données SQL. Comme vous les avez déjà créés, vous n'avez pas besoin de le faire lorsque vous créez le projet. Si vous voulez en créer un, activez cette case à cocher. Vous pouvez alors configurer la nouvelle application web et la base de données SQL comme vous l'avez fait précédemment quand vous avez déployé l'application.

5. Cliquez sur **Modifier l'authentification**.

	![Change Authentication](./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png)

7. Dans la boîte de dialogue **Modifier l'authentification**, choisissez **Aucune authentification** et cliquez sur **OK**.

	![Aucune authentification](./media/websites-dotnet-webjobs-sdk-get-started/noauth.png)

8. Dans la boîte de dialogue **Nouveau projet ASP.NET**, cliquez sur **OK**.

	Visual Studio crée la solution et le projet web.

9. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur la solution (et non sur le projet) et choisissez **Ajouter** > **Nouveau projet**.

11. Dans la boîte de dialogue **Ajouter un nouveau projet**, sélectionnez **Visual C#** > **Bureau Windows** > **Bibliothèque de classes**.

10. Nommez le projet *ContosoAdsCommon*, puis cliquez sur **OK**.

	Ce projet contient le contexte Entity Framework et le modèle de données que le programme frontal et le programme principal vont utiliser. Vous pouvez également définir les classes associées à Entity Framework dans le projet web et faire référence à ce projet de tâche web. Mais, dans ce cas, ce dernier aura une référence inutile aux assemblys web.

### Ajout d'un projet d'application console dont le déploiement de tâches web est activé

1. Cliquez avec le bouton droit sur le projet web (et non sur la solution ou le projet de bibliothèque de classes), puis cliquez sur **Ajouter** > **Nouveau projet de tâche web Azure**.

	![New Azure WebJob Project menu selection](./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png)

2. Dans la boîte de dialogue **Ajouter une tâche web Azure**, entrez ContosoAdsWebJob pour le **Nom du projet** et le **Nom de la tâche web**. Laissez **Mode d'exécution de la tâche web** sur **Exécuter en continu**.

3.  Cliquez sur **OK**.

	Visual Studio crée une application console configurée pour se déployer comme une tâche web lorsque vous déployez le projet web. Pour cela, il a effectué les tâches suivantes après la création du projet :

	* ajout d'un fichier *webjob-publish-settings.json* dans le dossier des propriétés du projet de tâche web ;
	* ajout d'un fichier *webjobs-list.json* dans le dossier des propriétés du projet web ;
	* installation du package NuGet Microsoft.Web.WebJobs.Publish dans le projet de tâche web.

	Pour plus d’informations sur ces modifications, consultez la rubrique [Déployer des tâches web à l’aide de Visual Studio](websites-dotnet-deploy-webjobs.md).

### Ajout de packages NuGet

Le nouveau modèle de projet pour un projet de tâche web installe automatiquement le package NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) du Kit de développement logiciel (SDK) WebJobs et ses dépendances.

L’une des dépendances du Kit de développement logiciel WebJobs installée automatiquement dans le projet WebJob est la bibliothèque cliente de stockage Azure (SCL, Storage Client Library). Toutefois, vous devez l’ajouter au projet web pour qu’elle fonctionne avec les objets blob et les files d’attente.

1. Ouvrez la boîte de dialogue **Gérer les packages NuGet** pour la solution.

2. Dans le volet de gauche, sélectionnez **Packages installés**.

3. Recherchez le package *Azure Storage* et cliquez sur **Gérer**.

4. Dans la boîte de dialogue **Sélectionner les projets**, activez la case à cocher **ContosoAdsWeb**, puis cliquez sur **OK**.

	Ces trois projets ont recours à Entity Framework pour utiliser les données de la base de données SQL.

5. Dans le volet gauche, sélectionnez **En ligne**.

6. Recherchez le package NuGet *EntityFramework* et installez-le dans les trois projets.


### Définition des références de projet

Les projets web et de tâches web utilisent la base de données SQL ; les deux nécessitent une référence au projet ContosoAdsCommon.

1. Dans le projet ContosoAdsWeb, définissez une référence au projet ContosoAdsCommon. Cliquez avec le bouton droit sur le projet ContosoAdsWeb, puis cliquez sur **Ajouter** > **Référence**. Dans la boîte de dialogue **Gestionnaire de références**, sélectionnez **Solution** > **Projets** > **ContosoAdsCommon** et cliquez sur **OK**.

1. Dans le projet ContosoAdsWebJob, définissez une référence au projet ContosoAdsCommon.

	Le projet web nécessite des références pour utiliser des images et accéder aux chaînes de connexion.

3. Dans le projet ContosoAdsWebJob, définissez une référence à `System.Drawing` et `System.Configuration`.

### Ajout de fichiers de code et de configuration

Ce didacticiel n'explique pas comment [créer des contrôleurs et des vues MVC à l'aide de la structure](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started), [comment écrire du code Entity Framework qui fonctionne avec les bases de données SQL Server](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc), [ni les bases de la programmation asynchrone dans ASP.NET 4.5](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async). Il ne reste donc qu'à copier les fichiers de code et de configuration de la solution téléchargée dans la nouvelle solution. Ensuite, les sections suivantes montrent et expliquent les éléments essentiels de ce code.

Pour ajouter des fichiers à un projet ou à un dossier, cliquez avec le bouton droit sur le projet ou le dossier, puis cliquez sur **Ajouter** > **Élément existant**. Sélectionnez les fichiers et cliquez sur **Ajouter**. Si un message vous demande si vous souhaitez remplacer les fichiers existants, cliquez sur **Oui**.

1. Dans le projet ContosoAdsCommon, supprimez le fichier *Class1.cs* et ajoutez à la place les fichiers suivants du projet téléchargé.

	- *Ad.cs*
	- *ContosoAdscontext.cs*
	- *BlobInformation.cs*<br/><br/>

2. Dans le projet ContosoAdsWeb, ajoutez les fichiers suivants du projet téléchargé.

	- *Web.config*
	- *Global.asax.cs*  
	- Dans le dossier *Controllers* : *AdController.cs*.
	- Dans le dossier *Views\\Shared* : fichier *\_Layout.cshtml*
- Dans le dossier *Views\\Home* : *Index.cshtml*.
	- Dans le dossier *Views\\Ad* (à créer) : cinq fichiers *.cshtml*.<br/><br/>

3. Dans le projet ContosoAdsWebJob, ajoutez les fichiers suivants du projet téléchargé.

	- *App.config* (indiquez le type de fichier **Tous les fichiers**)
	- *Program.cs*
	- *Functions.cs*

Vous pouvez maintenant générer, exécuter et déployer l'application en suivant les instructions fournies précédemment dans ce didacticiel. Toutefois, avant cela, arrêtez la tâche web toujours en cours d’exécution dans la première application web dans laquelle vous l’avez déployée. Dans le cas contraire, cette tâche web traitera les messages de file d’attente créés localement ou par l’application en cours d’exécution dans une nouvelle application web, car ils utilisent tous le même compte de stockage.

## <a id="code"></a>Vérifier le code de l’application

Les sections suivantes présentent le code utilisé avec le Kit de développement logiciel (SDK) WebJobs et des objets blob et des files d'attente Azure.

> [AZURE.NOTE]Pour le code spécifique au kit de développement logiciel (SDK) WebJobs, accédez aux sections [Program.cs et Functions.cs](#programcs).

### ContosoAdsCommon - Ad.cs

Le fichier Ad.cs définit une énumération des catégories de publicité et une classe d'entité POCO pour les informations de publicité.

		public enum Category
		{
		    Cars,
		    [Display(Name="Real Estate")]
		    RealEstate,
		    [Display(Name = "Free Stuff")]
		    FreeStuff
		}

		public class Ad
		{
		    public int AdId { get; set; }

		    [StringLength(100)]
		    public string Title { get; set; }

		    public int Price { get; set; }

		    [StringLength(1000)]
		    [DataType(DataType.MultilineText)]
		    public string Description { get; set; }

		    [StringLength(1000)]
		    [DisplayName("Full-size Image")]
		    public string ImageURL { get; set; }

		    [StringLength(1000)]
		    [DisplayName("Thumbnail")]
		    public string ThumbnailURL { get; set; }

		    [DataType(DataType.Date)]
		    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
		    public DateTime PostedDate { get; set; }

		    public Category? Category { get; set; }
		    [StringLength(12)]
		    public string Phone { get; set; }
		}

### ContosoAdsCommon - ContosoAdsContext.cs

La classe ContosoAdsContext spécifie que la classe Ad est utilisée dans une collection DbSet, qui est stockée par Entity Framework dans une base de données SQL.

		public class ContosoAdsContext : DbContext
		{
		    public ContosoAdsContext() : base("name=ContosoAdsContext")
		    {
		    }
		    public ContosoAdsContext(string connString)
		        : base(connString)
		    {
		    }
		    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
		}

La classe a deux constructeurs. Le premier est utilisé par le projet web et spécifie le nom d'une chaîne de connexion stockée dans le fichier Web.config de l'environnement d'exécution Azure. Le second vous permet de passer la chaîne de connexion existante. Le projet de rôle de travail, qui ne comporte pas de fichier Web.config. nécessite cette opération. Vous avez vu précédemment où est stockée cette chaîne de connexion, et vous allez voir comme le code la récupère quand il instancie la classe DbContext.

### ContosoAdsCommon - BlobInformation.cs

La classe `BlobInformation` permet de stocker les informations d’un objet blob d’image dans un message de file d’attente.

		public class BlobInformation
		{
		    public Uri BlobUri { get; set; }

		    public string BlobName
		    {
		        get
		        {
		            return BlobUri.Segments[BlobUri.Segments.Length - 1];
		        }
		    }
		    public string BlobNameWithoutExtension
		    {
		        get
		        {
		            return Path.GetFileNameWithoutExtension(BlobName);
		        }
		    }
		    public int AdId { get; set; }
		}


### ContosoAdsWeb - Global.asax.cs

Le code appelé par la méthode `Application_Start` crée un conteneur d’objets blob *images* et une file d’attente *images*, s’ils n’existent pas déjà. Cela garantit que, lorsque vous commencez à utiliser un nouveau compte de stockage, le conteneur d'objets blob et la file d'attente nécessaires sont créés automatiquement.

Le code a accès au compte de stockage en utilisant la chaîne de connexion du fichier *Web.config* ou l'environnement d'exécution Azure.

		var storageAccount = CloudStorageAccount.Parse
		    (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

Il obtient ensuite une référence au conteneur d'objets blob *images*, crée le conteneur s'il n'existe pas déjà et définit les autorisations d'accès au nouveau conteneur. Par défaut, les nouveaux conteneurs ne donnent accès aux objets blob qu’aux clients disposant d’informations d’identification de compte de stockage. L’application web a besoin que les objets blob soient publics pour afficher des images en utilisant des URL qui pointent vers les objets blob de ces images.

		var blobClient = storageAccount.CreateCloudBlobClient();
		var imagesBlobContainer = blobClient.GetContainerReference("images");
		if (imagesBlobContainer.CreateIfNotExists())
		{
		    imagesBlobContainer.SetPermissions(
		        new BlobContainerPermissions
		        {
		            PublicAccess = BlobContainerPublicAccessType.Blob
		        });
		}

Du code similaire obtient une référence à la file d'attente *blobnamerequest* et crée une nouvelle file d'attente. Dans ce cas, aucune modification des autorisations n'est nécessaire. Plus loin dans ce didacticiel, la section [ResolveBlobName](#resolveblobname) explique pourquoi la file d'attente dans laquelle l'application web écrit est utilisée uniquement pour obtenir les noms des objets blob et non pour créer des vignettes.

		CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
		var imagesQueue = queueClient.GetQueueReference("blobnamerequest");
		imagesQueue.CreateIfNotExists();

### ContosoAdsWeb - \_Layout.cshtml

Le fichier *\_Layout.cshtml* définit le nom d'application dans l'en-tête et le pied de page, et crée une entrée de menu « Ads ».

### ContosoAdsWeb - Views\\Home\\Index.cshtml

Le fichier *Views\\Home\\Index.cshtml* affiche les liens de catégorie sur la page d'accueil. Les liens transmettent la valeur entière de l’énumération `Category` d’une variable querystring à la page Ads Index.

		<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
		<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
		<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
		<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### ContosoAdsWeb - AdController.cs

Dans le fichier *AdController.cs*, le constructeur appelle la méthode `InitializeStorage` pour créer les objets de la bibliothèque cliente Azure Storage, qui fournissent une API pour les objets blob et les files d’attente.

Le code obtient ensuite une référence au conteneur d'objets blob *images* comme vu précédemment dans *Global.asax.cs*. Ce faisant, il définit une [stratégie de nouvelles tentatives](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) par défaut appropriée pour une application web. La stratégie de nouvelles tentatives d'interruption exponentielle par défaut peut suspendre l'application web pendant plus d'une minute en cas de tentatives répétées pour une erreur temporaire. La stratégie de nouvelle tentative spécifiée ici laisse 3 secondes après chaque nouvelle tentative, jusqu'à 3.

		var blobClient = storageAccount.CreateCloudBlobClient();
		blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
		imagesBlobContainer = blobClient.GetContainerReference("images");

Un code similaire obtient une référence à la file d'attente *images*.

		CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
		queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
		imagesQueue = queueClient.GetQueueReference("blobnamerequest");

La plupart du code du contrôleur permet généralement d'utiliser un modèle de données Entity Framework en utilisant une classe DbContext. La méthode HttpPost `Create` est une exception, car elle télécharge un fichier et l’enregistre dans le stockage d’objets blob. Le classeur de modèles fournit un objet [HttpPostedFileBase](http://msdn.microsoft.com/library/system.web.httppostedfilebase.aspx) à la méthode.

		[HttpPost]
		[ValidateAntiForgeryToken]
		public async Task<ActionResult> Create(
		    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
		    HttpPostedFileBase imageFile)

Si l'utilisateur a sélectionné un fichier à télécharger, le code met à jour le fichier, l'enregistre dans un objet blob et met à jour l'enregistrement de base de données AD avec une URL qui pointe vers l'objet blob.

		if (imageFile != null && imageFile.ContentLength != 0)
		{
		    blob = await UploadAndSaveBlobAsync(imageFile);
		    ad.ImageURL = blob.Uri.ToString();
		}

Le code qui procède au téléchargement se trouve dans la méthode `UploadAndSaveBlobAsync`. Il crée un nom GUID pour l'objet blob, télécharge et enregistre le fichier, et renvoie une référence vers l'objet blob enregistré.

		private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
		{
		    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
		    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
		    using (var fileStream = imageFile.InputStream)
		    {
		        await imageBlob.UploadFromStreamAsync(fileStream);
		    }
		    return imageBlob;
		}

Une fois que la méthode HttpPost `Create` a téléchargé un objet blob et mis à jour la base de données, elle crée un message de file d’attente pour informer ce processus d’arrière-plan qu’une image est prête à être convertie en vignette.

		BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		await thumbnailRequestQueue.AddMessageAsync(queueMessage);

Le code de la méthode HttpPost `Edit` est similaire, mais si l’utilisateur sélectionne un nouveau fichier image, les objets blob qui existent pour cette publicité doivent être supprimés.

		if (imageFile != null && imageFile.ContentLength != 0)
		{
		    await DeleteAdBlobsAsync(ad);
		    imageBlob = await UploadAndSaveBlobAsync(imageFile);
		    ad.ImageURL = imageBlob.Uri.ToString();
		}

Voici le code qui supprime les objets blob lorsque vous supprimez une publicité :

		private async Task DeleteAdBlobsAsync(Ad ad)
		{
		    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
		    {
		        Uri blobUri = new Uri(ad.ImageURL);
		        await DeleteAdBlobAsync(blobUri);
		    }
		    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
		    {
		        Uri blobUri = new Uri(ad.ThumbnailURL);
		        await DeleteAdBlobAsync(blobUri);
		    }
		}
		private static async Task DeleteAdBlobAsync(Uri blobUri)
		{
		    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
		    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
		    await blobToDelete.DeleteAsync();
		}

### ContosoAdsWeb - Views\\Ad\\Index.cshtml et Details.cshtml

Le fichier *Index.cshtml* affiche des vignettes avec les autres données de publicité :

		<img  src="@Html.Raw(item.ThumbnailURL)" />

Le fichier *Details.cshtml* affiche l'image intégrale :

		<img src="@Html.Raw(Model.ImageURL)" />

### ContosoAdsWeb - Views\\Ad\\Create.cshtml et Edit.cshtml

Les fichiers *Create.cshtml* et *Edit.cshtml* spécifient l’encodage de formulaire qui permet au contrôleur d’obtenir l’objet `HttpPostedFileBase`.

		@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

Un élément `<input>` indique au navigateur de fournir une boîte de dialogue de sélection de fichier.

		<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <a id="programcs"></a>ContosoAdsWebJob - Program.cs

Lorsque la tâche web démarre, la méthode `Main` appelle la méthode `JobHost.RunAndBlock` du Kit de développement logiciel (SDK) WebJobs pour commencer l’exécution des fonctions déclenchées sur le thread actuel.

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

### <a id="generatethumbnail"></a>ContosoAdsWebJob - Functions.cs - Méthode GenerateThumbnail

Le Kit de développement logiciel (SDK) WebJobs appelle cette méthode lorsqu'un message de file d'attente est reçu. Cette méthode crée une vignette et place son URL dans la base de données.

		public static void GenerateThumbnail(
		[QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
		[Blob("images/{BlobName}", FileAccess.Read)] Stream input,
		[Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)
		{
		    using (Stream output = outputBlob.OpenWrite())
		    {
		        ConvertImageToThumbnailJPG(input, output);
		        outputBlob.Properties.ContentType = "image/jpeg";
		    }

		    // Entity Framework context class is not thread-safe, so it must
		    // be instantiated and disposed within the function.
		    using (ContosoAdsContext db = new ContosoAdsContext())
		    {
		        var id = blobInfo.AdId;
		        Ad ad = db.Ads.Find(id);
		        if (ad == null)
		        {
		            throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
		        }
		        ad.ThumbnailURL = outputBlob.Uri.ToString();
		        db.SaveChanges();
		    }
		}

* L’attribut `QueueTrigger` demande au Kit de développement logiciel (SDK) WebJobs d’appeler cette méthode lorsqu’un nouveau message est reçu dans la file d’attente thumbnailrequest.

		[QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

	L’objet `BlobInformation` dans le message de file d’attente est automatiquement désérialisé dans le paramètre `blobInfo`. Lorsque la méthode est terminée, le message de file d'attente est supprimé. Si la méthode échoue avant de se terminer, le message de file d'attente n'est pas supprimé ; après un bail de 10 minutes, il est libéré pour être à nouveau sélectionné et traité. Cette séquence ne se répète pas indéfiniment si un message provoque toujours une exception. Après 5 tentatives ayant échoué de traitement d'un message, celui-ci est déplacé dans la file d'attente nommée {queuename}-poison. Vous pouvez configurer le nombre maximal de tentatives.

* Les deux attributs `Blob` fournissent des objets qui sont liés aux objets blob : un pour l’objet blob d’image existant et un pour le nouvel objet blob miniature créé par la méthode.

		[Blob("images/{BlobName}", FileAccess.Read)] Stream input,
		[Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

	Les noms des objets blob proviennent des propriétés de l’objet `BlobInformation` reçu dans le message de file d’attente ((`BlobName` et `BlobNameWithoutExtension`). Pour profiter de toutes les fonctionnalités de la bibliothèque cliente de stockage, vous pouvez utiliser des objets blob à l’aide de la classe `CloudBlockBlob`. Si vous voulez réutiliser du code écrit pour des objets `Stream`, optez pour la classe `Stream`.

Pour plus d’informations sur l’écriture de fonctions utilisant des attributs du Kit de développement logiciel (SDK) WebJobs, voir les ressources suivantes :

* [Utilisation du stockage de file d’attente Microsoft Azure avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Utilisation du stockage d’objets blob Azure avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Utilisation du stockage de tables Microsoft Azure avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Utilisation de Microsoft Azure Service Bus avec le Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-service-bus.md)

> [AZURE.NOTE]
>
> * Si votre application web s'exécute sur plusieurs machines virtuelles, plusieurs WebJobs seront exécutés simultanément et, dans certains cas, cela peut entraîner le traitement multiple des mêmes données. Cela n'est pas un problème si vous utilisez la file d'attente, le blob et les déclencheurs Service Bus intégrés. Le Kit de développement logiciel (SDK) garantit que vos fonctions ne seront traitées qu'une seule fois pour chaque message ou objet blob.
>
> * Pour plus d’informations sur la façon d’implémenter l’arrêt approprié, consultez la rubrique [Arrêt approprié](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).
>
> * Le code de la méthode `ConvertImageToThumbnailJPG` (non représenté) utilise des classes dans l’espace de noms `System.Drawing` pour plus de simplicité. Cependant, les classes de cet espace de noms ont été conçues pour être utilisées avec Windows Forms. Elles ne sont pas prises en charge dans un service Windows ou ASP.NET. Pour plus d’informations sur les options de traitement d’images, consultez les rubriques [Génération d’images dynamiques](http://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) et [Redimensionnement d’images approfondi ](http://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na).

## Étapes suivantes

Dans ce didacticiel, nous avons vu une simple application multiniveau qui utilise le Kit de développement logiciel (SDK) WebJobs pour le traitement principal. Cette section propose des suggestions pour en savoir plus sur les applications à plusieurs niveaux ASP.NET et WebJobs.

### Fonctionnalités manquantes

L'application est intentionnellement simple pour un didacticiel de prise en main. Dans une application réelle, vous implémenteriez l’[injection de dépendances](http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) et les [modèles de référentiel et d’élément de travail](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), et vous utiliseriez [une interface pour la connexion](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), les [migrations Code First EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) pour gérer les changements de modèles de données et la [résilience des connexions EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) pour gérer les erreurs réseau temporaires.

### Mise à l’échelle de WebJobs

Les tâches web s’exécutent dans le contexte d’une application web et ne sont pas évolutives séparément. Par exemple, si vous disposez d’une instance d’application web Standard, vous n’avez qu’une seule instance en cours d’exécution du processus en arrière-plan. Cette dernière utilise certaines ressources du serveur (unité centrale, mémoire, etc.) qui seraient autrement disponibles pour distribuer du contenu web.

Si le trafic varie au cours de la journée ou de la semaine et si le traitement principal dont vous avez besoin peut attendre, vous pouvez planifier l'exécution de vos tâches web aux heures de faible trafic. Si la charge est encore trop élevée pour cette solution, vous pouvez exécuter le serveur principal comme un WebJob dans une application web distincte dédiée à cet effet. Vous pouvez ensuite faire évoluer votre application web principale indépendamment de votre application web frontale.

Pour plus d’informations, consultez [Mise à l’échelle WebJobs](websites-webjobs-resources.md#scale).

### Éviter les arrêts dus à l’expiration des applications web

Pour vous assurer que vos WebJobs sont toujours en cours d’exécution sur toutes les instances de votre application web, vous devez activer la fonctionnalité [AlwaysOn](http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx).

### Utilisation du Kit de développement logiciel (SDK) WebJobs en dehors de WebJobs

Un programme qui utilise le Kit SDK WebJobs ne doit pas obligatoirement s’exécuter dans Azure dans une tâche web. Il peut s'exécuter localement, ou dans d'autres environnements tels qu'un rôle de travail de service cloud ou un service Windows. Toutefois, vous ne pouvez accéder au tableau de bord du Kit de développement logiciel (SDK) WebJobs que par le biais d’une application web Azure. Pour utiliser le tableau de bord, vous devez connecter l’application web au compte de stockage que vous utilisez en définissant la chaîne de connexion AzureWebJobsDashboard sous l’onglet **Configurer** du portail Classic. Vous pouvez ensuite accéder au tableau de bord à l’aide de l’URL suivante :

https://{webappname}.scm.azurewebsites.net/azurejobs/#/functions

Pour plus d'informations, consultez le billet de blog [Récupération d'un tableau de bord pour un développement local avec le Kit de développement logiciel (SDK) WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx) (notez cependant qu'il affiche un ancien nom de chaîne de connexion).

### Plus de documentation relative à WebJobs

Pour plus d’informations, consultez [Ressources de documentation relatives à Azure WebJobs](http://go.microsoft.com/fwlink/?LinkId=390226).

<!---HONumber=AcomDC_1217_2015-->