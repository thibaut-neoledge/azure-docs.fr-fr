<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publishing with TFS" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure web sites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online" authors="ghogen" solutions="" manager="" editor="" />

Livraison continue sur Azure au moyen de Visual Studio Online
=============================================================

Visual Studio Online (précédemment appelé Team Foundation Service) est une version de service hébergé dans le cloud du logiciel populaire TFS (Team Foundation Server) de Microsoft. Elle offre des fonctionnalités hautement personnalisables de gestion du code source et des builds, un workflow souple des processus de développement et d'équipe, un suivi des problèmes et des éléments de travail, et bien plus encore. Vous pouvez configurer vos projets d'équipe Visual Studio Online afin de les générer et de les déployer automatiquement sur des sites Web Azure ou des services cloud. Pour plus d'informations sur la procédure à suivre pour configurer un système de génération et de déploiement continus au moyen d'un serveur TFS local, consultez la rubrique [Remise continue pour Cloud Services dans Azure](../cloud-services-dotnet-continuous-delivery).

Ce didacticiel part du principe que vous avez déjà installé Visual Studio 2013 et le Kit de développement logiciel (SDK) Azure sur votre système. Si Visual Studio 2013 n'est pas déjà installé, téléchargez-le en choisissant le lien **Test gratuit de Visual Studio** sur [www.visualstudio.com](http://www.visualstudio.com). Pour installer le Kit de développement logiciel (SDK) Azure, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=239540).

Pour configurer un service cloud permettant de générer et de déployer automatiquement sur Azure au moyen de Visual Studio Online, procédez comme suit :

-   [Étape 1 : inscription sur Visual Studio Online](#step1)

-   [Étape 2 : archivage d'un projet dans le contrôle de code source](#step2)

-   [Étape 3 : connexion du projet à Azure](#step3)

-   [Étape 4 : exécution des modifications et déclenchement d'une régénération et d'un redéploiement](#step4)

-   [Étape 5 : redéploiement d'une build antérieure (facultatif)](#step5)

-   [Étape 6 : modification du déploiement de production (services cloud uniquement)](#step6)

Inscription sur Visual Studio OnlineÉtape 1 : inscription sur Visual Studio Online
----------------------------------------------------------------------------------

1.  Créez un compte Visual Studio Online en accédant à <http://www.visualstudio.com>. Cliquez sur le lien **Se connecter**. Vous avez besoin d'un compte Microsoft pour vous connecter. Si c'est la première fois que vous vous connectez, vous êtes invité à fournir certaines informations vous concernant, comme votre nom et votre adresse de messagerie. ![](./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG)

2.  Si ce n'est pas votre première connexion, vous voyez cet écran lorsque vous vous connectez. Cliquez sur le lien **Créer un compte maintenant**<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG)

3.  Créez une URL de compte pour votre nouveau projet. Votre compte se présentera comme suit : https://&lt;nom\_compte\>.visualstudio.com.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG)

4.  À présent, vous pouvez créer votre premier projet. Entrez un nom et une description pour le projet. Choisissez le système de contrôle de version que vous voulez utiliser. TFVC (Team Foundation Version Control) ou Git sont tous les deux pris en charge. Pour plus d'informations sur ces options, consultez la page [Utiliser un contrôle de version](http://go.microsoft.com/fwlink/?LinkId=324037). La présente procédure part du principe que vous utilisez TFVC. Ensuite, choisissez le modèle de processus utilisé par votre organisation et choisissez le bouton **Créer le projet**. Pour plus d'informations sur les modèles de processus, consultez la page [Utiliser des artefacts de projet d'équipe, choisir un modèle de processus](http://go.microsoft.com/fwlink/?LinkId=324035).<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs1.png)

5.  Au terme de la création du projet, cliquez sur le bouton **Ouvrir avec Visual Studio afin de se connecter** pour lancer automatiquement Visual Studio en étant connecté à votre projet d'équipe. Si des boîtes de dialogue de sécurité apparaissent, choisissez Autoriser.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs2.png)

Archivage d'un projet dans le contrôle de code sourceÉtape 2 : archivage d'un projet dans le contrôle de code source
--------------------------------------------------------------------------------------------------------------------

1.  Dans Visual Studio, ouvrez la solution à déployer, ou créez-en une. Vous pouvez déployer un site Web ou un service cloud (application Azure) en suivant les étapes de cette procédure. Si vous voulez créer une solution, créez un projet de service cloud Azure ou ASP.NET MVC. Vérifiez que le projet cible .NET Framework 4 ou 4.5, et si vous créez un projet de service cloud, ajoutez un rôle Web ASP.NET MVC et un rôle de travail, et choisissez Application Internet pour le rôle Web. Lorsque vous y êtes invité, choisissez **Application Internet**. Si vous voulez créer un site Web, choisissez le modèle de projet Application Web ASP.NET, puis sélectionnez MVC. Consultez la page [Prise en main d'Azure et ASP.NET](http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-get-started/).

2.  Ouvrez le menu contextuel pour la solution et sélectionnez **Ajouter la solution au contrôle de code source**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs5.png)

3.  Acceptez ou modifiez les valeurs par défaut et choisissez le bouton **OK**. Au terme du processus, les icônes du contrôle de code source apparaissent dans l'Explorateur de solutions.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs6.png)

4.  Ouvrez le menu contextuel de la solution et choisissez **Archiver**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs7.png)

5.  Dans la zone Modifications en attente de Team Explorer, tapez un commentaire pour l'archivage et choisissez le bouton **Archiver**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs8.png)

<br/>
Remarquez les options permettant d'inclure ou d'exclure des modifications spécifiques lorsque vous archivez. Si des modifications souhaitées ont été exclues, choisissez le lien **Tout inclure**.<br/>
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs9.png)

Connexion du projet à AzureÉtape 3 : connexion du projet à Azure
----------------------------------------------------------------

1.  Maintenant que vous disposez d'un projet d'équipe VSO contenant du code source, vous êtes prêt à connecter votre projet d'équipe à Azure. Dans le [portail Azure](http://manage.windowsazure.com), sélectionnez votre service cloud ou site Web, ou créez-en un en sélectionnant l'icône + en bas à gauche et en choisissant **Service cloud** ou **Site Web**, puis **Création rapide**. Choisissez le lien **Configurer la publication avec Visual Studio Online**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs10.png)

2.  Dans l'Assistant, tapez le nom de votre compte Visual Studio Online dans la zone de texte et cliquez sur le lien **Autoriser maintenant**. Vous serez peut-être invité à vous connecter.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs11.png)

3.  Dans la boîte de dialogue contextuelle OAuth, choisissez **Accepter** pour configurer votre projet d'équipe dans VSO.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs12.png)

4.  Si le processus d'autorisation aboutit, une zone déroulante affiche une liste de vos projets d'équipe Visual Studio Online. Sélectionnez le nom du projet d'équipe que vous avez créé aux étapes précédentes et choisissez la coche de l'Assistant.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs13.png)

5.  Une fois votre projet lié, des instructions s'affichent pour vous permettre d'archiver les modifications dans votre projet d'équipe Visual Studio Online. Lors du prochain archivage, Visual Studio Online générera et déploiera votre projet sur Azure. Essayez maintenant en cliquant sur le lien **Archiver depuis Visual Studio**, puis sur le lien **Lancer Visual Studio** (ou le bouton **Visual Studio** équivalent en bas de l'écran du portail).<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs14.png)

Déclenchement d'une régénérationÉtape 4 : déclenchement d'une régénération et d'un redéploiement de votre projet
----------------------------------------------------------------------------------------------------------------

1.  Dans Visual Studio Team Explorer, cliquez sur le lien **Explorateur du contrôle de code source**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs15.png)

