>[!NOTE]
>Pour les ressources qui ne sont pas fixes, vous pouvez demander d’augmenter les quotas en ouvrant un ticket de support. Ne créez **pas** d’autres comptes Azure Media Services pour obtenir des limites supérieures.

| Ressource | Limite par défaut | 
| --- | --- | 
| Comptes AMS (Azure Media Services) dans un seul abonnement | 25 (fixe) |
| Actifs par compte AMS | 1 000 000|
| Tâches chaînées par travail | 30 (fixe) |
| Actifs par tâche | 50 |
| Actifs par travail | 100 |
| Travaux par compte AMS | 50 000<sup>2</sup> |
| Localisateurs uniques associés à un actif à un moment donné | 5<sup>4</sup> |
| Canaux en direct par compte AMS  |5|
| Programmes dans un état Arrêté par canal  |50|
| Programmes en cours d’exécution par canal  |3|
| Points de terminaison de diffusion en continu en cours d’exécution par compte AMS|2|
| Unités de diffusion en continu par point de terminaison de diffusion en continu  |10 |
| Unités réservées de média (RU) par compte AMS |25 (S1, S2)<br/>10 (S3) <sup>1</sup> | 
| Comptes de stockage | 1 000<sup>5</sup> (fixe) |
| Stratégies | |1,000,000<sup>6</sup> |
 
<sup>1</sup> Les unités réservées S3 ne sont pas disponibles en Inde-Ouest.

<sup>2</sup> Ce nombre comprend les travaux en file d’attente, terminés, actifs et annulés. Il n’inclut pas les travaux supprimés. Vous pouvez supprimer les anciens travaux à l’aide de **IJob.Delete** ou de la requête HTTP **DELETE**.

<sup>3</sup> Lors d’une requête visant à répertorier les entités de travail, un maximum de 1 000 est renvoyé par requête. Si vous souhaitez effectuer le suivi de l’ensemble des travaux soumis, vous pouvez utiliser top/skip comme décrit dans [Options de requête du système OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Les localisateurs ne sont pas conçus pour gérer le contrôle d'accès par utilisateur. Pour accorder différents droits d’accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM). Pour plus d’informations, consultez [cette](../articles/media-services/media-services-content-protection-overview.md) section.

<sup>5</sup> Les comptes de stockage doivent provenir du même abonnement Azure.

<sup>6</sup> Un nombre limite de 1 000 000 a été défini pour les différentes stratégies AMS (par exemple, pour la stratégie de localisateur ou pour ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Vous devez appliquer le même ID de stratégie si vous utilisez toujours le même nombre de jours, les mêmes autorisations d’accès, etc.



<!--HONumber=Jan17_HO2-->


