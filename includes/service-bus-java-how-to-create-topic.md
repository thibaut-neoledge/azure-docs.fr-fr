## Présentation des rubriques et des abonnements Service Bus

Les rubriques et les abonnements Service Bus prennent en charge un modèle de communication de messagerie *de publication et d'abonnement*. Lors de l’utilisation de rubriques et d’abonnements, les composants d’une application distribuée ne communiquent pas directement entre eux ; ils échangent plutôt des messages via une rubrique, qui fait office d’intermédiaire.

![TopicConcepts](./media/service-bus-java-how-to-create-topic/sb-topics-01.png)

Contrairement aux files d’attente Service Bus, où chaque message est traité par un seul consommateur, les rubriques et les abonnements fournissent une forme de communication « un-à-plusieurs », à l'aide d'un modèle de publication et d'abonnement. Il est possible d’inscrire plusieurs abonnements à une rubrique. Lorsqu’un message est envoyé à une rubrique, il est alors mis à disposition de chaque abonnement pour être géré ou traité indépendamment.

Un abonnement à une rubrique ressemble à une file d’attente virtuelle qui reçoit des copies des messages envoyés à la rubrique. Vous pouvez éventuellement inscrire des règles de filtre pour une rubrique par abonnement, ce qui vous permet de filtrer et de restreindre les messages d’une rubrique reçus en fonction des abonnements à une rubrique.

Les rubriques et les abonnements Service Bus vous permettent de mettre votre infrastructure à l’échelle pour traiter de très nombreux messages parmi un très grand nombre d’utilisateurs et d’applications.

## Création d'un espace de noms de service

Pour commencer à utiliser les rubriques et les abonnements Service Bus dans Azure, vous devez d’abord créer un espace de noms de service. Ce dernier fournit un conteneur d’étendue pour l’adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms de service :

1.  Connectez-vous au [portail de gestion Azure][].

2.  Dans le volet de navigation gauche du portail de gestion, cliquez sur **Service Bus**.

3.  Dans le volet inférieur du portail de gestion, cliquez sur **Créer**. ![][0]

4.  Dans la boîte de dialogue **Add a new namespace**, entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible. ![][2]

5.  Après vous être assuré que le nom de l'espace de noms est disponible, choisissez le pays ou la région où votre espace de noms doit être hébergé (veillez à utiliser le même pays ou la même région que celui ou celle où vous déployez vos ressources de calcul).

	IMPORTANT : choisissez la **même région** que celle que vous prévoyez de sélectionner pour le déploiement de votre application. Vous bénéficiez ainsi des meilleures performances.

6. 	Laissez les autres champs de la boîte de dialogue avec leurs valeurs par défaut (**Messaging** et **Niveau Standard**), puis cliquez sur la coche. Le système crée l'espace de noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.

	![][6]


## Obtention d’informations d’identification de gestion par défaut pour l’espace de noms

Afin d’effectuer des opérations de gestion, comme la création d’une rubrique ou d’un abonnement, sur le nouvel espace de noms, vous devez obtenir les informations de gestion associées. Ces informations d’identification sont disponibles sur le portail de gestion Azure.

### Pour obtenir les informations de gestion du portail

1.  Dans le volet de navigation gauche, cliquez sur le nœud **Service Bus** pour afficher la liste des espaces de noms disponibles : ![][0]

2.  Cliquez sur l’espace de noms que vous venez de créer dans la liste affichée : ![][3]

3.  Cliquez sur **Configurer** pour afficher les stratégies d’accès partagé pour votre espace de noms. ![](./media/service-bus-java-how-to-create-topic/sb-queues-14.png)

4.  Notez la clé primaire ou copiez-la dans le Presse-papiers.


  [portail de gestion Azure]: http://manage.windowsazure.com
  [0]: ./media/service-bus-java-how-to-create-topic/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-create-topic/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-create-topic/sb-queues-09.png
  [4]: ./media/service-bus-java-how-to-create-topic/sb-queues-06.png

  [6]: ./media/service-bus-java-how-to-create-topic/getting-started-multi-tier-27.png
  [34]: ./media/service-bus-java-how-to-create-topic/VSProperties.png

<!---HONumber=July15_HO4-->