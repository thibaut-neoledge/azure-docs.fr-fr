Nous allons ici utiliser le déclencheur **Service Bus - Réception d’un message dans une file d’attente** pour initier un flux de travail logique application lorsqu’un nouvel élément est ajouté à une file d’attente Service Bus.

>[AZURE.NOTE]Vous serez invité à vous connecter avec votre chaîne de connexion Service Bus si vous n’avez pas encore créé une connexion à Service Bus.

1. Saisissez *service bus* dans la zone de recherche sur le concepteur d’applications logiques, puis sélectionnez le déclencheur **Service Bus - Réception d’un message dans une file d’attente**.  
![Image de déclencheur Service Bus 1](./media/connectors-create-api-servicebus/trigger-1.png)   
- Le contrôle **Réception d’un message dans une file d’attente** s’affiche.  
![Image de déclencheur Service Bus 2](./media/connectors-create-api-servicebus/trigger-2.png)   
- Saisissez le nom de la file d’attente Service Bus que vous souhaitez que le déclencheur surveille.  
![Image de déclencheur Service Bus 3](./media/connectors-create-api-servicebus/trigger-3.png)   

À ce stade, votre application logique a été configurée avec un déclencheur. Lorsqu’un nouvel élément est reçu dans la file d’attente que vous avez sélectionnée, le déclencheur lance une série d'autres déclencheurs et actions dans le flux de travail.

<!---HONumber=AcomDC_0810_2016-->