Le tableau suivant décrit chaque quota, limite, valeur par défaut et limitation principaux dans Azure Scheduler.

| Ressource | Description de la limite |
| --- | --- |
| **Taille du travail** |La taille maximale du travail est de 16 Ko. Si une commande PUT ou PATCH génère un travail qui dépasse ces limites, un code d'état 400 demande incorrecte est retourné. |
| **Taille d'URL de la requête** |La taille maximale de l'URL de la requête est de 2 048 caractères. |
| **Taille de l'en-tête d'agrégat** |La taille maximale de l'en-tête d'agrégat est de 4 096 caractères. |
| **Nombre d'en-têtes** |Le nombre maximal d'en-têtes est 50. |
| **Taille du corps** |La taille maximale du corps est 8 192 caractères. |
| **Période de récurrence** |La période de récurrence maximale est 18 mois. |
| **Délai jusqu'à l'heure de début** |Le « délai jusqu'à l'heure de début » maximal est 18 mois. |
| **Historique des travaux** |Le corps de réponse maximal stocké dans l'historique des travaux est 2 048 octets. |
| **Fréquence** |Le quota de fréquence maximale par défaut est 1 heure dans une collection de travaux gratuite et 1 minute dans une collection de travaux standard. La fréquence maximale est configurable dans une collection de travaux pour être inférieure à la valeur maximale. Tous les travaux dans la collection de travaux sont limités la valeur définie dans la collection de travaux. Si vous essayez de créer un travail avec une fréquence supérieure à la fréquence maximale de la collection de travaux, la requête échoue avec un code d'état 409 conflit. |
| **Travaux** |Le quota de travaux maximum par défaut est 5 travaux dans une collection de travaux gratuite et 50 travaux dans une collection de travaux standard. Le nombre maximal de travaux est configurable dans une collection de travaux. Tous les travaux dans la collection de travaux sont limités la valeur définie dans la collection de travaux. Si vous essayez de créer plus de travaux que le quota maximal, la requête échoue avec un code d'état 409 conflit. |
| **Conservation de l'historique des travaux** |L’historique des travaux est conservé pendant 2 mois maximum ou jusqu’à ce qu’il atteigne 1 000 exécutions. |
| **Conservation des travaux terminés et ayant généré une erreur** |Les travaux terminés et ayant généré une erreur sont conservés pendant 60 jours. |
| **Délai d'expiration** |Il existe un délai d'expiration de requête (non modifiable) statique de 30 secondes pour les actions HTTP. Pour les opérations en cours d'exécution plus longues, suivez les protocoles HTTP asynchrones ; par exemple, retourner un 202 immédiatement, mais continuer de travailler en arrière-plan. |

<!---HONumber=AcomDC_0316_2016-->