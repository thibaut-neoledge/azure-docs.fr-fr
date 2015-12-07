<properties 
	pageTitle="Déploiement d'un service cloud dans un environnement intermédiaire (Node.js) | Microsoft Azure" 
	description="Découvrez comment déployer votre application Azure vers un environnement intermédiaire, puis comment la déployer vers un environnement de production grâce à l'échange d'adresses IP virtuelles (VIP)." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="robmcm"/>



# Déploiement intermédiaire d'une application dans Azure

Une application packagée peut être déployée dans l'environnement intermédiaire d'Azure afin d'être testée avant de la déplacer vers l'environnement de production dans lequel elle sera accessible sur Internet. L'environnement intermédiaire est exactement comme l'environnement de production, à la différence que vous ne pouvez accéder à l'environnement intermédiaire qu'avec une URL masquée générée par Azure. Une fois que vous avez vérifié que votre application fonctionne correctement, elle peut être déployée dans l'environnement de production après avoir effectué un échange d'adresses IP virtuelles (VIP).

> [AZURE.NOTE]Les étapes figurant dans cet article s'appliquent uniquement aux applications Node hébergées en tant que service cloud Azure.

## Étape 1 : déploiement intermédiaire d'une application

Cette tâche explique comment effectuer un déploiement intermédiaire d’une application au moyen de **Microsoft Azure PowerShell**.

1.  Lorsque vous publiez un service, transmettez simplement le paramètre **-Slot** à la cmdlet **Publish-AzureServiceProject**.

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  Connectez-vous au [portail de gestion Azure] et sélectionnez **Cloud Services**. Une fois que le service cloud a été créé et que le statut de la colonne **Intermédiaire** est passé à **En cours d'exécution**, cliquez sur le nom du service.

	![portail affichant un service en cours d'exécution][cloud-service]

3.  Sélectionnez le **Tableau de bord**, puis **Intermédiaire**.

	![tableau de bord du service cloud][cloud-service-dashboard]

4. Notez la valeur de l'entrée **URL du site** à droite. Le nom DNS est un ID interne masqué qu'Azure a généré.

    ![URL du site][cloud-service-staging-url]

À présent, vous pouvez vérifier que l'application fonctionne correctement dans l'environnement intermédiaire en utilisant l'URL du site intermédiaire.

## Étape 2 : mise à niveau d'une application en production en échangeant les adresses IP virtuelles

Une fois que vous avez vérifié la version mise à niveau d'une application dans l'environnement intermédiaire, vous pouvez la rendre rapidement disponible en production en permutant les adresses IP virtuelles des environnements intermédiaire et de production.

> [AZURE.NOTE]Cette étape part du principe que vous avez déjà déployé une application en production et créé une version intermédiaire de la version mise à niveau de l'application.

1.  Connectez-vous au [portail de gestion Azure], cliquez sur **Cloud Services**, puis sélectionnez le nom du service.

2.  Dans le **Tableau de bord**, sélectionnez **Intermédiaire**, puis cliquez sur **Échanger** en bas de la page. Cette action ouvre la boîte de dialogue Échange d'adresses IP virtuelles.

    ![boîte de dialogue Échange d'adresses IP virtuelles][vip-swap-dialog]

3.  Passez en revue les informations, puis cliquez sur **OK**. La mise à jour des deux déploiements commence lorsque le déploiement intermédiaire passe en production et vice-versa.

Vous avez correctement effectué un déploiement intermédiaire et mis à niveau un déploiement de production en échangeant les adresses IP virtuelles avec le déploiement en phase intermédiaire.

## Ressources supplémentaires

- [Comment déployer une mise à niveau de service vers l’environnement de production en échangeant les adresses IP virtuelles dans Azure]

[portail de gestion Azure]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Comment déployer une mise à niveau de service vers l’environnement de production en échangeant les adresses IP virtuelles dans Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production

<!---HONumber=AcomDC_1125_2015-->