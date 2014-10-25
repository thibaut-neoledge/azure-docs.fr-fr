<properties pageTitle="Get Started with the Azure WebJobs SDK" metaKeywords="Azure tutorial, Azure WebJobs tutorial, Azure multi-tier tutorial, MVC tutorial, Azure blobs tutorial, Azure queues tutorial, Azure storage tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The frontend runs in a website, and the backend runs as a WebJob. The app uses Entity Framework, SQL Database, and Azure storage queues and blobs." metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="Get Started with the Azure WebJobs SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Prise en main du Kit de développement logiciel (SDK) WebJobs Azure

Ce didacticiel présente le Kit de développement logiciel (SDK) WebJobs et vous explique comment créer une application multiniveau ASP.NET MVC qui l'utilise dans un [site web Azure][site web Azure]. L'application utilise la [Base de données SQL Azure][Base de données SQL Azure], le [service Blob Azure][service Blob Azure] et le [service de File d'attente Azure][service de File d'attente Azure].

L'exemple d'application concerne un panneau d'affichage publicitaire. Les utilisateurs créent une publicité en entrant du texte et en téléchargeant une image. Ils peuvent voir une liste de publicités avec des images en vignette qu'ils peuvent agrandir en sélectionnant la publicité de leur choix. Voici une capture d'écran :

![Ad list][Ad list]

Vous pouvez [télécharger le projet Visual Studio][télécharger le projet Visual Studio] dans la galerie de code MSDN.

## Sommaire

Ce didacticiel comprend les sections suivantes :

-   [Configuration requise][Configuration requise]
-   [Contenu][Contenu]
-   [Kit de développement logiciel (SDK) WebJobs][Kit de développement logiciel (SDK) WebJobs]
-   [Architecture de l'application][Architecture de l'application]
-   [Configuration de l’environnement de développement][Configuration de l’environnement de développement]
-   [Génération, exécution et déploiement de l'application][Génération, exécution et déploiement de l'application]
-   [Création intégrale de l'application][Création intégrale de l'application]
-   [Révision du code de l'application][Révision du code de l'application]
-   [Résolution des problèmes][Résolution des problèmes]
-   [Étapes suivantes][Étapes suivantes]

## <span id="prerequisites"></span></a>Conditions préalables

Ce didacticiel suppose que vous savez utiliser les projets [ASP.NET MVC][ASP.NET MVC] ou [Web Forms][Web Forms] dans Visual Studio. L'exemple d'application utilise MVC, mais une grande part du didacticiel concerne également Web Forms.

Les instructions du didacticiel sont valables pour les produits suivants :

-   Visual Studio 2013
-   Visual Studio 2013 Express pour le Web

Si aucun de ces produits n'est installé, Visual Studio 2013 Express pour le web est automatiquement installé en même temps que le Kit de développement logiciel (SDK).

[WACOM.INCLUDE [free-trial-note][free-trial-note]]

## <span id="learn"></span></a>Contenu

Ce didacticiel explique comment effectuer les tâches suivantes :

-   configuration de votre ordinateur pour le développement Azure en installant le Kit de développement logiciel (SDK) Azure ;
-   création du projet d'application console qui se déploie automatiquement comme une tâche web Azure lorsque vous déployez le projet web associé ;
-   test d'un serveur principal de Kit de développement logiciel (SDK) WebJobs localement sur l'ordinateur de développement ;
-   publication d'une application avec un serveur principal des tâches web dans un site web Azure ;
-   téléchargement et enregistrement de fichiers dans le service Blob Azure ;
-   utilisation du Kit de développement logiciel (SDK) Azure WebJobs avec des files d'attente et des objets blob Azure Storage.

## <span id="webjobssdk"></span></a>Présentation du Kit de développement logiciel de (SDK) WebJobs

Les [tâches web][tâches web] sont une fonctionnalité des sites web Azure qui permet d'exécuter un programme ou un script dans le même contexte que votre site web. Vous ne devez pas utiliser le Kit de développement logiciel (SDK) WebJobs pour exécuter un programme comme une tâche web. La fonction du Kit de développement logiciel (SDK) WebJobs est de simplifier l'écriture de code qui fonctionne avec des files d'attente, des objets blob et des tables Azure Storage et des files d'attente Service Bus.

Ce Kit comprend les composants suivants :

-   **Packages NuGet**. Les packages NuGet que vous ajoutez à un projet Visual Studio fournissent une infrastructure que votre code utilise pour fonctionner avec le service Azure Storage ou des files d'attente Service Bus.
-   **Tableau de bord**. Cette partie du Kit de développement logiciel (SDK) WebJobs est incluse dans les sites web Azure ; elle offre des fonctions évoluées de surveillance et de diagnostic pour les programmes que vous écrivez avec les packages NuGet. Vous n'avez pas à écrire du code pour utiliser ces fonctions.

Vous pouvez exécuter un programme qui utilise n'importe où les packages NuGet du Kit de développement logiciel (SDK) WebJobs ; il n'est pas indispensable de l'exécuter comme une tâche web Azure. Cependant, le tableau de bord est disponible uniquement comme extension d'un site web Azure. Par exemple, vous pouvez exécuter un programme qui utilise localement le Kit de développement logiciel (SDK) WebJobs sur l'ordinateur de développement ou dans un rôle de travail de service cloud Azure. Vous pouvez alors recueillir des informations de surveillance en configurant le tableau de bord du Kit de développement logiciel (SDK) WebJobs d'un site web Azure de façon à utiliser le même compte de stockage que votre programme SDK.

À la date de rédaction de ce didacticiel, le Kit de développement logiciel (SDK) WebJobs est dans sa version bêta. Il ne prend pas en charge l'utilisation en production.

### Scénarios typiques

Voici quelques scénarios typiques plus faciles à gérer avec le Kit de développement logiciel (SDK) Azure WebJobs :

-   Traitement d'images ou autres tâches imposant une forte charge au processeur. Une caractéristique courante des sites web est le téléchargement d'images ou de vidéos. Vous voulez souvent manipuler le contenu après le téléchargement, mais vous ne voulez pas faire attendre l'utilisateur pendant ce temps.
-   Traitement de files d'attente. Un serveur web frontal utilise souvent des files d'attente pour communiquer avec un service principal. Lorsque le site web doit effectuer un travail, il envoie un message à une file d'attente. Un service principal extrait les messages de la file d'attente et effectue le travail. Par exemple, vous pouvez utiliser des files d'attente pour le traitement d'images : lorsque l'utilisateur a téléchargé divers fichiers, placez leurs noms dans un message de file d'attente pour que le serveur principal les traite. Vous pouvez également utiliser des files d'attente pour améliorer la réactivité d'un site. Par exemple, au lieu d'écrire directement dans une base de données SQL, écrivez dans une file d'attente, indiquez à l'utilisateur que vous avez terminé et laissez le serveur principal gérer le travail sur les bases de données relationnelles ayant une latence élevée.
-   Agrégation RSS. Si vous avez un site qui maintient une liste de flux RSS, vous pouvez récupérer tous les articles des flux dans un traitement en arrière-plan.
-   Maintenance de fichiers (par exemple, agrégation ou nettoyage de fichiers journaux). Vous voudrez peut-être que des fichiers journaux soient créés par plusieurs sites ou pour des périodes données que vous voulez associer pour les soumettre à des tâches d'analyse. Vous voudrez peut-être aussi planifier l'exécution hebdomadaire d'une tâche de nettoyage des fichiers journaux.
-   Autres tâches longues que vous voulez exécuter dans un thread en arrière-plan (par exemple, envoi de courriers électroniques).

Pour éviter que votre site web se mette en veille après une période d'inactivité, ce qui interromprait une longue tâche s'exécutant en arrière-plan, vous pouvez utiliser la fonction [AlwaysOn][AlwaysOn] des sites web Azure.

