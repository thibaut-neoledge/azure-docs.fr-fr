<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande,tdykstra"></tags>

# Configuration et déploiement de l'application Azure Email Service - 2 sur 5

Ce didacticiel, le deuxième d'une série de cinq, explique comment générer et déployer l'exemple d'application Azure Email Service. Pour obtenir des informations sur l'application et la série de didacticiels, consultez le [premier didacticiel de la série][].

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   configurer votre ordinateur pour le développement Azure en installant le Kit de développement logiciel (SDK) Azure ;
-   configurer et tester l'application Azure Email Service sur votre ordinateur local ;
-   publier l'application sur Azure ;
-   afficher et modifier les tables, files d'attente et objets blob Azure avec l'Explorateur de serveurs Visual Studio ;
-   configurer le suivi et afficher les données associées ;
-   mettre à l'échelle l'application par augmentation du nombre d'instances de rôle de travail.

### Sections du didacticiel

-   [Configuration de l’environnement de développement][]
-   [Téléchargement et exécution de la solution terminée][]
-   [Afficher le stockage de développement dans Visual Studio][]
-   [Création d’un compte de stockage Azure][]
-   [Création d'un service cloud][]
-   [Configuration de l'application pour Azure Storage][]
-   [Configuration de l'application pour l'utilisation de SendGrid][]
-   [Déploiement de l'application dans Azure][]
-   [Passage de l'application de la gestion intermédiaire à la production][]
-   [Configuration et affichage des données de suivi][]
-   [Ajout d'une autre instance de rôle de travail pour traiter une charge accrue][]

[WACOM.INCLUDE [install-sdk-2013-only][]]

## <a name="downloadcnfg"></a><span class="short-header">Téléchargement et exécution</span>Téléchargement et exécution de la solution terminée

1.  Téléchargez et décompressez la [solution terminée][].

2.  Démarrez Visual Studio.

3.  Dans le menu **Fichier**, choisissez **Ouvrir un projet**, accédez à l'emplacement où vous avez téléchargé la solution, puis ouvrez le fichier solution.

4.  Appuyez sur Ctrl+Maj+B pour générer la solution.

    Par défaut, Visual Studio restaure automatiquement le contenu du package NuGet, qui n'était pas inclus dans le fichier *.zip*. Si les packages ne sont pas restaurés, installez-les manuellement en ouvrant la boîte de dialogue **Gérer les packages NuGet pour la solution** et en cliquant sur le bouton **Restaurer** en haut à droite.

5.  Dans l'**Explorateur de solutions**, assurez-vous que **AzureEmailService** est sélectionné comme projet de démarrage.

6.  Appuyez sur Ctrl+F5 pour exécuter l’application.

    La page d'accueil de l'application apparaît dans votre navigateur.

7.  Cliquez sur **Listes de diffusion** dans la barre de menus.

    (Les captures d'écran montrent le style de page web des modèles de projet Visual Studio 2012, mais le contenu est identique pour Visual Studio 2013.)

    ![Exécuter l'application][]

8.  Cliquez sur **Create New**.

9.  Entrez quelques données de test, puis cliquez sur **Créer**.

    ![Exécuter l'application][1]

10. Créez encore quelques entrées de la liste de diffusion.

    ![Page d'index de la liste de diffusion][]

11. Cliquez sur **Abonnés**, puis ajoutez quelques abonnés. Définissez **Vérifié** sur `true`.

    ![Page d'index de l'abonné][]

12. Préparez l'ajout de messages en créant un fichier *.txt* contenant le corps d'un courrier électronique que vous souhaitez envoyer. Créez ensuite un fichier *.htm* contenant le même texte, mais avec un peu de HTML (par exemple, mettez l'un des mots du message en gras ou en italique). Vous utiliserez ces fichiers à l'étape suivante.

13. Cliquez sur **Messages**, puis ajoutez quelques messages. Sélectionnez les fichiers que vous avez créés à l'étape précédente. Ne changez pas la date planifiée qui, par défaut, arrive à échéance dans une semaine. L'application ne peut pas envoyer de messages tant que vous n'avez pas configuré SendGrid.

    ![Page de création du message][]

    ![Page d'index du message][]

    Les données que vous avez entrées et affichées sont gérées par l'émulateur de stockage Azure. Ce dernier utilise une base de données SQL Server Express LocalDB pour émuler le fonctionnement d'Azure Storage dans le cloud. L'application utilise l'émulateur de stockage, car le projet était configuré pour utiliser ce dernier lorsque vous l'avez téléchargé. Ce paramètre est stocké dans les fichiers *.cscfg* à l'intérieur du projet **AzureEmailService**. Le fichier *ServiceConfiguration.Local.cscfg* détermine ce qui est utilisé lorsque vous exécutez l'application localement dans Visual Studio, et le fichier *ServiceConfiguration.Cloud.cscfg* détermine ce qui est utilisé lorsque vous déployez l'application dans le cloud. Plus tard, vous verrez comment configurer l'application pour utiliser un compte Azure Storage.

14. Fermez le navigateur.

## <a name="StorageExpVS"></a>Affichage du stockage de développement dans Visual Studio

Le navigateur **Azure Storage** dans l'**Explorateur de serveurs** est un moyen direct et pratique d'utiliser les ressources Azure Storage.

1.  Dans le menu **Affichage** de Visual Studio, choisissez **Explorateur de serveurs**.

2.  Développez les nœuds **Azure** et **Stockage**, puis le nœud **(Développement)** sous le nœud **Azure Storage**.

    Si vous n'êtes pas déjà connecté à Azure dans Visual Studio, vous êtes invité à entrer vos informations d'identification Azure. Entrez celles correspondant à l'abonnement dans lequel vous souhaitez exécuter le service cloud.

3.  Développez **Tables** pour afficher les tables que vous avez créées lors des étapes précédentes.

    ![Explorateur de serveurs][]

4.  Double-cliquez sur la table **MailingList**.

    ![Explorateur de stockage VS][]

    Regardez comment la fenêtre affiche les différents schémas dans la table. Les entités `MailingList` ont les propriétés `Description` et `FromEmailAddress`, et les entités `Subscriber` la propriété `Verified` (plus `SubscriberGUID` qui n'est pas visible, l'image n'étant pas suffisamment large). La table comporte des colonnes pour toutes les propriétés, et si une ligne de la table concerne une entité qui n'a pas une propriété donnée, la cellule correspondante est vide.

Un autre outil pour exploiter les ressources Azure Storage est l'[Explorateur de stockage Azure][].

## <a name="createWASA"></a>Création d'un compte de stockage Azure

Lorsque vous exécutez l'exemple d'application dans Visual Studio, vous pouvez accéder aux tables, aux files d'attente et aux objets blob dans l'émulateur de stockage Azure ou dans un compte Azure Storage dans le cloud. Dans cette section du didacticiel, vous allez créer un compte Azure Storage. Dans une section ultérieure, vous configurerez Visual Studio pour qu'il utilise ce compte.

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure][].

2.  Dans le [portail de gestion Azure][], cliquez sur **Stockage**, puis cliquez sur **Nouveau**.

	![Nouveau stockage][]

1.  Cliquez sur **Create**.

	![Création rapide][]

1.  Dans la zone d'entrée de l'URL, entrez un préfixe d'URL.

    Ce préfixe, associé au texte visible sous la zone, sera l'URL unique de votre compte de stockage. Si le préfixe que vous entrez a déjà été utilisé par quelqu'un d'autre, un message au-dessus de la zone de texte vous indique que le nom de stockage est déjà utilisé. Vous devez alors choisir un autre préfixe.

2.  Définissez la région sur la zone dans laquelle vous voulez déployer l'application.

3.  Dans la liste déroulante **Réplication**, sélectionnez **Locally redundant**.

    Lorsque la géo-réplication est activée pour un compte de stockage, le contenu stocké est répliqué à un emplacement secondaire pour activer le basculement vers cet emplacement en cas de sinistre majeur à l'emplacement principal. La géo-réplication peut engendrer des coûts supplémentaires. Dans le cas des comptes test et de développement, vous êtes en général peu enclin à payer pour la géo-réplication. Pour plus d'informations, consultez la page [Gestion des comptes de stockage][].

4.  Cliquez sur **Create Storage Account**.

    Dans l'image, un compte de stockage est créé avec l'URL `aestest3.core.windows.net`.

    ![créer un stockage avec un préfixe d'URL][]

    Cette étape peut prendre plusieurs minutes. En attendant, vous pouvez répéter ces opérations et créer un compte de stockage de production. Il est souvent pratique d'avoir un compte de stockage test pour le développement local, un autre compte de stockage test pour les tests dans Azure et un compte de stockage de production.

5.  Cliquez sur le compte test que vous avez créé à l'étape précédente, puis cliquez sur l'icône **Manage Access Keys**.

    ![Gérer les clés][]

    ![GUID des clés][]

    Visual Studio configure automatiquement les chaînes de connexion avec l'une de ces clés lorsque vous sélectionnez le compte de stockage. Vous pouvez également mettre à jour les chaînes de connexion manuellement.

    L'existence de deux clés vous permet de changer périodiquement la clé utilisée sans provoquer une interruption du service pour une application. Vous pouvez régénérer la clé que vous n'utilisez pas, puis changer la chaîne de connexion dans votre application de manière à utiliser la clé régénérée. S'il n'y avait qu'une seule clé, l'application perdrait la connectivité au compte de stockage lors de la régénération de la clé. Les clés affichées dans l'image ne sont plus valides, car elles ont été régénérées après la capture de l'image.

## <a name="createcloudsvc"></a><span class="short-header">Création d'un service cloud</span>Création d'un service cloud

1.  Dans le [portail de gestion Azure][], cliquez sur **Services de cloud**, puis sur l'icône **Nouveau**.

    ![Cloud rapide][]

2.  Cliquez sur **Create**.

3.  Dans la zone d'entrée de l'URL, entrez un préfixe d'URL.

    À l'instar de l'URL de stockage, cette URL doit être unique ; un message d'erreur s'affiche si le préfixe choisi est déjà utilisé par quelqu'un d'autre.

4.  Définissez la région sur la zone dans laquelle vous voulez déployer l'application.

    Vous devez créer le service cloud dans la région où vous avez créé le compte de stockage. Lorsque le service cloud et le compte de stockage se trouvent dans des centres de données différents (différentes régions), la latence augmente et la bande passante en dehors du centre de données vous est facturée, alors qu'elle est gratuite dans un centre de données.

    Les groupes d'affinités Azure fournissent un mécanisme pour minimiser la distance entre les ressources dans un centre de données, ce qui peut réduire la latence. Ce didacticiel n'utilise pas de groupes d'affinités. Pour plus d'informations, consultez la page [Création d'un groupe d'affinités dans Azure][].

5.  Cliquez sur **Create Cloud Service**.

    Dans l'image suivante, un service cloud est créé avec l'URL aescloud.cloudapp.net.

    ![créer un stockage avec un préfixe d'URL][2]

## <a name="conf4azureStorage"></a><span class="short-header">Utilisation de votre compte de stockage</span>Configuration de l'application pour utiliser votre compte Azure Storage

Ensuite, vous allez apprendre à configurer l'application de telle sorte qu'elle utilise votre compte Azure Storage au lieu du stockage de développement lorsqu'elle s'exécute dans Visual Studio.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **MvcWebRole** sous **Rôles** dans le projet **AzureEmailService**, puis cliquez sur **Propriétés**.

    ![Clic droit, Propriétés][]

2.  Dans la fenêtre **MvcWebRole [Role]**, cliquez sur l'onglet **Paramètres**.

3.  Dans la liste déroulante **Configuration du service**, sélectionnez **Local**.

4.  Sélectionnez l'entrée **StorageConnectionString**. Un bouton représentant des points de suspension (**...**) apparaît à l'extrémité droite de la ligne. Cliquez dessus pour ouvrir la boîte de dialogue **Storage Account Connection String**.

    ![Clic droit, Propriétés][3]

5.  Dans la boîte de dialogue **Créer une chaîne de connexion de stockage**, cliquez sur **Votre abonnement**, et choisissez votre **Abonnement** et votre **Nom de compte** de stockage.

    ![Clic droit, Propriétés][4]

6.  Suivez la procédure que vous avez utilisée pour la chaîne de connexion `StorageConnectionString` pour définir la chaîne de connexion `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

7.  En suivant la même procédure que celle utilisée pour les deux chaînes de connexion du rôle MvcWebRole, définissez les chaînes de connexion pour les rôles WorkerRoleA et workerRoleB.

8.  Ouvrez le fichier **ServiceConfiguration.Local.cscfg** situé dans le projet **AzureEmailService**.

    L'élément `Role` de `MvcWebRole` contient un élément `ConfigurationSettings` avec les paramètres que vous avez mis à jour à l'aide de l'interface utilisateur de Visual Studio.

          <Role name="MvcWebRole">
            <Instances count="1" />
            <ConfigurationSettings>
              <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
              <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
            </ConfigurationSettings>
          </Role>

    Dans les éléments `Role` des deux rôles de travail, vous trouverez les deux mêmes chaînes de connexion.

    Vous pouvez modifier ces fichiers directement sans activer la fenêtre **[Role]** dans Visual Studio. Pour plus d'informations sur les fichiers de configuration, consultez la page [Configuration d'un projet Azure][]

### Test de l'application configurée pour utiliser votre compte de stockage

1.  Appuyez sur Ctrl+F5 pour exécuter l’application. Entrez des données en cliquant sur les liens **Listes de diffusion**, **Abonnés** et **Messages**, comme vous l'avez fait préalablement dans ce didacticiel.

2.  Dans Visual Studio, ouvrez l'**Explorateur de serveurs**.

3.  Développez le nœud **Stockage** sous le nœud **Azure**, puis développez le nœud pour le compte de stockage pour lequel l'application est configurée.

4.  Développez **Tables**, puis double-cliquez sur la table `MailingList` pour afficher les données que vous avez entrées sur les pages **Mailing List** et **Subscriber**.

### Étapes facultatives pour désactiver le démarrage automatique de l'émulateur de stockage Azure

Si vous n'utilisez pas l'émulateur de stockage, vous pouvez réduire le délai de démarrage du projet et utiliser moins de ressources locales en désactivant le démarrage automatique pour l'émulateur de stockage Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet cloud **AzureEmailService** et sélectionnez **Propriétés**.

    ![Sélection des propriétés du projet cloud][]

2.  Sélectionnez l'onglet **Développement**.

3.  Définissez **Start Azure storage emulator** sur **False**.

    ![Désactivation du démarrage automatique pour l'émulateur de stockage][]

    **Remarque** : exécutez cette dernière opération uniquement si vous n'utilisez pas l'émulateur de stockage.

    Cette fenêtre permet également de changer le fichier de **configuration du service** utilisé lorsque vous exécutez l'application localement en passant de **Local** à **Cloud** (de *ServiceConfiguration.Local.cscfg* à *ServiceConfiguration.Cloud.cscfg*).

4.  Dans la zone de notification Windows, cliquez avec le bouton droit sur l'émulateur de calcul et cliquez sur **Shutdown Storage Emulator**.

    ![ASE][]

## <a name="sendGrid"></a><span class="short-header">SendGrid</span>Configuration de l'application pour l'utilisation de SendGrid

L'exemple d'application utilise SendGrid pour l'envoi des courriers électroniques. Pour envoyer les courriers électroniques avec SendGrid, vous devez configurer un compte SendGrid, puis mettre à jour un fichier de configuration avec vos informations d'identification SendGrid.

<div class="note"><p><strong>Remarque&nbsp;:</strong> si vous ne voulez pas ou ne pouvez pas utiliser SendGrid, vous pouvez ais&eacute;ment utiliser &agrave; la place votre propre service de messagerie. Le code qui utilise SendGrid est isol&eacute; dans deux m&eacute;thodes dans le r&ocirc;le de travail&nbsp;B. Le <a href="/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/">didacticiel 5</a> explique ce que vous devez modifier pour mettre en &oelig;uvre une autre m&eacute;thode d'envoi des courriers &eacute;lectroniques. Dans ce cas, vous pouvez ignorer cette proc&eacute;dure et poursuivre le didacticiel&nbsp;; tout le reste de l'application fonctionnera (pages Web, planification des courriers &eacute;lectroniques, etc.), &agrave; l'exception de l'envoi des courriers &eacute;lectroniques.</p></div>

### Création d'un compte SendGrid

1.  Suivez les instructions de la page [Envoi de courriers électroniques à l'aide de SendGrid avec Azure][] pour créer un compte gratuit.

### Mise à jour des informations d'identification de SendGrid dans les propriétés du rôle de travail

Dans l'une des précédentes sections du didacticiel, lorsque vous avez défini les informations d'identification du compte de stockage pour le rôle Web et les deux rôles de travail, vous avez peut-être remarqué que le rôle de travail B possédait trois paramètres absents du rôle Web ou du rôle de travail A. Vous pouvez maintenant utiliser la même interface utilisateur pour configurer ces paramètres (sélectionnez **Cloud** dans la liste déroulante **Configuration du service**).

Les étapes suivantes présentent une autre méthode de définition des propriétés, consistant à modifier le fichier de configuration.

1.  Modifiez le fichier *ServiceConfiguration.Cloud.cscfg* dans le projet `AzureEmailService` et entrez les valeurs de nom d'utilisateur et de mot de passe SendGrid que vous avez obtenues à l'étape précédente dans l'élément `WorkerRoleB` qui inclut ces paramètres. Le code suivant concerne l'élément WorkerRoleB.

    ![SendGridSettings][]

2.  Il existe également un paramètre AzureMailServiceURL. Définissez cette valeur sur l'URL que vous avez sélectionnée à la création de votre service cloud Azure, par exemple : "<http://aescloud.cloudapp.net> ».

En mettant à jour le fichier de configuration du service cloud, vous configurez les paramètres qui seront utilisés lorsque l'application sera exécutée dans le cloud. Si vous voulez que l'application envoie des courriers électroniques pendant qu'elle s'exécute localement, vous devez également mettre à jour le fichier *ServiceConfiguration.Local.cscfg*.

## <a name="deployAz"></a><span class="short-header">Déploiement sur Azure</span>Déploiement de l'application sur Azure

Pour déployer l'application, vous pouvez créer un package dans Visual Studio et le charger à l'aide du portail de gestion Azure, ou effectuer la publication directement à partir de Visual Studio. Dans ce didacticiel, vous allez utiliser la méthode de publication.

Vous allez commencer par publier l'application dans l'environnement intermédiaire, puis vous ferez passer le déploiement intermédiaire en production.

### Mise en œuvre des restrictions d'adresse IP

Lors du déploiement intermédiaire, l'application est publiquement accessible à toute personne qui connaît l'URL. Par conséquent, la première étape consiste à mettre en œuvre les restrictions d'adresse IP pour s'assurer qu'aucune personne non autorisée ne pourra s'en servir. Dans une application de production, vous mettez en place un mécanisme d'authentification et d'autorisation comme l'identité ASP.NET, mais ces fonctions ont été omises dans l'exemple d'application pour simplifier sa configuration, son déploiement et les tests.

1.  Ouvrez le fichier *Web.Release.config* situé dans le dossier racine du projet `MvcWebRole` et remplacez la valeur 127.0.0.1 de l'attribut **ipAddress** par votre adresse IP. Pour voir le fichier **Web.Release.config** dans l'**Explorateur de solutions**, vous devez développer le fichier *Web.config*.

    Vous pouvez trouver votre adresse IP en recherchant « Trouver mon adresse IP » avec [Bing][] ou un autre moteur de recherche.

    Lorsque l'application est publiée, les transformations spécifiées dans le fichier *Web.release.config* sont appliquées et les éléments de restriction de l'adresse IP sont mis à jour dans le fichier *web.config* qui est déployé dans le cloud. Vous pouvez afficher le fichier *web.config* transformé dans le dossier *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* après la création du package.

### Configuration de l'application pour utiliser votre compte de stockage lorsqu'elle s'exécute dans le cloud

Dans l'une des précédentes sections du didacticiel, lorsque vous avez défini les informations d'identification du compte de stockage pour le rôle Web et les deux rôles de travail, vous avez défini les informations d'identification à utiliser lorsque vous exécutez l'application localement. Vous devez à présent définir les informations d'identification du compte de stockage à utiliser lorsque vous exécutez l'application dans le cloud.

Pour ce test, vous allez utiliser pour le cloud les mêmes informations d'identification que celles employées pour l'exécution locale. Si vous déployez une application de production, normalement, vous n'utiliserez pas le même compte que pour les tests. Il est également recommandé d'utiliser en production des comptes différents pour la chaîne de connexion de diagnostic et pour la chaîne de connexion de stockage. Pour les besoins du test, vous utiliserez cependant le même compte.

Vous pouvez utiliser la même interface utilisateur pour configurer les chaînes de connexion (veillez simplement à sélectionner **Cloud** dans la liste déroulante **Configuration du service**). Une autre solution consiste à modifier le fichier de configuration, comme décrit aux étapes suivantes.

1.  Ouvrez le fichier *ServiceConfiguration.Local.cscfg* dans le projet **AzureEmailService** et copiez les éléments `Setting` pour `StorageConnectionString` et `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

2.  Ouvrez le fichier *ServiceConfiguration.Cloud.cscfg* du projet **AzureEmailService**, puis collez les éléments copiés dans l'élément `Configuration Settings` pour `MvcWebRole`, `WorkerRoleA`, et `WorkerRoleB`, en remplacement des éléments `Setting` présents.

3.  Vérifiez que les éléments du rôle Web et des deux rôles de travail définissent tous les mêmes chaînes de connexion.

### Publication de l'application

1.  S'il n'est pas déjà ouvert, lancez Visual Studio et ouvrez la solution **AzureEmailService**.

2.  Cliquez avec le bouton droit sur le projet cloud **AzureEmailService** et sélectionnez **Publier**.

    ![Package][]

    La boîte de dialogue **Publish Azure Application** s'affiche.

    ![Package Cloud][]

3.  Si vous avez utilisé la méthode automatique pour importer les informations d'identification du compte de stockage précédemment, votre abonnement Azure apparaît dans la liste déroulante et vous pouvez le sélectionner, puis cliquer sur **Suivant**. Sinon, cliquez sur **Sign in to download credentials** et suivez les instructions décrites à la page [Configuration de l'application pour Azure Storage][] pour télécharger et importer vos paramètres de publication.

4.  Sous l'onglet **Paramètres communs**, vérifiez que le service cloud est sélectionné dans la liste déroulante **Service cloud**.

5.  Dans la liste déroulante **Environnement**, sélectionnez **Intermédiaire** à la place de **Production**.

    ![Tableau de bord][]

6.  Conservez les paramètres par défaut **Release** pour **Configuration de build** et **Cloud** pour **Configuration du service**.

    Les paramètres par défaut sous l'onglet **Advanced** conviennent pour ce didacticiel. Plusieurs paramètres utiles pour le développement et les tests sont regroupés sous l'onglet **Advanced**. Pour plus d'informations sur cet onglet, consultez la page [Assistant Publication d'application Azure][].

7.  Cliquez sur **Suivant**.

8.  À l'étape **Résumé** de l'Assistant, cliquez sur l'icône **enregistrer** (icône représentant une disquette affichée à droite de la liste déroulante Profil cible) pour enregistrer les paramètres de publication.

    À la prochaine publication de l'application, les paramètres enregistrés seront utilisés et vous n'aurez plus besoin de parcourir les étapes de l'Assistant Publication.

9.  Passez en revue les paramètres, puis cliquez sur **Publier**.

    ![pub][]

	La fenêtre **Journal des activités Azure** s'ouvre dans Visual Studio.

1.  Cliquez sur l'icône représentant une flèche vers la droite pour développer les détails du déploiement.

    ![pub][5]

    ![pub][6]

    Le déploiement peut durer environ 5 minutes, voire plus.

2.  Lorsque le déploiement est terminé, cliquez sur l'**URL du site Web** pour lancer l'application.

    ![Tableau de bord][7]

3.  Entrez des données sur les pages Web **Liste de diffusion**, **Abonné** et **Message** pour tester l'application.

    **Remarque** : supprimez l'application une fois que vous l'avez testée afin d'éviter de payer pour des ressources que vous n'utilisez pas. Si vous utilisez un [compte d'évaluation Azure gratuit][], les trois rôles déployés consommeront l'équivalent de votre limite mensuelle en une quinzaine de jours. Pour supprimer un déploiement avec le portail de gestion Azure, sélectionnez le service cloud et cliquez sur **SUPPRIMER** en bas de la page, puis sélectionnez le déploiement de production ou intermédiaire.

    ![pub][8]

4.  Dans le journal des activités Azure de Visual studio, sélectionnez **Ouvrir dans l'Explorateur de serveurs**.

    Sous **Calcul Azure**, dans l'**Explorateur de serveurs**, vous pouvez surveiller le déploiement. Si vous avez sélectionné **Enable Remote Desktop for all roles** dans l'Assistant **Publish Azure Application**, vous pouvez cliquer avec le bouton droit sur une instance de rôle et sélectionner **Connect using Remote Desktop**.

    ![pub][9]

## <a name="swap"></a>Passage de l'application de la gestion intermédiaire à la production

1.  Dans le [portail de gestion Azure][], cliquez sur l'icône **Services de cloud** dans le volet gauche, puis sélectionnez votre service cloud et cliquez sur l'onglet **Tableau de bord**.

2.  Cliquez sur **Swap**.

3.  Cliquez sur **Oui** pour terminer l'échange de l'adresse IP virtuelle. Cette étape peut prendre plusieurs minutes.

    ![Tableau de bord][10]

4.  Lorsque l'échange est terminé, faites défiler l'onglet **Tableau de bord** du déploiement **Production** jusqu'à la section **Aperçu rapide** en bas à droite de la page. Comme vous pouvez le remarquer, l'**URL du site** n'affiche plus un préfixe GUID, mais le nom de votre service cloud.

    ![Tableau de bord][11]

5.  Cliquez sur le lien sous **URL du site** ou copiez-le et collez-le dans un navigateur pour tester l'application en production.

    Si vous n'avez pas changé les paramètres du compte de stockage, les données que vous avez entrées lors du test de la version intermédiaire de l'application s'affichent lorsque vous exécutez l'application dans le cloud.

## <a name="trace"></a>Configuration et affichage des données de suivi

Le suivi est un outil inestimable pour le débogage d'une application cloud. Dans cette section du didacticiel, vous allez apprendre à afficher les données de suivi.

1.  Vérifiez que la chaîne de connexion de diagnostic est configurée pour utiliser votre compte Azure Storage, et non le stockage de développement.

    Si vous avez suivi les instructions dans les précédentes sections de ce didacticiel, elles seront identiques. Vous pouvez le vérifier soit en utilisant l'interface utilisateur de Visual Studio (onglet **Paramètres** dans les **Propriétés** des rôles), soit en examinant les fichiers *ServiceConfiguration.\*.cscfg*.

	**Remarque :** il est recommandé d'utiliser des comptes de stockage différents pour les données de suivi et pour les données de production. Par souci de simplification, cependant, c'est le même compte qui a été configuré pour le suivi dans ce didacticiel.

1.  Dans Visual Studio, ouvrez *WorkerRoleA.cs* dans le projet **WorkerRoleA**, recherchez `ConfigureDiagnostics` et examinez la méthode `ConfigureDiagnostics`.

        private void ConfigureDiagnostics()
        {
            DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
            config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
            config.Logs.BufferQuotaInMB = 500;
            config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
            config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

            DiagnosticMonitor.Start(
                "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                config);
        }

    Dans ce code, `DiagnosticMonitor` est configuré pour stocker jusqu'à 500 Mo d'informations de suivi (au-delà de 500 Mo, les données les plus anciennes sont remplacées) et pour stocker tous les messages de suivi (LogLevel.Verbose). `ScheduledTransferPeriod` transfère les données de suivi vers le stockage toutes les minutes. Vous devez définir `ScheduledTransferPeriod` pour enregistrer les données de suivi.

    La méthode `ConfigureDiagnostics` dans chacun des rôles de travail et web configure l'écouteur de suivi pour enregistrer les données lorsque vous appelez l'API de suivi. Pour plus d'informations, consultez la page [Utilisation du suivi dans les applications cloud Azure][]

2.  Dans l'**Explorateur de serveurs**, double-cliquez sur **WADLogsTable** (développez **Azure** / **Stockage** / **nom\_de\_votre\_compte\_de\_stockage** / **Tables**) pour le compte de stockage que vous avez ajouté précédemment. Vous pouvez entrer un [filtre Services de données WCF][] pour limiter les entités affichées. Dans l'image suivante, seuls les avertissements et les messages d'erreur sont affichés.

    ![Tableau de bord][12]

## <a name="addRole"></a>Ajout d'une autre instance de rôle de travail pour traiter une charge accrue

Pour mettre à l'échelle les ressources de calcul dans les rôles Azure, vous avez le choix entre deux approches : spécifier [la taille des machines virtuelles][] et/ou spécifier le nombre d'instances de machines virtuelles en cours d'exécution.

La taille des machines virtuelles est spécifiée dans l'attribut `vmsize` de l'élément `WebRole` ou `WorkerRole` dans le fichier *ServiceDefinition.csdef*. Le paramètre par défaut est `Small`, ce qui correspond à un cœur et 1,75 Go de RAM. Dans le cas des applications multithread qui utilisent beaucoup de mémoire, d'espace disque et de bande passante, vous pouvez augmenter la taille des machines virtuelles pour bénéficier de performances accrues. Par exemple, une machine virtuelle `ExtraLarge` comporte 8 cœurs d'UC et 14 Go de RAM. L'augmentation de la mémoire, du nombre de cœurs d'UC et de la bande passante sur une même machine est désignée par le terme *augmentation d'échelle*. Les bons candidats pour une augmentation d'échelle incluent les applications Web ASP.NET qui utilisent [des méthodes asynchrones][]. Pour obtenir une description des ressources fournies par chaque taille de machine virtuelle, consultez la page [Taille des machines virtuelles][la taille des machines virtuelles].

Le rôle de travail B dans cette application est le composant restrictif sous une charge élevée, car le travail d'envoi des courriers électroniques lui incombe. Le rôle de travail A crée simplement des messages de file d'attente, qui ne mobilisent pas beaucoup de ressources. Dans la mesure où le rôle de travail B n'est pas multithread et n'a pas un grand encombrement mémoire, il n'est pas un bon candidat pour une augmentation d'échelle. La mise à l'échelle du rôle de travail B peut se faire de manière linéaire (les performances sont presque multipliées par deux lorsque le nombre d'instances double) en augmentant le nombre d'instances. L'augmentation du nombre d'instances de calcul est appelée *montée en charge*. Chaque instance a un coût ; aussi la montée en charge doit-elle avoir lieu uniquement quand votre application l'exige.

Vous pouvez effectuer la montée en charge d'un rôle Web ou de travail en mettant à jour le paramètre dans l'interface utilisateur de Visual Studio ou en modifiant directement les fichiers *ServiceConfiguration.\*.cscfg*. Le nombre d'instances est spécifié sous l'onglet **Configuration** de la fenêtre **Propriétés** du rôle et dans l'élément `Instances` à l'intérieur des fichiers *.cscfg*. Lorsque vous mettez à jour le paramètre, vous devez déployer le fichier de configuration mis à jour pour que la modification soit appliquée. Ou bien, pour les augmentations temporaires de la charge, vous pouvez modifier le nombre d'instances de rôle manuellement ou configurer Azure pour modifier automatiquement le nombre d'instances en fonction de critères que vous définissez. Pour plus d'informations sur la mise à l'échelle automatique, consultez [le dernier didacticiel de cette série][].

Dans cette section du didacticiel, vous allez configurer la montée en charge du rôle de travail B en utilisant le portail de gestion, après avoir vu comment procéder dans Visual Studio.

Dans Visual Studio, vous devez d'abord cliquer avec le bouton droit sur le rôle sous **Rôles** dans le projet cloud et cliquer sur **Propriétés**.

![Clic droit, Propriétés][]

Vous devez ensuite sélectionner l'onglet **Configuration** à gauche et sélectionner **Cloud** dans la liste déroulante **Configuration du service**.

![Nombre d'instances][]

Comme vous pouvez le remarquer, il est également possible de configurer la taille des machines virtuelles sous cet onglet.

Les étapes suivantes expliquent comment effectuer la montée en charge à l'aide du portail de gestion Azure.

1.  Dans le portail de gestion Azure, sélectionnez votre service cloud, puis cliquez sur **Scale**.

2.  Augmentez le nombre d'instances pour le rôle de travail B, puis cliquez sur **Enregistrer**.

    ![augmenter le nombre d'instances][]

    La mise en service des nouvelles machines virtuelles peut prendre plusieurs minutes.

3.  Sélectionnez l'onglet **Instances** pour afficher chaque instance de rôle dans votre application.

    ![afficher les instances][]

## <a name="nextsteps"></a>Étapes suivantes

Vous savez maintenant comment configurer, déployer et mettre à l'échelle l'application terminée. Les didacticiels suivants montrent comment créer l'application à partir de zéro. Dans le [didacticiel suivant][], vous allez générer le rôle Web.

Pour obtenir des liens sur les ressources supplémentaires pour utiliser les tables, les files d'attente et les objets blob Azure Storage, consultez le [dernier didacticiel de cette série][].

<div><a href="/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">Didacticiel&nbsp;3</a></div>

  [premier didacticiel de la série]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Configuration de l’environnement de développement]: #setupdevenv
  [Afficher le stockage de développement dans Visual Studio]: #StorageExpVS
  [Téléchargement et exécution de la solution terminée]: #downloadcnfg
  [Création d’un compte de stockage Azure]: #createWASA
  [Création d'un service cloud]: #createcloudsvc
  [Configuration de l'application pour Azure Storage]: #conf4azureStorage
  [Configuration de l'application pour l'utilisation de SendGrid]: #sendGrid
  [Déploiement de l'application dans Azure]: #deployAz
  [Passage de l'application de la gestion intermédiaire à la production]: #swap
  [Configuration et affichage des données de suivi]: #trace
  [Ajout d'une autre instance de rôle de travail pour traiter une charge accrue]: #addRole
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [solution terminée]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [Exécuter l'application]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
  [did5]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
  [Page d'index de la liste de diffusion]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
  [Page d'index de l'abonné]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
  [Page de création du message]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
  [Page d'index du message]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
  [Explorateur de serveurs]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
  [Explorateur de stockage VS]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
  [Explorateur de stockage Azure]: http://azurestorageexplorer.codeplex.com/
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Nouveau stockage]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
  [Création rapide]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
  [Gestion des comptes de stockage]: /en-us/manage/services/storage/how-to-manage-a-storage-account/
  [créer un stockage avec un préfixe d'URL]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
  [Gérer les clés]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
  [GUID des clés]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
  [Cloud rapide]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
  [Création d'un groupe d'affinités dans Azure]: http://msdn.microsoft.com/en-us/library/jj156209.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
  [Clic droit, Propriétés]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
  [3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
  [Configuration d'un projet Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405486.aspx
  [Sélection des propriétés du projet cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
  [Désactivation du démarrage automatique pour l'émulateur de stockage]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
  [ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png
  [Envoi de courriers électroniques à l'aide de SendGrid avec Azure]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid"
  [SendGridSettings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
  [Bing]: http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "trouver mon adresse IP"
  [Package]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
  [Package Cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
  [Tableau de bord]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
  [Assistant Publication d'application Azure]: http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "assistant publication"
  [pub]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
  [6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png
  [compte d'évaluation Azure gratuit]: http://www.windowsazure.com/en-us/pricing/free-trial/ "compte d'évaluation gratuit"
  [8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png
  [Utilisation du suivi dans les applications cloud Azure]: http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Utilisation du suivi dans Azure"
  [filtre Services de données WCF]: http://msdn.microsoft.com/en-us/library/windowsazure/ff683669.aspx "filtre WCF"
  [12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
  [la taille des machines virtuelles]: http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "Tailles des machines virtuelles"
  [des méthodes asynchrones]: http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "Async MVC"
  [le dernier didacticiel de cette série]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [Nombre d'instances]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png
  [augmenter le nombre d'instances]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
  [afficher les instances]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
  [didacticiel suivant]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [dernier didacticiel de cette série]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
