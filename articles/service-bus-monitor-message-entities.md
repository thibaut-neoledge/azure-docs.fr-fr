<properties linkid="service-bus-monitor-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Monitor Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to monitor your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Monitor Service Bus Messaging Entities" authors="" solutions="" />

Surveillance des entités de messagerie Service Bus
==================================================

Cette rubrique décrit la gestion et la surveillance de vos entités Service Bus à l'aide du [portail de gestion Azure](http://manage.windowsazure.com). Ce dernier propose une vue complète de l'état des files d'attente et des rubriques, dont vous pouvez également suivre l'utilisation.

Suivi de l'activité sur les files d'attente Service Bus
-------------------------------------------------------

Pour surveiller une file d'attente Service Bus, procédez comme suit :

1.  Connectez-vous à la [version préliminaire du portail de gestion Azure](http://manage.windowsazure.com).
2.  Cliquez sur l'icône **Service Bus** dans la barre de navigation de gauche pour obtenir la liste des espaces de noms du service.
3.  Cliquez sur l'espace de noms qui contient la file d'attente que vous voulez surveiller.
4.  Dans la barre en haut de la page, cliquez sur **Files d'attente**.
5.  Cliquez sur le nom de la file d'attente que vous souhaitez surveiller. Le tableau de bord de la file d'attente s'affiche.
6.  Les files d'attente contiennent les activités que vous avez créées. Vous pouvez consulter ces informations sur différentes plages horaires. Par défaut, la plage est d'une heure, mais vous pouvez cliquer sur la liste déroulante à côté de l'heure pour choisir une autre plage horaire : dernières 24 heures, 7 derniers jours ou 30 derniers jours. Vous pouvez consulter les données avec une précision allant jusqu'à 5 minutes pour la plage horaire d'une heure, une heure pour la plage horaire de 24 heures, et une journée pour les plages horaires de 7 et 30 jours.

Pour toutes les files d'attente, les graphiques suivants sont affichés :

-   **Messages entrants** : nombre de messages mis en file d'attente au cours de cette plage horaire.
-   **Messages sortants** : nombre de messages retirés de la file d'attente au cours de cette plage horaire.
-   **Longueur** : nombre de messages dans l'entité à la fin de cette plage horaire.
-   **Taille** : espace de stockage (en mégaoctets) utilisé par l'entité à la fin de cette plage horaire.

### Aperçu rapide

Dans le tableau de bord, **Aperçu rapide** affiche l'état actuel de la file d'attente dans **Longueur de la file d'attente**. Les autres propriétés de la file d'attente ou de la rubrique sont également affichées. Ces informations sont actualisées toutes les 10 secondes.

![](./media/service-bus-monitor-message-entities/QueueDashboard.png)

Suivi de l'activité sur les rubriques Service Bus
-------------------------------------------------

Pour surveiller une rubrique Service Bus, procédez comme suit :

1.  Connectez-vous à la [version préliminaire du portail de gestion Azure](http://manage.windowsazure.com).
2.  Cliquez sur l'icône **Service Bus** dans la barre de navigation de gauche pour obtenir la liste des espaces de noms du service.
3.  Cliquez sur l'espace de noms qui contient la rubrique que vous voulez surveiller.
4.  Dans la barre en haut de la page, cliquez sur **Rubriques**.
5.  Cliquez sur le nom de la rubrique que vous souhaitez surveiller. Le tableau de bord de la rubrique s'affiche.

Le tableau de bord de la rubrique est similaire au tableau de bord de la file d'attente, à l'exception des mesures d'utilisation. Le nombre de messages sortants et leur longueur ne sont pas présents dans le tableau de bord de la rubrique, car ces informations sont différentes pour chaque abonnement à une rubrique. L'onglet **Surveiller** permet d'ajouter des mesures d'utilisation (nombre de messages sortants et longueur) par abonnement à une rubrique. Pour ajouter ces mesures, cliquez sur l'onglet **Surveiller**. Cliquez ensuite sur **Ajouter des métriques** en bas de la page, puis choisissez les abonnements sous la rubrique.

![](./media/service-bus-monitor-message-entities/AddMetrics.png)

