<properties
	pageTitle="Intégration continue dans Visual Studio Team Services à l’aide de projets de projets Groupe de ressources Azure | Microsoft Azure"
	description="Décrit la configuration de l’intégration continue dans Visual Studio Team Services en utilisant des projets de déploiement Groupe de ressources Azure dans Visual Studio."
	services="visual-studio-online"
	documentationCenter="na"
	authors="mlearned"
	manager="erickson-doug"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/01/2016"
	ms.author="mlearned" />

# Intégration continue dans Visual Studio Team Services à l’aide de projets de déploiement de groupe de ressources Azure

Pour déployer un modèle Azure, vous devez effectuer les tâches pour franchir différentes étapes : Générer, Tester, Copier sur Azure (également appelé « gestion intermédiaire ») et le modèle de déploiement. Il existe deux façons de procéder dans Visual Studio Team Services (Visual Studio Team Services). Les deux méthodes fournissent les mêmes résultats. Par conséquent, choisissez celle qui convient le mieux à votre flux de travail.

-	Ajoutez une opération unique à la définition de build qui exécute le script PowerShell inclus dans le projet de déploiement du groupe de ressources Azure (Deploy-AzureResourceGroup.ps1). Le script copie les artefacts et déploie ensuite le modèle.
-	Ajouter plusieurs étapes de build à Visual Studio Team Services, chacune effectuant une tâche intermédiaire.

Cet article montre comment utiliser la première option (utilisez une définition de build pour exécuter le script PowerShell). Un des avantages de cette option est que le script utilisé par les développeurs dans Visual Studio est le même que celui qui est utilisé par Visual Studio Team Services. Cette procédure suppose que vous disposez déjà d’un projet de déploiement Visual Studio contrôlé dans Visual Studio Team Services.

## Copier des artefacts sur Azure 

Quel que soit le scénario, si vous disposez de tous les artefacts nécessaires au déploiement du modèle, vous devrez octroyer un accès au Gestionnaire de ressources Azure. Ces artefacts peuvent inclure des fichiers tels que :

-	des modèles imbriqués
-	des scripts de configuration et DSC
-	fichiers binaires d’application

