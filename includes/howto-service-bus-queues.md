<a id="what-are-service-bus-queues"></a>

## Présentation des files d'attente Service Bus

Les files d’attente Service Bus prennent en charge un modèle de **communication de messagerie répartie**
. Lors de l'utilisation de files d'attente, les composants d'une application distribuée ne
communiquent pas directement entre eux ; ils échangent plutôt des messages via
une file d'attente, qui fait office d'intermédiaire. Un producteur de messages (expéditeur)
remet un message à la file d'attente, puis poursuit son traitement.
De manière asynchrone, un consommateur de message (destinataire) extrait le message de la
file d'attente, puis le traite. L'expéditeur ne doit pas forcément attendre une réponse
du destinataire pour continuer à traiter et à envoyer d'autres
messages. Les files d'attente permettent la remise de messages à un ou plusieurs destinataires concurrents sur le principe du **premier entré, premier sorti (FIFO)**.
 Autrement dit, les messages sont en général
reçus et traités par les destinataires dans l'ordre dans lequel ils ont été
ajoutés à la file d'attente ; chaque message est reçu et traité par un
seul consommateur de message.

![QueueConcepts][]

Les files d'attente Service Bus constituent une technologie à usage généraliste utilisable
dans une grande diversité de situations :

-   Communication entre les rôles web et les rôles de travail dans une application multiniveau
    Azure
-   Communication entre les applications locales et les applications hébergées par Azure
    dans une solution hybride
-   Communication entre les composants d'une application distribuée
    exécutée en local dans différentes entreprises ou dans différents services d'une
    entreprise

L'utilisation de files d'attente permet une meilleure montée en charge de vos applications et
une plus grande résilience dans votre architecture.

<a id="create-a-service-namespace"></a>

## Création d'un espace de noms de service

Pour commencer à utiliser les files d'attente Service Bus dans Azure, vous devez d'abord
créer un espace de noms de service. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms de service :

1.  Connectez-vous au [portail de gestion Azure][].

2.  Dans le volet de navigation gauche du portail de gestion, cliquez sur
    **Service Bus**.

3.  Dans le volet inférieur du portail de gestion, cliquez sur **Créer**.
    ![][]

4.  Dans la boîte de dialogue **Ajouter un nouvel espace de noms**, entrez un nom d'espace de noms.
    Le système vérifie immédiatement la disponibilité du nom.
    ![][1]

5.  Après vous être assuré de la disponibilité du nom de l'espace de noms,
    choisissez le pays ou la région où votre espace de noms doit être hébergé
    (veillez à utiliser le même pays ou la même région que celui ou celle
    où vous déployez vos ressources de calcul).

    IMPORTANT : choisissez la **même région** que celle que vous prévoyez
    de sélectionner pour le déploiement de votre application. Vous bénéficiez ainsi des meilleures performances.

6.  Cliquez sur la coche. Le système crée l'espace de
    noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes
    afin que le système approvisionne des ressources pour votre compte.

    ![][2]

L'espace de noms que vous avez créé apparaît alors dans le portail de gestion.
Son activation peut prendre un peu de temps. Attendez que l'état soit **Actif** avant de
continuer.

<a id="obtain-default-credentials"></a>

## Obtention d'informations d'identification de gestion par défaut pour l'espace de noms


Pour effectuer des opérations de gestion, comme la création d'une file d’attente sur
le nouvel espace de noms, vous devez obtenir les informations de gestion
associées. Vous pouvez obtenir ces informations d’identification du Portail de gestion ou de l’Explorateur de serveurs de Visual Studio.

### Pour obtenir les informations de gestion du portail

1.  Dans le volet de navigation gauche, cliquez sur le nœud **Service Bus** pour
    afficher la liste des espaces de noms disponibles :
    ![][3]

2.  Sélectionnez l'espace de noms que vous venez de créer dans la liste affichée :
    ![][4]

3.  Cliquez sur **Connection Information**.
    ![][5]

4.  Dans la boîte de dialogue **Access connection information**, recherchez les entrées **Default Issuer** et **Default Key**. Notez ces valeurs, car elles sont utilisées ci-dessous pour effectuer des opérations avec l’espace de noms.

### Pour obtenir les informations de gestion de l’Explorateur de serveurs

Pour obtenir les informations de connexion en utilisant Visual Studio plutôt que le Portail de gestion, suivez la procédure décrite [ici][], dans la section intitulé **Connexion à Azure à partir de Visual Studio**. Lorsque vous vous connectez à Azure, le nœud **Service Bus** sous l’arborescence **Microsoft Azure** dans l’Explorateur de serveurs est automatiquement rempli par les espaces de noms que vous avez déjà créés. Cliquez avec le bouton droit sur un espace de noms, puis cliquez sur **Properties** pour que la chaîne de connexion et les autres métadonnées associées à cet espace de noms s’affichent dans le volet **Properties** de Visual Studio.

Notez la valeur **SharedAccessKey** ou copiez-la dans le Presse-papiers :

![][6]

  [QueueConcepts]: ./media/howto-service-bus-queues/sb-queues-08.png
  [portail de gestion Azure]: http://manage.windowsazure.com
  []: ./media/howto-service-bus-queues/sb-queues-03.png
  [1]: ./media/howto-service-bus-queues/sb-queues-04.png
  [2]: ./media/howto-service-bus-queues/getting-started-multi-tier-27.png
  [3]: ./media/howto-service-bus-queues/sb-queues-13.png
  [4]: ./media/howto-service-bus-queues/sb-queues-09.png
  [5]: ./media/howto-service-bus-queues/sb-queues-06.png
  [ici]: http://http://msdn.microsoft.com/en-us/library/windowsazure/ff687127.aspx
  [6]: ./media/howto-service-bus-queues/VSProperties.png
