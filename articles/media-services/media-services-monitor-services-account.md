<properties 
	pageTitle="Surveiller un compte Media Services" 
	description="Explique comment configurer la surveillance de votre compte Media Services dans Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>





#<a id="monitormediaservicesaccount"></a>Comment surveiller un compte Media Services

Cet article fait partie des séries [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md) et [workflow de vidéo en flux continu Media Services](media-services-live-streaming-workflow.md).

Le tableau de bord Azure Media Services présente des mesures d'utilisation et des informations de compte dont vous pouvez vous servir pour gérer votre compte Media Services.

Vous pouvez surveiller le nombre de travaux d'encodage en file d'attente, de tâches d'encodage ayant échoué, de travaux d'encodage actifs, représentés par les données d'entrée et de sortie de l'encodeur, ainsi que l'utilisation du stockage d'objets blob associée à votre compte Media Services. En outre, si vous diffusez du contenu en continu à vos clients, vous pouvez également récupérer diverses mesures de diffusion en continu. Vous pouvez choisir de surveiller vos données sur les 6 dernières heures, les dernières 24 heures ou les 7 derniers jours.
 
>[AZURE.NOTE]La surveillance des données de stockage dans le portail de gestion Azure occasionne des frais supplémentaires. Pour plus d'informations, consultez la page [Storage Analytics et facturation](http://go.microsoft.com/fwlink/?LinkId=256667).

##<a id="configuremonitoring"></a>Procédure : surveillance d'un compte Media Services

1. Dans le [portail de gestion](http://go.microsoft.com/fwlink/?LinkID=256666), cliquez sur **Media Services**, puis sur le nom du compte Media Services pour ouvrir le tableau de bord. 

	![MediaServices_Dashboard][dashboard]

2. Pour surveiller vos travaux ou vos données d'encodage, commencez simplement à envoyer des travaux d'encodage à Media Services ou commencez à diffuser en continu du contenu pour les clients en utilisant la diffusion en continu à la demande Azure Media Services. Les premières données de surveillance devraient apparaître sur le tableau de bord après un délai d'environ une heure.

##<a id="configuringstorage"></a>Procédure : surveillance de l'utilisation de votre stockage d'objets blob (facultatif)
1. Cliquez sur le nom **COMPTE DE STOCKAGE** sous la section **aperçu rapide**.
2. Cliquez sur le lien **page de configuration**, puis faites défiler l'écran vers le bas jusqu'aux paramètres de **surveillance** pour les services BLOB, de Table et de File d'attente, présentés ci-dessous.

	>[AZURE.NOTE]Les objets blob sont le seul type de stockage pris en charge dans Media Services.

	![StorageOptions][storage_options_scoped]

3. Dans **surveillance**, définissez le niveau de surveillance et la stratégie de rétention de données pour les objets blob :

-  Pour définir le niveau de surveillance, sélectionnez l'une des options suivantes :

      **Minimal** : collecte des mesures telles que l'entrée/sortie, la disponibilité, la latence et les pourcentages de réussite, qui sont agrégées pour les services Blob, Table et File d'attente.

      **Verbose** : outre les mesures minimales, collecte le même ensemble de mesures pour chaque opération de stockage de l'API Azure Storage Service. Les mesures détaillées permettent d'analyser plus précisément les problèmes survenant durant le fonctionnement d'une application.

      **Off** : désactive la surveillance. Les données de surveillance existantes sont conservées jusqu'au terme de la période de rétention.

- Pour définir la stratégie de rétention de données, dans **Rétention (en jours)**, tapez le nombre de jours durant lesquels les données sont conservées (de 1 à 365 jours). Si vous ne souhaitez pas définir de stratégie de rétention, entrez 0. Dans ce cas, il vous appartient de supprimer ou non les données de surveillance. Nous vous recommandons de définir une stratégie de rétention en fonction de la durée de conservation que vous souhaitez appliquer aux données d'analyse de votre compte, de sorte que les données d'analyse anciennes et non utilisées puissent être supprimées par le système sans frais.

4. Une fois la configuration de la surveillance terminée, cliquez sur **Enregistrer**. De même que les mesures Media Services, les données de surveillance doivent commencer à s'afficher dans le tableau de bord au bout d'une heure environ. Les mesures sont stockées dans le compte de stockage dans quatre tables nommées $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue et $MetricsCapacityBlob. Pour plus d'informations, consultez la page [À propos des métriques Storage Analytics](http://go.microsoft.com/fwlink/?LinkId=256668).


<!-- Images -->
[dashboard]: ./media/media-services-monitor-services-account/media-services-dashboard.png
[storage_options_scoped]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png

 

<!---HONumber=62-->