### Exemples de code

Le code de traitement des tâches typiques fonctionnant avec Azure Storage est simple. Dans une application console, vous écrivez des méthodes pour les tâches en arrière-plan que vous voulez exécuter et vous les décorez avec des attributs du Kit de développement logiciel (SDK) WebJobs. Votre méthode `Main` crée un objet `JobHost` qui coordonne les appels aux méthodes que vous écrivez. L'infrastructure du SDK WebJobs sait quand appeler vos méthodes d'après les attributs associés que vous utilisez dans ces méthodes. Par exemple :

        static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

L'objet `JobHost` est un conteneur d'un ensemble de fonctions en arrière-plan. L'objet `JobHost` surveille ces fonctions, recherche les événements qui les déclenchent et exécute les fonctions lorsque des événements de déclenchement se produisent. Vous appelez une méthode `JobHost` pour indiquer si vous voulez que le processus du conteneur s'exécute sur le thread actif ou dans un thread en arrière-plan. Dans l'exemple, la méthode `RunAndBlock` exécute le processus en permanence sur le thread actif.

Dans cet exemple, du fait que la méthode `ProcessQueueMessage` comporte l'attribut `QueueTrigger`, le déclencheur de cette fonction est la réception d'un nouveau message en file d'attente. L'objet `JobHost` recherche les nouveaux messages dans la file d'attente spécifiée (« webjobsqueue » dans cet exemple) ; lorsqu'un message est trouvé, il appelle `ProcessQueueMessage`. L'attribut `QueueTrigger` indique également à l'infrastructure de lier le paramètre `inputText` à la valeur du message en file d'attente :

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
    [Blob(&quot;containername/blobname&quot;)]TextWriter writer)
</pre>

L'infrastructure lie également l'objet `TextWriter` à un objet blob nommé « blobname » dans un conteneur nommé « containername » :

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
    [Blob(&quot;containername/blobname&quot;)]TextWriter writer)
</pre>

La fonction utilise ensuite ces paramètres pour écrire la valeur du message en file d'attente dans l'objet blob :

        writer.WriteLine(inputText);

Comme vous le constatez, les caractéristiques du déclencheur et du classeur du Kit de développement logiciel (SDK) WebJobs simplifient considérablement le code que vous devez écrire avec des objets Azure Storage. L'infrastructure du SDK écrit pour vous le code de bas niveau nécessaire à la gestion des files d'attente et au traitement des objets blob. Elle crée des files d'attente qui n'existent pas encore, ouvre une file d'attente, lit les messages en attente, supprime les messages en attente une fois qu'ils ont été traités, crée des conteneurs d'objets blob qui n'existent pas encore, écrit dans les objets blob, etc.

Le SDK WebJobs offre différentes manières d'utiliser Azure Storage. Par exemple, si le paramètre que vous décorez avec l'attribut `QueueTrigger` est un tableau d'octets ou un type personnalisé, il est automatiquement désérialisé à partir de JSON. De plus, vous pouvez utiliser l'attribut `BlobTrigger` pour déclencher un processus lorsqu'un nouvel objet blob est créé dans votre compte Azure Storage. Remarque : alors que `QueueTrigger` trouve les nouveaux messages en file d'attente en quelques secondes, `BlobTrigger` peut prendre jusqu'à 20 minutes pour détecter un nouvel objet blob. `BlobTrigger` recherche des objets blob au démarrage de `JobHost` et examine ensuite les journaux Azure Storage pour détecter de nouveaux objets blob.

## <span id="contosoads"></span></a>Architecture de l'application

L'exemple d'application utilise le [modèle de travail centré sur les files d'attente][service de File d'attente Azure] pour décharger le travail de création de vignettes exigeant en ressources vers un processus principal.

L'application stocke les publicités dans une base de données SQL et utilise Entity Framework Code First pour créer les tables et accéder aux données. Pour chaque publicité, la base de données stocke deux URL, une pour l'image à taille réelle et une pour la vignette.

![Ad table][Ad table]

Lorsqu'un utilisateur télécharge une image, le site web frontal la stocke dans un [objet blob Azure][service Blob Azure] et stocke les informations de la publicité dans la base de données avec une URL qui pointe vers l'objet blob. En même temps, il écrit un message dans une file d'attente Azure. Un processus principal s'exécutant en tant que tâche web Azure utilise le SDK WebJobs pour interroger la file d'attente sur la présence de nouveaux messages. Lorsqu'un nouveau message arrive, la tâche web crée une vignette pour cette image et met à jour le champ de la base de données des URL des vignettes pour cette publicité. Le schéma suivant montre l'interaction des parties de l'application :

![Contoso Ads architecture][Contoso Ads architecture]

### Autre architecture

Les tâches web s'exécutent dans le contexte d'un site web et ne sont pas évolutives séparément. Par exemple, si vous avez une instance de site web Standard, vous ne pouvez avoir qu'une seule instance en cours d'exécution du processus en arrière-plan. Elle utilise des ressources du serveur (processeur, mémoire, etc.) qui seraient autrement disponibles pour distribuer du contenu web.

Si le trafic varie au cours de la journée ou de la semaine et si le traitement principal dont vous avez besoin peut attendre, vous pouvez planifier l'exécution de vos tâches web aux heures de faible trafic. Si la charge est alors encore trop importante, vous pouvez envisager d'autres environnements pour votre programme principal. Exemples :

-   Exécution du programme en tant que tâche web dans un site web séparé dédié à cette tâche. Vous pouvez alors mettre à l'échelle votre site web principal indépendamment à partir de votre site web frontal.
-   Exécution du programme dans un rôle de travail Azure Cloud Services. Si vous choisissez cette option, vous pouvez exécuter le programme frontal dans un rôle de travail de service cloud ou dans un site web.

Ce didacticiel explique comment exécuter le programme frontal dans un site web et le programme principal en tant que tâche web dans le même site web. Pour plus d'informations sur le choix du meilleur environnement pour votre scénario, consultez la rubrique [Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines][Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines].

[WACOM.INCLUDE [install-sdk-2013-only][install-sdk-2013-only]]

## <span id="storage"></span></a>Création d'un compte de stockage Azure

Un compte de stockage Azure fournit des ressources pour stocker les données de file d'attente et d'objet blob dans le cloud. Le Kit de développement logiciel (SDK) WebJobs l'utilise également pour enregistrer les données de journalisation du tableau de bord.

Dans une application réelle, on crée généralement des comptes distincts pour les données d'application et de journalisation, et des comptes distincts pour les données de test et de production. Pour ce didacticiel, vous allez utiliser un seul compte.

1.  Dans le [portail de gestion Azure][portail de gestion Azure], cliquez sur **Nouveau** - **Data Services** - **Stockage** - **Création rapide**.

2.  Dans la zone **URL**, entrez un préfixe d'URL.

    Ce préfixe, associé au texte visible sous la zone, sera l'URL unique de votre compte de stockage. Si le préfixe que vous entrez est déjà utilisé, vous devez en choisir un autre.

    Dans l'image à la fin de cette section, un compte de stockage est créé avec l'URL `contosoads.core.windows.net`.

3.  Dans la liste déroulante **Région**, sélectionnez la région la plus proche de vous.

    Ce paramètre spécifie le centre de données Azure qui hébergera votre compte de stockage. Pour ce didacticiel, votre choix ne fait pas de différence importante mais, pour un site de production, vous voulez que votre serveur web et votre compte de stockage se trouvent dans la même région pour minimiser les temps de latence et les charges d'acheminement des données. Le site web (que vous créez ensuite) doit être aussi proche que possible des navigateurs qui y accèdent afin de minimiser les temps de latence.

