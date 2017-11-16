```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|maxPollingInterval|60000|Intervalle maximal (en millisecondes) entre les interrogations de la file d’attente.| 
|visibilityTimeout|0|Intervalle de temps entre les nouvelles tentatives en cas d’échec du traitement d’un message.| 
|batchSize|16|Nombre de messages en file d’attente à récupérer et à traiter en parallèle. La valeur maximale est de 32.| 
|maxDequeueCount|5|Nombre de tentatives de traitement d’un message avant de le placer dans la file d’attente de messages incohérents.| 
|newBatchThreshold|batchSize/2|Seuil auquel un nouveau lot de messages est extrait.| 
