Le tableau suivant liste les quotas et les limites propres à Azure Event Hubs. Pour plus d’informations sur Event Hubs, consultez [Tarification Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). Pour plus d’informations sur la tarification et d’autres quotas pour Service Bus, voir la présentation [Tarification Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

| Limite | Scope | Type | Comportement en cas de dépassement | Valeur |
|--------------------------------------------------|-------------|--------|------------------------------------------------------------------------------------------------------------------------|----------|
| Nombre d’Event Hubs par espace de noms | Espace de noms | Statique | Les demandes suivantes de création d’un espace de noms seront rejetées. | 10 |
| Nombre de partitions par Event Hub | Entité | Statique | - | 32 |
| Nombre de groupes de consommateurs par Event Hub | Entité | Statique | - | 20 |
| Nombre de connexions AMQP par espace de noms | Espace de noms | Statique | Les demandes suivantes de connexions supplémentaires seront rejetées et une exception sera reçue par le code appelant. | 5 000 |
| Taille d’événement maximale | À l’échelle du système | Statique | - | 256 Ko |
| Nombre de récepteurs non Epoch par groupe de consommateurs | Entité | Statique | - | 5 |
| Période de rétention maximale des données d’événement | Entité | Statique | - | 1 à 7 jours |
| Unités de débit maximales | Espace de noms | Statique | Le dépassement de la limite d’unités de débit entraînera la limitation de vos données et la génération d’une **ServerBusyException**. Vous pouvez demander une plus grande quantité d’unités de débit pour le niveau Standard en complétant un ticket de support. Les unités de débit supplémentaires sont disponibles par blocs de 20 sur la base d’un engagement d’achat ferme. | 20 |

<!---HONumber=AcomDC_0615_2016-->
