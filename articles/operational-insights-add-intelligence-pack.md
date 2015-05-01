<properties 
    pageTitle="Packs d'analyse décisionnelle Operational Insights" 
    description="Grâce aux packs d'analyse décisionnelle, vous pouvez ajouter des fonctionnalités supplémentaires à Operational Insights" 
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
    ms.topic="article" 
    ms.date="03/20/2015" 
    ms.author="banders"/>

# Packs d'analyse décisionnelle Operational Insights

Microsoft Azure Operational Insights inclut le module de configuration de base pour l'évaluation. Toutefois, vous pouvez obtenir des fonctionnalités supplémentaires en lui ajoutant des packs d'analyse décisionnelle à partir de la page d'aperçu.

![image de l'icône de packs d'analyse décisionnelle](./media/operational-insights-add-intelligence-pack/overview-intelpacks.png)

Après avoir ajouté un pack d'analyse décisionnelle, les données sont collectées à partir des serveurs dans votre infrastructure et envoyées au service Operational Insights. Le traitement des données par le service Operational Insights peut prendre de quelques minutes à plusieurs heures. Une fois les données traitées par le service, vous pouvez les consulter dans Operational Insights.

Vous pouvez facilement supprimer un pack d'analyse décisionnelle lorsque vous n'en avez plus besoin. Lorsque vous supprimez un pack d'analyse décisionnelle, ses données ne sont pas envoyées au service Operational Insights, ce qui réduit la quantité de données utilisées par rapport à votre quota quotidien.

## Packs d'analyse décisionnelle pris en charge par Microsoft Monitoring Agent

Pour le moment, les serveurs qui sont connectés directement à Microsoft Azure Operational Insights à l'aide de Microsoft Monitoring Agent peuvent utiliser la plupart des packs d'analyse décisionnelle disponibles, notamment :

- [Mises à jour du système](operational-insights-updates.md)

- [Gestion du journal](operational-insights-log-collection.md)

- [Logiciel anti-programme malveillant](operational-insights-antimalware.md)

- [Suivi des modifications](operational-insights-change-tracking.md)

- [Évaluation de SQL et d'Active Directory](operational-insights-assessment.md)

Les packs d'analyse décisionnelle suivants ne sont toutefois  *pas* pris en charge par Microsoft Monitoring Agent

- [Gestion de la capacité](operational-insights-capacity.md)

- [Évaluation de la configuration](operational-insights-configuration-assessment.md)

La collecte de journaux IIS est prise en charge sur les ordinateurs équipés de :

- Windows Server 2012

- Windows Server 2012 R2

### Ajouter un pack d'analyse décisionnelle


1. Sur la page d'aperçu de Operational Insights, cliquez sur la vignette **Packs d'analyse décisionnelle**.


2. Vous pouvez en savoir plus sur chaque pack d'analyse décisionnelle disponible en vous rendant sur la page Galerie des packs d'analyse décisionnelle d'Operational Insights. Cliquez sur le nom du pack d'analyse décisionnelle que vous souhaitez ajouter à Operational Insights.


3. Des informations détaillées sur le pack d'analyse décisionnelle que vous avez choisi s'affichent sur la page du pack en question. Cliquez sur **Ajouter**.


4. Sur la page de confirmation, cliquez sur **Accepter** pour accepter les conditions d'utilisation et la déclaration de confidentialité.


5. Une nouvelle vignette représentant le pack d'analyse décisionnelle que vous avez ajouté apparaît sur la page d'aperçu dans Operational Insights. Vous pouvez commencer à l'utiliser une fois que le service Operational Insights a traité vos données.




### Supprimer un pack d'analyse décisionnelle



1. Sur la page d'aperçu de Operational Insights, cliquez sur la vignette **Packs d'analyse décisionnelle**.


2. Sur la page Galerie des packs d'analyse décisionnelle Operational Insights, cliquez sur **Supprimer** sous le pack d'analyse décisionnelle que vous souhaitez supprimer.


3. Sur la page de confirmation, cliquez sur **Oui** pour supprimer le pack d'analyse décisionnelle.




<!--HONumber=52-->