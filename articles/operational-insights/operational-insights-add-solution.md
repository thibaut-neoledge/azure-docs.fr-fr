<properties
    pageTitle="Solutions Operational Insights"
    description="Grâce aux solutions, vous pouvez ajouter des fonctionnalités supplémentaires à Operational Insights."
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="banders"/>

# Solutions Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights inclut le module de configuration de base pour l'évaluation. Toutefois, vous pouvez obtenir des fonctionnalités supplémentaires en lui ajoutant des solutions à partir de la page d'aperçu.

![image de l'icône de solutions](./media/operational-insights-add-solution/sol-gallery.png)

Après avoir ajouté une solution, les données sont collectées à partir des serveurs dans votre infrastructure et envoyées au service Operational Insights. Le traitement des données par le service Operational Insights peut prendre de quelques minutes à plusieurs heures. Une fois les données traitées par le service, vous pouvez les consulter dans Operational Insights.

Vous pouvez facilement supprimer une solution quand vous n'en avez plus besoin. Quand vous supprimez une solution, ses données ne sont pas envoyées au service Operational Insights, ce qui réduit la quantité de données utilisées par rapport à votre quota quotidien.

## Solutions prises en charge par Microsoft Monitoring Agent

Pour le moment, les serveurs qui sont connectés directement à Microsoft Azure Operational Insights à l'aide de Microsoft Monitoring Agent peuvent utiliser la plupart des solutions disponibles, notamment :

- [Mises à jour du système](operational-insights-updates.md)

- [Gestion du journal](operational-insights-log-collection.md)

- [Logiciel anti-programme malveillant](operational-insights-antimalware.md)

- [Suivi des modifications](operational-insights-change-tracking.md)

- [Évaluation de SQL et d'Active Directory](operational-insights-assessment.md)

Les solutions suivantes ne sont toutefois *pas* prises en charge par Microsoft Monitoring Agent et requièrent System Center Operations Manager (SCOM).

- [Gestion de la capacité](operational-insights-capacity.md)

- [Gestion des alertes](operational-insights-alerts.md)

- [Évaluation de la configuration](operational-insights-solutions.md#configuration-assessment)

Reportez-vous à [Considérations relatives à Operations Manager avec Operational Insights](operational-insights-operations-manager.md) pour obtenir des conseils sur l'utilisation de ces solutions avec Operations Manager.

La collecte de journaux IIS est prise en charge sur les ordinateurs équipés de :

- Windows Server 2012

- Windows Server 2012 R2

### Pour ajouter une solution


1. Dans la page d'aperçu d'Operational Insights, cliquez sur la vignette **Galerie des solutions**.


2. Vous pouvez en savoir plus sur chaque solution disponible en consultant la page Galerie des solutions d'Operational Insights. Cliquez sur le nom de la solution que vous souhaitez ajouter à Operational Insights.


3. Des informations détaillées sur la solution que vous avez choisie s'affichent dans la page de la solution en question. Cliquez sur **Ajouter**.


4. Dans la page de confirmation, cliquez sur **Accepter** pour accepter les conditions d'utilisation et la déclaration de confidentialité.


5. Une nouvelle vignette représentant la solution que vous avez ajoutée apparaît dans la page d'aperçu dans Operational Insights. Vous pouvez commencer à l'utiliser une fois que le service Operational Insights a traité vos données.




### Pour supprimer une solution



1. Dans la page d'aperçu d'Operational Insights, cliquez sur la vignette **Galerie des solutions**.


2. Dans la page Galerie des solutions Operational Insights, cliquez sur **Supprimer** sous la solution que vous souhaitez supprimer.


3. Dans la page de confirmation, cliquez sur **Oui** pour supprimer la solution.

<!---HONumber=July15_HO4-->