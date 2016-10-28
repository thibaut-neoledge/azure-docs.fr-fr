Le tableau suivant répertorie les limites associées aux différents niveaux de service (S1, S2, S3 et F1). Pour plus d'informations sur le coût de chaque *unité* dans chaque couche, consultez [la tarification du IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Ressource | S1 Standard | S2 Standard | S3 Standard | F1 Gratuit |
| -------- | ----------- | ----------- | ----------- | ------- |
| Messages par jour | 400 000 | 6 000 000 | 300 000 000 | 8 000 |
| Nombre maximum d'unités | 200 | 200 | 200 | 1 |

> [AZURE.NOTE] Si vous prévoyez d’utiliser plus de 200 unités avec un hub de niveau S1, S2 ou S3, veuillez contacter le support Microsoft.

Le tableau suivant répertorie les limites qui s'appliquent aux ressources IoT Hub :

| Ressource | Limite |
| -------- | ----- |
| Nombre maximal d’IoT Hubs payants par abonnement Azure | 10 |
| Nombre maximal d’IoT Hubs gratuits par abonnement Azure | 1 |
| Nombre maximal d’identités de périphériques<br/> renvoyées dans un seul appel | 1 000 |
| Conservation maximale des messages IoT Hub pour les messages de l’appareil vers le cloud | 7 jours |
| Taille maximale du message appareil-à-cloud | 256 KB |
| Taille maximale du lot appareil-à-cloud | 256 KB |
| Nombre maximal de messages dans le lot appareil-à-cloud | 500 |
| Taille maximale du message cloud-à-appareil | 64 Ko |
| Durée de vie maximale des messages cloud-à-appareil | 2 jours |
| Nombre de remises maximal pour les messages <br/> Cloud vers appareil | 100 |
| Nombre de remises maximal pour les messages de commentaire <br/> en réponse à un message Cloud vers appareil | 100 |
| Durée de vie maximale des messages de commentaire <br/> en réponse à un message Cloud vers appareil | 2 jours |

> [AZURE.NOTE] Si vous avez besoin de plus de 10 IoT Hubs payants dans un abonnement Azure, contacter le support technique Microsoft.

Le service IoT Hub limite les demandes cas de dépassement des quotas suivants :

| Limitation | Valeur par hub |
| -------- | ------------- |
| Opérations de registre d’identité <br/> (création, récupération, création de listes, mise à jour, suppression), <br/> importation / exportation en bloc ou individuelle | 5000/min/unité (pour S3) <br/> 100/min/unité (pour S1 et S2). |
| Connexions d’appareils | 6 000/s/unité (pour S3), 120/s/unité (pour S2), 12/s/unité (pour S1). <br/>Minimum de 100/s. |
| Envois appareil-à-cloud | 6 000/s/unité (pour S3), 120/s/unité (pour S2), 12/s/unité (pour S1). <br/>Minimum de 100/s. |
| Envois cloud-à-appareil | 5 000/min/unité (pour S3), 100/min/unité (pour S1 et S2). |
| Réceptions cloud-à-appareil | 50 000/min/unité (pour S3), 1000/min/unité (pour S1 et S2). |
| Opérations de téléchargement de fichier | 5 000 notifications de téléchargement de fichier/min/unité (pour S3), 100 notifications de téléchargement de fichier/min/unité (pourS1 et S2). <br/> 10 000 URI de signature d’accès partagé peuvent être générés à la fois pour un compte de stockage.<br/> 10 URI de signature d’accès partagé/appareil peuvent être générés à la fois. |

<!---HONumber=AcomDC_0824_2016-->