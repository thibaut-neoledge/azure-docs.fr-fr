---
title: "Deploy Azure Log Analytics for Cloud Foundry Monitoring (Déployer l’infrastructure Nozzle d’Azure Log Analytics pour surveiller le système Cloud Foundry) | Microsoft Docs"
description: "Guide pas à pas pour déployer l’infrastructure Nozzle du compileur de fichiers log (Loggregator) de Cloud Foundry pour Azure Log Analytics, pour configurer Azure Log Analytics et la console OMS, et pour utiliser ces outils pour surveiller l’intégrité et les indicateurs de performance du système Cloud Foundry."
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f704a2638a4b6b57c014d502dd87303a55334672
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Deploy Azure Log Analytics for Cloud Foundry Monitoring (Déployer l’infrastructure Nozzle d’Azure Log Analytics pour surveiller le système Cloud Foundry)

## <a name="background"></a>Arrière-plan

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) est un service compris dans Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS). Il facilite la collecte et l’analyse de données générées depuis votre cloud et vos environnements locaux.

L’infrastructure Nozzle de Microsoft Azure Log Analytics est un composant de Cloud Foundry, qui envoie des mesures du [compileur de fichiers log de Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) Firehose à Azure Log Analytics. Avec l’infrastructure Nozzle, vous pouvez collecter, consulter et analyser l’intégrité et les indicateurs de performance de Cloud Foundry, sur plusieurs déploiements.

Dans ce document, vous apprenez à déployer l’infrastructure Nozzle dans votre environnement Cloud Foundry et à accéder aux données depuis la console OMS d’Azure Log Analytics.

## <a name="prerequisites"></a>Composants requis

### <a name="1-deploy-a-cf-or-pcf-environment-in-azure"></a>1. Déployer un environnement Cloud Foundry ou Pivotal Cloud Foundry sur Azure

Vous pouvez utiliser l’infrastructure Nozzle avec le déploiement d’un environnement Cloud Foundry open-source ou d’un environnement Pivotal Cloud Foundry.

