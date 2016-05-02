<properties
	pageTitle="Livraison continue avec Git et Visual Studio Team Services dans Azure | Microsoft Azure"
	description="Découvrez comment configurer vos projets d'équipe Visual Studio Team Services afin d’utiliser Git pour les générer et les déployer automatiquement vers la fonctionnalité Web App d’Azure App Service ou des Cloud Services."
	services="cloud-services"
	documentationCenter=".net"
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="tarcher"/>

# Diffusion continue sur Azure au moyen de Visual Studio Team Services et Git

Vous pouvez utiliser des projets d'équipe Visual Studio Team Services pour héberger un référentiel Git pour votre code source, et pour les créer et déployer automatiquement dans des applications Web ou des Cloud Services Azure lorsque vous placez une validation dans le référentiel.

Visual Studio 2013 et le Kit de développement logiciel (SDK) Azure doivent être installés sur votre système. Si Visual Studio 2013 n'est pas déjà installé, téléchargez-le en choisissant le lien **Test gratuit de Visual Studio** sur [www.visualstudio.com](http://www.visualstudio.com). Pour installer le Kit de développement logiciel (SDK) Azure, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE] Vous avez besoin d’un compte Visual Studio Team Services pour suivre ce didacticiel : vous pouvez [ouvrir un compte Visual Studio Team Services gratuit](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Pour configurer un service cloud permettant de générer et de déployer automatiquement sur Azure au moyen de Visual Studio Team Services, suivez ces étapes.

## 1 : Création d'un référentiel Git

