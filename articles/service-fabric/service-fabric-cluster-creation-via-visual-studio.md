<properties
   pageTitle="Configuration d'un cluster Service Fabric à l'aide de Visual Studio | Microsoft Azure"
   description="Décrit comment configurer un cluster Service Fabric à l’aide d’un modèle Azure Resource Manager (ARM) créé par un projet de groupe de ressources Azure dans Visual Studio"
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
   ms.date="02/12/2016"
   ms.author="karolz@microsoft.com"/>

# Configuration d’un cluster Service Fabric à l’aide de Visual Studio
Cet article décrit comment configurer un cluster Azure Service Fabric à l’aide de Visual Studio et d’un modèle Azure Resource Manager (ARM). Nous utiliserons un projet du groupe de ressources Azure Visual Studio pour créer le modèle. Une fois le modèle créé, il peut être déployé directement vers Azure à partir de Visual Studio, mais il peut également servir à partir d’un script ou dans le cadre de la fonctionnalité d’intégration continue (CI).

## Création d’un modèle de cluster Service Fabric à l’aide d’un projet de groupe de ressources Azure
Pour commencer, ouvrez Visual Studio et créez un projet de groupe de ressources Azure (disponible dans le dossier **Cloud**) :

![Boîte de dialogue Nouveau projet avec le projet du groupe de ressources Azure sélectionné][1]

Vous pouvez créer une solution Visual Studio pour ce projet ou l’ajouter à une solution existante.

