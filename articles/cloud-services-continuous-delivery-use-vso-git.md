<properties urlDisplayName="Publishing with Visual Studio Online" pageTitle="Livraison continue avec Visual Studio Online dans Azure" metaKeywords="" description="D&eacute;couvrez comment configurer vos projets d'&eacute;quipe Visual Studio Online afin de les g&eacute;n&eacute;rer et de les d&eacute;ployer automatiquement vers des sites web ou des services cloud Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Livraison continue sur Azure au moyen de Visual Studio Online et de Git" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Livraison continue sur Azure au moyen de Visual Studio Online et de Git

Vous pouvez utiliser et déployer des projets d'équipe Visual Studio Online pour héberger un référentiel Git pour votre code source, et créer et déployer automatiquement dans des sites web ou des services cloud Azure lorsque vous placez une validation dans le référentiel.

Visual Studio 2013 et le Kit de développement logiciel (SDK) Azure doivent être installés sur votre système. Si Visual Studio 2013 n'est pas déjà installé, téléchargez-le en choisissant le lien **Test gratuit de Visual Studio** sur [www.visualstudio.com][www.visualstudio.com]. Pour installer le Kit de développement logiciel (SDK) Azure, cliquez [ici][ici].

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Pour suivre ce didacticiel, vous avez besoin d'un compte Visual Studio Online&nbsp;:</h5>
<p>Vous pouvez <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">ouvrir gratuitement un compte Visual Studio Online</a>.</p>
</div>

Pour configurer un service cloud permettant de générer et de déployer automatiquement sur Azure au moyen de Visual Studio Online, procédez comme suit :

-   [Étape 1 : création d'un référentiel Git.][Étape 1 : création d'un référentiel Git.]

-   [Étape 2 : création d'un projet et placement dans votre référentiel Git.][Étape 2 : création d'un projet et placement dans votre référentiel Git.]

-   [Étape 3 : connexion du projet à Azure][Étape 3 : connexion du projet à Azure]

-   [Étape 4 : exécution des modifications et déclenchement d'une régénération et d'un redéploiement][Étape 4 : exécution des modifications et déclenchement d'une régénération et d'un redéploiement]

-   [Étape 5 : redéploiement d'une build antérieure (facultatif)][Étape 5 : redéploiement d'une build antérieure (facultatif)]

-   [Étape 6 : modification du déploiement de production][Étape 6 : modification du déploiement de production]

-   [Étape 7 : déploiement à partir d'une branche en cours d'utilisation][Étape 7 : déploiement à partir d'une branche en cours d'utilisation]

## <a name="step1"></a><span class="short-header">Étape 1 : Création d'un référentiel Git.</span>Étape 1 : création d'un référentiel Git

1.  Si vous n'avez pas encore un compte Visual Studio Online, [procédez comme suit][procédez comme suit]. Lorsque vous créez un projet d'équipe, choisissez Git comme système de contrôle de code source. Suivez les instructions de connexion de Visual Studio à votre projet d'équipe.

2.  Dans Team Explorer, sélectionnez le lien **Cloner ce référentiel**.
    ![][0]

3.  Indiquez l'emplacement de la copie locale et sélectionnez le bouton **Cloner**.

## <a name="step2"> </a><span class="short-header">Création d'un projet et validation dans le référentiel.</span>Étape 2 : création d'un projet et validation dans le référentiel

1.  Dans la section Solutions de Team Explorer, sélectionnez Nouveau lien pour créer un projet dans le référentiel local.
    ![][1]

2.  Vous pouvez déployer un site web ou un service cloud (application Azure) en suivant les instructions de cette procédure pas à pas.
    Créez un projet Windows Azure Cloud Service
    ou un projet ASP.NET MVC. Vérifiez que le projet cible .NET Framework 4 ou 4.5 et, si vous créez un projet de service cloud, ajoutez un rôle web et un rôle de travail ASP.NET MVC.
    Si vous voulez créer un site web, choisissez le modèle de projet Application web ASP.NET, puis sélectionnez MVC. Consultez la page [Prise en main d'Azure et ASP.NET][Prise en main d'Azure et ASP.NET].

3.  Ouvrez le menu contextuel de la solution et choisissez **Valider**.
    ![][2]

