<properties linkid="service-bus-manage-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Manage Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to create and manage your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Manage Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Gestion des entités de messagerie Service Bus

Cette rubrique décrit la création et la gestion de vos entités Service Bus à l'aide du [portail de gestion Azure][portail de gestion Azure]. Vous pouvez utiliser le portail pour créer des espaces de noms de service ou bien des entités de messagerie (files d'attente, rubriques ou abonnements). Vous pouvez aussi supprimer des entités ou modifier l'état des entités.

## Sommaire

-   [Création d'une entité Service Bus][Création d'une entité Service Bus]
-   [Suppression d'une entité Service Bus][Suppression d'une entité Service Bus]
-   [Désactivation ou activation d'une entité Service Bus][Désactivation ou activation d'une entité Service Bus]
-   [Ressources supplémentaires][Ressources supplémentaires]

## <span id="create"></span></a> Création d'une entité Service Bus

Le portail de gestion Azure offre deux manières de créer une entité Service Bus : *Création rapide* ou *Création personnalisée*.

### Création rapide

La fonction Création rapide vous permet de créer une file d'attente Service Bus, une rubrique ou un espace de noms de service de relais en une seul étape. Suivez cette procédure pour créer une entité Service Bus.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur l'icône **Nouveau** dans le coin inférieur gauche du portail de gestion.
3.  Cliquez sur l'icône **App Services**, puis sur **Service Bus Queue** (rubrique ou relais). Cliquez sur **Création rapide**, puis entrez le nom de la file d'attente, sa région et l'ID d'abonnement Azure.

    a. S'il s'agit de votre premier espace de noms dans la région sélectionnée, le portail suggère une file d'attente d'espace de noms, [nom de votre entité]-ns. Vous pouvez modifier cette valeur.

    b. Si vous disposez déjà d'au moins un espace de noms de service dans cette région, un espace de noms est automatiquement sélectionné. Vous pouvez choisir un autre espace de noms.

4.  Cliquez sur la coche à côté de **Créer une file d'attente** (ou rubrique).
5.  Une fois la file d'attente ou la rubrique créée, vous recevez le message **Création de la file d'attente ‘[Nom de la file d'attente]’ terminée**.

    a. Si vous ne disposez d'aucun espace de noms dans cette région ou dans cet abonnement Azure, un nouvel espace de noms est automatiquement créé. Dans ce cas, vous recevez deux messages de réussite : un pour la création de l'espace de noms et un autre pour la création de l'entité.

    ![][0]

Cliquez sur l'icône **Service Bus** dans la barre de navigation de gauche pour obtenir la liste des espaces de noms. Vous trouvez le nouvel espace de noms que vous venez de créer. Cliquez sur cet espace de noms dans la liste. Vous voyez l'entité que vous venez de créer sous cet espace de noms.

**Remarque** Il est possible que l'espace de noms ne soit pas immédiatement répertorié. La création de l'espace de noms de service et la mise à jour de l'interface du portail peuvent prendre quelques secondes.

**Remarque** L'utilisation de **Création rapide** pour un relais ne crée pas de nouveau point de terminaison de relais. Cette opération ne crée qu'un espace de noms sous lequel vous pouvez créer par programme un point de terminaison de relais. Pour plus d'informations, consultez la [documentation de Service Bus][documentation de Service Bus].

### Création personnalisée

La fonction **Création personnalisée** est une version plus détaillée, qui propose des boutons pour modifier les valeurs par défaut des propriétés de l'entité (file d'attente ou rubrique) créée. Pour créer une rubrique ou une entité avec **Création personnalisée**, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **Nouveau** dans le coin inférieur gauche du portail de gestion.
3.  Cliquez sur l'icône **App Services**, puis sur **Service Bus Queue** (rubrique ou relais). Enfin, cliquez sur Création personnalisée.
4.  Entrez le nom de la file d'attente, sa région et l'ID d'abonnement Azure.

    a. S'il s'agit de votre premier espace de noms de service dans la région sélectionnée, le portail suggère une file d'attente d'espace de noms, [nom de votre entité]-ns. Vous pouvez modifier cette valeur.

    b. Si vous disposez déjà d'au moins un espace de noms dans cette région, un espace de noms est automatiquement sélectionné. Vous pouvez choisir un autre espace de noms.

