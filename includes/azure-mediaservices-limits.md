<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Limite par défaut</th>
   <th align="left" valign="middle">Limite maximale</th>
</tr>
<tr>
   <td valign="middle"><p>Comptes AMS (Azure Media Services) dans un seul abonnement</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>Actifs par compte AMS</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>1&#160;000&#160;000</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tâches chaînées par travail</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>30</p></td>
</tr>
<tr>
   <td valign="middle"><p>Actifs par tâche</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Actifs par travail</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Travaux par compte AMS </p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50&#160;000<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Localisateurs uniques associés à un actif à un moment donné</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>5<sup>4</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Canaux en direct par compte AMS </p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Programmes dans un état arrêté par canal </p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Programmes en cours d'exécution par canal </p></td>
   <td valign="middle"><p>3</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Points de terminaison de diffusion en continu en cours d'exécution par compte AMS</p></td>
   <td valign="middle"><p>2</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Unités de diffusion en continu par point de terminaison de diffusion en continu </p></td>
   <td valign="middle"><p>10 </p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Unités d'encodage par compte AMS </p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup> Vous pouvez demander la mise à jour des limites pour ce quota en ouvrant un ticket de support. Ne créez pas d'autres comptes AMS pour augmenter les limites, mais envoyez un ticket de support.

<sup>2</sup> Ce nombre comprend les travaux en file d'attente, terminés, actifs et annulés. Il n'inclut pas les travaux supprimés. Vous pouvez supprimer les anciens travaux à l'aide de **IJob.Delete** ou de la requête HTTP **DELETE**.

<sup>3</sup> Lors d'une requête visant à répertorier les entités de travail, un maximum de 1 000 est renvoyé par requête. Si vous souhaitez effectuer le suivi de l'ensemble des travaux soumis, vous pouvez utiliser top/skip comme décrit dans [Options de requête du système OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Les localisateurs ne sont pas conçus pour gérer le contrôle d'accès par utilisateur. Pour accorder différents droits d'accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM).

<!---HONumber=62-->