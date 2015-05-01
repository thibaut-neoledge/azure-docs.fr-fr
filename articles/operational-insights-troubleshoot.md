<properties 
   pageTitle="Résolution des problèmes à l'aide d'Operational Insights"
   description="En savoir plus sur la résolution des problèmes à l'aide d'Operational Insights"
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

#Résolution des problèmes à l'aide d'Operational Insights
Vous pouvez utiliser les informations des sections suivantes pour vous aider à résoudre les problèmes. Si le problème que vous rencontrez n'est pas présenté dans cet article, vous pouvez consulter le [blog de l'équipe Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Diagnostic des problèmes de connexion pour Operational Insights

Étant donné que Microsoft Azure Operational Insights repose sur les données déplacées vers et depuis le cloud, les problèmes de connexion peuvent s'avérer handicapants. Utilisez les informations suivantes pour comprendre et résoudre vos problèmes de connexion.



<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Message d'erreur</b></td>
		<td><b>Causes possibles</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>La connectivité Internet a été vérifiée, mais la connexion au service Operational Insights n'a pas pu être établie. Réessayez plus tard.|</td>
		<td>Le service Operational Insights est en cours de maintenance. Attendez que la maintenance Operational Insights soit terminée.<p>Votre réseau a bloqué Operational Insights. Contactez votre administrateur réseau et demandez l'accès à Operational Insights ou utilisez un autre serveur comme passerelle.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Aucune connexion Internet n'a pu être établie. Vérifiez vos paramètres de proxy.</td>
		<td>Ce serveur n'est pas connecté à Internet. Vérifiez l'état de la connectivité Internet et connectez le serveur à Internet.<p>La configuration du proxy n'est pas correcte. Consultez <A HREF="operational-insights-proxy-filewall.md">Configuration des paramètres de pare-feu et de proxy</A> pour obtenir des informations relatives à la définition et la modification de vos paramètres de proxy.<p>Le serveur proxy requiert une authentification. Consultez <A HREF="operational-insights-proxy-filewall.md">Configuration des paramètres de pare-feu et de proxy</A> pour obtenir des informations relatives à la configuration d'Operations Manager pour utiliser un serveur proxy.</td>
    </tr>
    </tbody>
    </table>

## Résolution des problèmes de détection de SQL Server

Si vous exécutez Microsoft SQL Server 2008 R2, et que, malgré le déploiement de l'agent Operations Manager, vous ne voyez pas d'alertes pour ce serveur, vous avez peut-être un problème de détection.

Pour vérifier s'il s'agit de la source de votre problème, recherchez les deux problèmes suivants :

- Dans le journal des événements Operations Manager, vous voyez l'ID d'événement 4001. Cet événement indique qu'il existe une classe non valide.

- Dans le Gestionnaire de configuration SQL Server, lorsque vous affichez les services SQL Server, vous voyez le message d'erreur " Échec de l'appel de procédure distante. [0x0800706be] "

Si vous êtes confronté à ces deux problèmes, vous devez installer SQL Server 2008 R2 Service Pack 2. Pour télécharger ce service pack, consultez [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) dans le centre de téléchargement Microsoft.

Après avoir installé le service pack, vous devez voir des données Operational Insights pour le serveur dans les 24 heures.



<!--HONumber=52-->