1. Si vous ne disposez pas de compte Visual Studio Team Services, vous pouvez en obtenir un [ici](http://go.microsoft.com/fwlink/?LinkId=397665). Lorsque vous créez un projet d'équipe, choisissez Git comme système de contrôle de code source. Suivez les instructions de connexion de Visual Studio à votre projet d'équipe.

2. Dans **Team Explorer**, sélectionnez le lien **Cloner ce référentiel**.

	![][3]

3. Indiquez l’emplacement de la copie locale, puis sélectionnez le bouton **Cloner**.

## 2 : Création d'un projet et validation dans le référentiel

1. Dans la section **Solutions** de **Team Explorer**, sélectionnez le lien **Nouveau** pour créer un projet dans le référentiel local.

	![][4]

2. Vous pouvez déployer une application Web ou un service cloud (application Azure) en suivant les étapes de cette procédure. Créez un projet de service cloud Azure ou un projet MVC ASP.NET. Assurez-vous que le projet cible le .NET Framework 4 ou version ultérieure. Si vous créez un projet de service cloud, ajoutez un rôle web ASP.NET MVC et un rôle de travail. Pour créer une application web, choisissez le modèle de projet **Application web ASP.NET**, puis sélectionnez **MVC**. Pour plus d’informations, consultez la rubrique [Création d’une application web ASP.NET dans Azure App Service](../app-service-web/web-sites-dotnet-get-started.md).

3. Ouvrez le menu contextuel de la solution et choisissez **Valider**.

	![][7]

4. Si vous utilisez Git dans Visual Studio Team Services pour la première fois, vous devez fournir des informations pour vous identifier dans Git. Dans la zone **Modifications en attente** de **Team Explorer**, entrez votre nom d’utilisateur et votre adresse de messagerie. Entrez un commentaire pour la validation, puis sélectionnez le bouton **Valider**.

	![][8]

5. Notez les options à inclure ou exclure quand vous archivez des modifications spécifiques. Si les modifications voulues sont exclues, choisissez **Tout inclure**.

6. Vous avez maintenant validé les modifications dans votre copie locale du référentiel. Synchronisez ensuite ces modifications avec le serveur en choisissant le lien **Synchronisation**.

## 3 : Connexion du projet à Azure

1. Vous avez maintenant un référentiel Git dans Visual Studio Team Services contenant du code source : vous êtes prêt à connecter votre référentiel Git à Azure. Dans le [portail Azure Classic](http://manage.windowsazure.com), sélectionnez votre service cloud ou application web, ou créez-en un en sélectionnant l’icône + en bas à gauche et en choisissant **Service cloud** ou **Application web**, puis **Création rapide**.

	![][9]

3. Pour les Cloud Services, sélectionnez le lien **Configurer la publication avec Visual Studio Team Services**. Pour les applications web, cliquez sur le lien **Configurer le déploiement à partir du contrôle de code source**.

	![][10]

2. Dans l'Assistant, tapez le nom de votre compte Visual Studio Team Services dans la zone de texte et choisissez le lien **Autoriser maintenant**. Vous serez peut-être invité à vous connecter.

	![][11]

3. Dans la boîte de dialogue contextuelle **Demande de connexion**, sélectionnez **Accepter** pour autoriser Azure à configurer votre projet d’équipe dans Visual Studio Team Services.

	![][12]

4. Si la procédure d’autorisation réussit, une zone déroulante affiche la liste de vos projets d’équipe Visual Studio Team Services. Sélectionnez le nom du projet d'équipe que vous avez créé aux étapes précédentes et choisissez la coche de l'Assistant.

	![][13]

	La prochaine fois que vous placerez une validation dans votre référentiel, Visual Studio Team Services génèrera et déploiera votre projet dans Azure.

## 4 : Déclenchement d'une régénération et redéploiement de votre projet

1. Dans Visual Studio, ouvrez un fichier et modifiez-le. Par exemple, modifiez le fichier `_Layout.cshtml` sous le dossier Views\\Shared dans un rôle Web MVC.

	![][17]

2. Modifiez le texte du pied de page du site et enregistrez le fichier.

	![][18]

3. Dans l’**Explorateur de solutions**, ouvrez le menu contextuel du nœud de la solution, du nœud du projet ou du fichier que vous avez modifié, puis sélectionnez **Valider**.

4. Tapez un commentaire et sélectionnez **Valider**.

	![][20]

5. Sélectionnez le lien **Synchronisation**.

	![][38]

6. Sélectionnez le lien **Pousser** pour placer votre validation dans le référentiel dans Visual Studio Team Services. (Vous pouvez également utiliser le bouton **Synchronisation** pour copier vos validations dans le référentiel. La différence est que **Synchronisation** extrait également les dernières modifications du référentiel).

	![][39]

7. Choisissez le bouton **Accueil** pour revenir à la page d'accueil de **Team Explorer**.

	![][21]

8. Choisissez **Builds** pour afficher les builds en cours.

	![][22]

	**Team Explorer** indique qu’une build est disponible pour archivage.

	![][23]

9. Double-cliquez sur le nom de la build en cours pour afficher un journal détaillé de la génération.

10. Pendant la création de la build, examinez la définition de build créée lorsque vous avez utilisé l'Assistant pour la liaison à Azure. Ouvrez le menu contextuel de la définition de build et choisissez **Modifier la définition de build**.

	![][25]

11. Sous l’onglet **Déclencher**, vous allez voir que la définition de build prévoit par défaut un processus de génération pour chaque archivage. (Pour un service cloud, Visual Studio Team Services génère et déploie automatiquement la branche principale dans l'environnement intermédiaire. Vous devez quand même effectuer une opération manuelle pour le déploiement dans le site Web en activité. Pour une application web qui ne comporte pas d’environnement intermédiaire, elle déploie la branche principale directement dans le site en activité.

	![][26]

1. Sous l’onglet **Processus**, vous pouvez voir que l’environnement de déploiement est défini sur le nom de votre service cloud ou application web.

	![][27]

1. Spécifiez des valeurs pour les propriétés si vous souhaitez d'autres valeurs que celles par défaut. Les propriétés de publication dans Azure se trouvent dans la section **Déploiement** ; vous devrez peut-être configurer également les paramètres MSBuild. Exemple : dans un projet de service cloud, pour spécifier la configuration d’un autre service que « Cloud », configurez les paramètres MSbuild avec `/p:TargetProfile=[YourProfile]` où *[YourProfile]* correspond à un fichier de configuration de service avec un nom tel que ServiceConfiguration.*YourProfile*.cscfg.

	Le tableau suivant présente les propriétés disponibles dans la section **Déploiement** :

	|Propriété|Valeur par défaut|
	|---|---|
	|Autoriser les certificats non approuvés|Si cette propriété a la valeur false, des certificats SSL doivent être signés par une autorité racine.|
	|Autoriser la mise à niveau|Permet au déploiement de mettre à jour un déploiement existant au lieu d'en créer un. Conserve l'adresse IP.|
	|Ne pas supprimer|Si cette propriété a la valeur true, ne remplacez pas un déploiement sans rapport (la mise à niveau est autorisée).|
	|Chemin d'accès des paramètres de déploiement|Chemin d’accès à votre fichier .pubxml pour un site Web, relatif au dossier racine du référentiel. Ignorée pour les services cloud.|
	|Environnement de déploiement SharePoint|Identique au nom du service.|
	|Environnement de déploiement Azure|Nom de l’application web ou du service cloud|

1. À ce stade, la création de la build doit être terminée.

	![][28]

1. Si vous double-cliquez sur le nom de la build, Visual Studio affiche un **Résumé de la build**, y compris tous les résultats de test provenant de projets de test unitaires associés.

	![][29]

1. Dans le [portail Azure Classic](http://manage.windowsazure.com), vous pouvez afficher le déploiement associé sous l’onglet **Déploiements** quand l’environnement intermédiaire est sélectionné.

	![][30]

1.	Accédez à l'URL de votre site. Pour une application Web, sélectionnez simplement le bouton **Parcourir** dans le portail. Dans le cas d'un service cloud, choisissez l'URL dans la section **Aperçu rapide** de la page **Tableau de bord** représentant l'environnement intermédiaire.

	Les déploiements résultant de l'intégration continue de services cloud sont publiés dans l'environnement intermédiaire par défaut. Vous pouvez changer cela en affectant à la propriété **Environnement du service cloud de substitution** la valeur **Production**. Cet écran indique où se trouve l’URL du site dans la page du tableau de bord du service cloud.

	![][31]

	Un nouvel onglet de navigateur apparaît pour afficher votre site en cours d'exécution.

	![][32]

1.	Si vous apportez d'autres modifications à votre projet, vous déclenchez d'autres builds et vous accumulez plusieurs déploiements. Le plus récent est marqué comme Actif.

	![][33]

## 5 : Redéploiement d'une build antérieure

Cette étape est facultative. Dans le portail Azure Classic, choisissez un déploiement antérieur et sélectionnez **Redéployer** pour revenir à un archivage antérieur de votre site. Notez que cela déclenche une nouvelle génération dans TFS et crée une entrée dans l’historique de votre déploiement.

![][34]

## 6 : Modification du déploiement de production

Une fois que vous êtes prêt, vous pouvez promouvoir l’environnement intermédiaire en environnement de production en sélectionnant **Basculer** dans le portail Azure Classic. L'environnement intermédiaire récemment déployé passe à l'état de production et l'environnement de production précédent, le cas échéant, devient un environnement intermédiaire. Le déploiement actif peut être différent pour les environnements de production et intermédiaire, mais l'historique de déploiement des builds récentes est identique quel que soit l'environnement.

![][35]

## 6 : Déploiement à partir d'une branche en cours d'utilisation

Lorsque vous utilisez Git, vous apportez généralement des modifications à une branche en cours d'utilisation et les intégrez dans une branche principale lorsque votre déploiement est terminé. Pendant la phase de déploiement d'un projet, vous pouvez générer et déployer la branche en cours d'utilisation dans Azure.

1. Dans **Team Explorer**, sélectionnez le bouton **Accueil**, puis le bouton **Branches**.

	![][40]

2. Sélectionnez le lien **Nouvelle branche**.

	![][41]

3. Entrez le nom d'une branche (ex. « Utilisation en cours ») et sélectionnez **Créer une branche** pour créer une branche locale.

	![][42]

4. Publiez la branche. Sélectionnez le nom de la branche dans **Branches non publiées**, puis **Publier**.

	![][44]

6. Par défaut, seules les modifications apportées à la branche principale déclenchent une génération continue. Pour configurer une génération continue pour une branche en cours d’utilisation, sélectionnez la page **Builds** dans **Team Explorer** et sélectionnez **Modifier la définition de build**.

7. Ouvrez l'onglet **Paramètres de la source**. Sous **Branches surveillées pour l'intégration et la génération continue**, sélectionnez **Cliquez ici pour ajouter une ligne**.

	![][47]

8. Spécifiez la branche que vous avez créée (par ex. refs/heads/working).

	![][48]

9. Modifiez le code, ouvrez le menu contextuel du fichier modifié et sélectionnez **Valider**.

	![][43]

10. Sélectionnez le lien **Validations non synchronisées**, puis le bouton **Synchronisation** ou le lien **Pousser** pour copier les modifications dans la copie de la branche en cours d'utilisation dans Visual Studio Team Services.

	![][45]

11. Accédez à la vue **Builds** et recherchez la build qui vient d'être déclenchée pour la branche en cours d'utilisation.

## Étapes suivantes

Pour obtenir des conseils supplémentaires sur l’utilisation de Git avec Visual Studio Team Services, consultez la page [Développer et partager votre code dans Git à l’aide de Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) ; pour en savoir plus sur l’utilisation d’un référentiel Git qui n’est pas géré par Visual Studio Team Services pour la publication dans Azure, consultez la page [Déploiement continu à l’aide de Git dans Azure App Service](../app-service-web/web-sites-publish-source-control.md). Pour en savoir plus sur Visual Studio Team Services, consultez [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

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
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
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

<!---HONumber=AcomDC_0420_2016-->