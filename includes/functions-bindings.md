| Type | Service | Déclencheur* | Entrée | Sortie |  
| --- | --- | --- | --- | --- |  
| [Planification](../articles/azure-functions/functions-bindings-timer.md)  |Azure Functions |✔ | | |  
| [HTTP (REST ou webhook)](../articles/azure-functions/functions-bindings-http-webhook.md) |Azure Functions |✔ |  |✔\** |  
| [Stockage d’objets blob](../articles/azure-functions/functions-bindings-storage-blob.md) |Azure Storage |✔ |✔ |✔ |  
| [Événements](../articles/azure-functions/functions-bindings-event-hubs.md) |Hubs d'événements Azure |✔ | |✔ |  
| [Files d’attente](../articles/azure-functions/functions-bindings-storage-queue.md) |Azure Storage |✔ | |✔ |  
| [Files d’attente et rubriques](../articles/azure-functions/functions-bindings-service-bus.md) |Azure Service Bus |✔ | |✔ |  
| [Tables de stockage](../articles/azure-functions/functions-bindings-storage-table.md) |Azure Storage | |✔ |✔ |  
| [Tables SQL](../articles/azure-functions/functions-bindings-mobile-apps.md) |Azure Mobile Apps | |✔ |✔ |  
| [NoSQL DB](../articles/azure-functions/functions-bindings-documentdb.md) | Azure Cosmos DB | |✔ |✔ |  
| [Notifications Push](../articles/azure-functions/functions-bindings-notification-hubs.md) |Azure Notification Hubs | | |✔ |  
| [Texte SMS Twilio](../articles/azure-functions/functions-bindings-twilio.md) |Twilio | | |✔ |
| [Messagerie SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md) | SendGrid | | |✔ |

(\* -Tous les déclencheurs comportent des données d’entrée associées)

(\** - La liaison de sortie HTTP nécessite un déclencheur HTTP)


