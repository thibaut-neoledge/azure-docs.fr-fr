Il existe certaines limites au nombre de mesures et d’événements par application (c'est-à-dire, par clé d'instrumentation).

Les limites varient selon le [niveau de tarification](https://azure.microsoft.com/pricing/details/application-insights/) que vous choisissez.

**Ressource** | **Limite par défaut** | **Limite maximale**
-------- | ------------- | -------------
Points de données de session<sup>1, 2</sup> par mois | illimitée | 
Nombre total de points de données par mois pour les demandes, événements, dépendances, traces, exceptions et pages affichées | 5 millions | 50 millions<sup>3</sup>
Débit de données [Suivi et journalisation](../articles/application-insights/app-insights-search-diagnostic-logs.md) | 200 dp/s | 500 dp/s
Débit de données d’[exceptions](../articles/application-insights/app-insights-asp-net-exceptions.md) | 50 dp/s | 50 dp/s
Débit de données total pour les demandes, événements, dépendances et informations de télémétrie des pages affichées | 200 dp/s | 500 dp/s
Conservation des données brutes pour [Search](../articles/application-insights/app-insights-diagnostic-search.md) et [Analytics](../articles/application-insights/app-insights-analytics.md) | 7 jours
Conservation des données agrégées pour [Metrics Explorer](../articles/application-insights/app-insights-metrics-explorer.md) | 90 jours
Nombre de noms de [Propriétés](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Longueur du nom de propriété | 150 | 
Longueur de la valeur de propriété | 8 192 | 
Longueur des messages de trace et d’exception | 10000 |
Nombre de noms de [Mesures](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Longueur du nom de la mesure | 150 | 
[Tests de disponibilité](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> Un point de données est une valeur métrique individuelle ou un événement, avec les mesures et les propriétés jointes.

<sup>2</sup> Un point de données de session consigne le début ou la fin d’une session, ainsi que l’identité de l’utilisateur.

<sup>3</sup> Vous pouvez acheter une capacité supplémentaire au-delà de 50 millions.
 
[Tarification et quotas dans Application Insights](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0803_2016-->