<properties 
   pageTitle="Gestion des serveurs et des données d'utilisation"
   description="En savoir plus sur la quantité de données envoyée au service Operational Insights depuis vos serveurs"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Gestion des serveurs et des données d'utilisation

Operational Insights collecte des données et les envoie régulièrement au service Operational Insights.  Vous pouvez utiliser le tableau de bord **Utilisation** pour afficher la quantité de données envoyée au service Operational Insights. Le tableau de bord **Utilisation** vous indique également la quantité de données envoyée quotidiennement par les packs d'analyse décisionnelle et la fréquence à laquelle vos groupes d'administration envoient des données.

>[AZURE.NOTE] Si vous disposez un compte gratuit, vos envois au service Operational Insights sont limités à 500 Mo de données par jour. Si vous atteignez la limite quotidienne, l'analyse de données s'arrête et reprend le jour suivant.

Vous pouvez consulter votre utilisation à l'aide de la vignette **Serveurs et utilisation** sur le tableau de bord **Présentation** dans Operational Insights.

![image de la vignette Serveurs et utilisation](./media/operational-insights-usage/overview-servers-usage.png)

Si vous avez dépassé votre limite d'utilisation quotidienne ou si vous en êtes proche, vous pouvez éventuellement supprimer un pack d'analyse décisionnelle pour réduire la quantité de données que vous envoyez au service Operational Insights. Pour plus d'informations sur la suppression des packs d'analyse décisionnelle, consultez [Utilisation de la galerie pour ajouter ou supprimer des packs d'analyse décisionnelle](../operational-insights-add-intelligence-packs.md).

Si un groupe d'administration Operations Manager rencontre des problèmes d'envoi de données au service Operational Insights, vous pouvez résoudre le problème ou supprimer le groupe d'Operational Insights, si nécessaire.

![image du tableau de bord Utilisation](./media/operational-insights-usage/usage-dash.png)

Le tableau de bord **Utilisation** affiche les informations suivantes :

- Utilisation moyenne par jour

- Utilisation des données pour chaque pack d'analyse décisionnelle pour le jour même

- Fréquence à laquelle les serveurs de chaque groupe d'administration envoient des données au service Operational Insights

## Utilisation des données d'utilisation

1. Sur la page **Présentation**, cliquez sur la vignette **Serveurs et utilisation**.

2. Dans le tableau de bord **Utilisation**, affichez les catégories d'utilisation qui montrent les domaines vous intéressant.

3. Si des données relatives à un pack d'analyse décisionnelle utilisent inutilement une grande partie du quota alloué, vous pouvez envisager la suppression de ce pack d'analyse décisionnelle.

## Dépannage ou suppression des groupes d'administration

1. Sur la page **Présentation**, cliquez sur la vignette **Serveurs et utilisation**.

2. Dans le tableau de bord **Utilisation**, consultez les informations sur les groupes d'administration qui n'envoient pas de données.

3. Si un groupe d'administration n'envoie pas de données, vous pouvez cliquer sur **Dépannage** pour obtenir des informations de dépannage détaillées. Si vous ne souhaitez plus conserver un groupe d'administration ni les agents liés à ce dernier, cliquez sur **Supprimer**.



<!--HONumber=52--> 