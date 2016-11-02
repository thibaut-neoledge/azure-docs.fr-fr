<properties
	pageTitle="Vue d’ensemble d’Azure Diagnostics | Microsoft Azure"
	description="Utilisation des diagnostics Azure pour le débogage, la mesure des performances, la surveillance, l’analyse du trafic dans Cloud Services, Virtual Machines et Service Fabric"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/02/2016"
	ms.author="robb"/>


# Qu'est-ce que les diagnostics Microsoft Azure ?


Azure Diagnostics est la fonctionnalité Azure qui active la collecte de données de diagnostic dans une application déployée. Vous pouvez utiliser l'extension de diagnostic à partir de plusieurs sources différentes. Les sources actuellement prises en charge sont les rôles Web et les rôles de travail Azure Cloud Service, Azure Virtual Machines sous Microsoft Windows et Service Fabric. Les autres services Azure ont leurs propres diagnostics distincts.

## Données que vous pouvez collecter

Les diagnostics Azure peuvent collecter les types de données suivants :

Source de données|Description
---|---
Compteurs de performances | Système d’exploitation et compteurs de performances personnalisés
Journaux d’application | Messages de trace écrits par votre application
Journaux d’événements Windows | Informations envoyées au système de journalisation des événements Windows
.NET EventSource | Événements d’écriture de code à l'aide de la classe .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)
Journaux IIS | Informations au sujet des sites Web IIS
ETW basé sur les manifestes | Événements de suivi d’événements pour Windows générés par n’importe quel processus
Vidages sur incident | Informations sur l’état du processus en cas d’incident d’application
Journaux d'erreurs personnalisés | Journaux créés par votre application ou service
Journaux d’infrastructure Azure Diagnostic|Informations au sujet des diagnostics eux-mêmes

L'extension des diagnostics Azure peut transférer ces données vers un compte Azure Storage ou l'envoyer à des services tels que [Application Insights](./application-insights/app-insights-cloudservices.md). Ces données peuvent servir au débogage et à la résolution des problèmes, à mesurer les performances, à suivre l'utilisation des ressources, à analyser le trafic et à prévoir et analyser la capacité.


## Contrôle de version
Voir [Historique de contrôle de version des diagnostics Azure](azure-diagnostics-versioning-history.md).

## Étapes suivantes
Choisissez le service sur lequel vous voulez collecter des diagnostics et utilisez les articles suivants pour commencer. Utilisez les liens des diagnostics Azure généraux comme référence pour des tâches spécifiques.

## Web Apps
Notez que Web Apps n'utilise pas les diagnostics Azure. Trouver les informations équivalentes sur [Web Apps](./app-service-web/web-sites-enable-diagnostic-log.md)

## Cloud Services avec les diagnostics Azure
- Si vous utilisez Visual Studio, voir [Utilisation de Visual Studio pour suivre une application Cloud Services](./vs-azure-tools-debug-cloud-services-virtual-machines.md) pour commencer. Sinon, voir
- [Surveillance de Cloud Services à l'aide des diagnostics Azure](./cloud-services/cloud-services-how-to-monitor.md)
- [Configuration des diagnostics Azure dans une application Cloud Services](./cloud-services/cloud-services-dotnet-diagnostics.md)

Pour des rubriques plus avancées, voir

- [Utilisation des diagnostics avec Application Insights pour Cloud Services](./application-insights/app-insights-cloudservices.md)
- [Assurer le suivi du flux dans une application Cloud Services avec Diagnostics Azure](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Utilisation de PowerShell pour configurer les diagnostics sur Cloud Services](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## Virtual Machines avec les diagnostics Azure
- Si vous utilisez Visual Studio, voir [Utilisation de Visual Studio pour suivre Azure Virtual Machines](./vs-azure-tools-debug-cloud-services-virtual-machines.md) pour commencer. Sinon, voir
- [Configurer les diagnostics Azure sur une machine virtuelle Azure](./virtual-machines-dotnet-diagnostics.md)

Pour des rubriques plus avancées, voir

- [Utilisation de PowerShell pour configurer les diagnostics sur Azure Virtual Machines](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Créer une machine virtuelle Windows avec des fonctionnalités de surveillance et de diagnostics à l’aide d’un modèle Azure Resource Manager](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## Service Fabric avec les diagnostics Azure
Commencez avec [Surveillance d’une application Service Fabric](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). De nombreux autres articles sur les diagnostics Service Fabric sont disponibles dans l'arborescence de navigation sur la gauche, à partir de cet article.

## Articles généraux sur les diagnostics Azure
- [Configuration du schéma des diagnostics Azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) - Apprenez à modifier le fichier de schéma pour collecter et acheminer les données des diagnostics. Vous pouvez également utiliser Visual Studio pour modifier le fichier de schéma.
- [Mode de stockage des données des diagnostics Azure dans Azure Storage](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - Identifiez les noms des tables et des objets blob dans lesquels les données des diagnostics sont écrites.
- Apprenez à [utiliser les compteurs de performance dans les diagnostics Azure](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Apprenez à [acheminer les informations des diagnostics Azure vers Application Insights](./azure-diagnostics-configure-applicationinsights.md)
- Si vous rencontrez des difficultés avec le lancement des diagnostics ou la recherche des données dans les tables Azure Storage, voir [Résolution des problèmes des diagnostics Azure](./azure-diagnostics-troubleshooting.md)

<!---HONumber=AcomDC_0810_2016-->