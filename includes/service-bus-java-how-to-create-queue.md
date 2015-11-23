<a id="what-are-service-bus-queues"></a>
## Présentation des files d'attente Service Bus

Les files d’attente Service Bus prennent en charge un modèle de communication de **messagerie répartie**. Lors de l’utilisation de files d’attente, les composants d’une application distribuée ne communiquent pas directement entre eux ; ils échangent plutôt des messages via une file d’attente, qui fait office d’intermédiaire (broker). Un producteur de messages (expéditeur) remet un message à la file d’attente, puis continue son traitement. De manière asynchrone, un consommateur de message (destinataire) extrait le message de la file d’attente, puis le traite. L’expéditeur ne doit pas forcément attendre une réponse du destinataire afin de continuer à traiter et à envoyer d’autres messages. Les files d’attente permettent la livraison de messages sur le principe du **premier entré, premier sorti (FIFO)** à un ou plusieurs destinataires concurrents. Autrement dit, les messages sont en général reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente ; chaque message est reçu et traité par un seul consommateur de message uniquement.

![QueueConcepts](./media/service-bus-java-how-to-create-queue/sb-queues-08.png)

Les files d’attente Service Bus sont une technologie à usage généraliste pouvant servir à une grande diversité de situations :

-   Communication entre les rôles Web et les rôles de travail dans une application multiniveau Azure.
-   Communication entre les applications locales et les applications hébergées par Azure dans une solution hybride.
-   Communication entre les composants d’une application distribuée s’exécutant en local dans différentes organisations ou dans différents services d’une organisation.

L’utilisation de files d’attente permet une meilleure montée en charge de vos applications et une plus grande résilience dans votre architecture.

## Création d'un espace de noms de service

Pour commencer à utiliser les files d'attente Service Bus dans Azure, vous devez d'abord créer un espace de noms de service. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms de service :

1.  Connectez-vous au [portail Azure][].

2.  Dans le volet de navigation de gauche du portail, cliquez sur **Service Bus**.

3.  Dans le volet inférieur du portail, cliquez sur **Créer**. ![](./media/service-bus-java-how-to-create-queue/sb-queues-03.png)

4.  Dans la boîte de dialogue **Add a new namespace**, entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible. ![](./media/service-bus-java-how-to-create-queue/sb-queues-04.png)

5.  Après vous être assuré que le nom de l'espace de noms est disponible, choisissez le pays ou la région où votre espace de noms doit être hébergé (veillez à utiliser le même pays ou la même région que celui ou celle où vous déployez vos ressources de calcul).

	IMPORTANT : choisissez la **même région** que celle que vous prévoyez de sélectionner pour le déploiement de votre application. Vous bénéficiez ainsi des meilleures performances.

6. 	Gardez les valeurs par défaut des autres champs de la boîte de dialogue (**Messaging** et **Niveau Standard**), puis cliquez sur la coche. Le système crée l'espace de noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.

	![](./media/service-bus-java-how-to-create-queue/getting-started-multi-tier-27.png)

L’espace de noms que venez de créer apparaît alors dans le portail Azure. Son activation peut prendre un certain temps. Attendez que l'état de l’espace de noms soit **Actif** avant de continuer.

## Obtention d’informations d’identification de gestion par défaut pour l’espace de noms

Afin d’effectuer des opérations de gestion, comme la création d’une file d’attente, sur le nouvel espace de noms, vous devez obtenir les informations de gestion associées. Ces informations d’identification sont disponibles dans le portail Azure.

###Pour obtenir les informations de gestion du portail

1.  Dans le volet de navigation gauche, cliquez sur le nœud **Service Bus** pour afficher la liste des espaces de noms disponibles : ![](./media/service-bus-java-how-to-create-queue/sb-queues-13.png)

2.  Cliquez sur l’espace de noms que vous venez de créer dans la liste affichée.

3.  Cliquez sur **Configurer** pour afficher les stratégies d’accès partagé pour votre espace de noms. ![](./media/service-bus-java-how-to-create-queue/sb-queues-14.png)

4.  Notez la clé primaire ou copiez-la dans le Presse-papiers.

  [portail Azure]: http://manage.windowsazure.com

  [34]: ./media/service-bus-java-how-to-create-queue/VSProperties.png

<!---HONumber=Nov15_HO3-->