>[AZURE.NOTE] Si vous ne voyez pas le projet de groupe de ressources Azure sous le nœud Cloud, le Kit de développement logiciel (SDK) Azure n’est pas installé. Lancez Web Platform Installer ([à installer maintenant](http://www.microsoft.com/web/downloads/platform.aspx) si ce n’est pas déjà fait), puis recherchez « Azure SDK pour .NET » et installez la version compatible avec votre version de Visual Studio.

Une fois que vous avez cliqué sur le bouton OK, Visual Studio vous demande de sélectionner le modèle Resource Manager à créer :

![Boîte de dialogue Sélectionner le modèle Azure avec le modèle de cluster Service Fabric sélectionné][2]

Sélectionnez le modèle Service Fabric Cluster et cliquez à nouveau sur le bouton OK. Le projet et le modèle Resource Manager sont maintenant créés.

## Préparation du modèle pour le déploiement
Avant de déployer le modèle pour créer le cluster, vous devez fournir les valeurs des paramètres obligatoires du modèle. Ces valeurs de paramètres sont lues à partir du fichier `ServiceFabricCluster.param.dev.json`, qui se trouve dans le dossier `Templates` du projet de groupe de ressources. Ouvrez le fichier et spécifiez les valeurs suivantes :

|Nom du paramètre |Description|
|-----------------------  |--------------------------|
|clusterLocation |Nom de la **région Azure** où se trouvera le cluster Service Fabric. Par exemple, « East US ».|
|clusterName |Nom DNS (Domain Name System) du cluster Service Fabric qui est créé par le modèle. <br /><br /> Par exemple, si vous définissez ce paramètre sur `myBigCluster` et que le paramètre `clusterLocation` est défini sur East US, le nom du cluster devient `myBigCluster.eastus.cloudapp.azure.com`.|
|certificateThumbprint |Empreinte numérique du certificat qui sécurisera le cluster.|
|sourceVaultValue |*ID de ressource* du coffre de clés où est stocké le certificat qui sécurise le cluster.|
|certificateUrlValue |URL du certificat de sécurité du cluster.|

Le modèle Resource Manager Service Fabric Visual Studio crée un cluster sécurisé, qui est protégé par un certificat. Ce certificat est identifié par les trois derniers paramètres du modèle (`certificateThumbprint`, `sourceVaultValue` et `certificateUrlValue`) et doit exister dans un **coffre de clés Azure**. Pour plus d’informations sur la création du certificat de sécurité du cluster, consultez l’article [Sécurisation du cluster Service Fabric à l’aide de certificats](service-fabric-cluster-security.md#secure-a-service-fabric-cluster-by-using-certificates).

## Facultatif : Ajout de ports d'application publics
Vous pouvez également souhaiter modifier les ports d’application publics pour le cluster avant de le déployer. Par défaut, le modèle ouvre seulement deux ports TCP publics (80 et 8081). Si vous avez besoin d’autres ports pour vos applications, modifiez la définition de l’équilibreur de charge Azure dans le modèle. La définition est stockée dans le fichier modèle principal (`SecureFabricCluster.json`). Ouvrez ce fichier et recherchez `loadBalancedAppPort`. Vous remarquerez que chaque port est associé à trois artefacts :

1. Un paramètre de modèle qui définit la valeur du port TCP pour le port : ```json
	"loadBalancedAppPort1": {
	    "type": "int",
	    "defaultValue": 80
	}
	```

2. Une *sonde* qui définit la fréquence et la durée pendant lesquelles l'équilibrage de charge Azure tente d'utiliser un nœud Service Fabric spécifique avant de basculer vers un autre. Les sondes font partie de la ressource d’équilibreur de charge. Voici la définition de la sonde du premier port d’application par défaut : ```json
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

3. Une *règle d’équilibrage de charge* qui relie le port et la sonde, ce qui permet l’équilibrage de charge entre plusieurs nœuds de cluster Service Fabric : ```json
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
    ``` Si les applications que vous envisagez de déployer dans le cluster ont besoin de plus de ports, vous pouvez les ajouter en créant des définitions de règle supplémentaires pour la sonde et l’équilibrage de charge. Pour plus d’informations sur l’utilisation de l’équilibreur de charge Azure par le biais des modèles Resource Manager, consultez [Prise en main de la configuration d’un équilibreur de charge interne à l’aide d’Azure Resource Manager](../load-balancer/load-balancer-internal-arm-powershell.md).

## Déploiement du modèle à l’aide de Visual Studio
Une fois que vous avez enregistré toutes les valeurs de paramètres requises dans le fichier `ServiceFabricCluster.param.dev.json`, vous êtes prêt à déployer le modèle et à créer votre cluster Service Fabric. Cliquez avec le bouton droit sur le projet de groupe de ressources dans l’explorateur de solutions Visual Studio, puis sélectionnez **Déployer**. Visual Studio affiche la boîte de dialogue **Déployer vers le groupe de ressources**, vous demandant de vous authentifier auprès d’Azure si nécessaire :

![Boîte de dialogue Déployer vers le groupe de ressources][3]

La boîte de dialogue vous permet de choisir un groupe de ressources Resource Manager existant pour le cluster et vous permet d’en créer un. Normalement, il est judicieux d’utiliser un groupe de ressources distinct pour un cluster Service Fabric.

Quand vous cliquez sur le bouton Déployer, Visual Studio vous invite à confirmer les valeurs des paramètres du modèle. Cliquez sur le bouton **Enregistrer**. Un paramètre n’a pas une valeur persistante : le mot de passe du compte d’administrateur pour le cluster. Vous devez fournir une valeur de mot de passe quand Visual Studio vous en demande une.

>[AZURE.NOTE] Si PowerShell n’a jamais été utilisé pour administrer Azure à partir de l’ordinateur que vous utilisez actuellement, vous devez faire un peu de ménage. 1. Activez l’écriture de scripts PowerShell en exécutant la commande [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx). Pour les machines de développement, la stratégie « unrestricted » est généralement acceptable. 2. Décidez s’il faut autoriser la collecte de données de diagnostic à partir de commandes Azure PowerShell et exécutez [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) ou [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) si nécessaire. Si vous utilisez Azure PowerShell version 0.9.8 ou plus, ces commandes sont nommées `Enable-AzureDataCollection` et `Discable-AzureDataCollection`, respectivement. Cela permet d'éviter les invites inutiles lors du déploiement du modèle.

Vous pouvez surveiller la progression du processus de déploiement dans la fenêtre de sortie de Visual Studio. Une fois le déploiement de modèle terminé, votre nouveau cluster est prêt à utiliser.

Si des erreurs sont présentes, accédez au [portail Azure](https://portal.azure.com/) et vérifiez les **notifications**. Un déploiement de groupe de ressources ayant échoué laisse des informations de diagnostic détaillées.

>[AZURE.NOTE] Les clusters Service Fabric nécessitent un certain nombre de nœuds actifs en permanence pour maintenir la disponibilité et préserver l'état, situation appelée "conservation du quorum". Par conséquent, il est généralement déconseillé d'arrêter tous les ordinateurs du cluster, sauf si vous avez d'abord effectué une [sauvegarde complète de votre état](service-fabric-reliable-services-backup-restore.md).

## Étapes suivantes
- [En savoir plus sur la configuration de cluster Service Fabric à l’aide du portail Azure](service-fabric-cluster-creation-via-portal.md)
- [Apprenez à gérer et déployer des applications Service Fabric à l’aide de Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=AcomDC_0218_2016-->