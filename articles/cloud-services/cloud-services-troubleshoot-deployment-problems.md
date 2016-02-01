<properties
 pageTitle="Résoudre les problèmes de déploiement de service cloud | Microsoft Azure"
 description="Il existe quelques problèmes courants que vous pouvez rencontrer lors du déploiement d’un service cloud sur Azure. Cet article fournit des solutions pour certains d’entre eux."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Comment résoudre les problèmes de déploiement de service cloud que vous risquez de rencontrer

Lorsque vous déployez un package d’application de service cloud sur Azure, vous pouvez obtenir des informations concernant le déploiement à partir du volet **Propriétés** du portail Azure. Vous pouvez utiliser les détails figurant dans ce volet pour faciliter la résolution des problèmes avec le service cloud, et vous pouvez fournir ces informations au support technique Azure lors de l’ouverture d’une nouvelle demande de support.

Vous trouverez le volet **Propriétés** comme suit :

* Dans le portail Azure : cliquez sur le déploiement de votre service cloud, cliquez sur **Tous les paramètres**, puis sur **Propriétés**.
* Dans le portail Azure Classic : cliquez sur le déploiement de votre service cloud, cliquez sur **TABLEAU DE BORD**, cliquez sur le coin inférieur droit de la page (sous **Aperçu rapide**). N'oubliez pas que ce volet n’affiche aucun titre « Propriétés ».

> [AZURE.NOTE]Vous pouvez copier le contenu du volet Propriétés dans le Presse-papiers en cliquant sur l’icône dans le coin supérieur droit du volet.

## Contacter le Support technique Azure

Si vous avez besoin d’aide supplémentaire concernant n’importe quel point de cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](http://azure.microsoft.com/support/forums/).

Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**. Pour plus d’informations sur l’utilisation du support Azure, lisez la [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).



## Problème : Impossible d’accéder à mon site web bien que mon déploiement soit démarré et que toutes les instances de rôle soient prêtes

Le lien URL du site web affiché dans le portail n’inclut pas le port. Le port par défaut pour les sites web est 80. Toutefois, si votre application est configurée pour s’exécuter dans un autre port, vous devez ajouter le port correct à l’URL lorsque vous accédez au site web.

1. Dans le portail Azure, cliquez sur le déploiement de votre service cloud.
2. Dans le volet **Propriétés** du portail Azure, vérifiez les ports des instances de rôle (sous Points de terminaison d’entrée).
3. Si le port n’est pas *80*, ajoutez la valeur de port correcte à l’URL lorsque vous accédez à l’application. Pour spécifier un port non défini par défaut, tapez l’URL, suivie de deux-points (:) et du numéro de port sans espace.

## Problème : Mes instances de rôle ont redémarré sans action de ma part

Une réparation de service se produit automatiquement lorsqu’Azure détecte des nœuds problématiques et déplace les instances de rôle vers de nouveaux nœuds. Lorsque cela se produit, il est possible que vos instances de rôle redémarrent automatiquement. Pour savoir si une réparation de service a eu lieu :

1. Dans le portail Azure, cliquez sur le déploiement de votre service cloud.
2. Dans le volet **Propriétés** du portail Azure, passez en revue les informations et déterminez si une réparation de service a eu lieu au moment où vous avez observé le redémarrage des rôles.

Les rôles redémarreront également environ une fois par mois pendant les mises à niveau du système d’exploitation hôte et du système d’exploitation invité. Pour plus d’informations, consultez le billet de blog [Redémarrages d’instances de rôles pour cause de mises à niveau du système d’exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## Problème : Impossible d’effectuer un échange d’adresses IP virtuelles, une erreur s’affiche

Un échange d’adresses IP virtuelles n’est pas autorisé si une mise à jour de déploiement est en cours. Les mises à jour de déploiement peuvent se produire automatiquement dans les situations suivantes :

* Un nouveau système d’exploitation invité est disponible et votre installation est configurée pour les mises à jour automatiques
* Une réparation de service se produit

Pour savoir si une mise à niveau automatique vous empêche d’effectuer un échange d’adresses IP virtuelles :

1. Dans le portail Azure, cliquez sur le déploiement de votre service cloud.
2. Dans le volet **Propriétés** du portail Azure, examinez la valeur **État**. Si elle affiche **Prêt**, vérifiez la **Dernière opération** pour voir si une opération récente empêche l’échange d’adresses IP virtuelles.
3. Répétez les étapes 1 et 2 pour le déploiement en production.
4. Si une mise à jour automatique est en cours, attendez qu’elle se termine avant d’essayer d’effectuer l’échange d’adresses IP virtuelles.

## Problème : Une instance de rôle est exécutée en boucle entre Démarrée, Initialisation, Occupée et Arrêtée

Cette condition peut indiquer un problème avec votre code d’application, package ou fichier de configuration. Si la valeur est true, vous devez être en mesure de voir l’état changer à des intervalles de quelques minutes et le portail Azure peut indiquer des états tels que **Recyclage**, **Occupé** ou **Initialisation**. Cela indique qu’il existe un problème au niveau de l’application qui empêche l’exécution de l’instance de rôle.

Pour plus d’informations sur la façon de résoudre ce problème, consultez le billet de blog [Données de diagnostic de calcul PaaS Azure] et [Problèmes courants entraînant le recyclage des rôles](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## Problème : Mon application a cessé de fonctionner

1. Dans le portail Azure, cliquez sur l’instance de rôle.
2. Dans le volet **Propriétés** du portail Azure, tenez compte des conditions suivantes pour résoudre votre problème :
   * Si l’instance de rôle a été récemment arrêtée (vous pouvez vérifier la valeur **Nombre d’abandons**), il est possible que le déploiement soit en cours de mise à jour. Attendez de voir si l’instance de rôle recommence à fonctionner par elle-même.
   * Si l’instance de rôle est occupée, vérifiez votre code d’application pour voir si l’événement [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) est géré. Vous devrez peut-être ajouter ou corriger le code qui gère cet événement.
   * Passez en revue les données de diagnostic et les scénarios de résolution des problèmes dans le billet de blog [Données de diagnostic de calcul PaaS Azure].

>[AZURE.WARNING]Si vous redémarrez votre service cloud, vous réinitialisez les propriétés de déploiement, et effacez ainsi efficacement les informations du problème d’origine.

## Étapes suivantes

Affichez plus d’[articles de résolution des problèmes](..\?tag=top-support-issue&service=cloud-services) liés aux services cloud.

Pour savoir comment résoudre le problème des rôles de service Cloud à l’aide des données de diagnostic d’ordinateur Azure PaaS, consultez la [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0121_2016-->