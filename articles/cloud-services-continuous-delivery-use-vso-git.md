<properties urlDisplayName="Publishing with Visual Studio Online" pageTitle="Livraison continue avec Visual Studio Online dans Azure" metaKeywords="" description="Découvrez comment configurer vos projets d'équipe Visual Studio Online afin de les générer et de les déployer automatiquement vers des sites web ou des services cloud Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online and Git" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />




# Livraison continue sur Azure au moyen de Visual Studio Online et de Git

Vous pouvez utiliser et déployer des projets d'équipe Visual Studio Online pour héberger un référentiel Git pour votre code source, et créer et déployer automatiquement dans des sites web ou des services cloud Azure lorsque vous placez une validation dans le référentiel.

Visual Studio 2013 et le Kit de développement logiciel (SDK) Azure doivent être installés sur votre système. Si Visual Studio 2013 n'est pas déjà installé, téléchargez-le en choisissant le lien **Test gratuit de Visual Studio** sur [www.visualstudio.com](http://www.visualstudio.com). Pour installer le Kit de développement logiciel (SDK) Azure, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=239540).


<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Pour suivre ce didacticiel, vous avez besoin d'un compte Visual Studio Online :</h5>
<p>Vous pouvez <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">ouvrir un compte Visual Studio Online gratuitement</a>.</p>
</div>

Pour configurer un service cloud permettant de générer et de déployer automatiquement sur Azure au moyen de Visual Studio Online, procédez comme suit :

