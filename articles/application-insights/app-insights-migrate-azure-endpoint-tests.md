---
title: Migrer du point de terminaison Azure aux Tests de disponibilité Application Insights
description: Migration des tests de surveillance de point de terminaison Azure classiques vers les Tests de disponibilité Application Insights le 31 octobre 2016.
services: application-insights
documentationcenter: ''
author: soubhagyadash
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: awills

---
# <a name="moving-from-azure-endpoint-monitoring-to-application-insights-availability-tests"></a>Passage de la Surveillance de point de terminaison Azure aux Tests de disponibilité Application Insights
Utilisez-vous la [surveillance des points de terminaison](https://blogs.msdn.microsoft.com/mast/2013/03/03/windows-azure-portal-update-configure-web-endpoint-status-monitoring-preview/) pour vos applications Azure ? Le *31 octobre 2016*, cette fonctionnalité sera remplacée par les [Tests de disponibilité](app-insights-monitor-web-app-availability.md), nouveaux et plus puissants. Nous en avons déjà créé certains même s’ils sont désactivés jusqu’au 31 octobre 2016. 

Vous pouvez modifier les nouveaux tests et exécuter la transition vous-même si vous le souhaitez. Vous les trouverez sur le [portail Azure](https://portal.azure.com) dans le groupe de ressources Default-ApplicationInsights-CentralUS.

## <a name="what-are-availability-tests?"></a>Que sont les Tests de disponibilité ?
Le test de la disponibilité est une fonctionnalité Azure qui vérifie continuellement que tout site web ou service est disponible et en cours d’exécution en envoyant des requêtes HTTP (tests ping uniques ou tests web Visual Studio) à partir de 16 emplacements dans le monde entier. 

Dans le [portail Azure Classic](https://manage.windowsazure.com), ces tests étaient appelés Surveillance de point de terminaison. Leur portée était plus limitée. Les nouveaux tests de disponibilité constituent une amélioration importante :

* Jusqu’à 10 tests web Visual Studio ou tests ping par ressource Application Insights. 
* Jusqu’à 16 emplacements dans le monde entier pour envoyer des requêtes de test à votre application web. Meilleur contrôle des critères de réussite des tests. 
* Test de n’importe quel site ou service web, pas seulement les applications web Azure.
* Nouvelles tentatives de test : réduire les alertes de faux positifs en raison de problèmes réseau temporaires. 
* Webhooks peut recevoir des notifications HTTP POST pour les alertes.

![](./media/app-insights-migrate-azure-endpoint-tests/16-1test.png)

En savoir plus sur les [tests de disponibilité ici](app-insights-monitor-web-app-availability.md).

Les tests de disponibilité font partie de [Visual Studio Application Insights](app-insights-overview.md), un service d’analyse extensible pour les applications web.

## <a name="so-what's-happening-to-my-endpoint-tests?"></a>Qu’en est-il de mes tests de point de terminaison ?
* Nous avons copié vos tests de surveillance de point de terminaison dans les nouveaux Tests de disponibilité Application Insights.
* Les nouveaux Tests de disponibilité sont actuellement désactivés et les anciens tests de point de terminaison sont toujours utilisés.
* Les règles d’alerte n’ont *pas* été migrées. Les nouveaux tests ont été initialement configurés avec une règle par défaut :
  * Déclenchement lorsque plus de 1 emplacement signale des échecs durant les 5 dernières minutes.
  * Envoi d’un courrier électronique aux administrateurs de l’abonnement.

Dans le [portail Azure](https://portal.azure.com), vous trouverez les tests migrés dans le groupe de ressources « Default-ApplicationInsights-CentralUS ». Les noms des tests portent le préfixe « Migrated- ». 

## <a name="what-do-i-need-to-do?"></a>Que dois-je faire ?
* Si nous n’avons pas migré vos tests, les nouveaux tests de disponibilité sont [faciles à configurer](app-insights-monitor-web-app-availability.md).

### <a name="option-a:-do-nothing.-leave-it-to-us."></a>Option A : Ne faites rien. Laissez-nous nous en charger.
**Le 31 octobre 2016,** nous :

* Désactiverons les anciens tests de point de terminaison.
* Activerons les tests de disponibilité migrés.

### <a name="option-b:-you-manage-and/or-enable-the-new-tests."></a>Option B : Vous gérez et/ou activez les nouveaux tests.
* Examinez et modifiez les nouveaux tests de disponibilité dans le nouveau [portail Azure](https://portal.azure.com). 
  * Examinez les critères du déclencheur
  * Examinez les destinataires du message
* Activez les nouveaux tests
* Nous désactiverons les tests de surveillance de point de terminaison hérités le 31 octobre 2016 

### <a name="option-c:-opt-out"></a>Option C : Désabonnement
Si vous ne souhaitez pas utiliser les Tests de disponibilité, vous pouvez les supprimer dans le [portail Azure](https://portal.azure.com). Il existe également un lien de désabonnement au bas des messages électroniques de notification.

Nous supprimerons tout de même les anciens tests de point de terminaison le 31 octobre 2016. 

## <a name="how-do-i-edit-the-new-tests?"></a>Comment modifier les nouveaux tests ?
Connectez-vous au [portail Azure](https://portal.azure.com) et recherchez les tests web « Migrated- » : 

![Sélectionnez Groupes de ressources, Default-ApplicationInsights-CentralUS et ouvrez les test « migrés ».](./media/app-insights-migrate-azure-endpoint-tests/20.png)

Modifiez et/ou activez le test :

![](./media/app-insights-migrate-azure-endpoint-tests/21.png)

## <a name="why-is-this-happening?"></a>Que se passe-t-il ?
Meilleur service. L’ancien service de point de terminaison était nettement plus étroit. Vous ne pouviez fournir que deux URL pour les tests ping simples à partir de 3 géolocalisations sur une machine virtuelle Azure ou une application web. Les nouveaux tests peuvent exécuter des tests web à plusieurs étapes à partir de 16 emplacements maximum et vous pouvez spécifier jusqu’à 10 tests pour une application. Vous pouvez tester n’importe quelle URL, il n’est pas nécessaire que ce soit un site Azure.

Les nouveaux tests sont configurés séparément de l’application web ou de la machine virtuelle que vous testez. 

Nous migrons les tests pour nous assurer que vous puissiez continuer à les contrôler lors de l’utilisation du nouveau portail. 

## <a name="what-is-application-insights?"></a>Présentation d’Application Insights
Les nouveaux Tests de disponibilité font partie de [Visual Studio Application Insights](app-insights-overview.md). Voici une [vidéo de 2 minutes](http://go.microsoft.com/fwlink/?LinkID=733921).

## <a name="am-i-paying-for-the-new-tests?"></a>Les nouveaux tests me sont-ils facturés ?
Les tests migrés sont définis dans une ressource Application Insights avec le plan de tarification gratuit par défaut. Cela permet la collecte de jusqu’à 5 millions de points de données. Cette limite couvre facilement les volumes de données que vos tests utilisent actuellement. 

Bien sûr, si vous aimez Application Insights et créez d’autres tests de disponibilité ou adoptez plusieurs de ses fonctionnalités de diagnostic et de surveillance des performances, alors vous génèrerez davantage de points de données.  Toutefois, le résultat se limitera au fait que vous atteigniez le quota du plan de tarification gratuit. Vous ne recevrez pas de facture, sauf si vous optez pour les plans Standard ou Premium. 

[Plus d’informations sur la tarification et les quotas d’Application Insights](app-insights-pricing.md). 

## <a name="what-is-and-isn't-migrated?"></a>Qu’est-ce qui est migré et qu’est-ce qui ne l’est pas ?
Conservés à partir de vos anciens tests de point de terminaison :

* URL de point de terminaison à tester.
* Emplacements de zones géographiques à partir desquelles les requêtes sont envoyées.
* La fréquence de test reste définie sur 5 minutes.
* Le délai d’expiration de test reste défini sur 30 secondes. 

Non migrés :

* Règle d’alerte du déclencheur. La règle que nous avons définie se déclenche lorsque 1 emplacement signale des échecs dans un délai de 5 minutes.
* Destinataires de l’alerte. Les e-mails de notification seront envoyés aux propriétaires et copropriétaires de l’abonnement . 

## <a name="how-do-i-find-the-new-tests?"></a>Comment trouver les nouveaux tests ?
Vous pouvez modifier l’un des nouveaux tests maintenant si vous le souhaitez. Connectez-vous au [portail Azure](https://portal.azure.com), ouvrez **Groupes de ressources** et sélectionnez **Default-ApplicationInsights-CentralUS**. Dans ce groupe, vous trouverez les nouveaux tests web. [En savoir plus sur les nouveaux tests de disponibilité](app-insights-monitor-web-app-availability.md).

Veuillez noter que les nouvelles alertes par courrier électronique seront envoyées à partir de cette adresse : App Insights Alerts (ai-noreply@microsoft.com)

## <a name="what-happens-if-i-do-nothing?"></a>Que se passe-t-il si je ne fais rien ?
L’Option A s’applique. Nous activerons les tests migrés et configurerons les règles d’alerte par défaut comme indiqué ci-dessus. Vous devrez ajouter toutes les règles d’alerte personnalisées, les destinataires, comme indiqué ci-dessus. Nous désactiverons les tests de surveillance de point de terminaison hérités. 

## <a name="where-can-i-provide-feedback-on-this?"></a>Où puis-je fournir des commentaires ?
Nous apprécions vos commentaires. Veuillez nous [envoyer un courrier électronique](mailto:vsai@microsoft.com). 

<!--HONumber=Oct16_HO2-->