2.  Accédez à votre fichier solution et ouvrez-le.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs16.png)

3.  Dans l'Explorateur de solutions, ouvrez un fichier et modifiez-le. Par exemple, modifiez le fichier \_Layout.cshtml sous le dossier Views\\Shared dans un rôle Web MVC.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs17.png)

4.  Modifiez le logo du site et appuyez sur Ctrl+S pour enregistrer le fichier.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs18.png)

5.  Dans Team Explorer, choisissez le lien **Modifications en attente**.<br/>
    ![](./media/cloud-services-continuous-delivery-use-vso/tfs19.png)

6.  Tapez un commentaire et choisissez le bouton **Archiver**.<br/>
    ![](./media/cloud-services-continuous-delivery-use-vso/tfs20.png)

7.  Choisissez le bouton Accueil pour revenir à la page d'accueil de Team Explorer.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs21.png)

8.  Choisissez le lien **Builds** pour afficher les builds en cours.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs22.png)
<br/>
    Team Explorer indique qu'une build est disponible pour archivage.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs23.png)

9.  Double-cliquez sur le nom de la build en cours pour afficher un journal détaillé lors du processus de génération.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs24.png)

10. Pendant la création de la build, examinez la définition de build qui a été créée lorsque vous avez lié TFS à Azure au moyen de l'Assistant. Ouvrez le menu contextuel de la définition de build et choisissez **Modifier la définition de build**.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs25.png)
<br/>
     Dans l'onglet **Déclencher**, vous allez voir que la définition de build prévoit par défaut un processus de génération pour chaque archivage.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs26.png)
