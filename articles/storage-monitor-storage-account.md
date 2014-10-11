<properties linkid="manage-services-how-to-monitor-a-storage-account" urlDisplayName="How to monitor" pageTitle="How to monitor a storage account | Microsoft Azure" metaKeywords="Azure monitor storage accounts, storage account management portal, storage account dashboard, storage metrics table, storage metrics chart" description="Learn how to monitor a storage account in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Monitor a Storage Account" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# <span id="createstorageaccount"></span></a>Surveillance d'un compte de stockage

Vous pouvez surveiller vos comptes de stockage dans la version préliminaire du portail de gestion Azure. Pour chaque service de stockage associé au compte de stockage (Blob, File d'attente et Table), vous pouvez choisir le niveau de surveillance (minimal ou détaillé) et spécifier la stratégie de rétention de données appropriée.

Tant que vous n'avez pas configuré la surveillance d'un compte de stockage, aucune collecte de données de surveillance n'intervient et les graphiques de mesures du tableau de bord et la page **Monitor** sont vides.

<div class="dev-callout"> 
<b>Remarque</b> 
<p>L'analyse des donn&eacute;es de surveillance dans le portail de gestion occasionne des frais suppl&eacute;mentaires. Pour plus d'informations, consultez la page <a href="http://msdn.microsoft.com/en-us/library/windowsazure/hh360997.aspx">Storage Analytics et facturation</a>.</p> 
</div>

## Sommaire

-   [Configure monitoring for a storage account][]
-   [Customize the dashboard for monitoring][]
-   [Customize the Monitor page][]
-   [Add metrics to the metrics table][]
-   [Customize the metrics chart on the Monitor page][]
-   [Configure logging][]

## <span id="configurestoragemonitoring"></span></a> Configure monitoring for a storage account

1.  Dans le [portail de gestion][], cliquez sur **Storage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord.

2.  Cliquez sur **Configure**, puis faites défiler l'écran vers le bas jusqu'aux paramètres de surveillance (**monitoring**) pour les services Blob, Table et File d'attente, présentés ci-dessous.

    ![MonitoringOptions][]

3.  Dans **monitoring**, définissez le niveau de surveillance et la stratégie de rétention de données pour chaque service :

-   Pour définir le niveau de surveillance, sélectionnez l'une des options suivantes :

    **Minimal** : collecte des mesures telles que l'entrée/sortie, la disponibilité, la latence et les pourcentages de réussite, qui sont agrégées pour les services Blob, Table et File d'attente.

    **Verbose** : outre les mesures minimales, collecte le même ensemble de mesures pour chaque opération de stockage de l'API Azure Storage Service. Les métriques détaillées permettent d'analyser plus précisément les problèmes survenant durant le fonctionnement d'une application.

    **Off** : désactive la surveillance. Les données de surveillance existantes sont conservées jusqu'au terme de la période de rétention.

-   Pour définir la stratégie de rétention de données, dans **Retention (in days)**, tapez le nombre de jours durant lesquels les données sont conservées (de 1 à 365 jours). Si vous ne souhaitez pas définir de stratégie de rétention, entrez 0. Dans ce cas, il vous appartient de supprimer ou non les données de surveillance. Nous vous recommandons de définir une stratégie de rétention en fonction de la durée de conservation que vous souhaitez appliquer aux données d'analyse de votre compte, de sorte que les données d'analyse anciennes et non utilisées puissent être supprimées par le système sans frais.

1.  Une fois la configuration de la surveillance terminée, cliquez sur **Save**.

Les données de surveillance doivent commencer à s'afficher dans le tableau de bord et la page **Monitor** au bout d'une heure environ.

Les mesures sont stockées dans le compte de stockage dans quatre tables nommées $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue et $MetricsCapacityBlob. Pour plus d'informations, consultez la page [À propos des mesures Storage Analytics][].

Après avoir défini les niveaux de surveillance et les stratégies de rétention, vous pouvez choisir parmi les mesures disponibles dans le portail de gestion celles à surveiller et celles à représenter sur les graphiques de mesures. Un ensemble de mesures par défaut s'affiche à chaque niveau de surveillance. Vous pouvez utiliser **Add Metrics** pour ajouter ou supprimer des mesures dans la liste de mesures.

## <span id="customizestoragemonitoring"></span></a> Customize the dashboard for monitoring

Sur le tableau de bord, vous pouvez choisir jusqu'à six mesures sur les neuf disponibles pour les représenter sur le graphique de mesures. Pour chaque service (Blob, Table et File d'attente), les mesures Availability, Success Percentage et Total Requests sont disponibles. Les mesures disponibles sur le tableau de bord sont les mêmes, quel que soit le type de surveillance (minimale ou détaillée).

