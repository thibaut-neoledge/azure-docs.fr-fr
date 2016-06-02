## Création d’un concentrateur d’événements

1. Connectez-vous au [portail Azure Classic][] et cliquez sur **NOUVEAU** en bas de l’écran.

2. Cliquez successivement sur **App Services**, **Service Bus**, **Concentrateur d’événements**, puis sur **Création rapide**.

	![][1]

3. Attribuez un nom à votre concentrateur d’événements, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur d’événements**.

	![][2]

4. Si vous n’avez pas explicitement sélectionné un espace de noms existant dans une région donnée, le portail crée un espace de noms pour vous (généralement ***nom du concentrateur d’événements*-ns**). Cliquez sur cet espace de noms (dans cet exemple, **eventhub-ns**).

	![][3]

5. En bas de la page, cliquez sur **Informations de connexion**. Cliquez sur le bouton Copier (comme indiqué dans l’illustration suivante) pour copier la chaîne de connexion **RootManageSharedAccessKey** dans le Presse-papiers. Enregistrez cette chaîne de connexion pour l’utiliser plus tard dans ce didacticiel.

	![][4]

Votre concentrateur d’événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements. 

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-conn-str1.png


