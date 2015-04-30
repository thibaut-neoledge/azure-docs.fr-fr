<properties 
   pageTitle="Connexion à Operational Insights à partir de System Center Operations Manager" 
   description="Découvrez comment se connecter à Operational Insights via Operations Manager." 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Connexion à Operational Insights à partir de System Center Operations Manager 

Vous pouvez connecter Operational Insights à un environnement System Center Operations Manager existant. Cela vous permet d'utiliser les agents d'Operations Manager existants en tant qu'agents Operational Insights.

 >[AZURE.NOTE] La prise en charge d'Operational Insights est disponible à partir d'Operations Manager 2012 SP1 UR6 et Operations Manager 2012 R2 UR2. La prise en charge de proxy a été ajoutée dans SCOM 2012 SP1 UR7 et SCOM 2012 R2 UR3.

## Connexion de SCOM à Operational Insights et ajout d'agents

1. Dans la console Operations Manager, cliquez sur **Administration**.

2. Développez le nœud **Operational Insights** et cliquez sur **Connexion à Operational Insights**.

3. Cliquez sur le lien **Inscription à Operational Insights** et suivez les instructions qui s'affichent. 

4. Après avoir terminé l'Assistant Inscription, cliquez sur **Ajouter un ordinateur/groupe**.

5. Vous pouvez rechercher des ordinateurs ou des groupes surveillés par Operations Manager dans la boîte de dialogue **Recherche d'ordinateurs**. Sélectionnez les ordinateurs ou les groupes à intégrer à Operational Insights, cliquez sur **Ajouter**, puis sur **OK**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

[Configuration des paramètres de pare-feu et de proxy (facultatif)](https://msdn.microsoft.com/library/azure/dn884643.aspx)

<!--HONumber=52-->