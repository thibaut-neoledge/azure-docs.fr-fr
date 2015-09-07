<properties 
   pageTitle="Création d'un projet Azure avec Visual Studio"
	description="Création d'un projet Azure avec Visual Studio"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee"/>
<tags 
   ms.service="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/24/2015"
	ms.author="kempb"/>

# Création d'un projet Azure avec Visual Studio

Les outils Azure pour Visual Studio fournissent un modèle vous permettant de créer un service cloud pour Azure. Les outils vous aident également à configurer, déboguer et déployer le service cloud dans Azure.

Une solution de service cloud Azure contient les types de projets suivants :

- **Projet Azure** 
    
    Le projet Azure est associé aux projets de rôle de la solution. Il comprend également les fichiers de définition et de configuration de service. Le fichier de définition de service définit les paramètres d'exécution de votre application, notamment les rôles requis, les points de terminaison et la taille de la machine virtuelle. Le fichier de configuration de service configure le nombre d'instances d'un rôle exécutées et les valeurs des paramètres définis pour un rôle. Pour plus d'informations sur ces paramètres, consultez [Configuration des rôles pour un service cloud Azure avec Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx).

- **Projet de rôle web**
 
    Un rôle de travail effectue le traitement en arrière-plan. Un rôle de travail peut communiquer avec les services de stockage et avec d'autres services Internet. Un rôle de travail peut avoir un nombre quelconque de points de terminaison HTTP, HTTPS ou TCP.

    - **Rôle web ASP.NET**, pour la création d'une application ASP.NET avec un serveur web frontal
    - **Rôle web ASP.NET MVC5**
    - **Rôle web ASP.NET MVC4**
    - **Rôle web ASP.NET MVC3**
    - **Rôle web de service WCF**, pour la création d'un service WCF
    - **Rôle web d’application métier Silverlight** (requiert Visual Studio 2012)

- **Rôle de travail de cache**

    Un rôle qui fournit un cache dédié à votre application.

- **Rôle de travail avec file d'attente Service Bus**

    Une file d’attente Service Bus qui fournit la fonctionnalité de mise en file d’attente des messages pour communiquer avec le processus de travail. Pour plus d'informations, consultez la page [Utilisation des files d'attente Service Bus](http://go.microsoft.com/fwlink/?LinkId=260560).

## Pour créer un projet de service cloud Azure dans Visual Studio

1. Lancez Microsoft Visual Studio en tant qu'administrateur.

1. Dans le menu principal, sélectionnez **Fichier**, **Nouveau **, **Projet**.

1. Dans le volet **Types de projets**, sélectionnez **Cloud** à partir des nœuds de modèles de projets Visual C# ou Visual Basic.

1. Dans le volet **Modèles**, sélectionnez **Azure Cloud Service**.

1. Spécifiez la version de .NET Framework que vous souhaitez utiliser pour développer votre projet.

1. Entrez un nom et un emplacement pour votre projet et un nom pour la solution. Sélectionnez le bouton **OK**.

1. Dans la boîte de dialogue **Nouveau projet Azure**, sélectionnez les rôles que vous souhaitez ajouter et appuyez sur la touche de direction droite pour les ajouter à votre solution. Vous pouvez ajouter autant de rôles que nécessaire.

1. Pour renommer un rôle que vous avez ajouté à votre projet, passez la souris sur le rôle dans la boîte de dialogue **Nouveau projet Azure** et sélectionnez l’icône **Renommer** à droite du rôle. Vous pouvez également renommer un rôle dans votre solution une fois qu’il a été ajouté.

<!---HONumber=August15_HO9-->