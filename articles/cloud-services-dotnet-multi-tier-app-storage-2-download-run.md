<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" urlDisplayName="Step 2: Download and Run" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 2: Download and run" metaKeywords="Azure tutorial, deploying email service app, publishing email service" description="The second tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Configuring and Deploying the Azure Email Service application - 2 of 5" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

Configuration et déploiement de l'application Azure Email Service - 2 sur 5
===========================================================================

Ce didacticiel, le deuxième d'une série de cinq, explique comment générer et déployer l'exemple d'application Azure Email Service. Pour obtenir des informations sur l'application et la série de didacticiels, consultez le [premier didacticiel de la série](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

Ce didacticiel explique comment configurer votre ordinateur pour le développement Azure et comment déployer l'application Azure Email Service sur un service cloud Azure avec l'un des produits suivants :

-   Visual Studio 2012
-   Visual Studio 2012 Express pour le Web
-   Visual Studio 2010
-   Visual Web Developer Express 2010

> [WACOM.NOTE] Le lancement de Visual Studio 2013 et une mise à jour du portail de gestion et du Kit de développement logiciel (SDK) Azure ont eu lieu après la rédaction de ce didacticiel. Des remarques comme celle-ci ont été ajoutées aux points où vous devez procéder différemment si vous utilisez Visual Studio 2013 et le dernier Kit de développement logiciel (SDK). Ces remarques ont été rédigées en mars 2014 et la procédure révisée a été testée avec la version 2.3 du Kit de développement logiciel (SDK).

Vous pouvez ouvrir gratuitement un compte Azure. Si vous n'avez pas déjà Visual Studio 2013, le Kit de développement logiciel (SDK) installe automatiquement Visual Studio Express 2013 pour le Web. Vous pouvez donc commencer vos développements Azure gratuitement.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   configurer votre ordinateur pour le développement Azure en installant le Kit de développement logiciel (SDK) Azure ;
-   configurer et tester l'application Azure Email Service sur votre ordinateur local ;
-   publier l'application sur Azure ;
-   afficher et modifier les tables, files d'attente et objets blob Azure avec Visual Studio ou l'Explorateur de stockage Azure ;
-   configurer le suivi et afficher les données associées ;
-   mettre à l'échelle l'application par augmentation du nombre d'instances de rôle de travail.

**Remarque**

Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Vous pouvez créer un [compte d'évaluation gratuit](/en-us/pricing/free-trial/) ou [activer les avantages de votre abonnement MSDN](/en-us/pricing/member-offers/msdn-benefits/).

### Sections du didacticiel

-   [Configuration de l'environnement de développement](#setupdevenv)
-   [Configuration d'un compte Azure gratuit](#setupwindowsazure)
-   [Création d’un compte Azure Storage](#createWASA)
-   [Installation de l'Explorateur de stockage Azure](#installASE)
-   [Création d'un service cloud](#createcloudsvc)
-   [Téléchargement et exécution de la solution terminée](#downloadcnfg)
-   [Affichage des ressources de stockage des développeurs dans Visual Studio](#StorageExpVS)
-   [Configuration de l'application pour Azure Storage](#conf4azureStorage)
-   [Déploiement de l'application dans Azure](#deployAz)
-   [Passage de l'application de la gestion intermédiaire à la production](#swap)
-   [Configuration de l'application pour l'utilisation de SendGrid](#sendGrid)
-   [Configuration et affichage des données de suivi](#trace)
-   [Ajout d'une autre instance de rôle de travail pour traiter une charge accrue](#addRole)

> [WACOM.NOTE] Dans la section suivante consacrée à l'installation du Kit de développement logiciel (SDK), le lien correct si vous utilisez Visual Studio 2013 est <http://go.microsoft.com/fwlink/?LinkID=324322>.

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

Création d'un compte StorageCréation d'un compte de stockage Azure
------------------------------------------------------------------

Lorsque vous exécutez l'exemple d'application dans Visual Studio, vous pouvez accéder aux tables, aux files d'attente et aux objets blob dans le stockage de développement Azure ou dans un compte Azure Storage dans le cloud. Le stockage de développement utilise une base de données SQL Server Express LocalDB pour émuler le fonctionnement de Azure Storage dans le cloud. Dans ce didacticiel, vous commencerez par utiliser le stockage de développement, puis vous apprendrez à configurer l'application pour utiliser un compte de stockage sur le cloud lorsqu'elle s'exécute dans Visual Studio. Dans cette section du didacticiel, vous allez créer un compte Azure Storage. Dans une section ultérieure, vous configurerez Visual Studio pour qu'il utilise ce compte.

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure](http://manage.windowsazure.com).

2.  Dans le [portail de gestion Azure](http://manage.windowsazure.com), cliquez sur **Stockage**, puis cliquez sur **Nouveau**.

    ![Nouveau stockage](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png)

3.  Cliquez sur **Quick Create**.

    ![Création rapide](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png)

4.  Dans la zone d'entrée de l'URL, entrez un préfixe d'URL.

    Ce préfixe, associé au texte visible sous la zone, sera l'URL unique de votre compte de stockage. Si le préfixe que vous entrez a déjà été utilisé par quelqu'un d'autre, un message au-dessus de la zone de texte vous indique que le nom de stockage est déjà utilisé. Vous devez alors choisir un autre préfixe.

5.  Définissez la région sur la zone dans laquelle vous voulez déployer l'application.

6.  Dans la liste déroulante **Réplication**, sélectionnez **Locally redundant**.

    Lorsque la géo-réplication est activée pour un compte de stockage, le contenu stocké est répliqué à un emplacement secondaire pour activer le basculement vers cet emplacement en cas de sinistre majeur à l'emplacement principal. La géo-réplication peut engendrer des coûts supplémentaires. Dans le cas des comptes test et de développement, vous êtes en général peu enclin à payer pour la géo-réplication. Pour plus d'informations, consultez la page [Gestion des comptes de stockage](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

7.  Cliquez sur **Create Storage Account**.

    Dans l'image ci-dessous, un compte de stockage est créé avec l'URL `aestest3.core.windows.net`.

    ![créer un stockage avec un préfixe d'URL](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png)

    Cette étape peut prendre plusieurs minutes. En attendant, vous pouvez répéter ces opérations et créer un compte de stockage de production. Il est souvent pratique d'avoir un compte de stockage test pour le développement local, un autre compte de stockage test pour les tests dans Azure et un compte de stockage de production.

8.  Cliquez sur le compte test que vous avez créé à l'étape précédente, puis cliquez sur l'icône **Manage Access Keys**.

    ![Gérer les clés](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png)

    ![GUID des clés](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG)

    Pendant toute la durée de ce didacticiel, vous aurez besoin d'une **Clé d'accès primaire** ou d'une **Clé d'accès secondaire**. Vous pouvez utiliser l'une ou l'autre de ces clés dans une chaîne de connexion de stockage.

    L'existence de deux clés vous permet de changer périodiquement la clé utilisée sans provoquer une interruption du service pour une application. Vous pouvez régénérer la clé que vous n'utilisez pas, puis changer la chaîne de connexion dans votre application de manière à utiliser la clé régénérée. S'il n'y avait qu'une seule clé, l'application perdrait la connectivité au compte de stockage lors de la régénération de la clé. Les clés affichées dans l'image ne sont plus valides, car elles ont été régénérées après la capture de l'image.

9.  Copiez l'une de ces clés dans le Presse-papiers pour pouvoir l'utiliser dans la section suivante.

Installation d'ASEInstallation de l'Explorateur de stockage Azure
-----------------------------------------------------------------

L'**Explorateur de stockage Azure** (ASE) est un outil qui permet d'interroger et de mettre à jour les tables de stockage, files d'attente et objets blob Azure. Vous allez vous en servir tout au long de ces didacticiels pour vérifier que les données sont mises à jour correctement et pour créer des données de test.

1.  Installez l'[Explorateur de stockage Azure](http://azurestorageexplorer.codeplex.com/).

    Les captures d'écran affichent ASE 4, mais vous pouvez installer ASE 5 si vous préférez.

2.  Lancez l'**Explorateur de stockage Azure**, puis cliquez sur **Ajouter un compte**.

    ![Ajouter un compte ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add.png)

3.  Entrez le nom du compte de stockage test et collez la clé que vous avez copiée précédemment.

4.  Cliquez sur **Add Storage Account**.

    ![Ajouter un compte ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add2.png)

D'autres outils fonctionnant avec Azure Storage sont également disponibles. Pour plus d'informations, consultez la page [Explorateurs de stockage Azure (2014)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx).

Création d'un service cloudCréation d'un service cloud
------------------------------------------------------

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure](http://manage.windowsazure.com).

2.  Cliquez sur **Cloud Services**, puis sur l'icône **Nouveau**.

    ![Cloud rapide](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png)

3.  Cliquez sur **Quick Create**.

4.  Dans la zone d'entrée de l'URL, entrez un préfixe d'URL.

    À l'instar de l'URL de stockage, cette URL doit être unique ; un message d'erreur s'affiche si le préfixe choisi est déjà utilisé par quelqu'un d'autre.

5.  Définissez la région sur la zone dans laquelle vous voulez déployer l'application.

    Vous devez créer le service cloud dans la région où vous avez créé le compte de stockage. Lorsque le service cloud et le compte de stockage se trouvent dans des centres de données différents (différentes régions), la latence augmente et la bande passante en dehors du centre de données vous est facturée, alors qu'elle est gratuite dans un centre de données.

    Les groupes d'affinités Azure fournissent un mécanisme pour minimiser la distance entre les ressources dans un centre de données, ce qui peut réduire la latence. Ce didacticiel n'utilise pas de groupes d'affinités. Pour plus d'informations, consultez la page [Création d'un groupe d'affinités dans Azure](http://msdn.microsoft.com/fr-fr/library/jj156209.aspx).

6.  Cliquez sur **Create Cloud Service**.

    Dans l'image suivante, un service cloud est créé avec l'URL aescloud.cloudapp.net.

    ![créer un stockage avec un préfixe d'URL](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png)

    Vous pouvez passer à l'étape suivante sans attendre que celle-ci soit terminée.

Téléchargement et exécutionTéléchargement et exécution de la solution terminée
------------------------------------------------------------------------------

1.  Téléchargez et décompressez la [solution terminée](http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36).

2.  Démarrez Visual Studio avec des autorisations élevées.

    L'émulateur de calcul qui permet à Visual Studio d'exécuter un projet Azure localement exige des autorisations élevées.

    > [WACOM.NOTE] Avec le Kit de développement logiciel (SDK) 2.3 et les versions ultérieures, l'émulateur de calcul Azure par défaut n'exige pas d'autorisations élevées, mais ce projet est encore configuré pour utiliser l'émulateur de calcul d'origine et il nécessite donc des autorisations élevées.

3.  Restaurez les packages NuGet.

    Pour que la taille du téléchargement reste petite, la solution terminée est fournie sans les assemblys et sans autre contenu pour les packages NuGet installés. Lorsque vous ouvrez et générez la solution, NuGet obtient automatiquement tout le contenu du package. Pour que cela fonctionne, vous devez activer l'option de restauration des packages NuGet dans Visual Studio. Si vous ne l'avez pas encore fait, procédez comme suit.

    > [WACOM.NOTE] Dans Visual Studio 2013, le paramètre par défaut consiste à activer la restauration automatique des packages, mais dans la mesure où vous ouvrez un projet Visual Studio 2012, la restauration n'aura pas lieu automatiquement. Attendez d'avoir ouvert la solution, puis ouvrez la boîte de dialogue **Manage NuGet Packages**, comme indiqué ci-après. Un bouton **Restore** apparaît en haut à droite : cliquez dessus pour restaurer les packages.

         1. Dans le menu **Outils**, cliquez sur **Library Package Manager**, puis sur **Manage NuGet Packages for Solution**. 

         2. Dans le coin inférieur gauche de la boîte de dialogue **Manage NuGet Packages**, cliquez sur **Paramètres**.

         3. Dans le volet gauche de la boîte dialogue **Options**, sélectionnez **Général** sous **Gestionnaire de package**.

         4. Sélectionnez **Allow NuGet to download missing packages during build**.

    ![Activation de la restauration des packages NuGet](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/NuGetPkgRestore.png)

4.  Dans le menu **Fichier**, choisissez **Ouvrir un projet**, accédez à l'emplacement où vous avez téléchargé la solution, puis ouvrez le fichier solution.

5.  Dans l'**Explorateur de solutions**, assurez-vous que **AzureEmailService** est sélectionné comme projet de démarrage.

6.  Appuyez sur Ctrl+F5 pour exécuter l'application.

    > [WACOM.NOTE] Pour pouvoir générer la solution, vous devez ajouter des références aux assemblys du Kit de développement logiciel (SDK) actuel. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet MvcWebRole, puis cliquez sur **Ajouter** -- **Référence**. Sous **Assemblys**, cliquez sur **Extensions**. Sélectionnez *Microsoft.WindowsAzure.Diagnostics* et *Microsoft.WindowsAzure.ServiceRuntime*, puis cliquez sur **OK**. Procédez de même pour les projets WorkerRoleA et WorkerRoleB.

    La page d'accueil de l'application apparaît dans votre navigateur.

    ![Exécuter l'application](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png)

7.  Cliquez sur **Create New**.

8.  Entrez quelques données de test, puis cliquez sur **Créer**.

    ![Exécuter l'application](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png)

9.  Créez encore quelques entrées de la liste de diffusion.

    ![Page d'index de la liste de diffusion](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png)

10. Cliquez sur **Abonnés**, puis ajoutez quelques abonnés. Définissez **Vérifié** sur `true`.

    ![Page d'index de l'abonné](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png)

11. Préparez l'ajout de messages en créant un fichier *.txt* contenant le corps d'un courrier électronique que vous souhaitez envoyer. Créez ensuite un fichier *.htm* contenant le même texte, mais avec un peu de HTML (par exemple, mettez l'un des mots du message en gras ou en italique). Vous utiliserez ces fichiers à l'étape suivante.

12. Cliquez sur **Messages**, puis ajoutez quelques messages. Sélectionnez les fichiers que vous avez créés à l'étape précédente. Ne changez pas la date planifiée qui, par défaut, arrive à échéance dans une semaine. L'application ne peut pas envoyer de messages tant que vous n'avez pas configuré SendGrid.

    ![Page de création des messages](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png)
	<br/><br/>
     ![Page d'index des messages](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png)

Les données que vous avez entrées et affichées sont stockées dans le stockage de développement Azure. Le stockage de développement utilise une base de données SQL Server Express LocalDB pour émuler le fonctionnement de Azure Storage dans le cloud. L'application utilise le stockage de développement, car le projet était configuré pour utiliser ce dernier lorsque vous l'avez téléchargé. Ce paramètre est stocké dans les fichiers *.cscfg* à l'intérieur du projet **AzureEmailService**. Le fichier *ServiceConfiguration.Local.cscfg* détermine ce qui est utilisé lorsque vous exécutez l'application localement dans Visual Studio, et le fichier *ServiceConfiguration.Cloud.cscfg* détermine ce qui est utilisé lorsque vous déployez l'application dans le cloud. Plus tard, vous verrez comment configurer l'application pour utiliser le compte Azure Storage que vous avez créé précédemment.

Ressources de stockage des développeursAffichage des ressources de stockage des développeurs dans Visual Studio
---------------------------------------------------------------------------------------------------------------

Le navigateur **Azure Storage** dans l'**Explorateur de serveurs** fournit une vue en lecture seule pratique des ressources Azure Storage.

1.  Dans le menu **Affichage** de Visual Studio, choisissez **Explorateur de serveurs**.

2.  Développez le nœud **(Développement)** au-dessous du nœud **Azure Storage**.

    > [WACOM.NOTE] Avec le Kit de développement logiciel (SDK) actuel, développez **Azure** / **Stockage** / **(Développement)**.

3.  Développez **Tables** pour afficher les tables que vous avez créées lors des étapes précédentes.

    ![Explorateur de serveurs](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png)

4.  Double-cliquez sur la table **MailingList**.

    ![Explorateur de stockage VS](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png)

    Regardez comment la fenêtre affiche les différents schémas dans la table. Les entités `MailingList` ont une propriété `Description` et `FromEmailAddress`, et les entités `Subscriber` ont la propriété `Verified` (plus `SubscriberGUID`, qui n'est pas visible, l'image n'étant pas suffisamment large). La table comporte des colonnes pour toutes les propriétés, et si une ligne de la table concerne une entité qui n'a pas une propriété donnée, la cellule correspondante est vide.

Vous ne pouvez pas utiliser le navigateur de stockage dans Visual Studio pour mettre à jour ou supprimer des ressources Azure Storage. Vous pouvez utiliser l'[Explorateur de stockage Azure](http://azurestorageexplorer.codeplex.com/) pour mettre à jour ou supprimer des ressources de stockage de développement. Pour configurer l'Explorateur de stockage Azure de manière à utiliser le stockage de développement, activez la case à cocher **Developer Storage** dans la boîte de dialogue **Add Storage Account**.

> [WACOM.NOTE] Avec le dernier Kit de développement logiciel (SDK), vous pouvez mettre à jour le stockage de développement dans l'**Explorateur de serveurs**.

Utilisation de votre compte de stockageConfiguration de l'application pour utiliser votre compte Azure Storage
--------------------------------------------------------------------------------------------------------------

Ensuite, vous allez apprendre à configurer l'application de telle sorte qu'elle utilise votre compte Azure Storage au lieu du stockage de développement lorsqu'elle s'exécute dans Visual Studio. Il existe une nouvelle façon de procéder dans Visual Studio, introduite dans la version 1.8 du Kit de développement logiciel (SDK), et une ancienne méthode, qui implique la copie et le collage des paramètres depuis le portail de gestion Azure. La procédure suivante correspond à la nouvelle méthode de configuration des paramètres du compte de stockage.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **MvcWebRole** sous **Rôles** dans le projet **AzureEmailService**, puis cliquez sur **Propriétés**.

    ![Clic droit, Propriétés](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

2.  Cliquez sur l'onglet **Paramètres**. Dans la liste déroulante **Configuration du service**, sélectionnez **Local**.

3.  Sélectionnez l'entrée **StorageConnectionString**. Un bouton représentant des points de suspension (**...**) apparaît à l'extrémité droite de la ligne. Cliquez dessus pour ouvrir la boîte de dialogue **Storage Account Connection String**.

    ![Clic droit, Propriétés](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png)

4.  Dans la boîte de dialogue **Create Storage Connection String**, cliquez sur **Your subscription**, puis sur **Download Publish Settings**.

    > [WACOM.NOTE] Avec le dernier Kit de développement logiciel (SDK), cette boîte de dialogue vous permet de vous connecter à Azure et de sélectionner simplement le compte de stockage à utiliser.

    ![Clic droit, Propriétés](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png)

    Visual Studio lance une nouvelle instance de votre navigateur par défaut avec l'URL de la page de téléchargement des paramètres de publication du portail Azure. Si vous n'êtes pas connecté au portail, vous êtes invité à vous connecter. Une fois que vous êtes connecté, votre navigateur vous invite à enregistrer les paramètres de publication. Notez l'emplacement où vous enregistrez les paramètres.

    ![paramètres de publication](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-3.png)

5.  Dans la boîte de dialogue **Create Storage Connection String**, cliquez sur **Import**, puis accédez au fichier de paramètres de publication que vous avez enregistré à l'étape précédente.

6.  Sélectionnez l'abonnement et le compte de stockage que vous souhaitez utiliser, puis cliquez sur **OK**.

    ![sélectionner le compte de stockage](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-5.png)

7.  En suivant la même procédure que celle utilisée pour la chaîne de connexion `StorageConnectionString`, définissez la chaîne de connexion `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

    Il n'est pas nécessaire de télécharger de nouveau le fichier de paramètres de publication. Lorsque vous cliquez sur les points de suspension correspondant à la chaîne de connexion `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`, vous pouvez constater que la boîte de dialogue **Create Storage Connection String** a mémorisé vos informations d'abonnement. Lorsque vous cliquez sur la case d'option **Your subscription**, vous n'avez plus qu'à sélectionner l'**Abonnement** et le **Nom du compte** que vous avez sélectionnés précédemment, puis à cliquer sur **OK**.

8.  En suivant la même procédure que celle utilisée pour les deux chaînes de connexion du rôle MvcWebRole, définissez les chaînes de connexion pour les rôles WorkerRoleA et workerRoleB.

### Méthode manuelle de configuration des informations d'identification du compte de stockage

La procédure suivante correspond à la méthode manuelle de configuration des paramètres du compte de stockage. Si vous avez employé la méthode automatique décrite plus haut, vous pouvez ignorer cette procédure ou la parcourir pour découvrir tout ce que la méthode automatique a fait à votre place en coulisses.

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure](http://manage.windowsazure.com).

2.  Cliquez sur l'onglet **Stockage**, sur le compte test que vous avez créé à l'étape précédente, puis sur l'icône **Manage Keys**.

    ![Gérer les clés](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png)

    ![GUID des clés](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG)

3.  Copiez la clé d'accès primaire ou secondaire.

4.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **MvcWebRole** sous **Rôles** dans le projet **AzureEmailService**, puis cliquez sur **Propriétés**.

    ![Clic droit, Propriétés](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

5.  Cliquez sur l'onglet **Paramètres**. Dans la liste déroulante **Configuration du service**, sélectionnez **Local**.

6.  Sélectionnez l'entrée **StorageConnectionString**. Un bouton représentant des points de suspension (**...**) apparaît à l'extrémité droite de la ligne. Cliquez dessus pour ouvrir la boîte de dialogue **Storage Account Connection String**.

    ![Clic droit, Propriétés](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png)

7.  Dans la boîte de dialogue **Create Storage Connection String**, sélectionnez la case d'option **Manually entered credentials**. Entrez le nom de votre compte de stockage et la clé d'accès primaire ou secondaire que vous avez copiée sur le portail.

8.  Cliquez sur **OK**.

Vous pouvez utiliser la même procédure pour configurer les paramètres des rôles de travail ou propager les paramètres du rôle Web vers les rôles de travail en modifiant le fichier de configuration. Les étapes suivantes expliquent comment modifier le fichier de configuration. Cette opération fait partie de la méthode manuelle de définition des informations d'identification du stockage ; elle est inutile si vous avez déjà propagé les paramètres vers les rôles de travail à l'aide de la méthode automatique.

1.  Ouvrez le fichier **ServiceConfiguration.Local.cscfg** situé dans le projet **AzureEmailService**.

    L'élément `Role` de `MvcWebRole` contient un élément `ConfigurationSettings` avec les paramètres que vous avez mis à jour à l'aide de l'interface utilisateur de Visual Studio.

           <Role name="MvcWebRole">
             <Instances count="1" />
             <ConfigurationSettings>
               <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
               <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
             </ConfigurationSettings>
           </Role>

    Dans les éléments `Role` des deux rôles de travail, vous trouverez les deux mêmes chaînes de connexion.

2.  Supprimez les éléments `Setting` de ces deux chaînes de connexion des éléments `WorkerRoleA` et `WorkerRoleB`, puis copiez et collez à leur place les éléments `Setting` de l'élément `MvcWebRole`.

Pour plus d'informations sur les fichiers de configuration, consultez la page [Configuration d'un projet Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee405486.aspx)

### Test de l'application configurée pour utiliser votre compte de stockage

1.  Appuyez sur Ctrl+F5 pour exécuter l'application. Entrez des données en cliquant sur les liens **Listes de diffusion**, **Abonnés** et **Messages**, comme vous l'avez fait préalablement dans ce didacticiel.

Vous pouvez désormais utiliser l'**Explorateur de stockage Azure** ou l'**Explorateur de serveurs** pour afficher les données que l'application a entrées dans les tables Azure.

### Utilisation de l'Explorateur de stockage Azure pour afficher les données entrées dans votre compte de stockage

1.  Ouvrez l'**Explorateur de stockage Azure**.

2.  Sélectionnez le compte de stockage pour lequel vous avez entré les informations d'identification précédemment.

3.  Sous **Type de stockage**, sélectionnez **Tables**.

4.  Sélectionnez la table `MailingList`, puis cliquez sur **Requête** pour afficher les données que vous avez entrées sur les pages **Liste de diffusion** et **Abonné** de l'application.

  ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase1.png)

### Utilisation de l'Explorateur de serveurs pour afficher les données entrées dans votre compte de stockage

1.  Dans l'**Explorateur de serveurs** (ou l'**Explorateur de bases de données**), cliquez avec le bouton droit sur **Azure Storage**, puis cliquez sur **Ajouter un nouveau compte de stockage**.

2.  Suivez la même procédure que celle utilisée auparavant pour configurer les informations d'identification de votre compte de stockage.

3.  Développez le nouveau nœud sous **Azure Storage** pour afficher les données stockées dans votre compte de stockage Azure.

    ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se3.png)

### Étapes facultatives pour désactiver le démarrage automatique de l'émulateur de stockage Azure

Si vous n'utilisez pas l'émulateur de stockage, vous pouvez réduire le délai de démarrage du projet et utiliser moins de ressources locales en désactivant le démarrage automatique pour l'émulateur de stockage Azure.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet cloud **AzureEmailService** et sélectionnez **Propriétés**.

    ![Sélection des propriétés du projet cloud](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png)

2.  Sélectionnez l'onglet **Développement**.

3.  Définissez **Start Azure storage emulator** sur **False**.

    ![Désactivation du démarrage automatique pour l'émulateur de stockage](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png)

    **Remarque** : exécutez cette dernière opération uniquement si vous n'utilisez pas l'émulateur de stockage.

    Cette fenêtre permet également de changer le fichier de **configuration du service** utilisé lorsque vous exécutez l'application localement en passant de **Local** à **Cloud** (de *ServiceConfiguration.Local.cscfg* à *ServiceConfiguration.Cloud.cscfg*).

4.  Dans la zone de notification Windows, cliquez avec le bouton droit sur l'émulateur de calcul et cliquez sur **Shutdown Storage Emulator**.

    ![ASE](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png)

SendGridConfiguration de l'application pour l'utilisation de SendGrid
---------------------------------------------------------------------

L'exemple d'application utilise SendGrid pour l'envoi des courriers électroniques. Pour envoyer les courriers électroniques avec SendGrid, vous devez configurer un compte SendGrid, puis mettre à jour un fichier de configuration avec vos informations d'identification SendGrid.

**Remarque :** si vous ne voulez pas ou ne pouvez pas utiliser SendGrid, vous pouvez aisément utiliser à la place votre propre service de messagerie. Le code qui utilise SendGrid est isolé dans deux méthodes dans le rôle de travail B. Le [didacticiel 5][did5] explique ce que vous devez modifier pour mettre en œuvre une autre méthode d'envoi des courriers électroniques. Dans ce cas, vous pouvez ignorer cette procédure et poursuivre le didacticiel ; tout le reste de l'application fonctionnera (pages Web, planification des courriers électroniques, etc.), à l'exception de l'envoi des courriers électroniques.

### Création d'un compte SendGrid

1.  Suivez les instructions de la page [Envoi de courriers électroniques à l'aide de SendGrid avec Azure](http://www.windowsazure.com/fr-fr/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid") pour créer un compte gratuit.

### Mise à jour des informations d'identification de SendGrid dans les propriétés du rôle de travail

Dans l'une des précédentes sections du didacticiel, lorsque vous avez défini les informations d'identification du compte de stockage pour le rôle Web et les deux rôles de travail, vous avez peut-être remarqué que le rôle de travail B possédait trois paramètres absents du rôle Web ou du rôle de travail A. Vous pouvez maintenant utiliser la même interface utilisateur pour configurer ces paramètres (sélectionnez **Cloud** dans la liste déroulante **Configuration du service**).

Les étapes suivantes présentent une autre méthode de définition des propriétés, consistant à modifier le fichier de configuration.

1.  Modifiez le fichier *ServiceConfiguration.Cloud.cscfg* dans le projet `AzureEmailService` et entrez les valeurs de nom d'utilisateur et de mot de passe SendGrid que vous avez obtenues à l'étape précédente dans l'élément `WorkerRoleB` qui inclut ces paramètres. Le code suivant concerne l'élément WorkerRoleB.

    ![SendGridSettings](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png)

2.  Il existe également un paramètre AzureMailServiceURL. Définissez cette valeur sur l'URL que vous avez sélectionnée à la création de votre service cloud Azure, par exemple : « http://aescloud.cloudapp.net ».

En mettant à jour le fichier de configuration du service cloud, vous configurez les paramètres qui seront utilisés lorsque l'application sera exécutée dans le cloud. Si vous voulez que l'application envoie des courriers électroniques pendant qu'elle s'exécute localement, vous devez également mettre à jour le fichier *ServiceConfiguration.Local.cscfg*.

Déploiement sur AzureDéploiement de l'application sur Azure
-----------------------------------------------------------

Pour déployer l'application, vous pouvez créer un package dans Visual Studio et le charger à l'aide du portail de gestion Azure, ou effectuer la publication directement à partir de Visual Studio. Dans ce didacticiel, vous allez utiliser la méthode de publication.

Vous allez commencer par publier l'application dans l'environnement intermédiaire, puis vous ferez passer le déploiement intermédiaire en production.

### Mise en œuvre des restrictions d'adresse IP

Lors du déploiement intermédiaire, l'application est publiquement accessible à toute personne qui connaît l'URL. Par conséquent, la première étape consiste à mettre en œuvre les restrictions d'adresse IP pour s'assurer qu'aucune personne non autorisée ne pourra s'en servir. Dans une application de production, vous mettez en place un mécanisme d'authentification et d'autorisation comme le système d'abonnement ASP.NE, mais ces fonctions ont été omises dans l'exemple d'application pour simplifier sa configuration, son déploiement et les tests.

1.  Ouvrez le fichier *Web.Release.config* situé dans le dossier racine du projet `MvcWebRole` et remplacez la valeur 127.0.0.1 de l'attribut **ipAddress** par votre adresse IP. Pour voir le fichier **Web.Release.config** dans l'**Explorateur de solutions**, vous devez développer le fichier *Web.config*.

    Vous pouvez trouver votre adresse IP en recherchant « Trouver mon adresse IP » avec [Bing](http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "trouver mon adresse IP") ou un autre moteur de recherche.

    Lorsque l'application est publiée, les transformations spécifiées dans le fichier *Web.release.config* sont appliquées et les éléments de restriction de l'adresse IP sont mis à jour dans le fichier *web.config* qui est déployé dans le cloud. Vous pouvez afficher le fichier *web.config* transformé dans le dossier *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* après la création du package.

### Configuration de l'application pour utiliser votre compte de stockage lorsqu'elle s'exécute dans le cloud

Dans l'une des précédentes sections du didacticiel, lorsque vous avez défini les informations d'identification du compte de stockage pour le rôle Web et les deux rôles de travail, vous avez défini les informations d'identification à utiliser lorsque vous exécutez l'application localement. Vous devez à présent définir les informations d'identification du compte de stockage à utiliser lorsque vous exécutez l'application dans le cloud.

Pour ce test, vous allez utiliser pour le cloud les mêmes informations d'identification que celles employées pour l'exécution locale. Si vous déployez une application de production, normalement, vous n'utiliserez pas le même compte que pour les tests. Il est également recommandé d'utiliser en production des comptes différents pour la chaîne de connexion de diagnostic et pour la chaîne de connexion de stockage. Pour les besoins du test, vous utiliserez cependant le même compte.

Vous pouvez utiliser la même interface utilisateur pour configurer les chaînes de connexion (veillez simplement à sélectionner **Cloud** dans la liste déroulante **Configuration du service**). Une autre solution consiste à modifier le fichier de configuration, comme décrit aux étapes suivantes.

1.  Ouvrez le fichier *ServiceConfiguration.Local.cscfg* dans le projet **AzureEmailService** et copiez les éléments `Setting` de `StorageConnectionString` et `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

2.  Ouvrez le fichier *ServiceConfiguration.Cloud.cscfg* dans le projet **AzureEmailService** et collez les éléments copiés dans l'élément `Configuration Settings` de `MvcWebRole`, `WorkerRoleA` et `WorkerRoleB`, en remplaçant les éléments `Setting` qui y sont déjà présents.

3.  Vérifiez que les éléments du rôle Web et des deux rôles de travail définissent tous les mêmes chaînes de connexion.

### Publication de l'application

1.  S'il n'est pas déjà ouvert, lancez Visual Studio en tant qu'administrateur et ouvrez la solution **AzureEmailService**.

2.  Cliquez avec le bouton droit sur le projet cloud **AzureEmailService** et sélectionnez **Publier**.

    ![Package](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png)

    La boîte de dialogue **Publish Azure Application** s'affiche.

    ![Package Cloud](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png)

3.  Si vous avez utilisé la méthode automatique pour importer les informations d'identification du compte de stockage précédemment, votre abonnement Azure apparaît dans la liste déroulante et vous pouvez le sélectionner, puis cliquer sur **Suivant**. Sinon, cliquez sur **Sign in to download credentials** et suivez les instructions décrites à la page [Configuration de l'application pour Azure Storage](#conf4azureStorage) pour télécharger et importer vos paramètres de publication.

4.  Sous l'onglet **Paramètres communs**, vérifiez le paramètre dans la liste déroulante **Service de cloud computing**.

5.  Dans la liste déroulante **Environnement**, sélectionnez **Intermédiaire** à la place de **Production**.

    ![Tableau de bord](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png)

6.  Conservez les paramètres par défaut **Release** pour **Configuration de build** et **Cloud** pour **Configuration du service**.

    Les paramètres par défaut sous l'onglet **Advanced** conviennent pour ce didacticiel. Plusieurs paramètres utiles pour le développement et les tests sont regroupés sous l'onglet **Advanced**. Pour plus d'informations sur cet onglet, consultez la page [Assistant Publication d'application Azure](http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "assistant publication").

7.  Cliquez sur **Suivant**.

8.  À l'étape **Résumé** de l'Assistant, cliquez sur l'icône **enregistrer** (icône représentant une disquette affichée à droite de la liste déroulante Profil cible) pour enregistrer les paramètres de publication.

    À la prochaine publication de l'application, les paramètres enregistrés seront utilisés et vous n'aurez plus besoin de parcourir les étapes de l'Assistant Publication.

9.  Passez en revue les paramètres, puis cliquez sur **Publier**.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png)

    La fenêtre **Journal des activités Azure** s'ouvre dans Visual Studio.

10. Cliquez sur l'icône représentant une flèche vers la droite pour développer les détails du déploiement.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png)
     <br/><br/>
     ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png)

    Le déploiement peut durer environ 5 minutes, voire plus.

11. Lorsque le déploiement est terminé, cliquez sur l'**URL du site Web** pour lancer l'application.

    ![Tableau de bord](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png)

12. Entrez des données sur les pages Web **Liste de diffusion**, **Abonné** et **Message** pour tester l'application.

    **Remarque** : supprimez l'application une fois que vous l'avez testée afin d'éviter de payer pour des ressources que vous n'utilisez pas. Si vous utilisez un [compte d'évaluation Azure gratuit](http://www.windowsazure.com/fr-fr/pricing/free-trial/ "compte d'évaluation gratuit"), les trois rôles déployés consommeront l'équivalent de votre limite mensuelle en une quinzaine de jours. Pour supprimer un déploiement avec le portail de gestion Azure, sélectionnez le service cloud et cliquez sur **SUPPRIMER** en bas de la page, puis sélectionnez le déploiement de production ou intermédiaire.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png)

13. Dans le journal des activités Azure de Visual studio, sélectionnez **Ouvrir dans l'Explorateur de serveurs**.

    Sous **Calcul Azure**, dans l'**Explorateur de serveurs**, vous pouvez surveiller le déploiement. Si vous avez sélectionné **Enable Remote Desktop for all roles** dans l'Assistant **Publish Azure Application**, vous pouvez cliquer avec le bouton droit sur une instance de rôle et sélectionner **Connect using Remote Desktop**.

    ![pub](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png)

ProductionPassage de l'application de la gestion intermédiaire à la production
------------------------------------------------------------------------------

1.  Dans le [portail de gestion Azure](http://manage.windowsazure.com), cliquez sur l'icône **Cloud Services** dans le volet gauche, puis sélectionnez votre service cloud.

2.  Cliquez sur **Swap**.

3.  Cliquez sur **Oui** pour terminer l'échange de l'adresse IP virtuelle. Cette étape peut prendre plusieurs minutes.

    ![Tableau de bord](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png)

4.  Lorsque l'échange est terminé, cliquez sur l'icône **Cloud Services** dans le volet gauche, puis sélectionnez votre service cloud.

5.  Faites défiler l'onglet **Tableau de bord** du déploiement **Production** jusqu'à la section **quick glance** en bas à droite de la page. Comme vous pouvez le remarquer, l'**URL du site** n'affiche plus un préfixe GUID, mais le nom de votre service cloud.

    ![Tableau de bord](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png)

6.  Cliquez sur le lien sous **URL du site** ou copiez-le et collez-le dans un navigateur pour tester l'application en production.

    Si vous n'avez pas changé les paramètres du compte de stockage, les données que vous avez entrées lors du test de la version intermédiaire de l'application s'affichent lorsque vous exécutez l'application dans le cloud.

SuiviConfiguration et affichage des données de suivi
----------------------------------------------------

Le suivi est un outil inestimable pour le débogage d'une application cloud. Dans cette section du didacticiel, vous allez apprendre à afficher les données de suivi.

1.  Vérifiez que la chaîne de connexion de diagnostic est configurée pour utiliser votre compte Azure Storage, et non le stockage de développement.

    Si vous avez suivi les instructions dans les précédentes sections de ce didacticiel, elles seront identiques. Vous pouvez le vérifier soit en utilisant l'interface utilisateur de Visual Studio (onglet **Paramètres** dans les **Propriétés** des rôles), soit en examinant les fichiers *ServiceConfiguration.\*.cscfg*.

    **Remarque :** il est recommandé d'utiliser des comptes de stockage différents pour les données de suivi et pour les données de production. Par souci de simplification, cependant, c'est le même compte qui a été configuré pour le suivi dans ce didacticiel.

2.  Dans Visual Studio, ouvrez *WorkerRoleA.cs* dans le projet **WorkerRoleA**, recherchez `ConfigureDiagnostics` et examinez la méthode `ConfigureDiagnostics`.

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

    La méthode `ConfigureDiagnostics` dans chacun des rôles de travail et Web configure l'écouteur de suivi pour enregistrer les données lorsque vous appelez l'API de suivi. Pour plus d'informations, consultez la page [Utilisation du suivi dans les applications cloud Azure](http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Utilisation du suivi dans Azure")

3.  Dans l'**Explorateur de serveurs**, double-cliquez sur **WADLogsTable** (développez **Stockage** / **nom\_de\_votre\_compte\_de\_stockage** / **Tables**) pour le compte de stockage que vous avez ajouté précédemment. Vous pouvez entrer un [filtre Services de données WCF](http://msdn.microsoft.com/fr-fr/library/windowsazure/ff683669.aspx "filtre WCF") pour limiter les entités affichées. Dans l'image suivante, seuls les avertissements et les messages d'erreur sont affichés.

    ![Tableau de bord](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png)

Ajout d'une instance de rôleAjout d'une autre instance de rôle de travail pour gérer une charge accrue
------------------------------------------------------------------------------------------------------

Pour mettre à l'échelle les ressources de calcul dans les rôles Azure, vous avez le choix entre deux approches : spécifier [la taille des machines virtuelles](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee814754.aspx "Tailles des machines virtuelles") et/ou spécifier le nombre d'instances de machines virtuelles en cours d'exécution.

La taille des machines virtuelles est spécifiée dans l'attribut `vmsize` de l'élément `WebRole` ou `WorkerRole` dans le fichier *ServiceDefinition.csdef*. Le paramètre par défaut est `Small`, ce qui correspond à un cœur et 1,75 Go de RAM. Dans le cas des applications multithread qui utilisent beaucoup de mémoire, d'espace disque et de bande passante, vous pouvez augmenter la taille des machines virtuelles pour bénéficier de performances accrues. Par exemple, une machine virtuelle `ExtraLarge` comporte 8 cœurs d'UC et 14 Go de RAM. L'augmentation de la mémoire, du nombre de cœurs d'UC et de la bande passante sur une même machine est désignée par le terme *augmentation d'échelle*. Les bons candidats pour une augmentation d'échelle incluent les applications Web ASP.NET qui utilisent [des méthodes asynchrones](http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "Async MVC"). Pour obtenir une description des ressources fournies par chaque taille de machine virtuelle, consultez la page [Taille des machines virtuelles](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee814754.aspx "Tailles des machines virtuelles").

Le rôle de travail B dans cette application est le composant restrictif sous une charge élevée, car le travail d'envoi des courriers électroniques lui incombe. Le rôle de travail A crée simplement des messages de file d'attente, qui ne mobilisent pas beaucoup de ressources. Dans la mesure où le rôle de travail B n'est pas multithread et n'a pas un grand encombrement mémoire, il n'est pas un bon candidat pour une augmentation d'échelle. La mise à l'échelle du rôle de travail B peut se faire de manière linéaire (les performances sont presque multipliées par deux lorsque le nombre d'instances double) en augmentant le nombre d'instances. L'augmentation du nombre d'instances de calcul est appelée *montée en charge*. Chaque instance a un coût ; aussi la montée en charge doit-elle avoir lieu uniquement quand votre application l'exige.

Vous pouvez effectuer la montée en charge d'un rôle Web ou de travail en mettant à jour le paramètre dans l'interface utilisateur de Visual Studio ou en modifiant directement les fichiers *ServiceConfiguration.\*.cscfg*. Le nombre d'instances est spécifié sous l'onglet **Configuration** de la fenêtre **Propriétés** du rôle et dans l'élément `Instances` à l'intérieur des fichiers *.cscfg*. Lorsque vous mettez à jour le paramètre, vous devez déployer le fichier de configuration mis à jour pour que la modification soit appliquée. Ou bien, pour les augmentations temporaires de la charge, vous pouvez modifier le nombre d'instances de rôle dans le portail de gestion Azure. Vous pouvez également configurer le nombre d'instances avec l'API de gestion Azure. Enfin, vous pouvez utiliser le [bloc applicatif de mise à l'échelle automatique](/en-us/develop/net/how-to-guides/autoscaling/) pour une montée en charge automatique en fonction de l'augmentation de la charge. Pour plus d'informations sur la mise à l'échelle automatique, consultez les liens à la fin du [dernier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

Dans cette section du didacticiel, vous allez configurer la montée en charge du rôle de travail B en utilisant le portail de gestion, après avoir vu comment procéder dans Visual Studio.

Dans Visual Studio, vous devez d'abord cliquer avec le bouton droit sur le rôle sous **Rôles** dans le projet cloud et cliquer sur **Propriétés**.

![Clic droit, Propriétés](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png)

Vous devez ensuite sélectionner l'onglet **Configuration** à gauche et sélectionner **Cloud** dans la liste déroulante **Configuration du service**.

![Nombre d'instances](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png)

Comme vous pouvez le remarquer, il est également possible de configurer la taille des machines virtuelles sous cet onglet.

Les étapes suivantes expliquent comment effectuer la montée en charge à l'aide du portail de gestion Azure.

1.  Dans le portail de gestion Azure, sélectionnez votre service cloud, puis cliquez sur **Scale**.

2.  Augmentez le nombre d'instances pour le rôle de travail B, puis cliquez sur **Enregistrer**.

    ![augmenter le nombre d'instances](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png)

    La mise en service des nouvelles machines virtuelles peut prendre plusieurs minutes.

3.  Sélectionnez l'onglet **Instances** pour afficher chaque instance de rôle dans votre application.

    ![afficher les instances](./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png)

Étapes suivantesÉtapes suivantes
--------------------------------

Vous savez maintenant comment configurer, déployer et mettre à l'échelle l'application terminée. Les didacticiels suivants montrent comment créer l'application à partir de zéro. Dans le [didacticiel suivant](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/), vous allez générer le rôle Web.

Pour obtenir des liens sur les ressources supplémentaires pour travailler avec les tables, les files d'attente et les objets blob Azure Storage, consultez la fin du [dernier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).[Didacticiel 3](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/)



[Set up the development environment]: #setupdevenv
[Set up a free Azure account]: #setupwindowsazure
[Create an Azure Storage account]: #createWASA
[Install Azure Storage Explorer]: #installASE
[Create a Cloud Service]: #createcloudsvc
[Download and run the completed solution]: #downloadcnfg
[View developer storage in Visual Studio]: #StorageExpVS
[Configure the application for Azure Storage]: #conf4azureStorage
[Deploy the application to Azure]: #deployAz
[Promote the application from staging to production]: #swap
[Configure the application to use SendGrid]: #sendGrid
[Configure and view trace data]: #trace
[Add another worker role instance to handle increased load]: #addRole

[firsttutorial]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/

[tut3]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/
[tut5]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[NewPortal]: http://manage.windowsazure.com
[managestorage]: /en-us/manage/services/storage/how-to-manage-a-storage-account/
[autoscalingappblock]: /en-us/develop/net/how-to-guides/autoscaling/


[mtas-portal-new-storage]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
[mtas-storage-quick]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
[mtas-create-storage-url-test]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
[mtas-manage-keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
[mtas-guid-keys]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
[mtas-new-cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
[mtas-create-cloud]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
[mtas-ase-add]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add.png
[mtas-ase-add2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase-add2.png

[mtas-rt-prop]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
[mtas-mailinglist1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
[mtas-create1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
[mtas-mailing-list-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
[mtas-subscribers-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
[mtas-message-create-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
[mtas-message-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
[mtas-serverExplorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
[mtas-wasVSdata]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
[mtas-elip]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
[mtas-enter]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
[mtas-ase1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-ase1.png


[mtas-se3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se3.png
[mtas-aesp]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
[mtas-1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
[mtas-se4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png









[mtas-c6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
[mtas-c7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png

[mtas-sg]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
[mtas-trc]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
[mtas-instanceCnt]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png

[mtas-in3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
[mtas-in2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
[mtas-3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-3.png
[mtas-5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-5.png
[mtas-6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
[mtas-16]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
[mtas-7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
[mtas-8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
[mtas-9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
[mtas-11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
[mtas-12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
[mtas-c55]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png

[mtas-19]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png