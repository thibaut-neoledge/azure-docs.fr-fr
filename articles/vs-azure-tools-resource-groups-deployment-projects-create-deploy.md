<properties 
   pageTitle="Création et déploiement de projets de déploiement de groupe de ressources Azure"
   description="Création et déploiement de projets de déploiement de groupe de ressources Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="kempb" />

# Création et déploiement de projets de déploiement de groupe de ressources Azure

Le modèle de projet de déploiement de **groupe de ressources Azure** est disponible dans Visual Studio si le Kit de développement logiciel (SDK) Azure 2.6 est installé. Les projets de groupe de ressources Azure vous permettent de regrouper et de publier plusieurs ressources Azure connexes dans une même opération de déploiement. Les projets de groupe de ressources Azure utilisent une technologie appelée **Azure Resource Manager** pour leur mise en œuvre. **Azure Resource Manager** est un service d'API REST permettant de définir des groupes de ressources Azure qui contiennent plusieurs ressources Azure généralement utilisées ensemble et dont le cycle de vie est similaire. À l'aide des groupes de ressources, vous pouvez utiliser toutes les ressources d'un groupe avec un seul appel de fonction, au lieu d'appeler différentes fonctions pour chaque ressource. Pour plus d'informations sur les groupes de ressources Azure, consultez [Utilisation du portail Azure en version préliminaire pour gérer vos ressources Azure](./azure-portal/azure-preview-portal-using-resource-groups/).

Les projets de groupe de ressources Azure contiennent des modèles JSON Azure Resource Manager, qui définissent les éléments déployés dans un groupe de ressources. Pour plus d’informations, consultez [Langue du modèle Azure Resource Manager](resource-group-authoring-templates.md).

Azure Resource Manager intègre différents fournisseurs de ressources permettant de déployer des ressources telles qu'Ubuntu Server et Windows Server 2012 R2. Cette rubrique utilise une ressource nommée **Web Apps**, qui déploie un site Web de base vide dans Azure.

## Création de projets de groupe de ressources Azure

Au cours de cette procédure, vous apprenez à créer un projet de groupe de ressources Azure avec un modèle d'**application web**.

### Pour créer un projet de groupe de ressources Azure

