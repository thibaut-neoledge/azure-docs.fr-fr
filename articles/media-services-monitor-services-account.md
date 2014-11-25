<properties linkid="manage-services-mediaservices-monitor-a-media-services-account" urlDisplayName="How to monitor" pageTitle="Monitor a Media Services Account - Azure" metaKeywords="" description="Describes how to configure monitoring for your Media Services account in Azure." metaCanonical="" services="media-services" documentationCenter="" title="How to Monitor a Media Services Account" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree" />

# <span id="monitormediaservicesaccount"></span></a>Surveillance d'un compte Media Services

Le tableau de bord Azure Media Services présente des mesures d'utilisation et des informations de compte dont vous pouvez vous servir pour gérer votre compte Media Services.

Vous pouvez surveiller le nombre de travaux d'encodage en file d'attente, de tâches d'encodage ayant échoué, de travaux d'encodage actifs, représentés par les données d'entrée et de sortie de l'encodeur, ainsi que l'utilisation du stockage d'objets blob associée à votre compte Media Services. En outre, si vous diffusez du contenu en continu à vos clients, vous pouvez également récupérer diverses mesures de diffusion en continu. Vous pouvez choisir de surveiller vos données sur les 6 dernières heures, les dernières 24 heures ou les 7 derniers jours.

**Remarque** La surveillance des données de stockage dans le portail de gestion Azure occasionne des frais supplémentaires. Pour plus d'informations, consultez la page [Storage Analytics et facturation][Storage Analytics et facturation].

## <span id="configuremonitoring"></span></a> Surveillance d'un compte Media Services

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **Media Services**, puis sur le nom du compte Media Services pour ouvrir le tableau de bord.

    ![MediaServices\_Dashboard][MediaServices\_Dashboard]

2.  Pour surveiller vos travaux ou vos données d'encodage, commencez simplement à envoyer des travaux d'encodage à Media Services ou commencez à diffuser en continu du contenu pour les clients en utilisant la diffusion en continu à la demande Azure Media Services. Les premières données de surveillance devraient apparaître sur le tableau de bord après un délai d'environ une heure.

## <span id="configuringstorage"></span></a> Surveillance de l'utilisation de votre stockage d'objets blob (facultatif)

1.  Cliquez sur le nom **COMPTE DE STOCKAGE** sous la section **aperçu rapide**.
2.  Cliquez sur le lien **page de configuration**, puis faites défiler l'écran vers le bas jusqu'aux paramètres de **surveillance** pour les services BLOB, de Table et de File d'attente, présentés ci-dessous.

    **Remarque** Les objets blob sont le seul type de stockage pris en charge dans Media Services.

    ![StorageOptions][StorageOptions]

3.  Dans **surveillance**, définissez le niveau de surveillance et la stratégie de rétention de données pour les objets blob :

-   Pour définir le niveau de surveillance, sélectionnez l'une des options suivantes :

    **Minimal** : collecte des mesures telles que l'entrée/sortie, la disponibilité, la latence et les pourcentages de réussite, qui sont agrégées pour les services Blob, Table et File d'attente.

    **Verbose** : outre les mesures minimales, collecte le même ensemble de mesures pour chaque opération de stockage de l'API Azure Storage Service. Les métriques détaillées permettent d'analyser plus précisément les problèmes survenant durant le fonctionnement d'une application.

    **Off** : désactive la surveillance. Les données de surveillance existantes sont conservées jusqu'au terme de la période de rétention.

-   Pour définir la stratégie de rétention de données, dans **Rétention (en jours)**, tapez le nombre de jours durant lesquels les données sont conservées (de 1 à 365 jours). Si vous ne souhaitez pas définir de stratégie de rétention, entrez 0. Dans ce cas, il vous appartient de supprimer ou non les données de surveillance. Nous vous recommandons de définir une stratégie de rétention en fonction de la durée de conservation que vous souhaitez appliquer aux données d'analyse de votre compte, de sorte que les données d'analyse anciennes et non utilisées puissent être supprimées par le système sans frais.

1.  When you finish the monitoring configuration, click **Save**.
    Similar to Media Services metrics, you should start seeing monitoring data on the dashboard after about an hour.
    Metrics are stored in the storage account in four tables named $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue, and $MetricsCapacityBlob. Pour plus d'informations, consultez la page [À propos des métriques Storage Analytics][À propos des métriques Storage Analytics].

<!-- Images -->

  [Storage Analytics et facturation]: http://go.microsoft.com/fwlink/?LinkId=256667
  [portail de gestion]: http://go.microsoft.com/fwlink/?LinkID=256666
  [StorageOptions]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png
  [À propos des métriques Storage Analytics]: http://go.microsoft.com/fwlink/?LinkId=256668
