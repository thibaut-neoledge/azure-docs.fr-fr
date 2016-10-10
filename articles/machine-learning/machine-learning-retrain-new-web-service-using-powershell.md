<properties
	pageTitle="Retrain a New Web service using the Machine Learning Management PowerShell cmdlets (Reformer un nouveau service web à l’aide des applets de commande PowerShell de gestion Machine Learning) | Microsoft Azure"
	description="Apprenez à reformer un modèle par programme et à mettre à jour le service web pour utiliser le modèle reformé dans Azure Machine Learning à l’aide des applets de commande PowerShell de gestion Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondlaghaeian"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="v-donglo"/>

# Retrain a New Web service using the Machine Learning Management PowerShell cmdlets (Reformer un nouveau service web à l’aide des applets de commande PowerShell de gestion Machine Learning)

Lorsque vous reformez un nouveau service web, vous mettez à jour la définition de service web prédictif pour référencer le nouveau modèle formé.

### Conditions préalables

Vous devez avoir configuré une expérience d’apprentissage et une expérimentation prédictive comme indiqué dans Reformer des modèles de Machine Learning par programmation. Pour plus d’informations sur la création des expériences de formation prédictive, consultez [Retrain Machine Learning models programmatically (Reformer des modèles de Machine Learning par programmation)](Retrain Machine Learning models programmatically.md).

Ce processus requiert l’installation des applets de commande Azure Machine Learning. Pour obtenir des informations sur l’installation des applets de commande Machine Learning, consultez la référence [Azure Machine Learning Cmdlets](https://msdn.microsoft.com/library/azure/mt767952.aspx) (Applets de commande Azure Machine Learning) sur MSDN.
 
Copiez les informations suivantes à partir de la sortie de reformation :

* BaseLocation
* RelativeLocation

Voici les étapes à suivre :
1.	Connectez-vous à votre compte Azure Resource Manager.
2.	Obtenir la définition du service web
3.	Exporter la définition du service web au format JSON
4.	Mettez à jour la référence sur l’objet blob ilearner dans le JSON.
5.	Importer le JSON dans une définition du service web
6.	Mettre à jour le service web avec la nouvelle définition du service web

### Se connecter à son compte Azure Resource Manager 

Vous devez tout d’abord vous connecter à votre compte Azure à partir de l’environnement PowerShell à l’aide de l’applet de commande [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx).

### Obtenir la définition du service web

Ensuite, obtenez le service web en appelant l’applet de commande [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx). La définition du service web est une représentation interne du modèle formé du service web et n’est pas directement modifiable. Vérifiez que vous récupérez la définition du service web pour votre expérience prédictive et non pour votre expérience de formation.

	$wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Pour déterminer le nom du groupe de ressources d’un service web existant, exécutez l’applet de commande Get-AzureRmMlWebService sans paramètres pour afficher les services web dans votre abonnement. Recherchez le service web et examinez son ID de service web. Le nom du groupe de ressources est le quatrième élément de l’ID, juste après l’élément *resourceGroups*. Dans l’exemple suivant, le nom du groupe de ressources est Default-MachineLearning-SouthCentralUS.

	Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph 
	Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237 
	Name : RetrainSamplePre.2016.8.17.0.3.51.237 
	Location : South Central US 
	Type : Microsoft.MachineLearning/webServices 
	Tags : {} 

Pour déterminer le nom du groupe de ressources d’un service web existant, vous pouvez également vous connecter au portail des services web Microsoft Azure Machine Learning. Sélectionnez le service web. Le nom de groupe de ressources est le cinquième élément de l’URL du service web, juste après l’élément *resourceGroups*. Dans l’exemple suivant, le nom du groupe de ressources est Default-MachineLearning-SouthCentralUS.

	https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237 


### Exporter la définition du service web au format JSON

Pour modifier la définition du modèle formé de manière à utiliser le modèle nouvellement formé, vous devez d’abord utiliser l’applet de commande [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) pour l’exporter vers un fichier au format JSON.
  
	Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### Mettez à jour la référence sur l’objet blob ilearner dans le JSON.

Dans les ressources, recherchez le [modèle formé], mettez à jour la valeur *uri* dans le nœud *locationInfo* avec l’URI de l’objet blob ilearner. L’URI est générée en combinant *BaseLocation* et *RelativeLocation* à partir de la sortie de l’appel de reformation BES.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### Importer le JSON dans une définition du service web

Vous devez utiliser l’applet de commande [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) pour convertir le fichier JSON modifié en une définition du service web que vous pouvez utiliser pour mettre à jour l’expérience prédictive.

	$wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


### Mettre à jour le service web avec la nouvelle définition du service web

Enfin, vous utilisez l’applet de commande [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) pour mettre à jour l’expérience prédictive.

	Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd 

##Résumé

À l’aide des applets de commande PowerShell Machine Learning, vous pouvez mettre à jour le modèle formé d’un service web prédictif en permettant des scénarios de type :

* Nouvel apprentissage périodique d’un modèle avec de nouvelles données.
* Distribution d’un modèle auprès des clients dans le but de leur permettre d’effectuer à nouveau l’apprentissage du modèle avec leurs propres données.

<!---HONumber=AcomDC_0928_2016-->