1. Dans Visual Studio, sélectionnez **Fichier**, **Nouveau projet**, choisissez **C#** ou **Visual Basic**. Puis choisissez **Cloud** et le projet de **groupe de ressources Azure**.

    ![Projet de déploiement cloud](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Choisissez le modèle à déployer sur Azure Resource Manager. Pour cet exemple, nous allons sélectionner le modèle** Application Web**.

    ![Sélectionner un modèle](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796669.png)

    Vous pouvez également ajouter ultérieurement des ressources au groupe de ressources.

    >[AZURE.NOTE]La liste des modèles disponibles est récupérée en ligne et peut être modifiée.

    Visual Studio crée un projet de déploiement de groupe de ressources Azure pour une application web.

1. Développez les nœuds du projet de déploiement pour voir les éléments créés.

    Puisque nous avons choisi le modèle Application web pour cet exemple, vous voyez les fichiers suivants.

|Nom de fichier|Description|
|---|---|
|Deploy-AzureResourceGroup.ps1|Script PowerShell qui appelle des commandes PowerShell à déployer sur Azure Resource Manager.

**Remarque** Visual Studio utilise ce script PowerShell pour déployer votre modèle. Les modifications apportées à ce script affectant également le déploiement dans Visual Studio, soyez prudent.| !WebSite.json|Fichier de configuration qui spécifie les détails des éléments à déployer sur Azure Resource Manager.| |WebSite.param.dev.json|Fichier de paramètres contenant des valeurs spécifiques requises par le fichier de configuration.| |AzCopy.exe|Outil dont le script PowerShell se sert pour copier des fichiers du chemin d'accès de stockage local vers le conteneur du compte de stockage. Cet outil ne sert que si vous configurez le projet de déploiement afin de déployer votre code en même temps que le modèle.

Tous les projets de déploiement de groupe de ressources Azure contiennent ces quatre fichiers de base. D'autres projets peuvent contenir des fichiers supplémentaires pour prendre en charge d'autres fonctionnalités.

## Personnalisation d'un projet de groupe de ressources Azure

Vous pouvez personnaliser un projet de déploiement en modifiant les fichiers de modèle JSON qui décrivent les ressources Azure à déployer. JSON signifie JavaScript Object Notation. Il s'agit d'un format de données sérialisées facile à utiliser.

Les projets de groupe de ressources Azure comprennent deux fichiers de modèle dans le nœud **Modèles** dans l'Explorateur de solutions que vous pouvez modifier : un fichier de modèle Azure Resource Manager et un fichier de paramètres.

- **Les fichiers de modèle Azure Resource Manager** (qui ont l'extension .json) spécifient les fichiers contenant les ressources désirées, ainsi que les paramètres requis par le projet de déploiement, tels que le nom et l'emplacement du site. Ils indiquent également les dépendances des composants dans le groupe de ressources Azure, ainsi que leurs propriétés, telles que les noms, les balises et les règles relatives aux déclencheurs. Vous pouvez modifier ce fichier pour ajouter vos propres fonctionnalités. Par exemple, vous pouvez ajouter une base de données au modèle. Pour connaître les paramètres à spécifier, consultez la documentation de chaque fournisseur de ressources. Pour plus d'informations, consultez [Fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790572.aspx).

- Les **fichiers de paramètres** (qui ont l'extension. param.*.json) contiennent des valeurs pour les paramètres spécifiés dans le fichier de configuration, qui sont nécessaires pour chaque fournisseur de ressources. Dans cet exemple, le fichier de configuration pour une application web (WebSite.json) définit les paramètres pour siteName et siteLocation. Durant le déploiement, vous êtes invité à fournir des valeurs pour les paramètres dans le fichier de modèle, et ces valeurs sont stockées dans le fichier de paramètres. Vous pouvez également modifier le fichier de paramètres directement.

Les fichiers JSON peuvent être modifiés dans l'éditeur Visual Studio. En installant les [Outils PowerShell pour Visual Studio](https://visualstudiogallery.msdn.microsoft.com/c9eb3ba8-0c59-4944-9a62-6eee37294597), vous bénéficiez également des fonctions de mise en surbrillance en couleur de la syntaxe, de correspondance d'accolades et IntelliSense, qui facilitent la lecture et l'édition des scripts PowerShell. Un lien pour installer les outils PowerShell s'affiche en haut de l'éditeur s'ils ne sont pas déjà installés.

![Installer des outils PowerShell](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796671.png)

Les fichiers JSON utilisent un schéma référencé au début de chaque fichier. Vous pouvez télécharger le schéma et l'analyser si vous souhaitez mieux le comprendre. Le schéma définit les éléments autorisés, les types et les formats des champs, les valeurs énumérées possibles, etc.

Si vous souhaitez déployer dans différentes configurations ou modifier régulièrement les paramètres, vous pouvez créer différentes copies du fichier *param*. Essayez d'utiliser le même modèle pour tous les environnements.

## Déploiement d'un projet de groupe de ressources Azure dans un groupe de ressources Azure

Lorsque vous déployez un projet de groupe de ressources Azure, faites-le dans un groupe de ressources Azure. Il s'agit simplement d'un regroupement logique dans Azure de ressources telles que des applications web, des bases de données, etc.

1. Dans le menu contextuel du nœud du projet de déploiement, choisissez **Déployer**, **Nouveau déploiement**.

    ![Déployer, élément de menu Nouveau déploiement](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    La boîte de dialogue **Déployer vers le groupe de ressources** s'affiche.

    ![Boîte de dialogue Déployer vers le groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796673.png)

1. Choisissez le groupe de ressources dans la zone de liste déroulante **Groupe de ressources**, ou créez-en un. Pour créer un groupe de ressources, ouvrez la zone de liste déroulante **Groupe de ressources**, puis choisissez **<Create New...>**.

    La boîte de dialogue **Créer un groupe de ressources** s'affiche.

    ![Boîte de dialogue Créer un groupe de ressources](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796674.png)

    >[AZURE.NOTE]En général, lorsque vous démarrez un nouveau projet de déploiement, vous commencez par créer un nouveau groupe de ressources dans lequel effectuer le déploiement.

1. Entrez un nom et un emplacement pour le groupe de ressources, puis choisissez **Créer**.

    L'emplacement du groupe de ressources ne doit pas être identique à celui des ressources, dans la mesure où les ressources d'un groupe peuvent s'étendre sur plusieurs régions.

1. Choisissez la configuration de modèle et les fichiers de paramètres à utiliser pour ce déploiement, ou acceptez le paramétrage par défaut.

    Pour modifier les propriétés d'une ressource, sélectionnez le bouton **Modifier les paramètres**. Si des paramètres obligatoires sont manquent lors du déploiement, la boîte de dialogue **Modifier les paramètres** s'affiche pour vous permettre de les fournir. S'il manque des valeurs à certains paramètres, **<null>** apparaît dans le champ **Valeur** en regard. Pour cet exemple (ressource d'application web), les paramètres requis comprennent le nom du site, le plan d'hébergement et l'emplacement du site (pour mémoire, ces valeurs de paramètres sont définies sur null par défaut dans le fichier de paramètres). Les autres paramètres ont des valeurs par défaut.

    ![Boîte de dialogue Modifier les paramètres](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796675.png)

1. Dans la boîte de dialogue **Modifier les paramètres**, entrez le nom du site, l'emplacement du site, le nom du plan d'hébergement, puis vérifiez les valeurs de toutes les autres propriétés. Lorsque vous avez terminé, cliquez sur le bouton **Enregistrer**.

    - Le paramètre *siteName* est la première partie de l'URL de la page web. Par exemple, dans l'URL nomdemonsiteweb.azurewebsites.net, le nom du site est nomdemonsiteweb.

    - Le paramètre *hostingPlanName* spécifie votre plan d'hébergement. Dans cet exemple, vous pouvez utiliser « Gratuit ». Pour plus d'informations sur les plans d'hébergement, consultez la rubrique [Présentation détaillée des plans Azure App Service](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/)

    - Le paramètre *siteLocation* fait référence à la région Azure dans laquelle le site doit être hébergé, par exemple « Ouest des États-Unis ». Pour obtenir la liste des régions disponibles, consultez la rubrique [Régions Azure](http://azure.microsoft.com/regions/).

1. Sélectionnez le bouton **Déployer** pour déployer le projet dans Azure.

    Vous pouvez suivre la progression du déploiement dans la fenêtre **Sortie**. Le déploiement peut prendre quelques minutes. Sa durée dépend de votre configuration.

    >[AZURE.NOTE]Il se peut que vous soyez invité à installer les applets de commande Microsoft Azure PowerShell. Ces applets de commande étant nécessaires pour déployer des groupes de ressources Azure, vous devez les installer.

1. Dans un navigateur, ouvrez le [Portail Azure](https://portal.azure.com/). Dans la mesure où il s'agit d'une nouvelle modification, un nouveau message de notification doit être disponible dans l'onglet **Notifications**. Sélectionnez-le pour afficher des détails concernant le nouveau groupe de ressources Azure. Pour afficher la liste de tous les groupes de ressources disponibles, accédez à l'onglet **Parcourir**, puis sélectionnez **Groupes de ressources**.

    ![Le groupe de ressources Azure approvisionné](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796676.png)

1. Si vous modifiez et redéployez votre projet, vous pouvez choisir le groupe de ressources existant directement dans le menu contextuel du projet de groupe de ressources Azure. Dans le menu contextuel, sélectionnez **Déployer**, puis le groupe de ressources dans lequel vous venez d'effectuer le déploiement.

    ![Groupe de ressources Azure déployé](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796677.png)

    Le déploiement d'un groupe de ressources Azure concerne uniquement ce projet. Si votre solution contient un projet de code ou d'autres projets, vous devez les déployer séparément.

## Utilisation de projets de déploiement cloud du Kit de développement logiciel (SDK) Azure 2.5 avec le Kit de développement logiciel (SDK) Azure 2.6

Si vous utilisez des projets de déploiement cloud créés avec le Kit de développement logiciel (SDK) Azure 2.5, vous devez procéder à une mise à niveau vers le Kit de développement logiciel (SDK) Azure 2.6 pour pouvoir utiliser les nouvelles fonctionnalités d'édition et de déploiement de modèles de ressources Azure. Pour réutiliser les modèles que vous avez créés avec le Kit de développement logiciel (SDK) Azure 2.5, la solution la plus simple consiste à créer la version Kit de développement logiciel (SDK) Azure 2.6 du projet, à déplacer vos modèles vers ce projet et à y apporter des modifications.

### Pour utiliser des projets de déploiement cloud créés à l'aide du Kit de développement logiciel (SDK) Azure 2.5 avec le Kit de développement logiciel (SDK) Azure 2.6.

1. Ajoutez un projet Groupe de ressources Azure du Kit de développement logiciel (SDK) Azure 2.6 à votre solution.

    1. Ouvrez la solution contenant votre projet de déploiement cloud créé avec le Kit de développement logiciel (SDK) Azure 2.5.

    1. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet**.

    1. Dans la boîte de dialogue **Nouveau projet**, recherchez le projet **Groupe de ressources Azure** sous **Visual C#**/**Cloud** ou **Visual Basic**/**Cloud**.
    
         Le nom de modèle de projet **Déploiement cloud** a été remplacé par Groupe de ressources Azure.

    1. Donnez un nom au projet.

    1. Sélectionnez **Ajouter à la solution** dans la zone de liste déroulante de solutions.

    1. Ensuite, vous êtes invité à sélectionner un modèle. Dans la mesure où vous déplacez vos modèles existants à partir de votre projet de déploiement cloud créé avec le Kit de développement logiciel (SDK) Azure 2.5, vous pouvez choisir un modèle quelconque. Choisissons le modèle vide au bas de la liste.

    1. Sélectionnez le bouton **OK**.
    
        Le nouveau projet est ajouté à votre solution.

1. Copiez votre modèle et les fichiers de paramètres du projet de déploiement cloud du Kit de développement logiciel (SDK) Azure 2.5 vers votre projet de groupe de ressources dans le Kit de développement logiciel (SDK) Azure 2.6.

    1. Dans l'Explorateur de solutions, recherchez le modèle et les fichiers de paramètres à copier dans le projet de déploiement du Kit de développement logiciel (SDK) Azure 2.5, sélectionnez-les, puis copiez-les.
    
    2. Collez les fichiers dans le dossier **Modèles** de votre nouveau projet de groupe de ressources du Kit de développement logiciel (SDK) Azure 2.6.

1. Dans l'Explorateur de solutions, recherchez le modèle et les fichiers de paramètres à copier dans le projet de déploiement du Kit de développement logiciel (SDK) Azure 2.5, sélectionnez-les, puis copiez-les.

1. Collez les fichiers dans le dossier Modèles de votre nouveau projet de groupe de ressources du Kit de développement logiciel (SDK) Azure 2.6.

1. Si vous déployez également une application Web dans votre modèle, vous devez créer une référence du nouveau projet de groupe de ressources du Kit de développement logiciel (SDK) Azure 2.6 vers votre application Web.

    1. Dans le menu contextuel du nœud **Références** de votre nouveau projet de groupe de ressources du Kit de développement logiciel (SDK) Azure 2.6 dans l'Explorateur de solutions, sélectionnez **Ajouter une référence**.

    1. Activez la case à cocher en regard de votre application Web dans la liste de projets, puis cliquez sur le bouton **OK**.

1. Dans le menu contextuel du nœud Références de votre nouveau projet de groupe de ressources du Kit de développement logiciel (SDK) Azure 2.6 dans l'Explorateur de solutions, sélectionnez Ajouter une référence.

1. Activez la case à cocher en regard de votre application Web dans la liste de projets, puis cliquez sur le bouton OK.

1. Renommez toutes les occurrences des paramètres *dropLocation* et *dropLocationSasToken* en *\_artifactsLocation* et *\_artifactsLocationSasToken*.

1. Si vous ne comptez pas les utiliser, vous pouvez supprimer le modèle et les fichiers de paramètres vides qui ont été automatiquement ajoutés au projet du Kit de développement logiciel (SDK) Azure 2.6 lors de sa création.

    1. Supprimez le fichier DeployTemplate.json.

    1. Supprimez le fichier DeploymentTemplate.param.dev.json.

1. Si vous avez apporté des modifications au script Publish-AzureResourceGroup.ps1 dans le projet du Kit de développement logiciel (SDK) Azure 2.5, vous devez transposer ces modifications dans le script Deploy-AzureResourceGroup.ps1 du projet du Kit de développement logiciel (SDK) Azure 2.6.

    Vous pouvez à présent utiliser la commande de déploiement pour déployer votre modèle dans le projet de groupe de ressources du Kit de développement logiciel (SDK) Azure 2.6 et tirer parti des nouvelles fonctionnalités de modification des modèles dans le Kit de développement logiciel (SDK) Azure 2.6. Une fois que le projet 2.6 fonctionne comme vous le souhaitez, vous pouvez supprimer le projet du Kit de développement logiciel (SDK) Azure 2.5 de votre solution.

## Raisons de la mise à jour du projet

Des modifications ont été apportées aux modèles déployés dans le Kit de développement logiciel (SDK) Azure 2.6. Elles rendent le script de déploiement du Kit de développement logiciel (SDK) Azure 2.5 et les modèles incompatibles. La première modification, qui est également la plus importante, est le lancement du déploiement. Dans le Kit de développement logiciel (SDK) Azure 2.5, nous avions compilé un code qui utilisait des API REST Azure pour charger votre modèle et lancer le déploiement. Nous avons reçu des commentaires de nombreux développeurs souhaitant que Visual Studio lance uniquement le script PowerShell inclus dans le projet. Par conséquent, dans le Kit de développement logiciel (SDK) Azure 2.6, la commande de déploiement lance le script PowerShell inclus dans le projet pour déployer le modèle. Cela vous permet de personnaliser le déploiement et d'obtenir que ces personnalisations s'exécutent toujours, que vous déployiez à partir de la ligne de commande à l'aide d'Azure PowerShell ou de Visual Studio à l'aide de la commande de déploiement. Pour déployer à partir de Visual Studio, vous devez utiliser le script de déploiement PowerShell du Kit de développement logiciel (SDK) Azure 2.6 s'il est installé.

Des ajustements ont été également apportés à un certain nombre de noms de variables et de tâches de génération afin de respecter les conventions d'affectation de noms dans les builds TFS (Team Foundation Server) automatisées et d'autres projets chez Microsoft. Dans Visual Studio, le code qui rassemble les variables et les valeurs nécessaires pour lancer le script PowerShell recherche ces nouveaux noms.

## Étapes suivantes

Pour savoir comment ajouter des ressources à votre groupe de ressources Azure dans Visual Studio, consultez la rubrique [Ajout de ressources à un groupe de ressources Azure](https://msdn.microsoft.com/library/azure/mt125415.aspx).

<!---HONumber=Sept15_HO2-->