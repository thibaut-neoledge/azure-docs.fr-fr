<properties
	pageTitle="Déploiement d’applications vers Azure App Service"
	description="Découvrir le fonctionnement du déploiement d’applications vers App Service"
	keywords="app service, azure app service, déployer, déploiement"
	services="app-service"
	documentationCenter=""
	authors="dariagrigoriu"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="dariagrigoriu"/>

# Vue d’ensemble du déploiement d’Azure App Service

Azure App Service fournit un ensemble de fonctionnalités enrichies et intégrées destiné à prendre en charge la création de flux de travail de déploiement puissants et flexibles. Le déploiement d’applications peut tirer parti d’options, notamment de l’intégration continue ou de la publication du contrôle de code source local, de WebDeploy et du FTP. La méthode recommandée pour le déploiement des applications de production est l’échange des emplacements de déploiement. Les emplacements de déploiement représentent les environnements intermédiaires et d’intégration associés aux applications de production. Ils peuvent être configurés et ciblés avec le trafic web pour la validation, et le trafic peut être échangé à la demande pour le déploiement vers la production, sans temps d’arrêt ni mise en route automatisée. Les étapes d’un flux de travail de déploiement peuvent être facilement automatisées via les produits de gestion des mises en production comme Visual Studio Release Management. Cela est utile pour la coordination avec d’autres ressources de la solution (par exemple, la banque de données), la périodicité et la réplication entre plusieurs unités de déploiement.

[AZURE.INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]

<!---HONumber=AcomDC_0413_2016-->