### Modèles imbriqués et les scripts de configuration
Lorsque vous utilisez les modèles fournis par Visual Studio (ou générés à l’aide d’extraits de code Visual Studio), le script PowerShell ne se contente pas de préparer les artefacts. Elle ajuste également l’URI correspondant aux différentes ressources de déploiement. Le script copie les artefacts vers un conteneur sécurisé dans Azure, crée un jeton SAP pour ce conteneur, puis transmet ces informations au déploiement du modèle. Consultez la section [Créer un modèle de déploiement](https://msdn.microsoft.com/library/azure/dn790564.aspx) pour en savoir plus sur les modèles imbriqués.

## Configurer le déploiement continu dans Visual Studio Team Services

Pour appeler le script PowerShell dans Visual Studio Team Services, vous devez mettre à jour votre définition de build. En bref, les étapes sont :

1.	la modification des définitions de build.
1.	la définition de l’autorisation Azure dans Visual Studio Team Services.
1.	l’ajout d’une étape de build d’Azure PowerShell faisant référence au script PowerShell dans le projet de déploiement de groupe de ressources Azure.
1.	la définition de la valeur du paramètre *-ArtifactsStagingDirectory* pour qu’il fonctionne avec un projet créé dans Visual Studio Team Services.

### Procédure pas à pas

La procédure suivante vous guidera à travers les étapes nécessaires à la configuration d’un déploiement continu dans Visual Studio Team Services

1.	Modifiez votre définition de build de Visual Studio Team Services et ajoutez une étape de build Azure PowerShell. Choisissez la définition de build sous la catégorie **Définitions de build**, puis choisissez le lien **Modifier**.

    ![][0]

1.	Ajoutez une nouvelle étape **Azure PowerShell** à la définition de build, puis sélectionnez le bouton **Ajouter une étape de build…**.

    ![][1]

1.	Choisissez la catégorie **Déployer une tâche**, sélectionnez la tâche **Azure PowerShell**, puis choisissez son bouton **Ajouter**.

    ![][2]

1.	Choisissez l’étape de build **Azure PowerShell**, puis renseignez ses valeurs.

    1.	Si vous disposez déjà d’un point de terminaison de service Azure ajouté à Visual Studio Team Services, sélectionnez l’abonnement dans la zone de liste déroulante **Abonnement Azure**, puis passez à la section suivante.

        Si vous n’avez pas de point de terminaison de service Azure dans Visual Studio Team Services, vous devez en ajouter un. Cette sous-section vous guide dans la procédure. Si votre compte Azure utilise un compte Microsoft (tel que Hotmail), vous devez procéder comme suit pour obtenir l’authentification d’un principal de service.

    1.	Choisissez le lien **Gérer** en regard de la zone de liste déroulante **Abonnement Azure**.

        ![][3]

    1. Choisissez **Azure** dans la zone de liste déroulante **Nouveau point de terminaison de Service**.

        ![][4]

    1.	Dans la boîte de dialogue **Ajouter un abonnement Azure**, sélectionnez l’option **Principal du Service**.

        ![][5]

    1.	Ajoutez les informations de votre abonnement Azure à la boîte de dialogue **Ajouter un abonnement Azure**. Vous devrez fournir les éléments suivants :
        -	ID d’abonnement
        -	Nom d'abonnement
        -	ID de principal de service
        -	Clé de principal de service
        -	ID client

    1.	Ajout d’un nom de votre choix à la zone de nom **Abonnement**. Cette valeur s’affichera plus tard dans la liste déroulante **Abonnement Azure** dans Visual Studio Team Services.

    1.	Si vous ne connaissez pas votre ID d’abonnement Azure, vous pouvez utiliser une des commandes suivantes pour l’obtenir.
        
        Pour les scripts PowerShell, utilisez :

        `Get-AzureRmSubscription`

        Pour l’interface de ligne de commande Azure, consultez :

        `azure account show`
    

    1.	Pour obtenir un ID de principal de service, une clé de principal de service et un ID client, suivez la procédure dans [Création de l’application Active Directory et du principal du service à l’aide du portail](resource-group-create-service-principal-portal.md) ou [Authentification d’un principal de service à l’aide d’Azure Resource Manager](resource-group-authenticate-service-principal.md).

    1.	Ajoutez les valeurs d’ID de principal du service, de clé de principal de service et d’ID de client à la boîte de dialogue **Ajouter un abonnement Azure**, puis choisissez le bouton **OK**.

        Vous disposez désormais d’un Principal de service valide à utiliser pour exécuter le script Azure PowerShell.

1.	Modifiez la définition de build, puis choisissez l’étape de build **Azure PowerShell**. Sélectionnez l’abonnement dans la zone de liste déroulante **Abonnement Azure**. (Si l’abonnement n’apparaît pas, choisissez le bouton **Actualiser**, puis le lien **Gérer**.

    ![][8]

1.	Fournissez un chemin d’accès pour le script PowerShell Deploy-azureresourcegroup.ps1. Pour ce faire, cliquez sur le bouton Points de suspension (...) en regard de la zone **Chemin du script**, accédez au script PowerShell de Deploy-azureresourcegroup.ps1 dans le dossier **Scripts** de votre projet, sélectionnez-le, puis cliquez sur le bouton **OK**.

    ![][9]

1. Après avoir sélectionné le script, mettez à jour le chemin d’accès au script afin de l’exécuter depuis Build.StagingDirectory (répertoire dans lequel *ArtifactsLocation* est défini). Vous pouvez le faire en ajoutant « $(Build.StagingDirectory)/ » au début du chemin de script.

    ![][10]

1.	Dans la zone **Arguments de script**, saisissez les paramètres suivants (sur une seule ligne). Lorsque vous exécutez le script dans Visual Studio, vous pouvez voir comment Visual Studio utilise les paramètres dans la fenêtre **Sortie**. Vous pouvez l’utiliser comme point de départ pour définir les valeurs de paramètre dans l’étape de build.

    | Paramètre | Description|
    |---|---|
    | -ResourceGroupLocation | Valeur de l’emplacement géographique où le groupe de ressources est localisé, par exemple **estdesétatsunis** ou **« Est des États-Unis »**. (Ajouter des guillemets simples si le nom comporte un espace). Consultez [Régions Azure](https://azure.microsoft.com/regions/) pour plus d’informations.| |
    | -ResourceGroupName | Nom du groupe de ressources utilisé pour ce déploiement.| |
    | -UploadArtifacts | Ce paramètre, lorsqu’il est présent, spécifie que les artefacts doivent être téléchargés vers Azure depuis le système local. Il vous suffit de définir ce commutateur si le déploiement de votre modèle requiert des artefacts supplémentaires pour les phases intermédiaires de l’utilisation du script PowerShell (tels que les scripts de configuration ou les modèles imbriqués). |
    | -StorageAccountName | Nom du compte de stockage utilisé pour les artefacts intermédiaires pour ce déploiement. Ce paramètre n’est obligatoire que si vous copiez des artefacts vers Azure. Ce compte de stockage n’est pas automatiquement créé par le déploiement. Il doit déjà exister.| |
    | -StorageAccountResourceGroupName | Nom du groupe de ressources contenant le compte de stockage. Ce paramètre n’est obligatoire que si vous copiez des artefacts vers Azure.| |
    | -TemplateFile | Chemin d’accès vers le fichier modèle dans le projet de déploiement de groupe de ressources Azure. Pour améliorer la flexibilité, utilisez un chemin d’accès vers ce paramètre relatif à l’emplacement du script PowerShell au lieu d’un chemin d’accès absolu.|
    | -TemplateParametersFile | Chemin d’accès vers le fichier de paramètre dans le projet de déploiement de groupe de ressources Azure. Pour améliorer la flexibilité, utilisez un chemin d’accès vers ce paramètre relatif à l’emplacement du script PowerShell au lieu d’un chemin d’accès absolu.|
    | -ArtifactStagingDirectory | Ce paramètre indique au script PowerShell le dossier à partir duquel les fichiers binaires du projet doivent être copiés. Cette valeur remplace la valeur par défaut utilisée par le script PowerShell. Pour utiliser Visual Studio Team Services, définissez la valeur sur : - ArtifactStagingDirectory $(Build.StagingDirectory) |

    Voici un exemple d’arguments de script (ligne divisée pour une meilleure lisibilité) :

    ```	
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'	
    ```

    Lorsque vous avez terminé, la zone **Arguments de script** ressemble en principe à ce qui suit.

    ![][11]

1.	Une fois que vous avez ajouté tous les éléments requis pour l’étape de build Azure PowerShell, choisissez le bouton **File d’attente** pour générer le projet. L’écran **Build** affiche la sortie du script PowerShell.

## Étapes suivantes

Pour plus d’informations sur Azure Resource Manager et les groupes de ressources Azure, voir l’article [Présentation d’Azure Resource Manager](resource-group-overview.md).


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png

<!---HONumber=AcomDC_0803_2016-->