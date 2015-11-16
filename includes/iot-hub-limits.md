Le tableau suivant répertorie les limites associées aux différents niveaux de service (S1, S2, F1). Pour plus d'informations sur le coût de chaque *unité* dans chaque couche, consultez [la tarification du IoT Hub](http://azure.microsoft.com/pricing/details/iot-hub/).

| Ressource | S1 Standard | S2 Standard | F1 Gratuit |
| -------- | ----------- | ----------- | ------- |
| Périphériques par unité | 500 | 500 | 10 |
| Messages par jour | 50 000 | 1 500 000 | 3 000 |
| Nombre maximum d'unités | 200 | 200 | 1 |
| Mises à jour de périphérique (création, mise à jour, <br/> suppression) par unité par jour | 1 100 | 1 100 | 1 100 |

> [AZURE.NOTE]Si vous prévoyez d'utiliser plus de 200 unités avec un hub de niveau S1 ou S2, veuillez contacter le support Microsoft.

Le tableau suivant répertorie les limites qui s'appliquent aux ressources IoT Hub :

| Ressource | Limite |
| -------- | ----- |
| Nombre d'IoT Hubs maximal par abonnement Azure | 10 |
| Nombre maximal d'identités de périphériques<br/> renvoyées dans un seul appel | 1 000 |
| Délai de rétention maximal des messages de l'IoT Hub | 7 jours |
| Taille maximale du message Appareil vers cloud | 256 KB |
| Taille maximale du lot Appareil vers cloud | 256 KB |
| Nombre maximal de messages dans le lot Appareil vers cloud | 500 |
| Taille maximale du message Cloud vers appareil | 64 Ko |
| Durée de vie maximale des messages Cloud vers appareil | 2 jours |
| Nombre de remises maximal pour les messages <br/> Cloud vers appareil | 100 |
| Nombre de remises maximal pour les messages de commentaire <br/> en réponse à un message Cloud vers appareil | 100 |
| Durée de vie maximale des messages de commentaire <br/> en réponse à un message Cloud vers appareil | 2 jours |

Le service IoT Hub IoT limite les demandes cas de dépassement des quotas suivants :

| Limitation | Valeur par hub |
| -------- | ------------- |
| Opérations de registre d'identité <br/> (création, récupération, création de listes, mise à jour, suppression),<br/> importation/exportation en bloc ou individuelle | 100/min/unité, jusqu’à 5 000/min |
| Connexions d’appareils | 100/s/unité |
| Envois de messages Appareil vers cloud | 2000/min/unité (pour S2), 60/min/unité (pour S1) <br/> 100/sec minimum |
| Opérations Cloud vers appareil <br/> (envois, réceptions, commentaires) | 100/min/unité |

<!---HONumber=Nov15_HO2-->