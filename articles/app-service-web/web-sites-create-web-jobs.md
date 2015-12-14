<properties 
	pageTitle="Exécuter des tâches en arrière-plan avec les tâches web" 
	description="Découvrez comment exécuter des tâches en arrière-plan dans les applications web Azure." 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Exécuter des tâches en arrière-plan avec les tâches web

## Vue d'ensemble

Vous pouvez exécuter des programmes ou des scripts dans WebJobs au niveau de votre application Web [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) de trois façons : à la demande, de façon continue, ou de façon planifiée. L’utilisation des tâches web n’entraîne aucun coût supplémentaire.

Cet article explique comment déployer WebJobs à l’aide du [portail Azure](https://portal.azure.com). Pour plus d’informations sur le déploiement à l’aide de Visual Studio ou d’un processus de diffusion continue, consultez [Déploiement de tâches Web Azure dans Web Apps](websites-dotnet-deploy-webjobs.md).

Le Kit de développement logiciel (SDK) Azure WebJobs simplifie de nombreuses tâches de programmation. Pour plus d’informations, consultez [Présentation du Kit de développement logiciel (SDK) WebJobs Azure](websites-dotnet-webjobs-sdk.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="acceptablefiles"></a>Types de fichier acceptables pour les scripts ou les programmes

Les types de fichier suivants sont acceptés :

* .cmd, .bat, .exe (en utilisant une commande Windows)
* .ps1 (en utilisant Powershell)
* .sh (en utilisant un interpréteur de commandes)
* .php (en utilisant PHP)
* .py (en utilisant Python)
* .js (en utilisant Node)
* .jar (en utilisant Java)

## <a name="CreateOnDemand"></a>Création d’une tâche Web à la demande sur le portail

1. Dans le panneau **Application web** du [portail Azure](http://portal.azure.com), cliquez sur **Tous les paramètres** > Tâches web pour afficher le panneau **Tâches web**.
	
	![Panneau Tâches Web](./media/web-sites-create-web-jobs/wjblade.png)
	
5. Cliquez sur **Ajouter**. La boîte de dialogue **Ajouter une tâche web Azure** s’affiche.
	
	![Panneau Ajouter une tâche Web](./media/web-sites-create-web-jobs/addwjblade.png)
	
2. Sous **Nom**, indiquez le nom de la tâche Web. Le nom doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (« - » et « \_ »).
	
4. Dans la zone **How to Run**, sélectionnez **Run on Demand**.
	
3. Dans la zone **Téléchargement de fichiers**, cliquez sur l’icône du dossier et recherchez le fichier .zip qui contient votre script. Ce fichier .zip doit contenir votre exécutable (.exe .cmd .bat .sh .php .py .js) ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script.
	
5. Cochez la case **Créer** pour télécharger le script dans votre application Web.
	
	Le nom que vous avez indiqué pour la tâche Web apparaît dans la liste, dans le panneau **Tâches web**.
	
6. Pour exécuter la tâche Web, cliquez sur son nom dans la liste avec le bouton droit, puis cliquez sur **Exécuter**.
	
	![Exécuter une tâche Web](./media/web-sites-create-web-jobs/runondemand.png)
	
## <a name="CreateContinuous"></a>Création d’une tâche Web exécutée en continu

1. Pour créer une tâche Web exécutée en continu, procédez comme pour la création d’une tâche Web à exécution unique, mais dans la zone **Mode d’exécution**, sélectionnez **Exécuter en continu**.

2. Pour démarrer ou arrêter une tâche Web exécutée en continu, cliquez avec le bouton droit sur la tâche Web dans la liste, puis cliquez sur **Démarrer** ou **Arrêter**.
	
> [AZURE.NOTE]Si votre application web est exécutée sur plusieurs instances, une tâche web exécutée en continu le sera sur toutes vos instances. Les tâches web à la demande et planifiées sont exécutées sur une seule instance sélectionnée pour l’équilibrage de charge par Microsoft Azure.
	
> Pour que les tâches Web continues s'exécutent de façon fiable sur toutes les instances, activez le paramètre de configuration Toujours actif* de l'application Web. Sinon, elles risquent de s’arrêter si le site de l'hôte SCM reste inactif pendant trop longtemps.

## <a name="CreateScheduledCRON"></a>Créer une tâche web planifiée à l’aide d’une expression CRON

Cette technique est disponible dans Web Apps s’exécutant en mode Standard ou Premium et suppose que le paramètre **Toujours actif** est activé sur l’application.

Pour transformer une tâche web à la demande en une tâche web planifiée, il vous suffit d’inclure un fichier `settings.job` à la racine du fichier compressé de la tâche web. Ce fichier JSON doit inclure une propriété `schedule` avec une [expression CRON](https://en.wikipedia.org/wiki/Cron), comme ci-dessous.

L’expression CRON est composée de 6 champs : `{second} {minute} {hour} {day} {month} {day of the week}`.

Par exemple, pour déclencher la tâche web toutes les 15 minutes, le `settings.job` se présente comme suit :

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

Autres exemples de planification CRON :

- Toutes les heures (autrement dit, chaque fois que le nombre de minutes est 0) : `0 0 * * * *` 
- Toutes les heures entre 9h et 17h : `0 0 9-17 * * *` 
- À 9h30 tous les jours : `0 30 9 * * *`
- À 9h30 tous les jours de la semaine : `0 30 9 * * 1-5`

**Remarque** : quand vous déployez une tâche web à partir de Visual Studio, veillez à marquer les propriétés du fichier `settings.job` comme « Copier si plus récent ».


## <a name="CreateScheduled"></a>Créer une tâche web planifiée à l’aide d’Azure Scheduler

L’autre technique ci-après fait appel à Azure Scheduler. Dans ce cas, votre tâche web n’a aucune connaissance directe de la planification. Au lieu de cela, Azure Scheduler est configuré pour déclencher votre tâche web selon une planification.

Le portail Azure ne permet pas encore de créer une tâche Web planifiée. Pour le moment, vous pouvez utiliser le [portail Classic](http://manage.windowsazure.com).

1. Sur le [portail Classic](http://manage.windowsazure.com), accédez à la page des tâches Web, puis cliquez sur **Ajouter**.

1. Dans la zone **Mode d’exécution**, sélectionnez **Exécuter selon une planification**.
	
	![Nouvelle tâche planifiée][NewScheduledJob]
	
2. Sélectionnez la **région Scheduler** pour votre tâche, puis cliquez sur la flèche située dans le coin inférieur droit de la boîte de dialogue pour passer à l'écran suivant.

3. Dans la boîte de dialogue **Créer une tâche**, sélectionnez un type de **périodicité** : **travail unique** ou **travail périodique**.
	
	![Périodicité de la planificiation][SchdRecurrence]
	
4. Choisissez également une date/heure de **début** : **maintenant** ou **à un moment donné**.
	
	![Heure de début de la planification][SchdStart]
	
5. Si vous voulez démarrer votre tâche à un moment précis, sélectionnez la date/heure de début sous **Starting On**.
	
	![Début de la planification à une heure spécifique][SchdStartOn]
	
6. Si vous sélectionnez une tâche récurrente, l'option **Recur Every** vous permet d'indiquer la fréquence d'occurrence, tandis que l'option **Ending On** vous permet d'indiquer une date/heure de fin.
	
	![Périodicité de la planification][SchdRecurEvery]
	
7. Si vous sélectionnez **Semaines**, vous pouvez sélectionner la zone **On a Particular Schedule** et indiquer les jours de la semaine où vous voulez exécuter la tâche.
	
	![Planifier les jours de la semaine][SchdWeeksOnParticular]
	
8. Si vous sélectionnez **Mois** et la zone **On a Particular Schedule**, vous pouvez configurer la tâche pour qu'elle démarre certains **jours** du mois.
	
	![Planifier à des dates spécifiques dans le mois][SchdMonthsOnPartDays]
	
9. Si vous sélectionnez **Jours de la semaine**, vous pouvez choisir les jours de la semaine durant lesquels exécuter la tâche.
	
	![Planifier des jours de semaine spécifiques dans le mois][SchdMonthsOnPartWeekDays]
	
10. Enfin, vous pouvez également utiliser l'option **Occurrences** pour choisir la semaine du mois (première, deuxième, troisième, etc.) durant laquelle exécuter la tâche, les jours de votre choix.
	
	![Planifier des jours de semaine spécifiques certaines semaines du mois][SchdMonthsOnPartWeekDaysOccurences]
	
11. Lorsque vous avez créé une ou plusieurs tâches, leurs noms sont affichés sous l'onglet WebJobs avec leur statut, leur type de planification et d'autres informations. L’historique répertorie les 30 dernières tâches web.
	
	![Liste des tâches][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Tâches planifiées et Azure Scheduler

Les tâches planifiées peuvent être configurées avec plus de précision sur les pages Azure Scheduler du [portail Classic](http://manage.windowsazure.com).

1.	Sur la page WebJobs, cliquez sur le lien **schedule** de la tâche pour accéder à la page du portail d'Azure Scheduler. 
	
	![Lien vers Azure Scheduler][LinkToScheduler]
	
2. Sur la page Scheduler, cliquez sur la tâche.
	
	![Tâches sur la page du portail Scheduler][SchedulerPortal]
	
3. La page **Job Action** s'ouvre : vous pouvez y configurer les tâches plus en détail.
	
	![Action de tâche PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Affichage de l’historique des tâches

1. Pour afficher l’historique d’exécution d’une tâche, notamment celui des tâches créées avec le Kit de développement logiciel (SDK) WebJobs, cliquez sur le lien correspondant sous la colonne **Journaux** du panneau Tâches web. Vous pouvez utiliser l'icône de Presse-papiers pour copier l'URL de la page du fichier journal dans le Presse-papiers.
	
	![Lien vers les journaux](./media/web-sites-create-web-jobs/wjbladelogslink.png)
		
2. En cliquant sur le lien, vous ouvrez la page de détail pour la tâche web. Cette page permet de savoir le nom de la commande exécutée, la date/heure de sa dernière exécution et si elle a réussi ou échoué. Sous **Recent job runs**, cliquez une fois pour afficher les détails supplémentaires.
	
	![WebJobDetails][WebJobDetails]
	
3. La page **WebJob Run Details** apparaît. Cliquez sur **Toggle Output** pour afficher le texte du contenu du journal. Le journal de sortie est au format texte.
	
	![Détails d'exécution de la tâche WebJob][WebJobRunDetails]
	
4. Pour afficher le texte de sortie dans une nouvelle fenêtre de navigateur, cliquez sur le lien **télécharger**. Pour télécharger le texte, cliquez avec le bouton droit sur le lien et utilisez les options de votre navigateur pour enregistrer le contenu du fichier.
	
	![Télécharger la sortie du journal][DownloadLogOutput]
	
5. Le lien **Tâches web** situé en haut de la page permet d’obtenir une liste des tâches Web sur le tableau de bord d’historique.
	
	![Lien Tâches Web][WebJobsLinkToDashboardList]
	
	![Liste des tâches Web dans le tableau de bord d’historique][WebJobsListInJobsDashboard]
	
	En cliquant sur l'un de ces liens, vous pouvez accéder à la page Détails de WebJob pour la tâche sélectionnée.


## <a name="WHPNotes"></a>Remarques
	
- Les applications web en mode Gratuit peuvent expirer si, pendant 20 minutes, aucune requête à destination du site (de déploiement) SCM et du portail de l'application web n'est ouverte dans Azure. Cette situation ne sera pas annulée par les requêtes à destination du site actif.
- Le code d'une tâche en continu doit être écrit pour s'exécuter dans une boucle infinie.
- Les tâches en continu sont uniquement exécutées comme tel lorsque l’application web est opérationnelle.
- Les modes Basique et Standard proposent la fonctionnalité Toujours actif qui, lorsqu’elle est activée, empêche les applications web de devenir inactives.
- Vous pouvez uniquement déboguer les tâches Web qui s’exécutent en continu. Le débogage des tâches Web planifiées et à la demande n’est pas pris en charge.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## <a name="NextSteps"></a>Étapes suivantes
 
Pour plus d’informations, consultez [Ressources Azure WebJobs][WebJobsRecommendedResources].

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l'ancien et le nouveau portail, consultez : [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

[PSonWebJobs]: http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]: http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
 

<!---HONumber=AcomDC_1203_2015-->