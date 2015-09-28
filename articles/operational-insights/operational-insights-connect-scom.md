<properties
   pageTitle="Connexion à Operational Insights à partir de System Center Operations Manager"
   description="Découvrez comment se connecter à Operational Insights via Operations Manager."
   services="operational-insights"
   documentationCenter=""
   authors="lauracr"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="lauracr"/>

# Connexion à Operational Insights à partir de System Center Operations Manager


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Vous pouvez connecter Operational Insights à un environnement System Center Operations Manager existant. Cela vous permet d’utiliser les agents d’Operations Manager existants pour la collecte de données. Pour plus d’informations sur l’utilisation d’Operations Manager avec Operational Insights, consultez la rubrique [Considérations relatives à Operations Manager avec Operational Insights](operational-insights-operations-manager.md).

Si vous utilisez Operations Manager pour surveiller les charges de travail suivantes, vous devez définir des comptes d’identification Operations Manager. Pour en savoir plus sur la configuration des comptes, consultez la rubrique [Considérations relatives à Operations Manager avec Operational Insights](operational-insights-operations-manager.md).

- Évaluation de SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE]La prise en charge d’Operational Insights est disponible à partir d’Operations Manager 2012 SP1 UR6 et Operations Manager 2012 R2 UR2. La prise en charge du proxy a été ajoutée dans System Center Operations Manager 2012 SP1 UR7 et System Center Operations Manager 2012 R2 UR3.

## Connexion d’Operations Manager à Operational Insights et ajout d’agents

1. Dans la console Operations Manager, cliquez sur **Administration**.

2. Développez le nœud **Operational Insights** et cliquez sur **Connexion à Operational Insights**.

3. Cliquez sur le lien **Inscription à Operational Insights** et suivez les instructions qui s’affichent.

4. Après avoir terminé l’Assistant Inscription, cliquez sur **Ajouter un ordinateur/groupe**. ![Operations Manager ajoute un ordinateur/groupe](./media/operational-insights-connect-scom/om01.png)
5. Vous pouvez rechercher des ordinateurs ou des groupes surveillés par Operations Manager dans la boîte de dialogue **Recherche d’ordinateurs**. Sélectionnez les ordinateurs ou les groupes à intégrer à Operational Insights, cliquez sur **Ajouter**, puis sur **OK**. ![Operations Manager ajoute des ordinateurs](./media/operational-insights-connect-scom/om02.png)
## Étapes suivantes

[Configuration des paramètres de pare-feu et de proxy (facultatif)](operational-insights-proxy-firewall.md)

<!---HONumber=Sept15_HO3-->