-   [Étape 1 : création d'un référentiel Git.][]

-   [Étape 2 : création d'un projet et placement dans votre référentiel Git.][]

-   [Étape 3 : connexion du projet à Azure][]

-   [Étape 4 : exécution des modifications et déclenchement d'une régénération et d'un redéploiement][]

-   [Étape 5 : redéploiement d'une build antérieure (facultatif)][]

-   [Étape 6 : modification du déploiement de production][]

-	[Étape 7 : déploiement à partir d'une branche en cours d'utilisation][]

<h2> <a name="step1"></a>Étape 1 : création d'un référentiel Git</h2>


1. Si vous n'avez pas encore un compte Visual Studio Online, [procédez comme suit](http://go.microsoft.com/fwlink/?LinkId=397665). Lorsque vous créez un projet d'équipe, choisissez Git comme système de contrôle de code source. Suivez les instructions de connexion de Visual Studio à votre projet d'équipe.

2. Dans Team Explorer, sélectionnez le lien **Cloner ce référentiel**. 
![][3]

3. Indiquez l'emplacement de la copie locale et sélectionnez le bouton **Cloner**.
 
<h2><a name="step2"> </a>Étape 2 : création d'un projet et validation dans le référentiel</h2>

1. Dans la section Solutions de Team Explorer, sélectionnez Nouveau lien pour créer un projet dans le référentiel local.<br/>
![][4]

2. Vous pouvez déployer un site web ou un service cloud (application Azure) en suivant les étapes de cette procédure.
Créez un projet de service cloud Azure ou un projet MVC ASP.NET. Vérifiez que le projet cible .NET Framework 4 ou 4.5, et si vous créez un projet de service cloud, ajoutez un rôle web ASP.NET MVC et un rôle de travail.
Si vous voulez créer un site web, choisissez le modèle de projet Application Web ASP.NET, puis sélectionnez MVC. Consultez la page [Prise en main d'Azure et ASP.NET](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-dotnet-get-started/).

3. Ouvrez le menu contextuel de la solution et choisissez **Valider**..<br/>
![][7]

4. Si vous utilisez Git dans Visual Studio Online pour la première fois, vous devez fournir des informations pour vous identifier dans Git. Dans la zone **Modifications en attente** de Team Explorer, entrez votre nom d'utilisateur et votre adresse de messagerie. Tapez un commentaire pour la validation et sélectionnez **Valider**.<br/>
![][8]

5. Remarquez les options permettant d'inclure ou d'exclure des modifications spécifiques lors de l'archivage. Si des modifications souhaitées sont exclues, choisissez le lien **Tout inclure**.<br/>

6. Vous avez maintenant validé les modifications dans votre copie locale du référentiel. Synchronisez ensuite ces modifications avec le serveur. Sélectionnez le lien **Synchronisation**.

<h2> <a name="step3"> </a>Étape 3 : connexion du projet à Azure</h2>

1. Vous avez maintenant un référentiel Git dans Visual Studio Online contenant du code source : vous êtes prêt à connecter votre référentiel Git à Azure.  Dans le [portail Azure](http://manage.windowsazure.com), sélectionnez votre service cloud ou site web, ou créez-en un en sélectionnant l'icône + en bas à gauche et en choisissant **Service cloud** ou **Site Web**, puis **Création rapide**.<br.>
![][9]

3. Pour les services cloud, sélectionnez le lien **Configurer la publication avec Visual Studio Online**. Pour les sites web, cliquez sur le lien **Configurer le déploiement à partir du contrôle de code source**.<br/>
![][10]

2. Dans l'Assistant, tapez le nom de votre compte Visual Studio Online dans la zone de texte et sélectionnez le lien **Autoriser maintenant**. Vous serez peut-être invité à vous connecter.<br/>
![][11]

3. Dans la boîte de dialogue contextuelle OAuth, sélectionnez **Accepter** pour configurer votre projet d'équipe dans Visual Studio Online.<br/>
![][12]

4. Si la procédure d'autorisation réussit, une zone déroulante affiche la liste de vos projets d'équipe Visual Studio Online.  Sélectionnez le nom du projet d'équipe que vous avez créé aux étapes précédentes et choisissez la coche de l'Assistant.<br/>
![][13]

La prochaine fois que vous placerez une validation dans votre référentiel, Visual Studio Online génèrera et déploiera votre projet dans Azure.<br/>


<h2><a name="step4"> </a>Étape 4 : Déclenchement d'une régénération et redéploiement de votre projet</h2>

1. Dans Visual Studio, ouvrez un fichier et modifiez-le. Par exemple, modifiez le fichier _Layout.cshtml sous le dossier Views\Shared dans un rôle Web MVC.<br/>
![][17]

2. Modifiez le texte du pied de page du site et enregistrez le fichier.<br/>
![][18]

3. Dans l'Explorateur de solutions, ouvrez le menu contextuel du nœud de la solution, du nœud du projet ou du fichier que vous avez modifié et sélectionnez **Valider**.<br/>

4. Tapez un commentaire et sélectionnez **Valider**.<br/>
![][20]

5. Sélectionnez le lien **Synchronisation**.<br/>
![][38]

6. Sélectionnez le lien **Émettre** pour placer votre validation dans le référentiel dans Visual Studio Online. (Vous pouvez également utiliser le bouton **Synchronisation** pour copier vos validations dans le référentiel. La différence est que **Synchronisation** extrait également les dernières modifications du référentiel).<br/>
![][39]

7. Choisissez le bouton Accueil pour revenir à la page d'accueil de Team Explorer.<br/>
![][21]

8. Choisissez **Builds** pour afficher les builds en cours.<br/>
![][22]
<br/>
Team Explorer indique qu'une build est disponible pour archivage.<br/>
![][23]

9. Double-cliquez sur le nom de la build en cours pour afficher un journal détaillé de la génération.<br/>

10. Pendant la création de la build, examinez la définition de build créée lorsque vous avez utilisé l'Assistant pour la liaison à Azure.  Ouvrez le menu contextuel de la définition de build et choisissez **Modifier la définition de build**.<br/>
![][25]
<br/>
Dans l'onglet **Déclencher**, vous allez voir que la définition de build prévoit par défaut un processus de génération pour chaque archivage. (Pour un service cloud, Visual Studio Online génère et déploie automatiquement la branche principale dans l'environnement intermédiaire. Vous devez quand même effectuer une opération manuelle pour le déploiement dans le site web en activité. Pour un site web qui ne comporte pas d'environnement intermédiaire, elle déploie la branche principale directement dans le site en activité.<br/>
![][26]
<br/>
Dans l'onglet **Processus**, vous pouvez voir que l'environnement de déploiement est défini sur le nom de votre service cloud ou site web.<br/>
![][27]
<br/>
Spécifiez des valeurs pour les propriétés si vous souhaitez d'autres valeurs que celles par défaut. Les propriétés de publication dans Azure se trouvent dans la section Déploiement ; vous devrez peut-être configurer également les paramètres MSBuild. Exemple : dans un projet de service cloud, pour spécifier la configuration d'un autre service que Cloud, configurez les paramètres MSbuild avec /p:TargetProfile=*YourProfile* où *YourProfile* correspond à un fichier de configuration de service avec un nom tel que ServiceConfiguration.*YourProfile*.cscfg.
Le tableau suivant présente les propriétés disponibles dans la section Déploiement :
	<table>
<tr><td><b>Propriété</b></td><td><b>Valeur par défaut</b></td></tr>
><tr><td>Autoriser les certificats non approuvés</td><td>Si cette propriété a la valeur false, des certificats SSL doivent être signés par une autorité racine.</td></tr>
<tr><td>Autoriser la mise à niveau</td><td>Permet au déploiement de mettre à jour un déploiement existant au lieu d'en créer un. Conserve l'adresse IP.</td></tr>
><tr><td>Ne pas supprimer</td><td>Si cette propriété a la valeur true, ne remplacez pas un déploiement sans rapport (la mise à niveau est autorisée).</td></tr>
<tr><td>Chemin d'accès des paramètres de déploiement</td><td>Chemin d'accès à votre fichier .pubxml pour un site web, relatif au dossier racine du référentiel. Ignorée pour les services cloud.</td></tr>
<tr><td>Environnement de déploiement SharePoint</td><td>Identique au nom du service</td></tr>
<tr><td>Environnement de déploiement Windows Azure</td><td>Nom du site web ou du service cloud</td></tr>
</table>
<br/>

11. À ce stade, la création de la build doit être terminée.<br/>
![][28]

12. Si vous double-cliquez sur le nom de la build, Visual Studio affiche un **Résumé de la build**, y compris tous les résultats de test provenant de projets de test unitaires associés.<br/>
![][29]

13. Dans le [portail Azure](http://manage.windowsazure.com), vous pouvez afficher le déploiement associé sous l'onglet Déploiements lorsque l'environnement intermédiaire est sélectionné.<br/>
![][30]

14.	Accédez à l'URL de votre site. Pour un site web, sélectionnez simplement le bouton **Parcourir** dans le portail. Dans le cas d'un service cloud, choisissez l'URL dans la section **Aperçu rapide** de la page **Tableau de bord** représentant l'environnement intermédiaire. Les déploiements résultant de l'intégration continue de services cloud sont publiés dans l'environnement intermédiaire par défaut. Vous pouvez changer cela en définissant la propriété Environnement du service cloud de substitution sur Production. Cet écran indique où se trouve l'URL du site dans la page du tableau de bord du service cloud : <br/>
![][31]
<br/>
Un nouvel onglet de navigateur apparaît pour afficher votre site en cours d'exécution.<br/>
![][32]

15.	Si vous apportez d'autres modifications à votre projet, vous déclenchez d'autres builds et vous accumulez plusieurs déploiements. Le plus récent est marqué comme Actif.<br/>
![][33]

<h2> <a name="step5"> </a>Étape 5 : redéploiement d'une build antérieure</h2>

Cette étape est facultative. Dans le portail de gestion, sélectionnez un déploiement antérieur et cliquez sur le bouton **Redéployer** pour revenir à un archivage antérieur de votre site. Notez que cette action va déclencher une nouvelle build dans TFS, et créer une nouvelle entrée dans l'historique de vos déploiements.<br/>
![][34]

<h2> <a name="step6"> </a>Étape 6 : modification du déploiement de production</h2>

 Une fois que vous êtes prêt, vous pouvez promouvoir l'environnement intermédiaire en environnement de production en sélectionnant **Basculer** dans le portail de gestion. L'environnement intermédiaire récemment déployé passe à l'état de production et l'environnement de production précédent, le cas échéant, devient un environnement intermédiaire. Le déploiement actif peut être différent pour les environnements de production et intermédiaire, mais l'historique de déploiement des builds récentes est identique quel que soit l'environnement.<br/>
![][35]

<h2> <a name="step7"> </a>Étape 6 : déploiement à partir d'une branche en cours d'utilisation.</h2>

Lorsque vous utilisez Git, vous apportez généralement des modifications à une branche en cours d'utilisation et les intégrez dans une branche principale lorsque votre déploiement est terminé. Pendant la phase de déploiement d'un projet, vous pouvez générer et déployer la branche en cours d'utilisation dans Azure.

1. Dans Team Explorer, sélectionnez le bouton **Accueil**, puis le bouton **Branches**.<br/>
![][40]

2. Sélectionnez le lien **Nouvelle branche**.<br/>
![][41]

3. Entrez le nom d'une branche (ex. " Utilisation en cours ") et sélectionnez **Créer une branche** pour créer une branche locale.<br/>
![][42]

4. Publiez la branche. Sélectionnez le nom de la branche dans **Branches non publiées**, puis **Publier**.<br/>
![][44]

6. Par défaut, seules les modifications apportées à la branche principale déclenchent une génération continue. Pour configurer une génération continue pour une branche en cours d'utilisation, sélectionnez la page Builds dans Team Explorer et sélectionnez **Modifier la définition de build**.

7. Ouvrez l'onglet **Paramètres de la source**. Sous **Branches surveillées pour l'intégration et la génération continue**, sélectionnez **Cliquez ici pour ajouter une ligne**.<br/>
![][47]

8. Spécifiez la branche que vous avez créée (par ex. refs/heads/working).
![][48]

9. Modifiez le code, ouvrez le menu contextuel du fichier modifié et sélectionnez **Valider**.<br/>
![][43]

10. Sélectionnez le lien **Validations non synchronisées**, puis le bouton **Synchronisation** ou le lien **Émettre** pour copier les modifications dans la copie de la branche en cours d'utilisation dans Visual Studio Online.
![][45]

11. Accédez à la vue **Builds** et recherchez la build qui vient d'être déclenchée pour la branche en cours d'utilisation.

Pour plus d'informations, consultez la page [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Pour des conseils supplémentaires sur l'utilisation de Git avec Visual Studio Online, consultez la page [Partager votre code dans Git] ;(http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) pour en savoir plus sur l'utilisation d'un référentiel Git qui n'est pas géré par Visual Studio Online pour la publication dans Azure, consultez la page [Publication à partir du contrôle de code source dans Sites Web Azure](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-publish-source-control).

[Étape 1 : création d'un référentiel Git.]: #step1
[Étape 2 : création d'un projet et placement dans votre référentiel Git.]: #step2
[Étape 3 : connexion du projet à Azure]: #step3
[Étape 4 : exécution des modifications et déclenchement d'une régénération et d'un redéploiement]: #step4
[Étape 5 : redéploiement d'une build antérieure (facultatif)]: #step5
[Étape 6 : modification du déploiement de production]: #step6
[Étape 7 : déploiement à partir d'une branche en cours d'utilisation]: #step7
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG

<!--HONumber=35.1-->