1.  Dans le [portail de gestion][], cliquez sur **Storage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord.

2.  Pour modifier les mesures représentées sur le graphique, effectuez l'une des actions suivantes :

-   Pour ajouter une nouvelle mesure au graphique, activez la case à cocher en regard de son en-tête. Dans un écran étroit, cliquez sur ***n* more** pour accéder aux en-têtes qui ne sont pas visibles dans la zone d'en-têtes.

-   Pour masquer une mesure représentée sur le graphique, désactivez la case à cocher à côté de son en-tête.

    ![Monitoring\_nmore][]

1.  Par défaut, le graphique indique les tendances, affichant uniquement la valeur actuelle de chaque mesure (option **Relative** dans la partie supérieure du graphique). Pour afficher un axe Y afin de voir les valeurs absolues, sélectionnez **Absolute**.

2.  Pour modifier la plage horaire des mesures affichées dans le graphique, sélectionnez 6 heures, 24 heures ou 7 jours en haut du graphique.

## <span id="customizemonitorpage"></span></a> Customize the Monitor page

Sur la page **Monitor**, vous pouvez afficher le jeu complet de mesures associé à votre compte de stockage.

-   Si vous avez configuré la surveillance minimale pour votre compte de stockage, des mesures telles que l'entrée/sortie, la disponibilité, la latence et les pourcentages de réussite sont agrégées à partir des services Blob, Table et File d'attente.

-   Si vous avez configuré la surveillance détaillée, les mesures sont disponibles à une résolution plus fine des opérations de stockage individuelles, en plus des agrégations au niveau des services.

Utilisez les procédures suivantes pour choisir les mesures de stockage à afficher dans les graphiques et le tableau des mesures figurant sur la page **Monitor**. Ces paramètres n'ont aucune incidence sur la collecte, l'agrégation et le stockage de données de surveillance dans le compte de stockage.

## <span id="addmonitoringmetrics"></span></a> Ajout de mesures à la table des mesures

1.  Dans le [portail de gestion][], cliquez sur **Storage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord.

2.  Cliquez sur **Monitor**.

    La page **Monitor** s'ouvre. Par défaut, le tableau des mesures affiche un sous-ensemble des mesures disponibles pour la surveillance. L'écran Monitor par défaut illustré ci-dessous représente un compte de stockage avec une surveillance détaillée configurée pour les trois services. Utilisez **Add Metrics** pour sélectionner parmi les mesures disponibles celles qui doivent faire l'objet d'une surveillance.

    ![Monitoring\_VerboseDisplay][]

    <div class="dev-callout"> 
<b>Remarque</b> 
<p>Tenez compte des frais au moment de s&eacute;lectionner les mesures. En effet, l'actualisation des &eacute;crans de surveillance s'accompagne de frais de transaction et de sortie. Pour plus d'informations, consultez la page <a href="http://msdn.microsoft.com/en-us/library/windowsazure/hh360997.aspx">Storage Analytics et facturation</a>.</p> 
</div>

3.  Cliquez sur **Add Metrics**.

    Les mesures d'agrégation disponibles dans le cadre de la surveillance minimale figurent en haut de la liste. Si la case à cocher est activée, la mesure figure dans la liste des mesures.

    ![AddMetricsInitialDisplay][]

4.  Placez le pointeur dans la partie droite de la boîte de dialogue pour afficher une barre de défilement que vous pouvez actionner pour faire défiler d'autres mesures.

    ![AddMetricsScrollbar][]