4.  Si vous utilisez Git dans Visual Studio Online pour la première fois, vous devez fournir des informations pour vous identifier dans Git. Dans la zone **Modifications en attente** de Team Explorer, entrez votre nom d'utilisateur et votre adresse de messagerie. Tapez un commentaire pour la validation et sélectionnez **Valider**.
    ![][3]

5.  Remarquez les options permettant d'inclure ou d'exclure des modifications spécifiques lorsque vous archivez. Si des modifications souhaitées sont exclues, choisissez le lien **Tout inclure**.

6.  Vous avez maintenant validé les modifications dans votre copie locale du référentiel. Synchronisez ensuite ces modifications avec le serveur. Sélectionnez le lien **Synchronisation**.

## <a name="step3"> </a><span class="short-header">Connexion du projet à Azure</span>Étape 3 : connexion du projet à Azure

1.  Vous avez maintenant un référentiel Git dans Visual Studio Online contenant du code source : vous êtes prêt à connecter votre référentiel Git à Azure. Dans le [portail Azure][portail Azure], sélectionnez votre service cloud ou votre site web, ou créez-en un en sélectionnant l'icône + en bas à gauche et en choisissant **Service cloud** ou **Site Web**, puis **Création rapide**.<br.>
    ![][4]

2.  Pour les services cloud, sélectionnez le lien **Configurer la publication avec Visual Studio Online**. Pour les sites web, cliquez sur le lien **Configurer le déploiement à partir du contrôle de code source**.
    ![][5]

3.  Dans l'Assistant, tapez le nom de votre compte Visual Studio Online dans la zone de texte et sélectionnez le lien **Autoriser maintenant**. Vous serez peut-être invité à vous connecter.
    ![][6]

4.  Dans la boîte de dialogue contextuelle OAuth, sélectionnez **Accepter** pour configurer votre projet d'équipe dans Visual Studio Online.
    ![][7]

5.  Si la procédure d'autorisation réussit, une zone déroulante affiche la liste de vos projets d'équipe Visual Studio Online. Sélectionnez le nom du projet d'équipe que vous avez créé aux étapes précédentes et choisissez la coche de l'Assistant.
    ![][8]

La prochaine fois que vous placerez une validation dans votre référentiel, Visual Studio Online génèrera et déploiera votre projet dans Azure.

## <a name="step4"> </a><span class="short-header">Déclenchement d'une régénération</span>Étape 4 : Déclenchement d'une régénération et redéploiement de votre projet

1.  Dans Visual Studio, ouvrez un fichier et modifiez-le. Par exemple, modifiez le fichier \_Layout.cshtml sous le dossier Views\\Shared dans un rôle Web MVC.
    ![][9]

2.  Modifiez le texte du pied de page du site et enregistrez le fichier.
    ![][10]

3.  Dans l'Explorateur de solutions, ouvrez le menu contextuel du nœud de la solution, du nœud du projet ou du fichier que vous avez modifié et sélectionnez **Valider**.

4.  Tapez un commentaire et sélectionnez **Valider**.
    ![][3]

5.  Sélectionnez le lien **Synchronisation**.
    ![][11]

6.  Sélectionnez le lien **Pousser** pour placer votre validation dans le référentiel dans Visual Studio Online. (Vous pouvez également utiliser le bouton **Synchronisation** pour copier vos validations dans le référentiel. La différence est que **Synchronisation** extrait également les dernières modifications du référentiel).
    ![][12]

7.  Choisissez le bouton Accueil pour revenir à la page d'accueil de Team Explorer.
    ![][13]

8.  Sélectionnez **Builds** pour afficher les builds en cours.
    ![][14]
    Team Explorer indique qu'une build est disponible pour l'archivage.
    ![][15]

9.  Double-cliquez sur le nom de la build en cours pour afficher un journal détaillé de la génération.

