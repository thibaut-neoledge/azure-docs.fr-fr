<properties 
	pageTitle="Utilisation de ReportViewer sur un site web | Microsoft Azure"
	description="Cette rubrique explique comment développer un site web Microsoft Azure à l’aide du contrôle ReportViewer de Visual Studio qui affiche un rapport stocké sur une machine virtuelle Microsoft Azure."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="guyinacube"
	manager="jhubbard"
	editor="monicar" 
	tags="azure-service-management" />
<tags 
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/14/2016"
	ms.author="asaxton" />

# Utilisation de ReportViewer sur un site web hébergé dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Vous pouvez créer un site web Microsoft Azure à l’aide du contrôle Visual Studio ReportViewer qui affiche un rapport stocké sur une machine virtuelle Microsoft Azure. Le contrôle ReportViewer se trouve dans une application web que vous créez à l’aide du modèle d’application web ASP.NET.

>[AZURE.IMPORTANT] Les modèles d’application web MVC ASP.NET ne prennent pas en charge le contrôle ReportViewer.

Pour incorporer ReportViewer dans votre site web Microsoft Azure, vous devez effectuer les tâches suivantes.

- **Ajout** d’assemblys au package de déploiement

- **Configuration** de l’authentification et de l’autorisation

- **Publication** de l’application web ASP.NET sur Azure

## Configuration requise

Consultez la section « Instructions générales et meilleures pratiques » dans [Business Intelligence de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-classic-ps-sql-bi.md).

>[AZURE.NOTE] Les contrôles ReportViewer sont fournis avec Visual Studio Standard Edition ou une version supérieure. Si vous utilisez Web Developer Express Edition, vous devez installer [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) pour avoir accès aux fonctions d’exécution de ReportViewer.
>
>ReportViewer configuré en mode de traitement local n’est pas pris en charge dans Microsoft Azure.

Lisez le livre blanc [Contrôle de visionneuse de rapports Reporting Services et serveurs de rapports basés sur une machine virtuelle Microsoft Azure](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## Ajout d’assemblys au package de déploiement

Lorsque vous hébergez votre application ASP.NET localement, les assemblys ReportViewer sont généralement installés directement dans le Global Assembly Cache (GAC) du serveur IIS lors de l’installation de Visual Studio, et sont accessibles directement par l’application. Toutefois, lorsque vous hébergez votre application ASP.NET dans le cloud, Microsoft Azure n’autorise aucune installation dans le GAC, de sorte que vous devez vous assurer que les assemblys ReportViewer sont disponibles en local pour votre application. Pour cela, vous pouvez leur ajouter des références dans votre projet et les configurer pour une copie en local.

En mode de traitement à distance, le contrôle ReportViewer utilise les assemblys suivants :

- **Microsoft.ReportViewer.WebForms.dll** : contient le code ReportViewer dont vous avez besoin pour l’utilisation de ReportViewer dans votre page. Une référence pour cet assembly est ajoutée à votre projet lors de la suppression d’un contrôle ReportViewer dans une page ASP.NET de votre projet.

- **Microsoft.ReportViewer.Common.dll** : contient les classes utilisées par le contrôle ReportViewer au moment de l’exécution. Il n’est pas automatiquement ajouté à votre projet.

### Pour ajouter une référence à Microsoft.ReportViewer.Common

- Cliquez avec le bouton droit sur le nœud **Références** de votre projet et sélectionnez **Ajouter une référence**, sélectionnez l’assembly dans l’onglet .NET, puis cliquez sur **OK**.

### Pour rendre les assemblys accessibles en local par votre application ASP.NET

1. Dans le dossier **Références**, cliquez sur l’assembly Microsoft.ReportViewer.Common de sorte que ses propriétés s’affichent dans le volet Propriétés.

1. Dans le volet Propriétés, affectez à **Copy Local** la valeur True.

1. Répétez les étapes 1 et 2 pour Microsoft.ReportViewer.WebForms.

### Pour obtenir le module linguistique ReportViewer

1. Installez le package redistribuable Microsoft Report Viewer 2012 Runtime approprié à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Sélectionnez la langue dans la liste déroulante afin que la page soit redirigée vers la page du Centre de téléchargement correspondante.

1. Cliquez sur **Télécharger** pour commencer le téléchargement du fichier ReportViewerLP.exe.

1. Après avoir téléchargé ReportViewerLP.exe, cliquez sur **Exécuter** pour une installation immédiate, ou cliquez sur **Enregistrer** pour enregistrer le fichier sur votre ordinateur. Si vous cliquez sur **Enregistrer**, n’oubliez pas le nom du dossier dans lequel vous enregistrez le fichier.

1. Recherchez le dossier d’enregistrement du fichier. Cliquez avec le bouton droit sur ReportViewerLP.exe, cliquez sur **Exécuter en tant qu’administrateur**, puis sur **Oui**.

1. Après avoir exécuté ReportViewerLP.exe, le dossier c:\\windows\\assembly contient les fichiers de ressources **Microsoft.ReportViewer.Webforms.Resources** et **Microsoft.ReportViewer.Common.Resources**.

### Pour configurer le contrôle ReportViewer localisé

1. Téléchargez et installez le package redistribuable Microsoft Report Viewer 2012 Runtime en suivant les instructions ci-dessus.

1. Créez le dossier <langue> dans le projet, puis copiez les fichiers d’assembly de ressources associés dans celui-ci. Les fichiers d’assembly de ressources à copier sont les suivants : **Microsoft.ReportViewer.Webforms.Resources.dll** et **Microsoft.ReportViewer.Common.Resources.dll**. Sélectionnez les fichiers d’assembly de ressources, puis, dans le volet Propriétés, affectez la valeur **Toujours copier** à **Copier dans le répertoire de sortie**.

1. Définissez la culture et la culture de l’interface utilisateur pour le projet web. Pour plus d’informations sur la définition de la culture et de la culture d’interface utilisateur pour une page web ASP.NET, voir [Définition de la culture et de la culture d’interface utilisateur pour la globalisation de page web ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## Configuration de l’authentification et de l’autorisation

ReportViewer doit utiliser les informations d’identification appropriées pour s’authentifier auprès du serveur de rapports, et ces informations d’identification doivent être autorisées par le serveur de rapports pour accéder aux rapports souhaités. Pour plus d’informations sur l’authentification, lisez le livre blanc [Contrôle de visionneuse de rapports Reporting Services et serveurs de rapports basés sur une machine virtuelle Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## Publier l’application web ASP.NET sur Azure

Pour obtenir des instructions sur la publication d’une application web ASP.NET sur Azure, consultez [Procédure : Migration et publication d’une application web vers Azure depuis Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) et [Prise en main de Web Apps et ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT] Si la commande permettant d’ajouter un projet de déploiement Azure ou d’ajouter un projet de service cloud Azure n’apparaît pas dans le menu contextuel, vous devez remplacer la version cible de .NET Framework du projet par .NET Framework 4.
>
>Ces deux commandes offrent la même fonctionnalité. L’une ou l’autre des commandes s’affiche dans le menu contextuel en fonction de la version du Kit de développement logiciel (SDK) Microsoft Azure installée.

## Ressources

[Rapports Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[Business Intelligence de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-classic-ps-sql-bi.md)

[Utiliser PowerShell pour créer une machine virtuelle Azure avec un serveur de rapports en mode natif](virtual-machines-windows-classic-ps-sql-report.md)

[Contrôle de la visionneuse de rapports Reporting Services et serveurs de rapports de machine virtuelle Microsoft Azure](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)

<!---HONumber=AcomDC_0629_2016-->