>[!NOTE]
>Pour les ressources qui ne sont pas fixes, vous pouvez demander d’augmenter les quotas en ouvrant un ticket de support. Ne créez **pas** d’autres comptes Azure Media Services pour obtenir des limites supérieures.

| Ressource | Limite par défaut | 
| --- | --- | 
| Comptes AMS (Azure Media Services) dans un seul abonnement | 25 (fixe) |
| Unités réservées de média (RU) par compte AMS |25 (S1, S2)<br/>10 (S3) <sup>1</sup> | 
| Travaux par compte AMS | 50 000<sup>2</sup> |
| Tâches chaînées par travail | 30 (fixe) |
| Actifs par compte AMS | 1 000 000|
| Actifs par tâche | 50 |
| Actifs par travail | 100 |
| Localisateurs uniques associés à un actif à un moment donné | 5<sup>4</sup> |
| Canaux en direct par compte AMS  |5|
| Programmes dans un état Arrêté par canal  |50|
| Programmes en cours d’exécution par canal  |3|
| Points de terminaison de diffusion en continu en cours d’exécution par compte AMS|2|
| Unités de diffusion en continu par point de terminaison de diffusion en continu  |10 |
| Comptes de stockage | 1 000<sup>5</sup> (fixe) |
| Stratégies | 1,000,000<sup>6</sup> |
| Taille du fichier| Dans certains scénarios, la taille maximale des fichiers pris en charge pour le traitement dans Media Services est soumise à une limite. <sup>7</sup> |
  
<sup>1</sup> Les unités réservées S3 ne sont pas disponibles en Inde-Ouest.

<sup>2</sup> Ce nombre comprend les travaux en file d’attente, terminés, actifs et annulés. Il n’inclut pas les travaux supprimés. Vous pouvez supprimer les anciens travaux à l’aide de **IJob.Delete** ou de la requête HTTP **DELETE**.

À compter du 1er avril 2017, les enregistrements de travaux dans votre compte de plus de 90 jours seront automatiquement supprimés, ainsi que leurs enregistrements de tâches associés, même si le nombre total d’enregistrements est inférieur au quota maximum. Si vous devez archiver les informations sur le travail/la tâche, vous pouvez utiliser le code décrit [ici](../articles/media-services/media-services-dotnet-manage-entities.md).

<sup>3</sup> Lors d’une requête visant à répertorier les entités de travail, un maximum de 1 000 est renvoyé par requête. Si vous souhaitez effectuer le suivi de l’ensemble des travaux soumis, vous pouvez utiliser top/skip comme décrit dans [Options de requête du système OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Les localisateurs ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Pour accorder différents droits d’accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM). Pour plus d’informations, consultez [cette](../articles/media-services/media-services-content-protection-overview.md) section.

<sup>5</sup> Les comptes de stockage doivent provenir du même abonnement Azure.

<sup>6</sup> Un nombre limite de 1 000 000 a été défini pour les différentes stratégies AMS (par exemple, pour la stratégie de localisateur ou pour ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Vous devez appliquer le même ID de stratégie si vous utilisez toujours le même nombre de jours, les mêmes autorisations d’accès, etc. Pour plus d’informations et un exemple, consultez [cette](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) section.

<sup>7</sup>Si vous chargez du contenu dans une ressource dans Azure Media Services dans l’objectif de le traiter avec des processeurs multimédias de notre service (par exemple, des encodeurs comme Media Encoder Standard et le workflow d’encodeur multimédia premium, ou des moteurs d’analyse comme Face Detector), vous devez être conscient des limites suivantes. 

| Types d’unités réservées de média | Taille maximale du fichier (Go)| 
| --- | --- | 
|S1    | 325|
|S2    | 640|
|S3    | 260|


<!--HONumber=Feb17_HO2-->