10. Pendant la création de la build, examinez la définition de build créée lorsque vous avez utilisé l'Assistant pour la liaison à Azure. Ouvrez le menu contextuel de la définition de build et choisissez **Modifier la définition de build.**
    ![][16]
    Dans l'onglet **Déclencher**, vous constatez que la définition de build prévoit par défaut une génération pour chaque archivage. (Pour un service cloud, Visual Studio Online génère et déploie automatiquement la branche principale dans l'environnement intermédiaire. Vous devez quand même effectuer une opération manuelle pour le déploiement dans le site web en activité. Pour un site web qui ne comporte pas d'environnement intermédiaire, elle déploie la branche principale directement dans le site en activité.
    ![][17]
    Dans l'onglet **Processus**, vous constatez que l'environnement de déploiement est configuré avec le nom de votre service cloud ou de votre site web.
    ![][18]
    Spécifiez des valeurs pour les propriétés si vous souhaitez des valeurs différentes de celles par défaut. Les propriétés de publication dans Azure se trouvent dans la section Déploiement ; vous devrez peut-être configurer également les paramètres MSBuild. Exemple : dans un projet de service cloud, pour spécifier la configuration d'un autre service que Cloud, configurez les paramètres MSbuild avec /p:TargetProfile=*YourProfile* où *YourProfile* correspond à un fichier de configuration de service avec un nom tel que ServiceConfiguration.*YourProfile*.cscfg.
    Le tableau ci-dessous indique toutes les propriétés disponibles dans la section Déploiement :

    <table>

    <tr>
    <td>
    **Propriété**

    </td>
    <td>
    **Valeur par défaut**

    </td>
    </tr>
    </p>
    > <tr>
    > <td>
    > Autoriser les certificats non approuvés
    >
    > </td>
    > <td>
    > Si cette propriété a la valeur false, des certificats SSL doivent être signés par une autorité racine.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Autoriser la mise à niveau
    >
    > </td>
    > <td>
    > Permet au déploiement de mettre à jour un déploiement existant au lieu d'en créer un. Conserve l'adresse IP.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Ne pas supprimer
    >
    > </td>
    > <td>
    > Si cette propriété a la valeur true, ne remplacez pas un déploiement sans rapport (la mise à niveau est autorisée).
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Chemin d'accès des paramètres de déploiement
    >
    > </td>
    > <td>
    > Chemin d'accès à votre fichier .pubxml pour un site web, relatif au dossier racine du référentiel. Ignorée pour les services cloud.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Environnement de déploiement SharePoint
    >
    > </td>
    > <td>
    > Identique au nom du service
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Environnement de déploiement Windows Azure
    >
    > </td>
    > <td>
    > Nom du site web ou du service cloud
    >
    > </td>
    > </tr>
    > </table>

11. À ce stade, la création de la build doit être terminée.
    ![][19]

12. Si vous double-cliquez sur le nom de la build, Visual Studio affiche un **Résumé de la build**, y compris tous les résultats de test provenant de projets de test unitaires associés.
    ![][20]

13. Dans le [portail Azure][portail Azure], vous pouvez afficher le déploiement associé sous l'onglet Déploiements lorsque l'environnement intermédiaire est sélectionné.
    ![][21]

14. Accédez à l'URL de votre site. Pour un site web, sélectionnez simplement le bouton **Parcourir** dans le portail. Dans le cas d'un service cloud, choisissez l'URL dans la section **Aperçu rapide** de la page **Tableau de bord** représentant l'environnement intermédiaire. Les déploiements résultant de l'intégration continue de services cloud sont publiés dans l'environnement intermédiaire par défaut. Vous pouvez changer cela en définissant la propriété Environnement du service cloud de substitution sur Production. Cet écran indique où se trouve l'URL du site dans la page du tableau de bord du service cloud :
    ![][22]
    Un nouvel onglet de navigateur apparaît pour afficher votre site en cours d'exécution.
    ![][23]

15. Si vous apportez d'autres modifications à votre projet, vous déclenchez d'autres builds et vous accumulez plusieurs déploiements. Le plus récent est marqué comme Actif.
    ![][24]

## <a name="step5"> </a><span class="short-header">Redéploiement d'une build antérieure</span>Étape 5 : redéploiement d'une build antérieure

Cette étape est facultative. Dans le portail de gestion, sélectionnez un déploiement antérieur et cliquez sur le bouton **Redéployer** pour revenir à un archivage antérieur de votre site. Notez que cette action va déclencher une nouvelle build dans TFS, et créer une nouvelle entrée dans l'historique de vos déploiements.
![][25]

## <a name="step6"> </a><span class="short-header">Modification du déploiement de production</span>Étape 6 : modification du déploiement de production