5.  Cliquez sur la flèche vers le bas en regard d'une mesure pour développer la liste des opérations qu'elle prend en charge. Sélectionnez chaque opération que vous souhaitez voir figurer dans le tableau des mesures du portail de gestion.

    Dans l'illustration suivante, la mesure AUTHORIZATION ERROR PERCENTAGE a été développée.

    ![ExpandCollapse][]

6.  Après avoir sélectionné des mesures pour tous les services, cliquez sur OK (coche) pour mettre à jour la configuration de la surveillance. Les mesures sélectionnées sont ajoutées au tableau des mesures.

7.  Pour supprimer une mesure du tableau, cliquez sur la mesure pour la sélectionner, puis sur **Delete Metric**, comme illustré ci-dessous.

    ![DeleteMetric][]

## <span id="customizemetricschart"></span></a> Customize the metrics chart on the Monitor page

1.  Sur la page **Monitor** du compte de stockage, dans le tableau des mesures, sélectionnez les mesures (six au maximum) à représenter sur le graphique de mesures. Pour sélectionner une mesure, cliquez sur la case à cocher à sa gauche. Pour supprimer une mesure du graphique, désactivez la case à cocher.

2.  Pour basculer entre un affichage des valeurs relatives (seule la valeur finale est affichée) et un affichage des valeurs absolues (axe Y affiché) dans le graphique, sélectionnez **Relative** ou **Absolute** en haut du graphique.

3.  Pour modifier la plage horaire des mesures affichées dans le graphique, sélectionnez **6 hours**, **24 hours** ou **7 days** en haut du graphique.

## <span id="configurelogging"></span></a> Configure logging

Pour chaque service de stockage accessible à votre compte de stockage (Blob, Table et File d'attente), vous pouvez enregistrer des journaux de diagnostic pour les demandes de lecture, d'écriture et/ou de suppression. De même, vous pouvez définir la stratégie de rétention de données de chaque service.

1.  Dans le [portail de gestion][], cliquez sur **Storage**, puis sur le nom du compte de stockage pour ouvrir le tableau de bord.

2.  Cliquez sur **Configure**, puis faites défiler la page **logging** (illustrée ci-dessous) à l'aide de la touche Bas du clavier.

    ![Storagelogging][]

3.  Pour chaque service (Blob, Table et File d'attente), configurez les paramètres suivants :

    -   Les types de demande à journaliser : demandes de lecture, d'écriture et de suppression.

    -   Le nombre de jours durant lesquels les données journalisées sont conservées. Entrez 0 si vous ne souhaitez pas définir de stratégie de rétention. Dans ce cas, il vous appartient de supprimer ou non les journaux.

4.  Cliquez sur **Enregistrer**.

Les journaux de diagnostic sont enregistrés dans un conteneur d'objets blob nommé $logs dans votre compte de stockage. Pour plus d'informations sur l'accès au conteneur $logs, consultez la page [À propos de la journalisation Storage Analytics][].

  [Storage Analytics et facturation]: http://msdn.microsoft.com/en-us/library/windowsazure/hh360997.aspx
  [Configure monitoring for a storage account]: #configurestoragemonitoring
  [Customize the dashboard for monitoring]: #customizestoragemonitoring
  [Customize the Monitor page]: #customizemonitorpage
  [Add metrics to the metrics table]: #addmonitoringmetrics
  [Customize the metrics chart on the Monitor page]: #customizemetricschart
  [Configure logging]: #configurelogging
  [portail de gestion]: https://manage.windowsazure.com/
  [MonitoringOptions]: ./media/storage-monitor-storage-account/Storage_MonitoringOptions.png
  [À propos des mesures Storage Analytics]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343258.aspx
  [Monitoring\_nmore]: ./media/storage-monitor-storage-account/storage_Monitoring_nmore.png
  [Monitoring\_VerboseDisplay]: ./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png
  [AddMetricsInitialDisplay]: ./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png
  [AddMetricsScrollbar]: ./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png
  [ExpandCollapse]: ./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png
  [DeleteMetric]: ./media/storage-monitor-storage-account/Storage_DeleteMetric.png
  [Storagelogging]: ./media/storage-monitor-storage-account/Storage_LoggingOptions.png
  [À propos de la journalisation Storage Analytics]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343262.aspx