4.  Dans la liste déroulante **Réplication**, sélectionnez **Redondant en local**.

    Lorsque la géo-réplication est activée pour un compte de stockage, le contenu stocké est répliqué dans un centre de données secondaire pour activer le basculement vers cet emplacement en cas de sinistre majeur à l'emplacement principal. La géo-réplication peut engendrer des coûts supplémentaires. Dans le cas des comptes test et de développement, vous êtes en général peu enclin à payer pour la géo-réplication. Pour plus d'informations, consultez la page [Gestion des comptes de stockage][Gestion des comptes de stockage].

5.  Cliquez sur **Create Storage Account**.

    ![New storage account][New storage account]

## <span id="download"></span></a>Téléchargement de l'application

1.  Téléchargez et décompressez la [solution terminée][télécharger le projet Visual Studio].

2.  Démarrez Visual Studio.

3.  Dans le menu **Fichier**, sélectionnez **Ouvrir** \> **Projet/Solution**, accédez à l'emplacement où vous avez téléchargé la solution, puis ouvrez le fichier solution.

4.  Appuyez sur Ctrl+Maj+B pour générer la solution.

    Par défaut, Visual Studio restaure automatiquement le contenu du package NuGet, qui n'était pas inclus dans le fichier *.zip*. Si les packages ne sont pas restaurés, installez-les manuellement en ouvrant la boîte de dialogue **Gérer les packages NuGet pour la solution** et en cliquant sur le bouton **Restaurer** en haut à droite.

5.  Dans l'**Explorateur de solutions**, vérifiez que **ContosoAdsWeb** est sélectionné comme projet de démarrage.

## <span id="configurestorage"></span></a>Configuration de votre application pour utiliser votre compte de stockage

1.  Ouvrez le fichier d'application *Web.config* dans le projet ContosoAdsWeb.

    Ce fichier contient des chaînes de connexion SQL et de stockage Azure pour utiliser des objets blob et des files d'attente.

    La chaîne de connexion SQL pointe vers une base de données [SQL Server Express LocalDB][SQL Server Express LocalDB].

    La chaîne de connexion de stockage comporte des espaces réservés où vous allez insérer le nom de votre compte de stockage et la clé d'accès dans les étapes suivantes.

	<pre class="prettyprint">&lt;connectionStrings&gt;
	  &lt;add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" /&gt;
	  &lt;add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
	&lt;/connectionStrings&gt;</pre>

    La chaîne de connexion de stockage est nommée AzureWebJobsStorage, car il s'agit du nom que le Kit de développement logiciel (SDK) WebJobs utilise par défaut. Nous utilisons ce nom ici de façon à ce que vous ne deviez définir qu'une seule valeur de chaîne de connexion dans l'environnement Azure.

2.  Dans le [portail de gestion Azure][1], sélectionnez votre compte de stockage et cliquez sur **Gérer les clés d'accès** au bas de la page.

    ![Manage Access Keys button][Manage Access Keys button]

    ![Manage Access Keys dialog][Manage Access Keys dialog]

3.  Remplacez *[accountname]* dans la chaîne de connexion par la valeur du champ **Nom du compte de stockage**.

4.  Remplacez *[accesskey]* dans la chaîne de connexion par la valeur du champ **Clé d'accès primaire**.

5.  Ouvrez le fichier *App.config* dans le projet ContosoAdsWebJob.

    Ce fichier comporte deux chaînes de connexion : une pour les données de l'application et une pour la journalisation. Pour ce didacticiel, vous allez utiliser le même compte pour les deux. Les chaînes de connexion ont les mêmes espaces réservés que nous avons vus précédemment.
  	<pre class="prettyprint">&lt;configuration&gt;
    &lt;connectionStrings&gt;
        &lt;add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
        &lt;add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
        &lt;add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;"/&gt;
    &lt;/connectionStrings&gt;
        &lt;startup&gt; 
            &lt;supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" /&gt;
    &lt;/startup&gt;
&lt;/configuration&gt;</pre>

    Par défaut, le Kit de développement logiciel (SDK) WebJobs recherche les chaînes de connexion AzureWebJobsStorage et AzureWebJobsDashboard. Vous pouvez également stocker la chaîne de connexion comme vous le souhaitez et la passer explicitement à l'objet `JobHost`.

6.  Remplacez toutes les occurrences de *[accountname]* et *[accesskey]* par les valeurs de votre compte de stockage comme vous l'avez fait pour le projet web. Vous pouvez également copier la chaîne de connexion finalisée du fichier *Web.config* dans les deux chaînes de connexion du fichier *App.config*.

7.  Enregistrez vos modifications.

## <span id="run"></span></a>Exécution locale de l'application

1.  Pour démarrer le programme web frontal de l'application, appuyez sur Ctrl+F5.

    Le navigateur par défaut ouvre la page d'accueil. Le projet web s'exécute, car vous l'avez défini comme projet de démarrage.

    ![Contoso Ads home page][Contoso Ads home page]

2.  Pour démarrer la tâche web principale de l'application, cliquez avec le bouton droit sur le projet ContosoAdsWebJob dans l'**Explorateur de solutions**, puis sur **Débogage** \> **Démarrer une nouvelle instance**.

    Une fenêtre d'application console s'ouvre et affiche « Job host started » pour indiquer qu'il est en cours d'exécution.

    ![Console application window showing that the backend is running][Console application window showing that the backend is running]

3.  Dans votre navigateur, cliquez sur **Créer une publicité**.

4.  Entrez des données de test et sélectionnez une image à télécharger, puis cliquez sur **Créer**.

    ![Create page][Create page]

    L'application ouvre la page Index, mais n'affiche pas de vignette pour la nouvelle publicité, car le processus n'a pas encore eu lieu.

    Entre-temps, après une brève attente, un message dans la fenêtre d'application console indique qu'un message en file d'attente a été reçu et traité.

    ![Console application window showing that a queue message has been processed][Console application window showing that a queue message has been processed]

