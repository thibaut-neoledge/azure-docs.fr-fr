<properties
    pageTitle="Activer l'affinité de session à l'aide du kit de ressources Azure pour Eclipse"
    description="Découvrez comment activer l'affinité de session à l'aide du kit de ressources Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# Activer l'affinité de session #

Dans le Kit de ressources Azure pour Eclipse, vous pouvez activer l'affinité de session HTTP, ou « sessions temporaires », pour vos rôles. L'illustration suivante montre la boîte de dialogue des propriétés de **l'équilibrage de charge** utilisé pour activer la fonctionnalité d'affinité de session :

![][ic719492]

## Pour activer l'affinité de session pour votre rôle ##

1. Cliquez avec le bouton droit sur le rôle dans l'Explorateur de projet Eclipse, cliquez sur **Azure**, puis sur **Équilibrage de charge**.
1. Dans la boîte de dialogue **Propriétés d'équilibrage de charge pour WorkerRole1** :
    1. Cochez **Activer l'affinité de session HTTP (sessions temporaires) pour ce rôle.**
    1. Pour le **point de terminaison d'entrée à utiliser**, sélectionnez un point de terminaison d'entrée à utiliser, par exemple, **http (public:80, private:8080)**. Votre application doit utiliser ce point de terminaison en tant que point de terminaison HTTP. Vous pouvez activer plusieurs points de terminaison pour votre rôle, mais vous ne pouvez en sélectionner qu'un seul pour prendre en charge des sessions temporaires.
    1. Régénérez votre application.

Après l'activation, si vous avez plusieurs instances de rôle, les requêtes HTTP provenant d'un client particulier continueront d'être gérées par la même instance de rôle.

Le Kit de ressources Eclipse le permet en installant un module IIS spécial appelé Application Request Routing (ARR) dans chacune de vos instances de rôle. ARR redirige les requêtes HTTP vers l'instance de rôle appropriée. Le kit de ressources reconfigure automatiquement le point de terminaison sélectionné afin que le trafic HTTP entrant soit tout d'abord dirigé vers le logiciel ARR. Le kit de ressources crée également un nouveau point de terminaison interne que votre serveur Java est configuré pour écouter. C'est le point de terminaison utilisé par ARR pour rediriger le trafic HTTP vers l'instance de rôle appropriée. De cette façon, chaque instance de rôle dans votre déploiement multi-instance sert de proxy inverse pour toutes les autres instances, activant ainsi les sessions temporaires.

## Remarques sur l'affinité de session ##

* L'affinité de session ne fonctionne pas dans l'émulateur de calcul. Les paramètres peuvent être appliqués dans l'émulateur de calcul sans interférer avec votre processus de génération ou d'exécution de l'émulateur de calcul, mais la fonction elle-même ne fonctionne pas dans l'émulateur de calcul.
* L'activation de l'affinité de session entraîne une augmentation de la quantité d'espace disque pris par votre déploiement dans Azure, car des logiciels supplémentaires seront téléchargés et installés dans vos instances de rôle lorsque votre service est démarré dans le cloud Azure.
* Le temps d'initialisation de chaque rôle sera plus long.
* Un point de terminaison interne, servant de dispositif de redirection de trafic comme indiqué ci-dessus, est ajouté.

Pour obtenir un exemple montrant comment gérer les données de session lorsque l'affinité de session est activée, consultez [Comment gérer les données de session avec l'affinité de session][].

## Voir aussi ##

[Kit de ressources Azure pour Eclipse][]

[Création d’une application « Hello World » pour Azure dans Eclipse][]

[Installation du kit de ressources Azure pour Eclipse][]

[Comment gérer les données de session avec l'affinité de session][]

Pour plus d'informations sur l'utilisation d'Azure avec Java, consultez le [Centre de développement Java][].

<!-- URL List -->

[Centre de développement Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une application « Hello World » pour Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Comment gérer les données de session avec l'affinité de session]: http://go.microsoft.com/fwlink/?LinkID=699539
[Installation du kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png

<!---HONumber=AcomDC_0817_2016-->