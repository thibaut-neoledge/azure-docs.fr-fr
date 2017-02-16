Il existe certaines limites au nombre de mesures et d’événements par application (c'est-à-dire, par clé d'instrumentation). 

Les limites varient selon le [plan de tarification](https://azure.microsoft.com/pricing/details/application-insights/) que vous choisissez.

| **Ressource** | **Limite par défaut** | **Remarque :**
| --- | --- | --- |
| Total des données par jour | 500 Go | Vous pouvez réduire cette quantité en définissant une limite. S’il vous faut une plus grande quantité, envoyez un e-mail à l’adresse suivante : AIDataCap@microsoft.com 
| Données gratuites par mois<br/> (plan tarifaire de base) | 1 Go | Données supplémentaires payantes par Go
| Limitation | 16 000 événements par seconde | Mesuré par minute. 
| Conservation des données | 90 jours | pour [Recherche](../articles/application-insights/app-insights-diagnostic-search.md), [Analytics](../articles/application-insights/app-insights-analytics.md) et [Metrics Explorer](../articles/application-insights/app-insights-metrics-explorer.md)
| Rétention des résultats détaillés du [test de disponibilité à plusieurs étapes](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 jours | Résultats détaillés de chaque étape
| Nombre de noms de [propriétés](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) et de [mesures](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)<sup>2</sup> | 200 | 
| Longueur des noms de propriétés et de mesures | 150 |
| Longueur de chaîne de valeur de propriété | 8 192 |
| Valeurs distinctes pour les propriétés<sup>3,4</sup> | 100 | >100 = > ne peut pas utiliser de propriété comme filtre dans Metrics Explorer
| Longueur des messages de trace et d’exception | 10000 |
| Nombre de [tests de disponibilité](../articles/application-insights/app-insights-monitor-web-app-availability.md) par application  | 10 |

1. Tous ces chiffres s’entendent par clé d’instrumentation.
2. Les noms des mesures sont définis dans TrackMetric et dans le paramètre de mesure d’autres appels Track*(). Les noms de mesure sont globaux pour chaque clé d’instrumentation.
3. Les propriétés peuvent être utilisées pour le filtrage et le regroupement uniquement lorsqu’il y a moins de 100 valeurs uniques pour chaque propriété. Une fois que le nombre de valeurs uniques dépasse 100, vous pouvez encore rechercher la propriété, mais vous ne pouvez plus l’utiliser pour les filtres ou le regroupement.
4. Les propriétés standard telles que le nom de la requête et l'URL de la page sont limitées à 1 000 valeurs uniques par semaine. Au-delà de 1 000 valeurs uniques, les valeurs supplémentaires sont marquées comme « Autres valeurs ». Les valeurs d’origine peuvent toujours être utilisées pour une recherche en texte intégral et pour le filtrage.


[Tarification et quotas dans Application Insights](../articles/application-insights/app-insights-pricing.md)

<!--HONumber=Jan17_HO3-->