5.  Lorsque ces messages s'affichent dans la fenêtre d'application console, actualisez la page Index pour afficher la vignette.

    ![Page d'index][Ad list]

6.  Cliquez sur l'option **Détails** de votre publicité pour afficher l'image intégrale.

    ![Details page][Details page]

Vous avez exécuté l'application sur votre ordinateur local. Elle utilise une base de données SQL Server sur votre ordinateur, mais travaille sur des files d'attente et des objets blob dans le cloud. Dans la section suivante, vous allez exécuter l'application dans le cloud en utilisant une base de données du cloud ainsi que des objets blob et des files d'attente du cloud.

## <span id="runincloud"></span></a>Exécution d'applications dans le cloud

Pour exécuter l'application dans le cloud, procédez comme suit :

-   Déploiement dans un site web Azure. Visual Studio crée automatiquement un site web Azure et une instance de base de données SQL.
-   Configurez le site web pour utiliser votre base de données SQL et votre compte de stockage Azure.

Après avoir créé quelques publicités dans le cloud, vous afficherez le tableau de bord du Kit de développement logiciel (SDK) WebJobs pour voir les fonctions de surveillance enrichies qu'il offre.

### Déploiement dans un site web Azure

1.  Fermez le navigateur et la fenêtre d'application console.

2.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ContosoAdsWeb, puis cliquez sur **Publier**.

3.  À l'étape **Profil** de l'Assistant **Publier le site web**, cliquez sur **Sites web Microsoft Azure**.

    ![Select Azure Website publish target][Select Azure Website publish target]

4.  Dans le champ **Sélectionner un site web existant**, cliquez sur **Se connecter**.

    ![Click Sign In][Click Sign In]

5.  Lorsque vous êtes connecté, cliquez sur Suivant.

    ![Click New][Click New]

6.  Dans la boîte de dialogue **Créer un site sur Microsoft Azure**, entrez un nom unique dans le champ **Nom du site**.

    L'URL complète se compose de ce que vous avez entré ici plus .azurewebsites.net (comme indiqué en regard de la zone de texte **Nom du site**). Exemple : si le nom du site est ContosoAds, l'URL est ContosoAds.azurewebsites.net.

7.  Dans la liste déroulante **Région**, sélectionnez la région que vous avez choisie pour votre compte de stockage.

    Ce paramètre spécifie le centre de données Azure où s'exécutera votre site web. Le placement du site web et de votre compte de stockage dans le même centre de données minimise les temps de latence et les frais d'acheminement des données.

8.  Dans la liste déroulante **Serveur de bases de données**, sélectionnez **Créer un serveur**.

    Si votre abonnement a déjà un serveur, vous pouvez sélectionner ce serveur dans la liste déroulante.

9.  Entrez un **Nom d'utilisateur de la base de données** et un **Mot de passe de la base de données**.

    Si vous avez sélectionné **New SQL Database server**, vous ne devez pas entrer un nom et un mot de passe existant ici, mais une nouvelle paire nom/mot de passe que vous allez choisir maintenant et utiliser ultérieurement lorsque vous accèderez à la base de données. Si vous avez sélectionné un serveur créé auparavant, vous devez entrer le mot de passe du compte d’utilisateur administratif déjà créé.

10. Cliquez sur **Create**.

    ![Create site on Microsoft Azure dialog][Create site on Microsoft Azure dialog]

    Visual Studio crée la solution, le projet web, le site web Azure et l'instance de la base de données SQL Azure.

11. À l'étape **Connexion** de l'Assistant **Publier le site web**, cliquez sur **Suivant**.

    ![Connection step][Connection step]

12. À l'étape **Paramètres**, sélectionnez **Utiliser cette chaîne de connexion au moment de l'exécution**, puis cliquez sur **Suivant**.

    ![Settings step][Settings step]

    Vous n'avez pas besoin d'utiliser la boîte de dialogue de connexion pour définir la chaîne de connexion SQL, car vous allez la définir plus tard dans l'environnement Azure.

    Vous pouvez ignorer les avertissements dans cette page.

    -   Normalement, le compte de stockage que vous utilisez lors de l'exécution dans Azure est différent de celui que vous utilisez localement. Néanmoins, pour ce didacticiel, vous utilisez le même dans les deux environnements. Il n'est donc pas nécessaire de transformer la chaîne de connexion AzureWebJobsStorage. Même si vous voulez vraiment utiliser un autre compte de stockage dans le cloud, vous n'avez pas besoin de transformer la chaîne de connexion du fait que l'application utilise un paramètre de l'environnement Azure lorsqu'elle s'exécute dans Azure. Nous aborderons cela plus tard dans ce didacticiel.

    -   Dans ce didacticiel, vous n'allez pas modifier le modèle de données utilisé pour la base de données ContosoAdsContext ; il n'est donc pas nécessaire d'utiliser Migrations Entity Framework Code First pour le déploiement. Code First crée automatiquement une base de données la première fois que l'application essaie d'accéder aux données SQL.

    Dans ce didacticiel, les valeurs par défaut des options sous **Options de publication des fichiers** sont correctes.

13. À l'étape **Aperçu**, cliquez sur **Démarrer l'aperçu**.

    ![Click Start Preview][Click Start Preview]

    Vous pouvez ignorer l'avertissement indiquant qu'aucune base de données n'est en cours de publication. Entity Framework Code First crée la base de données ; il n'est pas nécessaire de la publier.

    La fenêtre de l'aperçu indique que les fichiers binaires et de configuration du projet de tâche web sont copiés dans le dossier *app\_data\\jobs\\continuous* du site web.

    ![WebJobs files in preview window][WebJobs files in preview window]

14. Cliquez sur **Publier**.

    Visual Studio déploie l'application et ouvre l'URL de la page d'accueil dans le navigateur.

    Vous ne pouvez pas utiliser le site tant que vous n'avez pas défini les chaînes de connexion dans l'environnement Azure dans la section suivante. Une page d'erreur ou la page d'accueil s'affiche en fonction du site et des options de création de base de données que vous avez sélectionnées auparavant.

### Configurez le site web pour utiliser votre base de données SQL et votre compte de stockage Azure.

Par sécurité, il est conseillé [d'éviter de placer des informations sensibles (par exemple, des chaînes de connexion) dans des fichiers stockés dans des référentiels de code source][d'éviter de placer des informations sensibles (par exemple, des chaînes de connexion) dans des fichiers stockés dans des référentiels de code source]. Azure permet de le faire : vous pouvez définir une chaîne de connexion et d'autres paramètres dans l'environnement Azure. Les API de configuration ASP.NET sélectionnent automatiquement ces valeurs lorsque l'application s'exécute dans Azure. Dans cette section, vous allez définir les valeurs des chaînes de connexion dans Azure.

1.  Dans votre navigateur, accédez au portail de gestion Azure et sélectionnez le site web que vous avez déployé dans l'application Contoso Ads.

2.  Cliquez sur l'onglet **Configurer** et faites défiler vers le bas jusqu'à la section **Chaînes de connexion**.

3.  Remplacez le nom de la chaîne de connexion DefaultConnection par ContosoAdsContext.

    Azure a automatiquement créé cette chaîne de connexion lorsque vous avez créé le site avec une base de données associée ; il a donc la valeur correcte de la chaîne de connexion. Vous remplacez simplement le nom par celui que votre code recherche.

4.  Ajoutez deux chaînes de connexion nommées AzureWebJobsStorage et AzureWebJobsDashboard. Définissez le type sur Personnalisé et configurez la valeur de la chaîne de connexion avec celle que vous avez utilisée auparavant pour les fichiers *Web.config* et *App.config*. Vérifiez que vous incluez la chaîne de connexion complète, et pas uniquement la clé d'accès, sans guillemets.

    Le Kit de développement logiciel (SDK) WebJobs utilise ces chaînes de connexion : une pour les données de l'application et une pour la journalisation. Comme nous l'avons vu précédemment, le code du programme web frontal utilise aussi la chaîne pour les données de l'application.

5.  Cliquez sur **Enregistrer**.

    ![Connection strings in management portal][Connection strings in management portal]

6.  Cliquez sur l'onglet **Tableau de bord**, puis sur **Redémarrer**.

    La tâche web démarre automatiquement lorsque vous publiez, mais elle s'arrête lorsque vous modifiez la configuration. Pour la redémarrer, vous pouvez redémarrer le site ou la tâche web. Il est généralement conseillé de redémarrer le site après une modification de la configuration.

7.  Actualisez la fenêtre du navigateur contenant l'URL du site dans sa barre d'adresse.

    La page d'accueil s'affiche.

8.  Créez une publicité comme vous l'avez fait lorsque vous avez exécuté l'application localement.

    La page Index s'affiche d'abord sans vignette.

9.  Actualisez la page après quelques secondes : la vignette s'affiche.

    Si la vignette ne s'affiche pas, la tâche web n'a peut-être pas démarré automatiquement. Dans ce cas, accédez à l'onglet Tâches web dans le

### Affichage du tableau de bord du Kit de développement logiciel (SDK) WebJobs

1.  Dans le portail de gestion Azure, sélectionnez votre site web.

2.  Cliquez sur l'onglet **Tâches web**.

3.  Cliquez sur l'URL dans la colonne Journaux de votre tâche web.

    ![WebJobs tab][WebJobs tab]
    Un nouvel onglet ouvre le tableau de bord du Kit de développement logiciel (SDK) WebJobs dans le navigateur. Le tableau de bord indique que la tâche web est en cours d'exécution et affiche la liste des fonctions de votre code que le SDK a déclenchées.

4.  Cliquez sur une des fonctions pour afficher des informations sur son exécution.

    ![WebJobs SDK dashboard][WebJobs SDK dashboard]

    ![WebJobs SDK dashboard][2]

    Le bouton **Rappeler la fonction** de cette page commande à l'infrastructure du SDK de rappeler la fonction et vous permet de modifier les données déjà transmises à la fonction.

> [WACOM.NOTE] Lorsque le test est terminé, supprimez le site web et l'instance de la base de données SQL. Le site web est gratuit, mais l'instance de la base de données SQL et le compte de stockage cumulent des frais (minimaux du fait de la petite taille). De même, si vous le laissez s'exécuter, toute personne qui trouve votre URL peut créer et afficher des publicités. Dans le portail de gestion Azure, ouvrez l'onglet **Tableau de bord** de votre site web et cliquez sur le bouton **Supprimer** en bas de la page. Vous pouvez en même temps cocher une case pour supprimer l'instance de la base de données SQL. Si vous voulez juste empêcher temporairement l'accès au site, cliquez sur **Arrêter**. Dans ce cas, les frais continuent à s'accumuler pour la base de données SQL et le compte de stockage. Vous pouvez suivre une procédure similaire pour supprimer la base de données SQL et le compte de stockage lorsque vous n'en avez plus besoin.

## <span id="create"></span></a>Création intégrale de l'application

Dans cette section, vous effectuerez les tâches suivantes :

-   création d'une solution Visual Studio avec un projet web ;
-   ajout d'un projet de bibliothèque de classes pour la couche d'accès aux données partagée par le programme frontal et le programme principal ;
-   ajout d'un projet d'application console pour le programme principal, le déploiement de tâches web étant activé ;
-   ajout de packages NuGet ;
-   définition des références d'un projet ;
-   copie des fichiers de code et de configuration de l'application téléchargée que vous avez utilisée dans la section précédente de ce didacticiel ;
-   examen des parties du code qui fonctionnent avec les objets blob, les files d'attente et le Kit de développement logiciel (SDK) WebJobs Azure.

### Création d'une solution Visual Studio avec un projet web et un projet de bibliothèque de classes

1.  Dans Visual Studio, sélectionnez **Nouveau**, **Projet** dans le menu **Fichier**.

2.  Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C\#** \> **Web** \> **Application Web ASP.NET**.

3.  Nommez le projet ContosoAdsWeb, la solution ContosoAdsWebJobsSDK (modifiez le nom de la solution si vous la placez dans le même dossier que la solution téléchargée) et cliquez sur **OK**.

    ![Nouveau projet][Nouveau projet]

4.  Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle MVC et désactivez la case à cocher **Héberger dans le cloud** sous **Microsoft Azure**.

    En sélectionnant **Héberger dans le cloud**, Visual Studio crée automatiquement un site web Azure et une base de données SQL. Comme vous les avez déjà créés, vous n'avez pas besoin de le faire lorsque vous créez le projet. Si vous voulez en créer un, activez cette case à cocher. Vous pouvez alors configurer le nouveau site web et la base de données SQL comme vous l'avez fait précédemment lorsque vous avez déployé l'application.

5.  Cliquez sur **Modifier l'authentification**.

    ![Change Authentication][Change Authentication]

6.  Dans la boîte de dialogue **Modifier l'authentification**, choisissez **Aucune authentification** et cliquez sur **OK**.

    ![Aucune authentification][Aucune authentification]

7.  Dans la boîte de dialogue **Nouveau projet ASP.NET**, cliquez sur **OK**.

    Visual Studio crée la solution et le projet web.

8.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur la solution (et non sur le projet) et choisissez **Ajouter** \> **Nouveau projet**.

9.  Dans la boîte de dialogue **Ajouter un nouveau projet**, sélectionnez **Visual C\#** \> **Bureau Windows** \> **Bibliothèque de classes**.

10. Nommez le projet *ContosoAdsCommon*, puis cliquez sur **OK**.

    Ce projet contient le contexte Entity Framework et le modèle de données que le programme frontal et le programme principal vont utiliser. Vous pouvez également définir les classes associées à Entity Framework dans le projet web et faire référence à ce projet de tâche web. Mais, dans ce cas, ce dernier aura une référence inutile aux assemblys web.

### Ajout d'un projet d'application console dont le déploiement de tâches web est activé

1.  Cliquez avec le bouton droit sur le projet web (et non sur la solution ou le projet de bibliothèque de classes), puis cliquez sur **Ajouter** \> **Nouveau projet de tâche web Azure**.

    ![New Azure WebJob Project menu selection][New Azure WebJob Project menu selection]

2.  Dans la boîte de dialogue **Ajouter une tâche web Azure**, entrez ContosoAdsWebJob pour le **Nom du projet** et le **Nom de la tâche web**. Laissez **Mode d'exécution de la tâche web** sur **Exécuter en continu**.

3.  Cliquez sur **OK**.

    Visual Studio crée une application console configurée pour se déployer comme une tâche web lorsque vous déployez le projet web. Pour cela, il a effectué les tâches suivantes après la création du projet :

    -   ajout d'un fichier *webjob-publish-settings.json* dans le dossier des propriétés du projet de tâche web ;
    -   ajout d'un fichier *webjobs-list.json* dans le dossier des propriétés du projet web ;
    -   installation du package NuGet Microsoft.Web.WebJobs.Publish dans le projet de tâche web.

    Pour plus d'informations sur ces modifications, consultez la rubrique [Déploiement de tâches web en utilisant Visual Studio][Déploiement de tâches web en utilisant Visual Studio].

### Ajout de packages NuGet

Installez d'abord le Kit de développement logiciel (SDK) WebJobs dans le projet de tâche web.

1.  Ouvrez la boîte de dialogue **Gérer les packages NuGet** pour la solution.

2.  Dans le volet gauche, sélectionnez **En ligne**.

3.  Remplacez **Stable uniquement** par **Inclure la version préliminaire**.

4.  Recherchez le package NuGet *Microsoft.Azure.WebJobs* et installez-le dans le projet ContosoAdsWebJob.

    ![Find WebJobs SDK package][Find WebJobs SDK package]

    ![Install WebJobs SDK package only in WebJob project][Install WebJobs SDK package only in WebJob project]

    Cela installe également les packages liés, y compris un autre package du Kit de développement logiciel (SDK) WebJobs, *Microsoft.Jobs.Core*. Vous utilisez séparément l'autre package uniquement lorsque vous créez vos fonctions utilisateur dans une DLL indépendante ; pour ce didacticiel, vous écrivez tout le code dans l'application console. Vous avez besoin de la bibliothèque cliente Azure Storage (SCL) pour utiliser des files d'attente et des objets blob dans le projet web et dans le projet de tâches web.

Le package NuGet de cette bibliothèque est installé dans le projet de tâches web en tant que dépendance du Kit de développement logiciel (SDK) WebJobs. Mais vous avez également besoin de l'utiliser avec des objets blob et des files d'attente dans le projet web.

1.  Dans le volet de gauche, sélectionnez **Packages installés**.

2.  Recherchez le package *Azure Storage* et cliquez sur **Gérer**.

3.  Dans la boîte de dialogue **Sélectionner les projets**, activez la case à cocher **ContosoAdsWeb**, puis cliquez sur **OK**.

Ces trois projets ont recours à Entity Framework pour utiliser les données de la base de données SQL.

1.  Dans le volet gauche, sélectionnez **En ligne**.

2.  Remplacez **Inclure la version préliminaire** par **Stable uniquement**.

3.  Recherchez le package NuGet *EntityFramework* et installez-le dans les trois projets.

### Définition des références de projet

Les projets web et de tâches web utilisent la base de données SQL ; les deux nécessitent une référence au projet ContosoAdsCommon.

1.  Dans le projet ContosoAdsWeb, définissez une référence au projet ContosoAdsCommon. Cliquez avec le bouton droit sur le projet ContosoAdsWeb, puis cliquez sur **Ajouter** \> **Référence**. Dans la boîte de dialogue **Gestionnaire de références**, sélectionnez **Solution** \> **Projets** \> **ContosoAdsCommon** et cliquez sur **OK**.

2.  Dans le projet ContosoAdsWebJob, définissez une référence au projet ContosoAdsCommon.

Le projet web nécessite des références pour utiliser des images et accéder aux chaînes de connexion.

1.  Dans le projet ContosoAdsWebJob, définissez une référence à `System.Drawing` et à `System.Configuration`.

### Ajout de fichiers de code et de configuration

Ce didacticiel n'explique pas comment [créer des contrôleurs et des vues MVC à l'aide de la structure][ASP.NET MVC], [comment écrire du code Entity Framework qui fonctionne avec les bases de données SQL Server][comment écrire du code Entity Framework qui fonctionne avec les bases de données SQL Server], [ni les bases de la programmation asynchrone dans ASP.NET 4.5][ni les bases de la programmation asynchrone dans ASP.NET 4.5]. Il ne reste donc qu'à copier les fichiers de code et de configuration de la solution téléchargée dans la nouvelle solution. Ensuite, les sections suivantes montrent et expliquent les éléments essentiels de ce code.

