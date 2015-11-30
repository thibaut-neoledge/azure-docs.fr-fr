<properties
   pageTitle="Configuration d'un cluster Service Fabric à l'aide de Visual Studio | Microsoft Azure"
   description="Décrit comment configurer un cluster Service Fabric à l'aide d'un modèle Azure Resource Manager (ARM) créé par un projet de groupe de ressources Azure dans Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="karolz@microsoft.com"/>

# Configuration d'un cluster Service Fabric à l'aide de Visual Studio
Cet article décrit comment configurer un cluster [**Service Fabric**](http://azure.microsoft.com/documentation/services/service-fabric/) à l'aide de **Visual Studio** et d'un modèle [**Azure Resource Manager (ARM)**](https://azure.microsoft.com/documentation/articles/resource-group-overview/). Nous utiliserons le **projet du groupe de ressources Azure** Visual Studio pour créer le modèle. Une fois le modèle créé, il peut être déployé directement vers Azure à partir de Visual Studio, mais il peut également servir à partir d'un script ou dans le cadre de la fonctionnalité d'**intégration continue (CI)**.

## Création d'un modèle de cluster Service Fabric avec un projet de groupe de ressources Azure
Pour commencer, ouvrez Visual Studio et créez un projet de groupe de ressources Azure (disponible sous le dossier « Cloud ») :

![Boîte de dialogue Nouveau projet avec le projet du groupe de ressources Azure sélectionné][1]

Vous pouvez créer une nouvelle solution Visual Studio pour ce projet ou vous l'ajoutez à une solution existante.

>[AZURE.NOTE]Si vous ne voyez pas le projet de groupe de ressources Azure sous le nœud Cloud, le Kit de développement logiciel (SDK) Azure n'est pas installé. Lancez Web Platform Installer ([à installer à partir d'ici](http://www.microsoft.com/web/downloads/platform.aspx) si ce n'est pas déjà fait), puis recherchez « Azure SDK pour .NET » et installez la version compatible avec votre version de Visual Studio.

Lorsque vous cliquez sur OK Visual Studio vous invite à sélectionner le modèle ARM à créer :

![Boîte de dialogue Sélectionner le modèle Azure avec le modèle de cluster Service Fabric sélectionné][2]

Sélectionnez le modèle « Service Fabric Cluster » et cliquez à nouveau sur le bouton OK. Le projet et le modèle ARM seront créés.

## Préparation du modèle pour le déploiement
Avant de déployer le modèle pour créer le cluster, vous devez fournir les valeurs des paramètres requis du modèle. Ces valeurs de paramètres sont lues à partir du fichier `ServiceFabricCluster.param.dev.json`, qui se trouve dans le dossier `Templates` du projet de groupe de ressources. Ouvrez le fichier et spécifiez les valeurs suivantes :

|Nom du paramètre |Description|
|-----------------------  |--------------------------|
|clusterLocation |Nom de la **région Azure** où se trouvera le cluster Service Fabric. Par exemple, « Est des États-Unis ».|
|clusterName |Nom (DNS) du cluster Service Fabric qui est créé par le modèle. <br /><br /> Par exemple, si vous définissez ce paramètre sur « myBigCluster » et que le paramètre `clusterLocation` est défini sur Est des États-Unis, le nom du cluster sera `myBigCluster.eastus.cloudapp.azure.com`.|
|certificateThumbprint |Empreinte numérique du certificat qui sécurisera le cluster.|
|sourceVaultValue |*ID de ressource* du coffre de clés où est stocké le certificat qui sécurise le cluster.|
|certificateUrlValue |URL du certificat de sécurité du cluster.|

Modèle ARM Service Fabric Visual Studio crée un cluster sécurisé, qui est protégé par un certificat. Ce certificat est identifié par les trois derniers paramètres du modèle (`certificateThumbprint`, `sourceVaultValue` et `certificateUrlValue`) et doit exister dans un **coffre de clés Azure**. Pour plus d'informations sur la création du certificat de sécurité du cluster, consultez l'article [Sécurisation du cluster Service Fabric à l'aide de certificats](service-fabric-cluster-security.md).

## Facultatif : Ajout de ports d'application publics
Un autre aspect du modèle que vous pouvez modifier avant de le déployer sont les **ports d'application publics** pour le cluster. Par défaut, le modèle ouvre deux ports TCP publics (80 et 8081). Si vous avez besoin de plus de ports pour vos applications, vous devrez modifier la définition d'équilibrage de charge Azure dans le modèle. La définition est stockée dans le fichier modèle principal (`SecureFabricCluster.json`). Ouvrez le fichier et recherchez « loadBalancedAppPort ». Vous remarquerez que chaque port est associé à trois artefacts :

1. Un paramètre de modèle qui définit la valeur du port TCP pour le port : ```json
	"loadBalancedAppPort1": {
	    "type": "int",
	    "defaultValue": 80
	}
	```

2. Une *sonde* qui définit la fréquence et la durée pendant lesquelles l'équilibrage de charge Azure tente d'utiliser un nœud Service Fabric spécifique avant de basculer vers un autre. Les sondes font partie de la ressource d'équilibrage de charge. Voici la définition de la sonde du premier port d'application par défaut : ```json
	{
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[parameters('loadBalancedAppPort1')]",
            "protocol": "tcp"
        }
    }
	```

3. Une *règle d'équilibrage de charge* qui relie le port et la sonde et permet l'équilibrage de charge entre plusieurs nœuds de cluster Service Fabric : ```json
	{
	    "name": "AppPortLBRule1",
	    "properties": {
	        "backendAddressPool": {
	            "id": "[variables('lbPoolID0')]"
	        },
	        "backendPort": "[parameters('loadBalancedAppPort1')]",
	        "enableFloatingIP": false,
	        "frontendIPConfiguration": {
	            "id": "[variables('lbIPConfig0')]"
	        },
	        "frontendPort": "[parameters('loadBalancedAppPort1')]",
	        "idleTimeoutInMinutes": 5,
	        "probe": {
	            "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
	        },
	        "protocol": "tcp"
	    }
	}
    ``` Si les applications que vous envisagez de déployer dans le cluster ont besoin de davantage de ports, vous devrez les ajouter en créant des définitions de règle supplémentaires pour la sonde et l'équilibrage de charge. Pour plus d'informations sur l'utilisation de l'équilibrage de charge Azure via des modèles ARM, consultez l'article [Prise en main de la configuration d'un équilibreur de charge interne à l'aide d'Azure Resource Manager](https://azure.microsoft.com/documentation/articles/load-balancer-internal-arm-powershell/).

## Déploiement du modèle à l'aide de Visual Studio
Une fois que vous enregistrez toutes les valeurs de paramètres requises dans le fichier `ServiceFabricCluster.param.dev.json`, vous êtes prêt à déployer le modèle et à créer votre cluster Service Fabric. Cliquez avec le bouton droit sur le projet Groupe de ressources dans l'explorateur de solutions Visual Studio, puis sélectionnez « Déployer... ». Visual Studio affiche la boîte de dialogue « Déployer vers le groupe de ressources », vous demandant de vous authentifier auprès d'Azure si nécessaire :

![Boîte de dialogue Déployer vers le groupe de ressources][3]

La boîte de dialogue vous permet de choisir un groupe de ressources Azure RM existant pour le cluster et vous permet d'en créer un nouveau. En général, il est judicieux d'utiliser un groupe de ressources distinct pour un cluster Service Fabric.

Lorsque vous cliquez sur le bouton « Déployer », Visual Studio vous invite à confirmer les valeurs des paramètres du modèle. Cliquez sur le bouton Enregistrer. Il existe un paramètre qui n'a pas de valeur persistante : le mot de passe du compte d'administrateur pour le cluster. Veuillez indiquer une valeur de mot de passe lorsque Visual Studio vous le demande.

>[AZURE.NOTE]Si PowerShell n'a jamais utilisé pour administrer Azure à partir de l'ordinateur que vous utilisez actuellement, il est nécessaire de faire un peu de ménage : 1. Activez l'écriture de scripts PowerShell en exécutant la commande [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx). Pour les machines de développement, la stratégie « Unrestricted » est généralement acceptable. 2. Décidez s'il faut autoriser la collecte de données de diagnostic à partir de commandes Azure PowerShell et exécutez [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) ou [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) si nécessaire. Si vous utilisez Azure PowerShell version 0.9.8 ou plus, ces commandes sont nommées `Enable-AzureDataCollection` et `Discable-AzureDataCollection`, respectivement. Cela permet d'éviter les invites inutiles lors du déploiement du modèle.

Vous pouvez surveiller la progression du processus de déploiement dans la fenêtre de sortie de Visual Studio. Une fois le déploiement de modèle terminé, votre nouveau cluster est prêt à utiliser.

Si des erreurs sont présentes, accédez au [portail Azure](https://portal.azure.com/) et vérifiez les notifications. Un déploiement de groupe de ressources ayant échoué laissent des informations de diagnostic détaillées.

## Étapes suivantes
- [En savoir plus sur la configuration de cluster Service Fabric à l'aide du portail Azure](service-fabric-cluster-creation-via-portal.md)
- [Apprenez à gérer et déployer des applications Service Fabric à l'aide de Visual Studio](service-fabric-manage-applications-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=Nov15_HO4-->