<br/>
     Dans l'onglet **Processus**, vous pouvez voir que l'environnement de déploiement est défini sur le nom de votre service cloud ou site Web. Si vous utilisez des sites Web, les propriétés affichées seront différentes de celles figurant ici.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs27.png)
<br/>
     Spécifiez des valeurs pour les propriétés si vous souhaitez d'autres valeurs que celles par défaut. Le tableau suivant présente les valeurs par défaut des propriétés d'un service cloud :

    <table>
<tr><td><b>Propriété</b></td><td><b>Valeur par défaut</b></td></tr>
<tr><td>Autoriser la mise à niveau</td><td>true</td></tr>
<tr><td>Environnement du service cloud</td><td>Staging</td></tr>
<tr><td>Nom du service cloud</td><td>Nom du service auquel vous êtes connecté</td></tr>
<tr><td>Étiquette du déploiement</td><td>Identique au nom du service</td></tr>
<tr><td>Configuration du service</td><td>SServiceConfiguration.Cloud.cscfg</td></tr>
<tr><td>Nom du compte de stockage</td><td>Vide, ce qui signifie qu'il faut trouver un compte de stockage.</td></tr>
<tr><td>Profil de publication</td><td>Fichier .azurePubxml. Si vous en archivez un, vous pouvez le choisir ici.</td></tr>
</table>
<br/>
 Si la propriété du compte de stockage est laissée sans valeur, Azure en recherche une. S'il existe un compte de stockage portant le même nom que le service cloud, il est utilisé. Dans le cas contraire, Azure utilise un autre compte de stockage, ou s'il n'y a pas de compte de stockage, il en crée un. Le compte de stockage fournit un emplacement dans Azure pour le stockage de fichiers et d'autres données. Pour plus d'informations, consultez la page [Présentation d'un compte de stockage](http://www.windowsazure.com/en-us/documentation/articles/storage-whatis-account).

1.  À ce stade, la création de la build doit être terminée.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs28.png)

2.  Si vous double-cliquez sur le nom de la build, Visual Studio affiche un **Résumé de la build**, y compris tous les résultats de test provenant de projets de test unitaires associés.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs29.png)

3.  Dans le [portail Azure](http://manage.windowsazure.com), vous pouvez afficher le déploiement associé sous l'onglet Déploiements lorsque l'environnement intermédiaire est sélectionné.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs30.png)

4.  Accédez à l'URL de votre site. Dans le cas d'un site Web, cliquez simplement sur le bouton Parcourir dans la barre de commandes. Dans le cas d'un service cloud, choisissez l'URL dans la section **Aperçu rapide** de la page **Tableau de bord** représentant l'environnement intermédiaire d'un service cloud. Les déploiements résultant de l'intégration continue de services cloud sont publiés dans l'environnement intermédiaire par défaut. Vous pouvez changer cela en définissant la propriété Environnement du service cloud de substitution sur Production. Cet écran indique où se trouve l'URL de site dans la page de tableau de bord du service cloud :<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs31.png)<br/>
<br/>
     Un nouvel onglet de navigateur apparaît pour afficher votre site en cours d'exécution.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs32.png)

5.  Pour les services cloud, si vous apportez d'autres modifications à votre projet, vous déclenchez d'autres builds et vous accumulez plusieurs déploiements. Le plus récent est marqué comme étant actif.<br/>
     ![](./media/cloud-services-continuous-delivery-use-vso/tfs33.png)

Redéploiement d'une build antérieureÉtape 5 : redéploiement d'une build antérieure
----------------------------------------------------------------------------------

Cette étape (facultative) s'applique aux services cloud. Dans le portail de gestion, sélectionnez un déploiement antérieur et cliquez sur le bouton **Redéployer** pour revenir à un archivage antérieur de votre site. Notez que cette action va déclencher une nouvelle build dans TFS, et créer une nouvelle entrée dans l'historique de vos déploiements.<br/>
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs34.png)

Modification du déploiement de productionÉtape 6 : modification du déploiement de production
--------------------------------------------------------------------------------------------

Cette étape s'applique uniquement aux services cloud, pas aux sites Web. Une fois que vous êtes prêt, vous pouvez promouvoir l'environnement intermédiaire en environnement de production en choisissant le bouton Swap dans le portail de gestion. L'environnement intermédiaire récemment déployé passe à l'état de production et l'environnement de production précédent, le cas échéant, devient un environnement intermédiaire. Le déploiement actif peut être différent pour les environnements de production et intermédiaire, mais l'historique de déploiement des builds récentes est identique quel que soit l'environnement.<br/>
 ![](./media/cloud-services-continuous-delivery-use-vso/tfs35.png)

Pour plus d'informations, consultez la page [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Si vous utilisez Git, consultez les pages [Partagez votre code dans Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) et [Publication à partir du contrôle de code source sur des sites Web Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-publish-source-control).

