<properties
   pageTitle="Créer des modèles de déploiement Logic Apps | Microsoft Azure"
   description="Apprenez à créer des modèles de déploiement Logic Apps et à les utiliser pour la gestion des versions."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/25/2016"
   ms.author="jehollan"/>
   
# Créer des modèles de déploiement Logic Apps

Une fois que vous avez créé une application logique, il peut être utile de l’utiliser en tant que modèle de ressource Azure afin de déployer facilement l’application logique tous les environnements ou groupes de ressources dont vous avez besoin. Pour vous familiariser avec les modèles Resource Manager, consultez les articles [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md) et [Déployer des ressources à l’aide de modèles Azure Resource Manager](../resource-group-template-deploy.md)

## Présentation du modèle de déploiement d’applications logiques

Une application logique comporte 3 composants de base :

* **Ressource d’application logique**: ressource qui regroupe des informations sur certains éléments tels que le plan de tarification, l’emplacement et la définition de workflow.
* **Définition de workflow** : contenu qui s’affiche lorsque vous sélectionnez **Mode Code**, qui définit les étapes du flux et le mode d’exécution du moteur. Il s’agit de la propriété `definition` de la ressource d’application logique.
* **Connexions** : ressources distinctes qui permettent de stocker les métadonnées en toute sécurité autour de n’importe quelle connexions de connecteur, telles que les chaînes de connexion et les jetons d’accès. Dans une application logique, elles sont référencées dans les `parameters` de la ressource d’application logique.

Pour afficher tous ces éléments pour les applications logiques existantes, vous pouvez utiliser des outils tels que [l’Explorateur de ressources Azure](http://resources.azure.com).

Pour créer un modèle pour une application logique capable de fonctionner avec les déploiements de groupes de ressources, vous devez définir les ressources et les paramétrer selon vos besoins. Par exemple, si vous effectuez un déploiement dans un environnement de développement, de test et de production, vous préférerez sans doute utiliser différentes chaînes de connexion à une base de données SQL dans chaque environnement, ou bien procéder au déploiement dans différents abonnements ou groupes de ressources.

## Création d’un modèle de déploiement d’applications logiques

Plusieurs outils sont à votre disposition pour vous aider à créer un modèle de déploiement d’applications logiques. Vous pouvez créer un modèle manuellement en utilisant les ressources ci-dessus et en créant les dont vous avez besoin. Vous pouvez également utiliser le module PowerShell [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator). Le module open source évalue l’application logique et les connexions qu’elle utilise, puis génère les ressources de modèle avec les paramètres nécessaires au déploiement. Par exemple, si votre application logique a reçu un message à partir d’une file d’attente Service Bus et a ajouté des données à une base de données SQL Azure, l’outil conservera toute la logique d’orchestration et paramètrera les chaînes de connexion SQL et Service Bus afin qu’ils puissent être définis à la phase du déploiement.

>[AZURE.NOTE] Les connexions doivent se trouver dans le même groupe de ressources que l’application logique

### Installation du module PowerShell Logic App Template

Le moyen le plus simple d’installer le module consiste à utiliser [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) avec la commande `Install-Module -Name LogicAppTemplate`.

Vous pouvez également installer le module manuellement :

1. Téléchargez la dernière version du module [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Décompressez le dossier dans le dossier de votre module PowerShell (généralement `%UserProfile%\Documents\WindowsPowerShell\Modules`)

Afin que le module puisse fonctionner avec n’importe quel jeton d’accès client et abonnement, il est recommandé d’utiliser l’outil de ligne de commande [armclient](https://github.com/projectkudu/ARMClient). Pour plus d’informations sur `armclient`, cliquez [ici](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### Génération d’un modèle d’application logique via PowerShell

Après l’installation, vous pouvez générer un modèle avec la commande suivante :

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

Où `$SubscriptionId` est l’ID d’abonnement Azure Cette ligne commence par obtenir un jeton d’accès via `armclient`, puis le transmet au script PowerShell avant de générer le modèle dans un fichier `.json`.

## Ajout de paramètres à un modèle d’application logique

Une fois que vous disposez d’un modèle d’application logique, vous pouvez continuer à ajouter ou modifier tous les paramètres supplémentaires dont vous avez besoin. Par exemple, si votre définition inclut un ID de ressource à une fonction Azure ou à un workflow imbriqué dans lequel vous envisagez de déployer dans un déploiement unique, il peut être intéressant d’ajouter des ressources supplémentaires à votre modèle et de paramétrer les ID en fonction de vos besoins. Il en va de même pour toutes les références aux API personnalisées ou aux points de terminaison Swagger que vous pensez déployer avec chaque groupe de ressources.

## Déploiement d’un modèle d’application logique

Une fois que vous avez un modèle, vous pouvez procéder au déploiement à l’aide de n’importe quel outil (PowerShell, API REST, Visual Studio, Release Management) ou à l’aide du service de déploiement de modèle du portail Azure. Pour plus d’informations sur le déploiement, cliquez [ici](../resource-group-template-deploy.md). Il est recommandé de créer également un [fichier de paramètres](../resource-group-template-deploy.md#parameter-file) pour stocker les valeurs du paramètre.

### Autorisation des connexions OAuth

Après le déploiement, l’application logique fonctionnera de bout en bout avec des paramètres valides, mais les connexions de OAuth devront toujours être autorisées pour générer un jeton d’accès valide. Pour cela, ouvrez l’application logique dans le concepteur et autorisez les connexions. Si vous souhaitez automatiser l’opération, vous pouvez également utiliser un script pour approuver chaque connexion OAuth. Vous trouverez un exemple de script sur GitHub, sous le projet [Connection Auth](https://github.com/logicappsio/LogicAppConnectionAuth).

## Utilisation de Visual Studio Release Management

Le déploiement et la gestion d’environnements passent souvent par l’utilisation d’un outil tel que Visual Studio Release Management avec un modèle de déploiement d’application logique. VSTS inclut une tâche de [déploiement d’un groupe de ressources Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que vous pouvez ajouter dans n’importe quel pipeline de build ou de version. Vous devez disposer d’un [principal du service](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) pour obtenir l’autorisation de déployer, après quoi vous pourrez générer la définition de version.

1. Sous **Version**, commencez par sélectionner une **Nouvelle définition** avec une définition **Vide**.

    ![][1]

1. Choisissez les artefacts dont vous avez besoin. Il s’agira probablement du modèle d’application logique que vous avez généré manuellement ou dans le cadre du processus de génération.
1. Ajoutez une tâche de **déploiement d’un groupe de ressources Azure**.
1. Configurez-la avec un [principal du service](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/), puis référencez le **Modèle** et les fichiers **Paramètres de modèle**.
1. Continuez à suivre les étapes du processus de version pour les autres environnements, tests automatisés ou approbateurs dont vous avez besoin.
    
<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG

<!---HONumber=AcomDC_0601_2016-->