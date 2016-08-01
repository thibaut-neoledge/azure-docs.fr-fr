Ressource|Limite par défaut|Limite maximale
---|---|---
Comptes AMS (Azure Media Services) dans un seul abonnement||25
Actifs par compte AMS||1 000 000<sup>1</sup>
Tâches chaînées par travail||30
Actifs par tâche||50
Actifs par travail||100
Travaux par compte AMS ||50 000<sup>2</sup>
Localisateurs uniques associés à un actif à un moment donné||5<sup>4</sup>
Canaux en direct par compte AMS </p></td>|5</p></td>|N/A<sup>1</sup>
Programmes dans un état Arrêté par canal </p></td>|50</p></td>|N/A<sup>1</sup>
Programmes en cours d’exécution par canal </p></td>|3</p></td>|3
Points de terminaison de diffusion en continu en cours d’exécution par compte AMS</p></td>|2</p></td>|N/A<sup>1</sup>
Unités de diffusion en continu par point de terminaison de diffusion en continu </p></td>|10 </p></td>|N/A<sup>1</sup>
Unités d’encodage par compte AMS </p></td>|25</p></td>|N/A<sup>1</sup>
des comptes de stockage ; | |1 000<sup>5</sup>
Stratégies || 1 000 000<sup>6</sup>

<sup>1</sup> Vous pouvez demander la mise à jour des limites pour ce quota en ouvrant un ticket de support. Ne créez pas d’autres comptes AMS pour augmenter les limites, mais envoyez un ticket de support.

<sup>2</sup> Ce nombre comprend les travaux en file d’attente, terminés, actifs et annulés. Il n’inclut pas les travaux supprimés. Vous pouvez supprimer les anciens travaux à l’aide de **IJob.Delete** ou de la requête HTTP **DELETE**.

<sup>3</sup> Lors d’une requête visant à répertorier les entités de travail, un maximum de 1 000 est renvoyé par requête. Si vous souhaitez effectuer le suivi de l’ensemble des travaux soumis, vous pouvez utiliser top/skip comme décrit dans [Options de requête du système OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Les localisateurs ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Pour accorder différents droits d’accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM).

<sup>5</sup> Les comptes de stockage doivent provenir du même abonnement Azure.

<sup>6</sup> Un nombre limite de 1 000 000 a été défini pour les différentes stratégies AMS (par exemple, pour la stratégie de recherche ou pour ContentKeyAuthorizationPolicy). Vous devez appliquer le même ID de stratégie si vous utilisez toujours le même nombre de jours, les mêmes autorisations d’accès, etc.

<!---HONumber=AcomDC_0720_2016-->