* [Déployer Cloud Foundry sur Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Déployer Pivotal Cloud Foundry sur Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installer les outils en ligne de commande pour déployer l’infrastructure Nozzle

L’infrastructure Nozzle s’exécute comme une application, dans un environnement Cloud Foundry. Il vous faut donc l’interface de ligne de commande Cloud Foundry pour déployer l’application. Elle nécessite aussi l’autorisation d’accès au compileur de fichiers log Firehose et au contrôleur cloud. Il vous faut donc un client de ligne de commande UAA pour créer et configurer l’utilisateur.

* [Installer l’interface de ligne de commande Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Installer le client de ligne de commande UAA de Cloud Foundry](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Vérifiez que Rubygems est installé avant de configurer le client de ligne de commande UAA.

### <a name="3-create-an-oms-workspace-in-azure"></a>3. Créer un espace de travail OMS dans Azure

#### <a name="create-the-oms-workspace-manually"></a>Créer l’espace de travail OMS manuellement

Vous pouvez créer l’espace de travail OMS manuellement, puis charger les vues et alertes préconfigurées d’OMS après avoir terminé le déploiement de l’infrastructure Nozzle.

1. Dans le portail Azure, recherchez Log Analytics dans la liste des services du Marketplace, puis sélectionnez Log Analytics.
2. Cliquez sur Créer, puis sélectionnez les options pour les éléments suivants :

* Espace de travail OMS : entrez un nom pour votre espace de travail.
* Abonnement : si vous possédez plusieurs abonnements, choisissez celui sur lequel vous avez déployé l’environnement Cloud Foundry.
* Groupe de ressources : vous pouvez créer un groupe de ressources, ou utiliser celui de l’environnement Cloud Foundry que vous avez déployé.
* Lieu
* Niveau tarifaire : cliquez sur OK pour terminer.
> Pour plus d’informations, consultez l’article [Prise en main de Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)

#### <a name="create-the-oms-workspace-through-the-oms-template"></a>Créer l’espace de travail OMS via le modèle OMS

Vous pouvez créer l’espace de travail OMS, charger les vues et alertes préconfigurées d’OMS via la [solution Azure OMS Log Analytics pour Cloud Foundry](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution)

## <a name="deploy-the-nozzle"></a>Déployer l’infrastructure Nozzle

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Déployer l’infrastructure Nozzle en tant que vignette Ops Manager sur Pivotal Cloud Foundry

Si vous avez déployé Pivotal Cloud Foundry via Ops Manager, suivez ces instructions pour [Installer et configurer l’infrastructure Nozzle pour Pivotal Cloud Foundry](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). L’infrastructure Nozzle est alors installée en tant que vignette avec Ops Manager.

### <a name="deploy-the-nozzle-as-an-application-to-cloud-foundry"></a>Déployer l’infrastructure Nozzle en tant qu’application sur Cloud Foundry

Si vous n’utilisez pas Ops Manager pour Pivotal Cloud Foundry, vous devez envoyer par push l’infrastructure Nozzle en tant qu’application.

#### <a name="log-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Connectez-vous à votre déploiement Cloud Foundry en tant qu’administrateur via l’interface de ligne de commande de Cloud Foundry

Dans votre boîte de développement, exécutez la commande suivante :
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

> « SYSTEM_DOMAIN » correspond au nom de domaine de votre environnement Cloud Foundry. Vous pouvez le récupérer en recherchant « SYSTEM_DOMAIN » dans le fichier du manifeste de déploiement. 
> "CF_User" correspond au nom d’administrateur de l’environnement Cloud Foundry. Vous pouvez récupérer le nom et le mot de passe en recherchant dans la section « scim » le nom et « cf_admin_password » dans le fichier du manifeste de déploiement.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Créer un utilisateur Cloud Foundry et lui accorder les autorisations nécessaires

Dans votre boîte de développement, exécutez les commandes suivantes :
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

> « SYSTEM_DOMAIN » correspond au nom de domaine de votre environnement Cloud Foundry. Vous pouvez le récupérer en recherchant « SYSTEM_DOMAIN » dans le fichier du manifeste de déploiement.

#### <a name="download-the-latest-azure-log-analytics-nozzle-release"></a>Télécharger la version la plus récente de l’infrastructure Nozzle d’Azure Log Analytics

Dans votre boîte de développement, exécutez la commande suivante :
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables-in-manifestyml-in-your-current-directory"></a>Définir des variables d’environnement dans le fichier « manifest.yml » dans votre répertoire actuel

Voici le manifeste d’application de l’infrastructure Nozzle. Vous devez remplacer la valeur par les informations de votre espace de travail OMS.

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics, default is 10s.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics, default is 10s.
OMS_MAX_MSG_NUM_PER_BATCH : The max number of messages in a batch to OMS Log Analytics, default is 1000.
API_ADDR                  : The api URL of the CF environment, refer to "Push the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
DOPPLER_ADDR              : Loggregator's traffic controller URL, refer to "Deploy the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
FIREHOSE_USER             : CF user you created in "Push the Nozzle as an App to Cloud Foundry" section, who has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma separated list, valid event types are METRIC,LOG,HTTP
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the Trafficcontroller
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments
IDLE_TIMEOUT              : Keep Alive duration for the firehose consumer, default is 60s.
LOG_LEVEL                 : Logging level of the nozzle, valid levels: DEBUG, INFO, ERROR
LOG_EVENT_COUNT           : If true, the total count of events that the nozzle has received and sent will be logged to OMS Log Analytics as CounterEvents
LOG_EVENT_COUNT_INTERVAL  : The time interval of logging event count to OMS Log Analytics, default is 60s.
```

### <a name="push-the-application-from-your-dev-box"></a>Distribuer l’application depuis votre boîte de développement

Vérifiez que vous êtes dans le dossier « oms-log-analytics-firehose-nozzle », puis exécutez :
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Valider l’installation de l’infrastructure Nozzle

### <a name="from-apps-manager-for-pcf"></a>À partir du gestionnaire d’applications (pour Pivotal Cloud Foundry)

1. Connectez-vous à Ops Manager et assurez-vous que la vignette est affichée sur le tableau de bord de l’installation.
2. Connectez-vous au gestionnaire d’applications et assurez-vous que l’espace que vous avez créé pour l’infrastructure Nozzle est répertorié dans le rapport d’utilisation et que l’état est normal.

### <a name="from-dev-box"></a>À partir de la boîte de développement

Dans la fenêtre de l’interface de ligne de commande Cloud Foundry dans la boîte de développement, tapez :
```
cf apps
```
Assurez-vous que l’application OMS Nozzle est en cours d’exécution.

## <a name="view-the-data-in-oms-portal"></a>Consultez les données dans le portail OMS

### <a name="1-import-oms-view"></a>1. Importer les vues d’OMS

À partir du portail OMS, accédez à **Concepteur de vues** -> **Importer** -> **Parcourir**, sélectionnez un des fichiers omsview, par exemple, *Cloud Foundry.omsview*et enregistrez la vue. Une **vignette** s’affiche alors sur la page principale de présentation d’OMS. Cliquez sur la **vignette** pour afficher des mesures.

Les opérateurs peuvent personnaliser ces vues ou en créer des nouvelles via **Concepteur de vues**.

Le fichier *Cloud Foundry.omsview* est une version préliminaire du modèle de vue Cloud Foundry OMS. Un modèle par défaut entièrement configuré est en cours de création. Veuillez nous faire part de vos commentaires et suggestions dans la [section relative aux problèmes](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Créer des règles d'alerte

Les opérateurs peuvent [créer les alertes](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts), personnaliser les requêtes et les valeurs de seuil si nécessaire. Voici un ensemble d’alertes recommandées.

| Requête de recherche                                                                  | Générer l’alerte selon | Description                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Nombre de résultats < 1   | **bbs.Domain.cf-apps** indique si le domaine cf-apps est à jour, ce qui veut dire que les requêtes des applications Cloud Foundry du contrôleur cloud sont synchronisées avec bbs.LRPsDesired (lA souhaitées par Diego) pour l’exécution. Si aucune donnée n’est reçue, le domaine cf-apps n’est pas à jour dans la fenêtre de temps donnée. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | Nombre de résultats > 0   | Pour les cellules de Diego, 0 signifie sain, et 1, défectueux. Définissez une alerte au cas où plusieurs **cellules défectueuses de Diego** sont détectées dans la fenêtre de temps donnée. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Nombre de résultats > 0 | 1 signifie que le système est sain, et 0 signifie que le système est défectueux. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Nombre de résultats > 0   | Consul envoie périodiquement son état d’intégrité. 0 signifie que le système est sain, et 1 signifie que l’émetteur d’itinéraire détecte que **Consul est éteint**. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | Nombre de résultats > 0 | Le nombre d’écarts entre les messages **déposés** intentionnellement par Doppler en raison d’une régulation de flux. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Nombre de résultats > 0   | Le compileur de fichiers log envoie **LGR** pour indiquer les problèmes liés à la connexion. Par exemple, quand la sortie de message de journal est trop importante. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Nombre de résultats > 0   | Lorsque l’infrastructure Nozzle reçoit des alertes de faible consommation du compileur de fichiers log, elle envoie la valeur **SlowConsumerAlert** à OMS. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Nombre de résultats > 0   | Si le nombre d’écarts entre les **événements perdus** atteint un certain seuil, l’infrastructure Nozzle pourrait avoir du mal à s’exécuter. |

## <a name="scale"></a>Mettre à l'échelle

### <a name="scale-the-nozzle"></a>Mettre à l’échelle l’infrastructure Nozzle

Nous recommandons aux opérateurs de commencer avec au moins deux instances de l’infrastructure Nozzle. Firehose distribue la charge de travail sur toutes les instances de l’infrastructure Nozzle.
Pour être sûr que l’infrastructure Nozzle peut supporter le trafic de données de Firehose, l’opérateur doit configurer l’alerte **slowConsumerAlert** répertoriée dans la section « Créer des règles d’alerte ». Une fois alerté, suivez les [conseils relatifs à l’infrastructure Nozzle lente](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) pour déterminer si une mise à l’échelle est nécessaire.
Pour mettre à l’échelle l’infrastructure Nozzle, utilisez le gestionnaire d’applications ou l’interface de ligne de commande Cloud Foundry pour augmenter le nombre d’instances ou de ressources mémoire/disque pour l’infrastructure.

### <a name="scale-the-loggregator"></a>Mettre à l’échelle le compileur de fichiers log

Le compileur de fichiers log envoie le message de journal **LGR** pour indiquer les problèmes liés à la connexion. L’opérateur peut surveiller l’alerte pour déterminer si le compileur de fichiers log doit être mis à l’échelle.
Pour mettre à l’échelle le compileur de fichiers log, l’opérateur peut augmenter la mémoire tampon de Doppler ou ajouter des instances de serveur supplémentaires de Doppler dans le manifeste Cloud Foundry. Pour plus d’informations, consultez les [conseils pour la mise à l’échelle du compileur de fichiers log](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Mettre à jour

Pour mettre à jour l’infrastructure Nozzle à une version plus récente, téléchargez la nouvelle version de Nozzle, suivez les étapes dans la section « Déployer », puis envoyez de nouveau l’application par push.

Pour supprimer l’infrastructure Nozzle, procédez comme suit :

### <a name="from-the-ops-manager"></a>À partir d’Ops Manager

1. Se connecter à Ops Manager
2. Rechercher la vignette « Infrastructure Nozzle de Microsoft Azure Log Analytics pour Pivotal Cloud Foundry »
3. Cliquez sur l’icône de poubelle puis confirmez la suppression.

### <a name="from-the-dev-box"></a>À partir de la boîte de développement

Dans la fenêtre de l’interface de ligne de commande Cloud Foundry, tapez :
```
cf delete <App Name> -r
```

Les données contenues dans le portail OMS ne sont pas automatiquement supprimées lors de la suppression de l’infrastructure Nozzle. Elles expirent selon vos paramètres de rétention des log analytics d’OMS.

## <a name="support-and-feedback"></a>Support et commentaires

L’infrastructure Nozzle d’Azure Log Analytics est open-source. Faites-nous part de vos questions et commentaires dans la [section github](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Pour ouvrir une demande d’assistance Azure, utilisez la « machine virtuelle qui exécute Cloud Foundry » comme catégorie de service. 

## <a name="next-step"></a>Étape suivante

En plus des mesures de Cloud Foundry gérées par l’infrastructure Nozzle, vous pouvez utiliser un agent OMS pour obtenir des informations sur les données opérationnelles de niveau de la machine virtuelle (Syslog, performances, alertes, inventaire). L’agent est installé en tant qu’add-on Bosh sur vos machines virtuelles Cloud Foundry.
> Pour en savoir plus, consultez [Déployer l’agent OMS sur votre déploiement Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).
