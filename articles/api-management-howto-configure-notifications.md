<properties 
	pageTitle="Configuration des notifications et des modèles de messages électroniques dans Gestion des API Azure" 
	description="Apprenez à configurer des notifications et des modèles de messages électroniques dans Gestion des API Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Configuration des notifications et des modèles de messages électroniques dans Gestion des API Azure

Gestion des API Azure (version préliminaire) permet de configurer les notifications pour des événements spécifiques et de configurer les modèles de messages électroniques utilisés pour communiquer avec les administrateurs et les développeurs de l'instance Gestion des API. Cette rubrique vous présente comment configurer les notifications pour les événements disponibles. Elle offre également un aperçu de la configuration des modèles de messages électroniques utilisés pour ces événements.

## Dans cette rubrique

-   [Configuration des notifications de l'éditeur][Configuration des notifications de l'éditeur]
-   [Configuration des modèles de messages électroniques][Configuration des modèles de messages électroniques]

## <a name="publisher-notifications"> </a>Configuration des notifications de l'éditeur

Pour configurer les notifications, cliquez sur **Console de gestion** dans le portail Azure pour votre service Gestion des API. Vous accéderez au portail d'administration Gestion des API.

> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][Création d'une instance de service Gestion des API] dans le didacticiel [Prise en main de Gestion des API Azure][Prise en main de Gestion des API Azure].

![API Management console][API Management console]

Cliquez sur **Notifications** dans le menu **Gestion des API** à gauche pour voir les notifications disponibles.

![Publisher notifications][Publisher notifications]

La liste suivante répertorie les événements pour lesquels il est possible de configurer des notifications.

-   **Demandes d'abonnement (approbation nécessaire)** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification pour les demandes d'abonnement aux produits API nécessitant une approbation.
-   **Nouveaux abonnements** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification concernant les nouveaux abonnements aux produits API.
-   **Demandes de la galerie d'applications** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification lorsque de nouvelles applications sont proposées dans la galerie d'applications.
-   **CCI** : les destinataires du message et les utilisateurs spécifiés reçoivent une copie cachée de tous les messages envoyés aux développeurs.
-   **Nouveau problème ou commentaire** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification lorsqu'un problème ou un commentaire est envoyé sur le portail des développeurs.
-   **Message de fermeture de compte** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification lorsqu'un compte est fermé.
-   **Limite du quota d'abonnements bientôt atteint** : les destinataires du message et les utilisateurs spécifiés reçoivent une notification lorsque l'utilisation de l'abonnement approche le quota.

Pour chaque événement, vous pouvez spécifier les destinataires du message via la zone de texte d'adresse. Vous pouvez également sélectionner les utilisateurs dans une liste.

Pour spécifier les adresses à notifier, entrez-les dans la zone de texte d'adresse. Si vous devez entrer plusieurs adresses, séparez-les par des virgules.

![Notification recipients][Notification recipients]

Pour spécifier les utilisateurs à notifier, cliquez sur **Ajouter un destinataire**, activez la case à cocher à côté de l'utilisateur à notifier, puis cliquez sur **OK**.

> Notez que seuls les administrateurs figurent dans la liste.

Une fois les destinataires de la notification configurés, cliquez sur **Enregistrer** pour appliquer les modifications des destinataires.

> Si vous quittez l'onglet **Notifications de l'éditeur**, le portail Gestion des API vous avertit si les modifications n'ont pas été enregistrées.

## <a name="email-templates"> </a>Configuration des modèles de messages électroniques

L'outil Gestion des API contient des modèles de messages électroniques pour les messages envoyés dans le cadre de l'administration et de l'utilisation du service. Les modèles suivants sont fournis.

-   Demande d'ajout à la galerie d'applications approuvée
-   Lettre d'adieu au développeur
-   Notification de la limite du quota d'abonnement bientôt atteinte
-   Invitation de l'utilisateur
-   Nouveau commentaire ajouté à un problème
-   Nouveau problème reçu
-   Nouvel abonnement activé
-   Confirmation du renouvellement de l'abonnement
-   Refus de la demande d'abonnement
-   Réception de la demande d'abonnement

Ces modèles peuvent être modifiés comme vous le souhaitez.

Pour voir et configurer les modèles de messages électroniques de votre instance Gestion des API, cliquez sur **Notifications** dans le menu **Gestion des API** à gauche, puis sélectionnez l'onglet **Modèles de messages électroniques**.

![Email templates][Email templates]

Pour consulter ou modifier un modèle en particulier, sélectionnez-le dans la liste déroulante **Modèles**.

![Email templates list][Email templates list]

Pour chaque modèle de message, l'objet est au format texte et le corps au format HTML. Chaque élément peut être personnalisé.

![Email template editor][Email template editor]

La liste **Paramètres** contient une liste de paramètres qui, lorsqu'ils sont insérés dans l'objet ou dans le corps du message, sont remplacés par une valeur définie lorsque le message est envoyé. Pour insérer un paramètre, placez le curseur là où vous voulez insérer le paramètre, puis cliquez sur la flèche à gauche du nom du paramètre.

Cliquez sur **Aperçu** ou **Envoyer un test** pour voir à quoi ressemble le message ou bien pour envoyer un message test.

> Notez que les paramètres ne sont pas remplacés par les valeurs réelles lors de l'aperçu ou du test.
>
> Pour enregistrer les modifications apportées au modèle de message, cliquez sur **Enregistrer**. Pour annuler les modifications, cliquez sur **Annuler**.

  [Configuration des notifications de l'éditeur]: #publisher-notifications
  [Configuration des modèles de messages électroniques]: #email-templates
  [Création d'une instance de service Gestion des API]: ../api-management-get-started/#create-service-instance
  [Prise en main de Gestion des API Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
  [Publisher notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
  [Notification recipients]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
  [Email templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
  [Email templates list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
  [Email template editor]: ./media/api-management-howto-configure-notifications/api-management-email-template.png

<!--HONumber=46--> 