Une fois que vous êtes prêt, vous pouvez promouvoir l'environnement intermédiaire en environnement de production en sélectionnant **Basculer** dans le portail de gestion. L'environnement intermédiaire récemment déployé passe à l'état de production et l'environnement de production précédent, le cas échéant, devient un environnement intermédiaire. Le déploiement actif peut être différent pour les environnements de production et intermédiaire, mais l'historique de déploiement des builds récentes est identique quel que soit l'environnement.
![][26]

## <a name="step7"> </a><span class="short-header">Déploiement à partir d'une branche en cours d'utilisation</span>Étape 6 : déploiement à partir d'une branche en cours d'utilisation.

Lorsque vous utilisez Git, vous apportez généralement des modifications à une branche en cours d'utilisation et les intégrez dans une branche principale lorsque votre déploiement est terminé. Pendant la phase de déploiement d'un projet, vous pouvez générer et déployer la branche en cours d'utilisation dans Azure.

1.  Dans Team Explorer, sélectionnez le bouton **Accueil**, puis le bouton **Branches**.
    ![][27]

2.  Sélectionnez le lien **Nouvelle branche**.
    ![][28]

3.  Entrez le nom d'une branche (ex. « Utilisation en cours ») et sélectionnez **Créer une branche** pour créer une branche locale.
    ![][29]

4.  Publiez la branche. Sélectionnez le nom de la branche dans **Branches non publiées**, puis **Publier**.
    ![][30]

5.  Par défaut, seules les modifications apportées à la branche principale déclenchent une génération continue. Pour configurer une génération continue pour une branche en cours d'utilisation, sélectionnez la page Builds dans Team Explorer et sélectionnez **Modifier la définition de build**.

6.  Ouvrez l'onglet **Paramètres de la source**. Sous **Branches surveillées pour l'intégration et la génération continue**, sélectionnez **Cliquez ici pour ajouter une ligne**.
    ![][31]

7.  Spécifiez la branche que vous avez créée (par ex. refs/heads/working).
    ![][32]

8.  Modifiez le code, ouvrez le menu contextuel du fichier modifié et sélectionnez **Valider**.
    ![][3]

9.  Sélectionnez le lien **Validations non synchronisées**, puis le bouton **Synchronisation** ou le lien **Pousser** pour copier les modifications dans la copie de la branche en cours d'utilisation dans Visual Studio Online.
    ![][11]

10. Accédez à la vue **Builds** et recherchez la build qui vient d'être déclenchée pour la branche en cours d'utilisation.

Pour plus d'informations, consultez la page [Visual Studio Online][Visual Studio Online]. Pour des conseils supplémentaires sur l'utilisation de Git avec Visual Studio Online, consultez la page [Partager votre code dans Git][Partager votre code dans Git] ; pour en savoir plus sur l'utilisation d'un référentiel Git qui n'est pas géré par Visual Studio Online pour la publication dans Azure, consultez la page [Publication à partir du contrôle de code source dans Sites Web Azure][Publication à partir du contrôle de code source dans Sites Web Azure].

  [www.visualstudio.com]: http://www.visualstudio.com
  [ici]: http://go.microsoft.com/fwlink/?LinkId=239540
  [Étape 1 : création d'un référentiel Git.]: #step1
  [Étape 2 : création d'un projet et placement dans votre référentiel Git.]: #step2
  [Étape 3 : connexion du projet à Azure]: #step3
  [Étape 4 : exécution des modifications et déclenchement d'une régénération et d'un redéploiement]: #step4
  [Étape 5 : redéploiement d'une build antérieure (facultatif)]: #step5
  [Étape 6 : modification du déploiement de production]: #step6
  [Étape 7 : déploiement à partir d'une branche en cours d'utilisation]: #step7
  [procédez comme suit]: http://go.microsoft.com/fwlink/?LinkId=397665
  [0]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
  [1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
  [Prise en main d'Azure et ASP.NET]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-dotnet-get-started/
  [2]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
  [3]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
  [portail Azure]: http://manage.windowsazure.com
  [4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
  [5]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
  [6]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
  [7]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
  [8]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
  [9]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [10]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
  [11]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
  [12]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
  [13]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
  [14]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
  [15]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
  [16]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
  [19]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
  [28]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
  [29]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
  [30]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
  [31]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
  [32]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
  [Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Partager votre code dans Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Publication à partir du contrôle de code source dans Sites Web Azure]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-publish-source-control
