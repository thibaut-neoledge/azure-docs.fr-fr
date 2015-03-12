<properties 
	pageTitle="Déploiement d'un service cloud dans un environnement intermédiaire (Node.js) - Azure" 
	description="Découvrez comment déployer votre application Azure vers un environnement intermédiaire, puis comment la déployer vers un environnement de production grâce à l'échange d'adresses IP virtuelles (VIP)." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>







# Déploiement intermédiaire d'une application dans Azure

Une application peut être déployée dans l'environnement intermédiaire dans
Azure pour être testée avant de la transférer vers l'environnement de production
dans lequel l'application est accessible sur Internet. L'
environnement intermédiaire est identique à l'environnement de production, à l'exception
que vous pouvez uniquement accéder à l'application intermédiaire à l'aide d'une URL masquée
qui est générée par Azure. Après avoir vérifié que votre
application fonctionne correctement, vous pouvez la déployer sur l'environnement de production
en effectuant un échange d'IP virtuelle (VIP).

> [AZURE.NOTE] Les étapes figurant dans cet article s'appliquent uniquement aux applications Node hébergées en tant que service cloud Azure.

Cette procédure comprend les étapes suivantes :

-   [Étape 1: déploiement intermédiaire d'une application]
-   [Étape 2 : déploiement d'une application en production en échangeant les adresses IP virtuelles]

<h2><a id="step1"></a>Étape 1 : déploiement intermédiaire d'une application</h2>

Cette tâche explique comment effectuer un déploiement intermédiaire d'une application au moyen de **Windows
Azure PowerShell**.

1.  Lorsque vous publiez un service, transmettez simplement le paramètre **-Slot**
    à l'applet de commande **Publish-AzureServiceProject**.

    **Publish-AzureServiceProject -Slot staging**

2.  Connectez-vous au [portail de gestion Azure] et sélectionnez **Services cloud**. Une fois que le service cloud a été créé et que le statut de la colonne **Intermédiaire** est passé à **En cours d'exécution**, cliquez sur le nom du service.

	![portal displaying a running service][cloud-service]

3.  Sélectionnez le **Tableau de bord**, puis **Intermédiaire**.

	![cloud service dashboard][cloud-service-dashboard]

4. Notez la valeur de l'entrée **URL du site** à droite. Le nom DNS est un ID interne masqué qu'Azure a généré.

    ![site url][cloud-service-staging-url]

À présent, vous pouvez vérifier que l'application fonctionne correctement dans l'environnement intermédiaire en utilisant l'URL du site intermédiaire.

Pour un scénario de mise à niveau, dans lequel l'application intermédiaire est une mise à niveau
d'une application qui a déjà été déployée pour la production, vous pouvez
[mettre à niveau l'application dans l'environnement de production en échangeant les
adresses IP virtuelles][Étape 2 : déploiement d'une application en production en échangeant les adresses IP virtuelles].

<h2><a id="step2"></a>Étape 2 : mise à niveau d'une application en production en échangeant les adresses IP virtuelles</h2>

Après avoir vérifié la version mise à niveau d'une application dans l'
environnement intermédiaire, vous pouvez la rendre rapidement disponible en production en
échangeant les adresses IP virtuelles (VIP) des environnements intermédiaire et de
production.

> [AZURE.NOTE] Cette étape part du principe que vous avez déjà déployé une
application en production et créé une version intermédiaire de la version mise à niveau de
l'application.

1.  Connectez-vous au [portail de gestion Azure].
    Cliquez sur **Services cloud**, puis sélectionnez le nom du service.

2.  Dans le **Tableau de bord**, sélectionnez **Intermédiaire**, puis cliquez sur **Échanger** en bas de la page. Cette action ouvre la boîte de dialogue
    Échange d'adresses IP virtuelles.

    ![vip swap dialog][vip-swap-dialog]

3.  Passez en revue les informations, puis cliquez sur **OK**. La mise à jour des deux déploiements
    commence, le déploiement intermédiaire bascule dans l'environnement de production et
    le déploiement de production bascule dans l'environnement intermédiaire.

Vous avez correctement effectué un déploiement intermédiaire et mis à niveau un
déploiement de production en échangeant les adresses IP virtuelles avec le déploiement intermédiaire.

## Ressources supplémentaires

- [Comment déployer une mise à niveau de Service en Production en échangeant les adresses IP virtuelles dans Azure]
- [Présentation de la gestion des déploiements dans Azure]

  [Étape 1: déploiement intermédiaire d'une application]: #step1
  [Étape 2 : déploiement d'une application en production en échangeant les adresses IP virtuelles]: #step2
  [Portail de gestion Azure]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Comment déployer une mise à niveau de Service en Production en échangeant les adresses IP virtuelles dans Azure]: http://msdn.microsoft.com/library/windowsazure/ee517253.aspx
  [Présentation de la gestion des déploiements dans Azure]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx

<!--HONumber=45--> 
