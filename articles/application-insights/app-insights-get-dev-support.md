<properties 
	pageTitle="Comment obtenir une assistance technique auprès de l’équipe de développement Application Insights | Microsoft Azure" 
	description="Si vous êtes dans une situation qui nécessite l’intervention de l’équipe de développement Application Insights, vous trouverez ici la procédure à suivre pour envoyer les détails qui vous permettront d’obtenir une assistance technique." 
	services="application-insights" 
    documentationCenter=""
	authors="alexbulankou" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="albulank"/>
	
# Comment obtenir une assistance technique auprès de l’équipe de développement Application Insights
	
Lorsque vous rencontrez un problème technique avec [Visual Studio Application Insights](app-insights-overview.md), voici les différentes options dont vous disposez pour obtenir de l’aide :

## 1\. Vérifier les documents

* Manque-t-il des données ? Vérification : [échantillonnage](app-insights-sampling.md), [quotas et limitation](app-insights-pricing.md).
* Résolution des problèmes : [ASP.NET](app-insights-troubleshoot-faq.md) | [Java](app-insights-java-troubleshoot.md)

## 2\. Rechercher dans les forums

* [Forum MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

## 3\. Plan de support Azure ?

Il peut arriver dans certains cas que vous ayez besoin de l’aide des développeurs pour étudier votre cas particulier.

Si vous avez souscrit un [plan de support avec Microsoft Azure](https://azure.microsoft.com/support/plans/), vous pouvez [ouvrir un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## 4\. Contacter l’équipe Application Insights

Si vous n’avez pas de plan de support, notre équipe de développement est là pour offrir aux clients d’Application Insights la meilleure assistance avant la phase de disponibilité générale. Nous vous présentons **une nouvelle option de support**, dans laquelle vous pouvez décrire votre cas en nous envoyant un formulaire de commentaires sur le portail Azure. Un développeur de l’équipe Application Insights vous contactera alors pour vous aider à résoudre votre problème.


1. Dans le [portail Application Insights](https://portal.azure.com), cliquez sur l’émoticône situé dans l’angle supérieur droit :  

    ![Bouton Commentaires](./media/app-insights-get-dev-support/01.png)

2. Dans la zone de commentaires, veillez à spécifier **AppInsights** sur la première ligne avant d’ajouter les informations suivantes :

    ```

    AppInsights   
    ikey: <instrumentation key>   
    sdk: <SDK that you are using, including name and version>  
    issue: <please describe the problem you are having>

    ```   

    ![Boîte de dialogue Commentaires](./media/app-insights-get-dev-support/02.png)

3. Cochez la case « Yes, it is ok to email you » (Oui, vous pouvez me contacter par e-mail).

    ![Section d’envoi](./media/app-insights-get-dev-support/03.png)

Un ingénieur de l’équipe Application Insights vous contactera rapidement. Nous faisons de notre mieux pour vous offrir ce service ; autrement dit, aucun contrat SLA formel ne peut être établi à ce stade.

<!---HONumber=AcomDC_0615_2016-->