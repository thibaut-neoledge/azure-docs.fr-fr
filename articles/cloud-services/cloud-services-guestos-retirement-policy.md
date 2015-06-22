<properties 
   pageTitle="Guide de prise en charge et de stratégie de suppression pour le SE invité d'Azure | Azure" 
   description="Fournit des informations sur les éléments pris en charge par Microsoft en ce qui concerne le SE invité d'Azure utilisé par les services cloud." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="02/23/2015"
   ms.author="adegeo"/>

# Prise en charge et stratégie de suppression du SE invité d'Azure
Les informations de cette page concernent le système d'exploitation invité Azure ([SE invité](https://msdn.microsoft.com/library/azure/ff729422.aspx)) pour les rôles web et les services cloud worker (PaaS). Elles ne s'appliquent pas aux machines virtuelles (IaaS). 

Microsoft a publié sa [ politique de support pour le SE invité](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). La page que vous êtes en train de lire décrit l'implémentation de cette politique.

Cette politique développe les points suivants : 

1. Microsoft prendra en charge **au moins les deux dernières familles de systèmes d'exploitation invités**. Lorsqu'une famille est supprimée, les clients bénéficient d'un délai de 12 mois à compter de la date officielle de suppression pour passer à une famille de systèmes d'exploitation invités plus récente prise en charge.
2. Microsoft prendra en charge **au moins les deux dernières versions des familles des systèmes d'exploitation invités prises en charge**. 
3. Microsoft prendra en charge **au moins les deux dernières versions d'Azure SDK**. Lorsqu'une version du SDK est supprimée, les clients bénéficient d'un délai de 12 mois à compter de la date officielle de suppression pour passer à une version plus récente. 

Plus de deux familles ou versions peuvent parfois être prises en charge. Les informations officielles de prise en charge du SE invité seront affichées sur les [versions de SE invité d'Azure et la matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).


## Lorsqu'une version ou une famille de systèmes d'exploitation invités est supprimée 


Une nouvelle **famille** de systèmes d'exploitation invités est présentée après la publication d'une nouvelle version officielle du système d'exploitation Windows Sever. Chaque fois qu'une nouvelle famille de systèmes d'exploitation invités est présentée, Microsoft retirera la famille des systèmes d'exploitation invités la plus ancienne. 

Les nouvelles **versions** de SE invité sont présentées tous les mois environ afin d'intégrer les dernières mises à jour MSRC. En raison des mises à jour mensuelles régulières, une version de SE invité est généralement désactivée 60 jours après sa publication. Cela permet de conserver au moins deux versions de système d'exploitation invité pour chaque famille à disposition. 

### Processus de suppression d'une famille de SE invités 


Une fois la suppression annoncée, les clients bénéficient d'une période de " transition " de 12 mois avant que la famille la plus ancienne soit officiellement supprimée du service. Ce délai de transition peut être étendu à la discrétion de Microsoft. Les mises à jour seront publiées sur les [versions de système d'exploitation invité d'Azure et la matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).

Un processus de suppression progressive commencera 6 mois après le début de la période de transition. Pendant cette période :

1. Microsoft informe les clients de la suppression. 
2. La version la plus récente d'Azure SDK ne prend en charge la famille de SE invités supprimée.
3. De nouveaux déploiements et redéploiements de services cloud ne seront pas autorisés pour la famille supprimée.

Microsoft continuera à présenter la nouvelle version de système d'exploitation invité incorporant les dernières mises à jour MSRC jusqu'au dernier jour de la période de transition, appelé " expiration date " (date d'expiration). À ce stade, les services cloud en cours d'exécution sont pris en charge sous le contrat SLA Azure. Microsoft décide librement de forcer la mise à niveau, la suppression ou l'arrêt de ces services après cette date.



### Processus de suppression d'une version de SE invité 
Si les clients définissent la mise à jour automatique pour leur système d'exploitation invité, ils n'ont pas à se soucier de la gestion des versions de système d'exploitation invité. Ils utiliseront toujours la dernière version du SE invité.

Les versions de SE invité sont publiées chaque mois. En raison de la fréquence des publications régulières, chaque version est associée à un cycle de vie défini.

Après 60 jours de vie, une version obtient le statut " *désactivé* ". Cela signifie que la version est supprimée du portail de gestion Azure. De plus, elle ne peut plus être définie à partir du fichier de configuration CSCFG. Les déploiements existants continuent d'être exécutés, mais les nouveaux déploiements et les mises à jour de code et de configuration pour les déploiements existants ne seront pas autorisés. 

Ultérieurement, la version du système d'exploitation invité obtient le statut " *expiré* " et toutes les installations qui exécutent encore cette version sont obligatoirement mises à niveau et paramétrées pour mettre à jour automatiquement le système d'exploitation invité dans le futur. L'expiration se fait par lots. Ainsi, la période de temps entre la désactivation et l'expiration peut varier. 

Ces périodes peuvent être allongées à la discrétion de Microsoft pour faciliter les transitions des clients. Toutes les modifications sont communiquées sur les [versions de système d'exploitation invité d'Azure et la matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).



### Notifications pendant la suppression 

* **Suppression de famille** <br>Microsoft utilise les billets de blog et la notification du portail de gestion. Les clients qui utilisent encore une famille de systèmes d'exploitation invités supprimée sont informés par une communication directe (e-mail, messages sur le portail, appel téléphonique) aux administrateurs de service nommés. Toutes les modifications seront publiées sur cette page et les flux RSS seront mentionnés au début de cette page. 


* **Suppression de version** <br>Toutes les modifications seront publiées sur cette page et les flux RSS seront mentionnés au début de cette page, y compris les dates de publication, de désactivation et d'expiration. Les administrateurs de services recevront des e-mails s'ils ont des déploiements en cours d'exécution sur une version ou une famille de systèmes d'exploitation invités désactivée. La date d'envoi de ces e-mails peut varier. En général, ils sont envoyés au moins un mois avant la désactivation, bien que ce délai ne soit pas officiellement fixé. 


## Forum Aux Questions

**Atténuation des impacts de la migration**

Vous devez utiliser la famille la plus récente de SE invités pour concevoir vos services cloud. 

1. Commencez à planifier votre migration vers une famille plus récente au plus tôt. 
2. Configurez des déploiements de test temporaires pour tester votre service cloud en cours d'exécution sur la nouvelle famille. 
3. [Définissez votre version de SE invité](https://msdn.microsoft.com/library/azure/gg433101.aspx) sur " Automatique " (osVersion=* dans le fichier [.CSCFG](https://msdn.microsoft.com/library/azure/gg456324.aspx)) pour que la migration vers de nouvelles versions de système d'exploitation invité s'effectue automatiquement.

**Que se passe-t-il si mon application web nécessite une intégration plus avancée au système d'exploitation ?**

Si l'architecture de votre application web nécessite une intégration plus avancée au système d'exploitation sous-jacent, utilisez les fonctionnalités de plateforme prise en charge telles que les " [Tâches de démarrage](https://msdn.microsoft.com/library/windowsazure/gg456327.aspx) " ou d'autres mécanismes d'extensibilité qui peuvent exister par la suite. Sinon, vous pouvez également utiliser les [machines virtuelles Azure](http://www.windowsazure.com/home/scenarios/virtual-machines/) (IaaS - Infrastructure en tant que Service), qui vous permettent d'être responsable de la maintenance du système d'exploitation sous-jacent.


<!--HONumber=52--> 