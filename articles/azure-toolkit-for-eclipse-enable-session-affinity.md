---
title: "Activer l&quot;affinité de session à l&quot;aide du kit de ressources Azure pour Eclipse"
description: "Découvrez comment activer l&quot;affinité de session à l&quot;aide du kit de ressources Azure pour Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 88c595ec-7d85-40bd-9078-8d6be7b3f0fa
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: ab8623d6f9751ed6d71d9a5b1c0d5e939c442862
ms.lasthandoff: 04/22/2017


---
# <a name="enable-session-affinity"></a>Activer l'affinité de session
Dans le Kit de ressources Azure pour Eclipse, vous pouvez activer l'affinité de session HTTP, ou « sessions temporaires », pour vos rôles. L'illustration suivante montre la boîte de dialogue des propriétés de **l'équilibrage de charge** utilisé pour activer la fonctionnalité d'affinité de session :

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>Pour activer l'affinité de session pour votre rôle
1. Cliquez avec le bouton droit sur le rôle dans l’Explorateur de projet Eclipse, cliquez sur **Azure**, puis sur **Équilibrage de charge**.

2. Dans la boîte de dialogue **Propriétés d'équilibrage de charge pour WorkerRole1** :

   a. Cochez **Activer l'affinité de session HTTP (sessions temporaires) pour ce rôle.**

   b. Pour le **point de terminaison d’entrée à utiliser**, sélectionnez un point de terminaison d’entrée à utiliser, par exemple, **http (public:80, private:8080)**. Votre application doit utiliser ce point de terminaison en tant que point de terminaison HTTP. Vous pouvez activer plusieurs points de terminaison pour votre rôle, mais vous ne pouvez en sélectionner qu'un seul pour prendre en charge des sessions temporaires.

   c. Régénérez votre application.

Après l'activation, si vous avez plusieurs instances de rôle, les requêtes HTTP provenant d'un client particulier continueront d'être gérées par la même instance de rôle.

Le Kit de ressources Eclipse le permet en installant un module IIS spécial appelé Application Request Routing (ARR) dans chacune de vos instances de rôle. ARR redirige les requêtes HTTP vers l'instance de rôle appropriée. Le kit de ressources reconfigure automatiquement le point de terminaison sélectionné afin que le trafic HTTP entrant soit tout d'abord dirigé vers le logiciel ARR. Le kit de ressources crée également un nouveau point de terminaison interne que votre serveur Java est configuré pour écouter. C'est le point de terminaison utilisé par ARR pour rediriger le trafic HTTP vers l'instance de rôle appropriée. De cette façon, chaque instance de rôle dans votre déploiement multi-instance sert de proxy inverse pour toutes les autres instances, activant ainsi les sessions temporaires.

## <a name="notes-about-session-affinity"></a>Remarques sur l'affinité de session
* L'affinité de session ne fonctionne pas dans l'émulateur de calcul. Les paramètres peuvent être appliqués dans l'émulateur de calcul sans interférer avec votre processus de génération ou d'exécution de l'émulateur de calcul, mais la fonction elle-même ne fonctionne pas dans l'émulateur de calcul.

* L'activation de l'affinité de session entraîne une augmentation de la quantité d'espace disque pris par votre déploiement dans Azure, car des logiciels supplémentaires seront téléchargés et installés dans vos instances de rôle lorsque votre service est démarré dans le cloud Azure.

* Le temps d'initialisation de chaque rôle sera plus long.

* Un point de terminaison interne, servant de dispositif de redirection de trafic comme indiqué ci-dessus, est ajouté.


## <a name="see-also"></a>Voir aussi
[Kit de ressources Azure pour Eclipse][Azure Toolkit for Eclipse]

[Création d’une application Hello World pour Azure dans Eclipse][Creating a Hello World Application for Azure in Eclipse]

[Installation du kit de ressources Azure pour Eclipse][Installing the Azure Toolkit for Eclipse] 

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[How to Maintain Session Data with Session Affinity]: http://go.microsoft.com/fwlink/?LinkID=699539
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

