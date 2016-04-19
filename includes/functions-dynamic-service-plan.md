## Plan de service dynamique

Dans le Plan de service dynamique, vos applications de fonction sont affectées à une instance d’application de fonction. Si nécessaire, des instances supplémentaires sont ajoutées dynamiquement. Ces instances peuvent s’étendre sur plusieurs ressources de calcul afin de tirer le meilleur parti de l’infrastructure Azure disponible. En outre, les fonctions sont exécutées en parallèle, réduisant ainsi le temps total nécessaire pour traiter les demandes. Le temps d’exécution de chaque fonction est ajouté (en secondes) et agrégé par l’application de fonction conteneur. Le coût est calculé en fonction du nombre d’instances, de la taille de leur mémoire et du temps d’exécution total mesuré en gigaoctets par seconde. Cette option est donc idéale si vos besoins de calcul sont intermittents ou si les durées de vos tâches ont tendance à être très courtes. Elle vous permet en effet de payer pour les ressources de calcul uniquement lorsqu’elles sont réellement utilisées.

### Niveau de mémoire

Selon les fonctions dont vous avez besoin, vous pouvez sélectionner la quantité de mémoire nécessaire pour les exécuter dans l’application de fonction (conteneur de fonctions). La taille de la mémoire peut aller de **128 Mo à 1 536 Mo**. La taille de la mémoire sélectionnée correspond à la plage de travail dont toutes les fonctions qui font partie de votre application de fonction ont besoin. Si votre code nécessite plus de mémoire que la taille sélectionnée, l’instance de l’application de fonction est arrêtée en raison d’un manque de mémoire disponible.

### Mise à l'échelle

La plateforme de fonctions Azure (Azure Functions) évalue les besoins de trafic sur la base des déclencheurs configurés pour décider quand monter ou descendre en puissance. La granularité de la mise à l’échelle est l’application de fonction. Dans ce cas, la montée en puissance signifie l’ajout d’autres instances d’une application de fonction. À l’inverse, lorsque le trafic diminue, les instances de l’application de fonction sont désactivées. Leur nombre atteint zéro si aucune instance n’est en cours d’exécution.

### Consommation des ressources et facturation

En mode dynamique, l’allocation des ressources ne s’effectue pas de la même manière qu’avec le plan App Service standard. Par conséquent, le modèle de consommation est également différent et permet un « paiement à l’utilisation ». La consommation est rapportée par application de fonction uniquement lorsque le code est exécuté. Elle est calculée en multipliant la taille mémoire (en Go) par la durée totale d’exécution (en secondes) de toutes les fonctions exécutées au sein de cette application de fonction. L’unité de consommation est **Go/s (gigaoctet/seconde)**.

<!---HONumber=AcomDC_0406_2016-->