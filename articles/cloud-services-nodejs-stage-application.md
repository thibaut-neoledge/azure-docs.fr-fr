<properties linkid="dev-nodejs-enablestaging" urlDisplayName="Staging Deployment" pageTitle="Stage a cloud service deployment (Node.js) - Azure" metaKeywords="Azure staging, Azure application staging, Azure test environment, Azure staging environment, Azure Virtual IP swap, Azure VIP swap" description="Learn how to deploy your Azure application to a staging environment, then deploy to a production environment using Virtual IP (VIP) swap." metaCanonical=" " services="cloud-services" documentationCenter="nodejs" title="Staging an Application in Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Déploiement intermédiaire d'une application dans Azure

Une application packagée peut être déployée dans l'environnement
intermédiaire d'Azure afin d'être testée avant de la déplacer
vers l'environnement de production dans lequel elle sera accessible sur Internet.
L'environnement intermédiaire est exactement comme l'environnement de
production, à la différence que vous ne pouvez accéder à l'environnement
intermédiaire qu'avec une URL masquée générée par Azure. Une fois que vous avez vérifié
que votre application fonctionne correctement, elle peut être déployée
dans l'environnement de production après avoir effectué un échange d'adresses IP virtuelles (VIP).

<div class="dev-callout">
    <b>Remarque</b>
    <p>Les &eacute;tapes figurant dans cet article s'appliquent uniquement aux applications Node h&eacute;berg&eacute;es en tant que service cloud Azure.</p>
</div>

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : déploiement intermédiaire d'une application][]
-   [Étape 2 : déploiement d'une application en production en échangeant les adresses IP virtuelles][]

## <span id="step1"></span></a>Étape 1 : déploiement intermédiaire d'une application

Cette tâche explique comment effectuer un déploiement intermédiaire d'une
application au moyen de **Windows Azure PowerShell**.

1.  Lorsque vous publiez un service, transmettez simplement le paramètre
    **-Slot** à la cmdlet **Publish-AzureServiceProject**.

    **Publish-AzureServiceProject -Slot staging**

2.  Connectez-vous au [portail de gestion Azure][] et sélectionnez **Cloud Services**. Une fois que le service cloud a été créé et que le statut de la colonne **Intermédiaire** est passé à **En cours d'exécution**, cliquez sur le nom du service.

    ![portail affichant un service en cours d'exécution][]

3.  Sélectionnez le **Tableau de bord**, puis **Intermédiaire**.

    ![tableau de bord du service cloud][]

4.  Notez la valeur de l'entrée **URL du site** à droite. Le nom DNS est un ID interne masqué qu'Azure a généré.

    ![URL du site][]

À présent, vous pouvez vérifier que l'application fonctionne correctement dans l'environnement intermédiaire en utilisant l'URL du site intermédiaire.

Pour un scénario de mise à niveau, dans lequel l'application intermédiaire
est une version mise à niveau de celle ayant déjà été déployée en
production, vous pouvez [mettre à niveau l'application dans l'environnement
de production en échangeant les adresses IP virtuelles][Étape 2 : déploiement d'une application en production en échangeant les adresses IP virtuelles].

## <span id="step2"></span></a>Étape 2 : mise à niveau d'une application en production en échangeant les adresses IP virtuelles

Une fois que vous avez vérifié la version mise à niveau d'une
application dans l'environnement intermédiaire, vous pouvez la rendre
rapidement disponible en production en permutant les adresses IP
virtuelles des environnements intermédiaire et de production.

<div class="dev-callout">
<b>Remarque</b>
<p>Cette &eacute;tape part du principe que vous avez d&eacute;j&agrave; d&eacute;ploy&eacute; une application en production et cr&eacute;&eacute; une version interm&eacute;diaire de la version mise &agrave; niveau de l'application.</p>
</div>

1.  Connectez-vous au [portail de gestion Azure][]
    cliquez sur **Cloud Services**, puis sélectionnez le nom du service.

2.  Dans le **Tableau de bord**, sélectionnez **Intermédiaire**, puis cliquez sur **Échanger** en bas de la page. Cette action ouvre la boîte
    de dialogue Échange d'adresses IP virtuelles.

    ![boîte de dialogue Échange d'adresses IP virtuelles][]

3.  Passez en revue les informations, puis cliquez sur **OK**. La mise à jour des deux
    déploiements commence lorsque le déploiement intermédiaire passe
    en production et vice-versa.

Vous avez correctement effectué un déploiement intermédiaire et mis à
niveau un déploiement de production en échangeant les adresses IP virtuelles avec le déploiement en phase intermédiaire.

## Ressources supplémentaires

-   [Comment déployer une mise à niveau de service vers l'environnement de production en échangeant les adresses IP virtuelles dans Azure][]
-   [Présentation de la gestion des déploiements dans Azure][]

  [Étape 1 : déploiement intermédiaire d'une application]: #step1
  [Étape 2 : déploiement d'une application en production en échangeant les adresses IP virtuelles]: #step2
  [portail de gestion Azure]: http://manage.windowsazure.com
  [portail affichant un service en cours d'exécution]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
  [tableau de bord du service cloud]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [URL du site]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [boîte de dialogue Échange d'adresses IP virtuelles]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Comment déployer une mise à niveau de service vers l'environnement de production en échangeant les adresses IP virtuelles dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee517253.aspx
  [Présentation de la gestion des déploiements dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh386336.aspx
