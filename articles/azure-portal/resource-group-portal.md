<properties 
	pageTitle="Utilisation du portail Azure en version préliminaire pour gérer vos ressources Azure" 
	description="Regroupez plusieurs ressources dans un groupe logique qui devient la limite de cycle de vie pour les ressources qu’il contient." 
	services="azure-portal" 
	documentationCenter="" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tomfitz"/>


# Utilisation du portail Azure en version préliminaire pour gérer vos ressources Azure

## Introduction

Avant, pour gérer une ressource (une entité gérée par l’utilisateur, comme un serveur de base de données, une base de données ou un site web) dans Microsoft Azure, vous deviez exécuter les opérations sur une ressource à la fois. Avec une application complexe constituée de plusieurs ressources, la gestion devenait un casse-tête. Dans le portail Azure en version préliminaire, vous pouvez créer des groupes de ressources pour gérer toutes les ressources d’une application en même temps. Le concept de groupe de ressources est une nouveauté dans Azure, qui sert de limite de cycle de vie pour chaque ressource englobée.

Les groupes de ressources permettent de gérer toutes les ressources d'une application en même temps. Ils sont activés par la nouvelle fonctionnalité de gestion, le Gestionnaire de ressources Azure. Le Gestionnaire de ressources Azure permet de regrouper plusieurs ressources dans un groupe logique qui sert de limite de cycle de vie pour chaque ressource englobée. En général, un groupe contient des ressources associées à une application spécifique. Par exemple, un groupe peut contenir une ressource de site web qui héberge votre site web public, une base de données SQL qui stocke les données relationnelles utilisées par le site et un compte de stockage qui stocke les ressources non relationnelles.

Voici une présentation succincte de la façon dont vous pouvez utiliser les groupes de ressources dans le portail Azure.

## Création de groupes de ressources

Lorsqu’une ressource est créée dans le portail, elle est toujours intégrée à un groupe de ressources. Vous avez le choix entre créer un groupe de ressources et utiliser un groupe qui existe déjà dans le flux de création. <br><br />

![Créer un groupe de ressources](./media/resource-group-portal/1_createWebsite.png)

Quand vous créez une application qui comporte quelques ressources fonctionnant ensemble (par exemple un site web + une base de données), elle est toujours créée dans son propre groupe de ressources, ce qui vous permet de gérer le cycle de vie de l’ensemble des ressources associées à l’aide du groupe de ressources. Vous pouvez ajouter des ressources au groupe ou en supprimer au fil de l’évolution de votre application.

![Créer un groupe de ressources](./media/resource-group-portal/2_createWSandDB.png)

## Consultation des groupes de ressources

Vous pouvez consulter tous les groupes de ressources en cliquant sur la barre sur le côté gauche de l’écran. Un groupe de ressources comporte un volet qui affiche toutes les informations sur ce groupe de ressources particulier. Il permet également d'obtenir une vue unifiée des informations de facturation et de surveillance pour toutes les ressources du groupe.

Le résumé affiche une carte schématique de l’ensemble des ressources du groupe et de celles d’autres groupes qui lui sont liés. La carte des ressources indique également l’état de chaque ressource. ![Résumé du groupe de ressources](./media/resource-group-portal/3_1BrowseRGs.png)

La carte des ressources peut être personnalisée et agrandie pour afficher toutes les ressources d’un groupe et celles d’autres groupes liés. Elle peut être épinglée sur le tableau d’accueil, qui la copiera.

![épingler](./media/resource-group-portal/3_2BrowseRGs.png)

Un clic sur la carte des ressources permet de lancer la vue liste de toutes les ressources de la carte. Cette vue répertorie toutes les ressources d'un groupe ou les ressources associées. Un clic sur ces ressources permet de lancer leur panneau.

![consulter les ressources](./media/resource-group-portal/3_3BrowseRGs.png)

## Ajout de ressources à des groupes de ressources

Vous pouvez ajouter des ressources à un groupe de ressources au moyen de la commande **Ajouter** sur le panneau du groupe de ressources. Pour ajouter des ressources au groupe de ressources, suivez la procédure du flux.

![ajouter une ressource](./media/resource-group-portal/4_AddResource.png)

Remarque : il est déconseillé de placer un projet d’équipe dans le même groupe de ressources que d’autres ressources Azure. Si vous créez un projet d’équipe dans un nouveau compte et un nouveau groupe, puis créez un site web, le groupe de site sera automatiquement le dernier groupe utilisé (VSO), ce qui mélangera ressources du runtime et ressources de développement dans le même groupe.

## Suppression de groupes de ressources

Comme les groupes de ressources permettent de gérer le cycle de vie de l’ensemble des ressources englobées, la suppression d’un groupe de ressources entraîne celle de toutes les ressources qu’il contient. Vous pouvez également supprimer des ressources individuelles d'un groupe de ressources. Soyez prudent lorsque vous supprimez un groupe de ressources, car d'autres ressources peuvent y être liées. Vous pouvez voir toutes les ressources liées dans la carte des ressources et prendre les mesures nécessaires pour éviter des conséquences fâcheuses lorsque vous supprimez des groupes de ressources.

## Étapes suivantes
Mise en route

- [Présentation d’Azure Resource Manager](../resource-group-overview.md)  
- [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md)
- [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec le Gestionnaire des ressources Azure](../xplat-cli-azure-resource-manager.md)  
  
Création et déploiement d’applications
  
- [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md)  
- [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md)
- [Déployer une application complexe de manière prévisible dans Microsoft Azure](../app-service-web/app-service-deploy-complex-application-predictably.md)
- [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](../resource-group-deploy-debug.md)  
- [Fonctions des modèles Azure Resource Manager](../resource-group-template-functions.md)  
- [Opérations de modèle avancées](../resource-group-advanced-template.md)  
- [Déploiement de ressources Azure à l’aide de bibliothèques .NET et d’un modèle](../arm-template-deployment.md)
  
Organisation des ressources
  
- [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md)  
  
Gestion et audit de l’accès
  
- [Gestion et audit d’accès aux ressources](resource-group-rbac.md)  
- [Authentification d’un principal du service à l’aide d’Azure Resource Manager](../resource-group-authenticate-service-principal.md)  
- [Création d’un nouveau principal du service Azure à l’aide du portail Azure classique](../resource-group-create-service-principal-portal.md)  
  


 

<!---HONumber=July15_HO4-->