Pour ajouter des fichiers à un projet ou à un dossier, cliquez avec le bouton droit sur le projet ou le dossier, puis cliquez sur **Ajouter** \> **Élément existant**. Sélectionnez les fichiers et cliquez sur **Ajouter**. Si un message vous demande si vous souhaitez remplacer les fichiers existants, cliquez sur **Oui**.

1.  Dans le projet ContosoAdsCommon, supprimez le fichier *Class1.cs* et ajoutez à la place les fichiers suivants du projet téléchargé.

    -   *Ad.cs*
    -   *ContosoAdscontext.cs*
    -   *BlobInformation.cs*<br/><br/>

2.  Dans le projet ContosoAdsWeb, ajoutez les fichiers suivants du projet téléchargé.

    -   *Web.config*
    -   *Global.asax.cs*
    -   Dans le dossier *Controllers* : *AdController.cs.*
    -   Dans le dossier *Views\\Shared* : fichier *\_Layout.cshtml*.
    -   Dans le dossier *Views\\Home* : *Index.cshtml*.
    -   Dans le dossier *Views\\Ad* (créez d'abord le dossier) : cinq fichiers *.cshtml*.<br/><br/>

3.  Dans le projet ContosoAdsWebJob, ajoutez les fichiers suivants du projet téléchargé.

    -   *App.config* (indiquez le type de fichier **Tous les fichiers**)
    -   *Program.cs*

Vous pouvez maintenant générer, exécuter et déployer l'application en suivant les instructions fournies précédemment dans ce didacticiel. Cependant, avant cela, arrêtez la tâche web toujours en cours d'exécution dans le premier site web dans lequel vous l'avez déployée. Sinon, cette tâche web traite les messages en file d'attente créés localement ou par l'application en cours d'exécution dans un nouveau site web, du fait qu'ils utilisent tous le même compte de stockage.

## <span id="code"></span></a>Révision du code de l'application

Les sections suivantes présentent le code utilisé avec le Kit de développement logiciel (SDK) WebJobs et des objets blob et des files d'attente Azure. Pour le code propre au SDK, consultez la [section Program.cs][section Program.cs].

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

La classe a deux constructeurs. Le premier est utilisé par le projet web et spécifie le nom d'une chaîne de connexion stockée dans le fichier Web.config de l'environnement d'exécution Azure. Le second vous permet de passer la chaîne de connexion existante. Le projet de rôle de travail, qui ne comporte pas de fichier Web.config., nécessite cette opération. Vous avez vu précédemment où est stockée cette chaîne de connexion, et vous allez voir comme le code la récupère quand il instancie la classe DbContext.

### ContosoAdsCommon - BlobInformation.cs

La classe `BlobInformation` permet de stocker les informations sur un objet blob d'image dans un message en file d'attente.

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

Le code appelé par la méthode `Application_Start` crée un conteneur d'objets blob *images* et une file d'attente *images*, s'ils n'existent pas déjà. Cela garantit que, lorsque vous commencez à utiliser un nouveau compte de stockage, le conteneur d'objets blob et la file d'attente nécessaires sont créés automatiquement.

Le code a accès au compte de stockage en utilisant la chaîne de connexion du fichier *Web.config* ou l'environnement d'exécution Azure.

        var storageAccount = CloudStorageAccount.Parse
            (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

Il obtient ensuite une référence au conteneur d'objets blob *images*, crée le conteneur s'il n'existe pas déjà et définit les autorisations d'accès au nouveau conteneur. Par défaut, les nouveaux conteneurs donnent accès aux objets blob uniquement aux clients possédant des informations d'identification de compte de stockage. Pour le site web, les objets blob doivent être publics pour afficher des images en utilisant des URL qui pointent vers les objets blob des images.

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

Du code similaire obtient une référence à la file d'attente *blobnamerequest* et crée une nouvelle file d'attente. Dans ce cas, aucune modification des autorisations n'est nécessaire. Plus loin dans ce didacticiel, la section [ResolveBlobName][ResolveBlobName] explique pourquoi la file d'attente dans laquelle l'application web écrit est utilisée uniquement pour obtenir les noms des objets blob et non pour créer des vignettes.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        var imagesQueue = queueClient.GetQueueReference("blobnamerequest");
        imagesQueue.CreateIfNotExists();

### ContosoAdsWeb - \_Layout.cshtml

Le fichier *_Layout.cshtml* définit le nom d'application dans l'en-tête et le pied de page, et crée une entrée de menu « Ads ».

### ContosoAdsWeb - Views\\Home\\Index.cshtml

Le fichier *Views\\Home\\Index.cshtml* affiche les liens de catégorie sur la page d'accueil. Les liens passent la valeur entière de l'énumération `Category` dans une variable querystring sur la page Ads Index.

        <li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
        <li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
        <li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
        <li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### ContosoAdsWeb - AdController.cs

Dans le fichier *AdController.cs*, le constructeur appelle la méthode `InitializeStorage` pour créer les objets de la bibliothèque cliente Azure Storage, qui fournissent une API pour les objets blob et les files d'attente.

Le code obtient ensuite une référence au conteneur d'objets blob *images* comme vu précédemment dans *Global.asax.cs*. Ce faisant, il définit une [stratégie de nouvelles tentatives][stratégie de nouvelles tentatives] par défaut appropriée pour une application web. La stratégie de nouvelles tentatives de backoff exponentiel par défaut peut suspendre l'application web pendant plus d'une minute en cas de tentatives répétées pour une erreur temporaire. La stratégie de nouvelle tentative spécifiée ici laisse 3 secondes après chaque nouvelle tentative, jusqu'à 3.

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesBlobContainer = blobClient.GetContainerReference("images");

Un code similaire obtient une référence à la file d'attente *images*.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesQueue = queueClient.GetQueueReference("blobnamerequest");

La plupart du code du contrôleur permet généralement d'utiliser un modèle de données Entity Framework en utilisant une classe DbContext. La méthode HttpPost `Create` est une exception, car elle télécharge un fichier et l'enregistre dans le stockage d'objets blob. Le classeur de modèles fournit un objet [HttpPostedFileBase][HttpPostedFileBase] à la méthode.

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

Une fois que la méthode HttpPost `Create` a téléchargé un objet blob et mis à jour la base de données, elle crée un message de file d'attente pour informer ce processus principal qu'une image est prête à être convertie en vignette.

        BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        await thumbnailRequestQueue.AddMessageAsync(queueMessage);

Le code de la méthode HttpPost `Edit` est similaire, mais si l'utilisateur sélectionne un nouveau fichier image, les objets blob qui existent pour cette publicité doivent être supprimés.

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

Les fichiers *Create.cshtml* et *Edit.cshtml* spécifient l'encodage de formulaire qui permet au contrôleur d'obtenir l'objet `HttpPostedFileBase`.

        @using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

Un élément `<input>` indique au navigateur de fournir une boîte de dialogue de sélection de fichier.

        <input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <span id="programcs"></span></a>ContosoAdsWebJob - Program.cs - Méthodes Main et Initialize

Lorsque la tâche web démarre, la méthode `Main` appelle `Initialize` pour instancier le contexte de base de données Entity Framework. Elle appelle ensuite la méthode `JobHost.RunAndBlock` du Kit de développement logiciel (SDK) WebJobs pour commencer l'exécution dans un seul thread des fonctions déclenchées dans le thread actif.

        static void Main(string[] args)
        {
            Initialize();
        
            JobHost host = new JobHost();
            host.RunAndBlock();
        }
        
        private static void Initialize()
        {
            db = new ContosoAdsContext();
        }

### <span id="generatethumbnail"></span></a>ContosoAdsWebJob - Program.cs - Méthode GenerateThumbnail

Le Kit de développement logiciel (SDK) WebJobs appelle cette méthode lorsqu'un message de file d'attente est reçu. Cette méthode crée une vignette et place son URL dans la base de données.

        public static void GenerateThumbnail(
        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
        [Blob("images/{BlobName}")] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)
        {
            using (Stream output = outputBlob.OpenWrite())
            {
                ConvertImageToThumbnailJPG(input, output);
                outputBlob.Properties.ContentType = "image/jpeg";
            }
        
            var id = blobInfo.AdId;
            Ad ad = db.Ads.Find(id);
            if (ad == null)
            {
                throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
            }
            ad.ThumbnailURL = outputBlob.Uri.ToString();
            db.SaveChanges();
        }

-   L'attribut `QueueTrigger` indique au Kit de développement logiciel (SDK) WebJobs d'appeler cette méthode lorsqu'un nouveau message est reçu dans la file d'attente thumbnailrequest.

        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

    L'objet `BlobInformation` dans le message de file d'attente est automatiquement désérialisé dans le paramètre `blobInfo`. Lorsque la méthode est terminée, le message de file d'attente est supprimé. Si la méthode échoue avant de se terminer, le message de file d'attente n'est pas supprimé ; après un bail de 10 minutes, il est libéré pour être à nouveau sélectionné et traité. Cette séquence ne se répète pas indéfiniment si un message provoque toujours une exception. Après 5 tentatives ayant échoué de traitement d'un message, celui-ci est déplacé dans la file d'attente nommée {queuename}-poison. Vous pouvez configurer le nombre maximal de tentatives.

-   Les deux attributs `Blob` fournissent des objets liés à des objets blob : un à l'objet blob d'image existant et l'autre à un nouvel objet blob de vignette que la méthode crée.

        [Blob("images/{BlobName}")] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

    Les noms des objets blob proviennent des propriétés de l'objet `BlobInformation` reçu dans le message de file d'attente (`BlobName` et `BlobNameWithoutExtension`). Pour profiter de toutes les fonctionnalités de la bibliothèque cliente de stockage, vous pouvez faire appel à la classe `CloudBlockBlob` pour utiliser des objets blob. Si vous voulez réutiliser du code écrit pour utiliser des objets `Stream`, vous pouvez faire appel à la classe `Stream`.

> [WACOM.NOTE]
>
> -   Si votre site web s'exécute sur plusieurs machines virtuelles, ce programme s'exécute sur chaque machine, qui attend des déclencheurs et essaie d'exécuter les fonctions. Dans certains scénarios, cela peut entraîner que certaines fonctions traitent deux fois les mêmes données ; ces fonctions doivent donc être idempotentes (écrites de façon que, si elles sont appelées de manière répétitive avec les mêmes données d'entrée, elles ne produisent pas des résultats en double).
> -   Pour en savoir plus sur l'implémentation d'un arrêt correct, consultez l'[annonce de la version bêta 0.3.0 du Kit de développement logiciel (SDK) WebJobs][annonce de la version bêta 0.3.0 du Kit de développement logiciel (SDK) WebJobs].
> -   Le code de la méthode `ConvertImageToThumbnailJPG` (non représenté) utilise des classes dans l'espace de noms `System.Drawing` pour plus de simplicité. Cependant, les classes de cet espace de noms ont été conçues pour être utilisées avec Windows Forms. Elles ne sont pas prises en charge dans un service Windows ou ASP.NET.

### Tâches web et rôle de travail de service cloud

Si vous comparez le volume du code dans la méthode `GenerateThumbnails` de cet exemple d'application avec le code du rôle de travail dans la [version de service cloud de l'application][version de service cloud de l'application], vous constatez le travail important que le Kit de développement logiciel (SDK) WebJobs fait à votre place. L'avantage est plus important qu'il n'y paraît, car le code de l'exemple d'application de service cloud ne fait pas tout ce que vous feriez en production (par exemple, la gestion des messages incohérents) et que le Kit de développement logiciel (SDK) WebJobs fait pour vous.

La version de service cloud de cet exemple d'application comporte la méthode `ProcessQueueMessage` qui effectue les tâches suivantes :

-   récupération dans le message de file d'attente de l'ID d'enregistrement de base de données ;
-   lecture de la base de données pour récupérer l'URL de l'objet blob ;
-   conversion de l'image en vignette et enregistrement de la vignette dans un nouvel objet blob ;
-   mise à jour de l'enregistrement de base de données en ajoutant l'URL de l'objet blob de la vignette.

Dans la version de service cloud de l'application, l'ID de l'enregistrement est la seule information dans le message de file d'attente ; le processus en arrière-plan récupère l'URL de l'image dans la base de données. Dans la version de Kit de développement logiciel (SDK) WebJobs de l'application, le message de file d'attente contient l'URL de l'image de façon à pouvoir la fournir aux attributs `Blob`. Si le message de file d'attente n'avait pas l'URL de l'objet blob, vous devriez écrire du code dans la méthode pour récupérer cette URL et ensuite écrire du code pour lier les objets aux objets blob en entrée et en sortie au lieu de laisser le Kit de développement logiciel (SDK) WebJobs le faire pour vous.

### Utilisation du Kit de développement logiciel (SDK) WebJobs dans un rôle de travail

Comme nous l'avons vu, un programme qui utilise le Kit de développement logiciel (SDK) WebJobs n'est pas nécessairement exécuté dans Azure dans une tâche web. Il peut s'exécuter localement et également dans un rôle de travail. Cependant, vous pouvez accéder au tableau de bord du Kit de développement logiciel (SDK) WebJobs uniquement via un site web Azure. Pour utiliser le tableau de bord, vous devez connecter le site web au compte de stockage que vous utilisez en définissant la chaîne de connexion AzureWebJobsDashboard de l'onglet Configurer du portail de gestion. Vous pouvez alors accéder au tableau de bord en utilisant l'URL https://{websitename}.scm.azurewebsites.net/azurejobs/#/functions. Pour plus d'informations, consultez le billet de blog [Récupération d'un tableau de bord pour un développement local avec le Kit de développement logiciel (SDK) WebJobs][Récupération d'un tableau de bord pour un développement local avec le Kit de développement logiciel (SDK) WebJobs] (notez cependant qu'il affiche un ancien nom de chaîne de connexion).

## Étapes suivantes

Dans ce didacticiel, nous avons vu une simple application multiniveau qui utilise le Kit de développement logiciel (SDK) WebJobs pour le traitement principal. L'application est intentionnellement simple pour un didacticiel de prise en main. Par exemple, elle n'implémente pas l'[injection de dépendances][injection de dépendances] ni les [modèles de référentiel et d'élément de travail][modèles de référentiel et d'élément de travail], elle [n'utilise pas d'interface pour la connexion][n'utilise pas d'interface pour la connexion], ni les [migrations Code First EF][migrations Code First EF] pour gérer les changements de modèles de données ou la [résilience des connexions EF][résilience des connexions EF] pour gérer les erreurs réseau temporaires, etc.

Pour d'autres exemples qui montrent comment utiliser le Kit de développement logiciel (SDK) WebJobs dans d'autres scénarios, consultez le dossier [AzureWebJobs][AzureWebJobs] dans le projet ASP.NET CodePlex.

Lorsqu'une tâche web est exécutée dans un site web, vous pouvez résoudre les problèmes en mode débogage à distance comme vous le faites pour des sites web. Pour plus d'informations, consultez la page [Résolution des problèmes de sites Web Azure dans Visual Studio][Résolution des problèmes de sites Web Azure dans Visual Studio]. Vous devez attacher manuellement le processus d'une tâche web. Consultez les instructions Visual Studio 2012 du didacticiel pour plus d'informations à ce sujet.

Pour plus d'informations, consultez la page [Ressources recommandées pour les tâches web Azure][Ressources recommandées pour les tâches web Azure].

  [site web Azure]: /fr-fr/documentation/articles/fundamentals-application-models/#WebSites
  [Base de données SQL Azure]: http://msdn.microsoft.com/library/azure/ee336279
  [service Blob Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [service de File d'attente Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Ad list]: ./media/websites-dotnet-webjobs-sdk-get-started/list.png
  [télécharger le projet Visual Studio]: http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb
  [Configuration requise]: #prerequisites
  [Contenu]: #learn
  [Kit de développement logiciel (SDK) WebJobs]: #webjobssdk
  [Architecture de l'application]: #contosoads
  [Configuration de l’environnement de développement]: #setupdevenv
  [Génération, exécution et déploiement de l'application]: #storage
  [Création intégrale de l'application]: #create
  [Révision du code de l'application]: #code
  [Résolution des problèmes]: #troubleshoot
  [Étapes suivantes]: #next-steps
  [ASP.NET MVC]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [Web Forms]: http://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview
  [free-trial-note]: ../includes/free-trial-note.md
  [tâches web]: /fr-fr/documentation/articles/web-sites-create-web-jobs/
  [AlwaysOn]: http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx
  [Ad table]: ./media/websites-dotnet-webjobs-sdk-get-started/adtable.png
  [Contoso Ads architecture]: ./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png
  [Comparaison entre Sites Web Azure, Azure Cloud Services et Azure Virtual Machines]: /fr-fr/documentation/articles/choose-web-site-cloud-service-vm/
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Gestion des comptes de stockage]: /fr-fr/documentation/articles/storage-manage-storage-account/
  [New storage account]: ./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png
  [SQL Server Express LocalDB]: http://msdn.microsoft.com/en-us/library/hh510202.aspx
  [1]: http://manage.windowsazure.com/
  [Manage Access Keys button]: ./media/websites-dotnet-webjobs-sdk-get-started/mak.png
  [Manage Access Keys dialog]: ./media/websites-dotnet-webjobs-sdk-get-started/cpak.png
  [Contoso Ads home page]: ./media/websites-dotnet-webjobs-sdk-get-started/home.png
  [Console application window showing that the backend is running]: ./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png
  [Create page]: ./media/websites-dotnet-webjobs-sdk-get-started/create.png
  [Console application window showing that a queue message has been processed]: ./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png
  [Details page]: ./media/websites-dotnet-webjobs-sdk-get-started/details.png
  [Select Azure Website publish target]: ./media/websites-dotnet-webjobs-sdk-get-started/pubweb.png
  [Click Sign In]: ./media/websites-dotnet-webjobs-sdk-get-started/signin.png
  [Click New]: ./media/websites-dotnet-webjobs-sdk-get-started/clicknew.png
  [Create site on Microsoft Azure dialog]: ./media/websites-dotnet-webjobs-sdk-get-started/newdb.png
  [Connection step]: ./media/websites-dotnet-webjobs-sdk-get-started/connstep.png
  [Settings step]: ./media/websites-dotnet-webjobs-sdk-get-started/settingsstep.png
  [Click Start Preview]: ./media/websites-dotnet-webjobs-sdk-get-started/previewstep.png
  [WebJobs files in preview window]: ./media/websites-dotnet-webjobs-sdk-get-started/previewwjfiles.png
  [d'éviter de placer des informations sensibles (par exemple, des chaînes de connexion) dans des fichiers stockés dans des référentiels de code source]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets
  [Connection strings in management portal]: ./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png
  [WebJobs tab]: ./media/websites-dotnet-webjobs-sdk-get-started/wjtab.png
  [WebJobs SDK dashboard]: ./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png
  [2]: ./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png
  [Nouveau projet]: ./media/websites-dotnet-webjobs-sdk-get-started/newproject.png
  [Change Authentication]: ./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png
  [Aucune authentification]: ./media/websites-dotnet-webjobs-sdk-get-started/noauth.png
  [New Azure WebJob Project menu selection]: ./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png
  [Déploiement de tâches web en utilisant Visual Studio]: /fr-fr/documentation/articles/websites-dotnet-deploy-webjobs/
  [Find WebJobs SDK package]: ./media/websites-dotnet-webjobs-sdk-get-started/updstg.png
  [Install WebJobs SDK package only in WebJob project]: ./media/websites-dotnet-webjobs-sdk-get-started/updstg2.png
  [comment écrire du code Entity Framework qui fonctionne avec les bases de données SQL Server]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc
  [ni les bases de la programmation asynchrone dans ASP.NET 4.5]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [section Program.cs]: #programcs
  [ResolveBlobName]: #resolveblobname
  [stratégie de nouvelles tentatives]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling
  [HttpPostedFileBase]: http://msdn.microsoft.com/en-us/library/system.web.httppostedfilebase.aspx
  [annonce de la version bêta 0.3.0 du Kit de développement logiciel (SDK) WebJobs]: http://azure.microsoft.com/blog/2014/06/18/announcing-the-0-3-0-beta-preview-of-microsoft-azure-webjobs-sdk/http://azure.microsoft.com/blog/2014/06/18/announcing-the-0-3-0-beta-preview-of-microsoft-azure-webjobs-sdk/
  [version de service cloud de l'application]: /fr-fr/documentation/articles/cloud-services-dotnet-get-started/
  [https://{websitename}.scm.azurewebsites.net/azurejobs/\#/functions]: https://{websitename}.scm.azurewebsites.net/azurejobs/#/functions
  [Récupération d'un tableau de bord pour un développement local avec le Kit de développement logiciel (SDK) WebJobs]: http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx
  [injection de dépendances]: http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection
  [modèles de référentiel et d'élément de travail]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo
  [n'utilise pas d'interface pour la connexion]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log
  [migrations Code First EF]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
  [résilience des connexions EF]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
  [AzureWebJobs]: http://aspnet.codeplex.com/SourceControl/latest#Samples/AzureWebJobs/ReadMe.txt
  [Résolution des problèmes de sites Web Azure dans Visual Studio]: /fr-fr/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/
  [Ressources recommandées pour les tâches web Azure]: http://go.microsoft.com/fwlink/?LinkId=390226
