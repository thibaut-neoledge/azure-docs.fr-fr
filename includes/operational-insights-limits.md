
>[!NOTE]
>Log Analytics s’appelait auparavant Operational Insights.
>
>

Les limites suivantes s’appliquent aux ressources Log Analytics par abonnement :

| Ressource | Limite par défaut | Commentaires
| --- | --- | --- |
| Nombre d’espaces de travail gratuits par abonnement | 10 | Cette limite ne peut pas être augmentée. |
| Nombre d’espaces de travail payants par abonnement | N/A | Vous êtes limité par le nombre de ressources au sein d’un groupe de ressources et le nombre de groupes de ressources par abonnement. | 


Les limites suivantes s’appliquent à chaque espace de travail Log Analytics :

|  | Gratuit | Standard | Premium | Standalone | OMS |
| --- | --- | --- | --- | --- | --- |
| Volume de données collecté par jour |500 MO<sup>1</sup> |Aucun |Aucun | Aucun | Aucun
| Période de rétention des données |7 jours |1 mois |12 mois | 1 mois<sup>2</sup> | 1 mois <sup>2</sup>|

<sup>1</sup> Lorsque les clients atteignent leur limite quotidienne de transfert de données de 500 Mo, l’analyse des données s’interrompt et reprend au début de la journée suivante. Les journées sont basées sur l’heure UTC.

<sup>2</sup> La période de rétention de données pour les plans de tarification autonomes et OMS peut être augmentée à 730 jours.

| Catégorie | Limites | Commentaires
| --- | --- | --- |
| API du collecteur de données | La taille maximale d’une publication est de 30 Mo<br>La taille maximale des valeurs de champ est de 32 Ko | Fractionner les volumes plus importants en plusieurs publications<br>Les champs de plus de 32 Ko de champs sont tronqués. |
| API de recherche | 5 000 enregistrements renvoyés pour des données non agrégées<br>500 000 enregistrements pour des données agrégées | La recherche de données agrégées inclut la commande `measure`
 


<!--HONumber=Feb17_HO3-->


