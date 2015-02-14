<h2><a name="what-are-service-bus-topics"></a>Présentation des rubriques et des abonnements Service Bus</h2>

Les rubriques et les abonnements Service Bus prennent en charge un modèle de **communication de messagerie de publication et d'abonnement**. Lors de l'utilisation de rubriques et d'abonnements, les composants d'une application distribuée ne communiquent pas directement entre eux ; ils échangent plutôt des messages via une rubrique, qui fait office d'intermédiaire.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Contrairement aux files d'attente Service Bus, où chaque message est traité par un seul consommateur, les rubriques et les abonnements fournissent une forme de communication **un-à-plusieurs**, à l'aide d'un modèle de publication et d'abonnement. Il est possible d'inscrire plusieurs abonnements à une rubrique. Lorsqu'un message est envoyé à une rubrique, il est alors mis à disposition de chaque abonnement pour être géré ou traité indépendamment.

Un abonnement à une rubrique ressemble à une file d'attente virtuelle qui reçoit des copies des messages envoyés à la rubrique. Vous pouvez éventuellement inscrire des règles de filtre pour une rubrique par abonnement, ce qui vous permet de filtrer et de restreindre les messages d'une rubrique reçus en fonction des abonnements à une rubrique.

Les rubriques et les abonnements Service Bus vous permettent de mettre votre infrastructure à l'échelle pour traiter de très nombreux messages parmi un très grand nombre d'utilisateurs et d'applications.

<h2><a name="create-a-service-namespace"></a>Création d'un espace de noms de service</h2>

Pour commencer à utiliser les rubriques et les abonnements Service Bus dans Azure, vous devez d'abord créer un espace de noms de service. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms de service :

1.  Connectez-vous au [portail de gestion Azure][].

2.  Dans le volet de navigation gauche du portail de gestion, cliquez sur
    **Service Bus**.

3.  Dans le volet inférieur du portail de gestion, cliquez sur **Créer**.   
    ![][0]

4.  Dans la boîte de dialogue **Ajouter un nouvel espace de noms**, entrez un nom d'espace de noms.
    Le système vérifie immédiatement si le nom est disponible.   
    ![][2]

5.  Après vous être assuré que le nom de l'espace de noms est disponible, choisissez le pays ou la région où votre espace de noms doit être hébergé (veillez à utiliser le même pays ou la même région que celui ou celle où vous déployez vos ressources de calcul).

	IMPORTANT : choisissez la **même région** que celle que vous prévoyez de choisir pour le déploiement de votre application. Vous bénéficiez ainsi des meilleures performances.

6. 	Laissez les autres champs de la boîte de dialogue avec leurs valeurs par défaut (**Messaging** et **Niveau Standard**), puis cliquez sur la coche. Le système crée l'espace de noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.

	![][6]


<h2><a name="obtain-default-credentials"></a>Obtention d'informations d'identification de gestion par défaut pour l'espace de noms</h2>

Afin d'effectuer des opérations de gestion, comme la création d'une rubrique ou d'un abonnement, sur le nouvel espace de noms, vous devez obtenir les informations de gestion associées. Vous pouvez obtenir ces informations d'identification à partir du portail de gestion ou à partir de l'Explorateur de serveurs Visual Studio.

###Pour obtenir les informations de gestion du portail

1.  Dans le volet de navigation gauche, cliquez sur le nœud **Service Bus** pour afficher la liste des espaces de noms disponibles :    ![][0]

2.  Sélectionnez l'espace de noms que vous venez de créer dans la liste affichée :    ![][3]

3.  Cliquez sur **Informations de connexion**.    ![][4]

4.  Dans la boîte de dialogue **Accès aux informations de connexion**, recherchez les entrées **Émetteur par défaut** et **Clé par défaut**. Notez ces valeurs, car vous les utiliserez ci-dessous pour effectuer des opérations avec l'espace de noms. 

###Pour obtenir les informations de gestion de l'Explorateur de serveurs

Pour obtenir les informations de connexion en utilisant Visual Studio plutôt que le Portail de gestion, suivez la procédure décrite [ici](http://http://msdn.microsoft.com/fr-fr/library/windowsazure/ff687127.aspx), dans la section intitulée **Connexion à Azure à partir de Visual Studio**. Lorsque vous vous connectez à Azure, le nœud **Service Bus** sous l'arborescence **Microsoft Azure** dans l'Explorateur de serveurs est automatiquement rempli par les espaces de noms que vous avez déjà créés. Cliquez avec le bouton droit sur un espace de noms, puis cliquez sur **Propriétés** pour que la chaîne de connexion et les autres métadonnées associées à cet espace de noms s'affichent dans le volet **Propriétés** de Visual Studio. 

Notez la valeur **SharedAccessKey** u copiez-la dans le Presse-papiers :

![][34]

 
  [Portail de gestion Azure]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-topics/sb-queues-13.png
  [2]: ./media/howto-service-bus-topics/sb-queues-04.png
  [3]: ./media/howto-service-bus-topics/sb-queues-09.png
  [4]: ./media/howto-service-bus-topics/sb-queues-06.png
  
  [6]: ./media/howto-service-bus-topics/getting-started-multi-tier-27.png
  [34]: ./media/howto-service-bus-topics/VSProperties.png
<!--HONumber=42-->