5.  Cliquez sur **Suivant** pour insérer les autres propriétés.

    ![][1]

6.  Cliquez sur la coche pour créer la file d'attente.

    ![][2]

Cliquez sur l'icône **Service Bus** dans la barre de navigation de gauche pour obtenir la liste des espaces de noms du service. Vous trouvez le nouvel espace de noms que vous venez de créer. Cliquez sur cet espace de noms dans la liste. Vous voyez l'entité que vous venez de créer sous cet espace de noms.

## <span id="delete"></span></a> Suppression d'une entité Service Bus

À l'aide du portail, vous pouvez supprimer une entité de messagerie Service Bus, c'est-à-dire une file d'attente, une rubrique ou un abonnement. Pour supprimer une file d'attente ou une rubrique, procédez comme suit :

1.  Accédez à la vue de liste des espaces de noms de service et cliquez sur l'espace de noms sous lequel vous avez créé l'entité (file d'attente ou rubrique).
2.  Cliquez sur l'icône **Supprimer** en bas de la page et confirmez l'opération de suppression.

    ![][3]

**Remarque** La suppression des entités est définitive. Une fois supprimées, vous ne pouvez plus les récupérer. Par contre, vous pouvez créer une entité portant le même nom.

Pour supprimer l'abonnement à une rubrique, procédez comme suit :

1.  Accédez à la vue de liste des espaces de noms et cliquez sur l'espace de noms sous lequel vous avez créé la rubrique.
2.  Cliquez sur la rubrique sous laquelle vous avez créé l'abonnement.
3.  Cliquez sur l'onglet **Abonnements**, puis sélectionnez l'abonnement à supprimer.
4.  Cliquez sur l'icône **Supprimer** en bas de la page et confirmez l'opération de suppression.

## <span id="disableenable"></span></a> Désactivation ou activation d'une entité Service Bus

Vous pouvez utiliser le portail pour modifier l'état d'une entité Service Bus, c'est-à-dire une file d'attente ou une rubrique. Pour activer ou désactiver une file d'attente ou une rubrique, procédez comme suit :

1.  Accédez à la vue de liste des espaces de noms de service et cliquez sur l'espace de noms sous lequel vous avez créé l'entité (file d'attente ou rubrique).
2.  Cliquez sur **Disable** (ou **Enable**) en bas de la page.

    ![][4]

## <span id="seealso"></span></a>Ressources supplémentaires

[Azure Service Bus][Azure Service Bus]

[Centre de développement .NET][Centre de développement .NET] sur le site web Azure

[Création d'applications qui utilisent des rubriques et des abonnements Service Bus][Création d'applications qui utilisent des rubriques et des abonnements Service Bus]

[Files d'attente, rubriques et abonnements.][Files d'attente, rubriques et abonnements.]

  [portail de gestion Azure]: http://manage.windowsazure.com
  [Création d'une entité Service Bus]: #create
  [Suppression d'une entité Service Bus]: #delete
  [Désactivation ou activation d'une entité Service Bus]: #disableenable
  [Ressources supplémentaires]: #seealso
  [0]: ./media/service-bus-manage-message-entities/QueueQuickCreate.png
  [documentation de Service Bus]: http://www.windowsazure.com/fr-fr/develop/net/how-to-guides/service-bus-relay/
  [1]: ./media/service-bus-manage-message-entities/AddQueue1.png
  [2]: ./media/service-bus-manage-message-entities/ConfigureQueue.png
  [3]: ./media/service-bus-manage-message-entities/DeleteEntity.png
  [4]: ./media/service-bus-manage-message-entities/DisableEnable.png
  [Azure Service Bus]: http://go.microsoft.com/fwlink/?LinkId=266834
  [Centre de développement .NET]: http://go.microsoft.com/fwlink/?LinkID=262187
  [Création d'applications qui utilisent des rubriques et des abonnements Service Bus]: http://go.microsoft.com/fwlink/?LinkId=264293
  [Files d'attente, rubriques et abonnements.]: http://go.microsoft.com/fwlink/?LinkId=264291
