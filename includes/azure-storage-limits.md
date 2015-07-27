<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Limite par défaut</th>
</tr>
<tr>
   <td valign="middle"><p>To par compte de stockage</p></td>
   <td valign="middle"><p>500&#160;To</p></td>
</tr>
<tr>
   <td valign="middle"><p>Taille maximale d'un conteneur d'objets blob, d'une table ou d'une file d'attente</p></td>
   <td valign="middle"><p>500&#160;To</p></td>
</tr>
<tr>
   <td valign="middle"><p>Nombre maximal de conteneurs d'objets blob, de partages de fichiers, de tables, de files d'attente, d'entités ou de messages par compte de stockage</p></td>
   <td valign="middle"><p>La seule limite est celle de 500&#160;To de la capacité du compte de stockage</p></td>
</tr>
<tr>
   <td valign="middle"><p>Taille maximale d'un partage de fichiers</p></td>
   <td valign="middle"><p>5&#160;To</p></td>
</tr>
<tr>
   <td valign="middle"><p>Nombre maximal de fichiers dans un partage de fichiers</p></td>
   <td valign="middle"><p>La seule limite est celle de 5&#160;To de la capacité totale du partage de fichiers</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximum 8&#160;Ko d’opérations E/S par disque persistant (machine virtuelle avec niveau de base)</p></td>
   <td valign="middle"><p>300<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximum 8&#160;Ko d’opérations E/S par disque persistant (machine virtuelle avec niveau Standard)</p></td>
   <td valign="middle"><p>500<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Taux de demandes total (objets de 1&#160;Ko) par compte de stockage</p></td>
   <td valign="middle"><p>Jusqu’à 20&#160;000&#160;opérations E/S, entités par seconde ou messages par seconde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Débit cible pour un objet blob unique</p></td>
   <td valign="middle"><p>Jusqu'à 60&#160;Mo par seconde ou jusqu'à 500&#160;demandes par seconde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Débit cible pour une file d'attente unique (messages de 1&#160;Ko)</p></td>
   <td valign="middle"><p>Jusqu'à 2&#160;000&#160;messages par seconde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Débit cible pour une partition de table unique (entités de 1&#160;Ko)</p></td>
   <td valign="middle"><p>Jusqu'à 2&#160;000&#160;entités par seconde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Débit cible pour un partage de fichier unique (version préliminaire)</p></td>
   <td valign="middle"><p>Jusqu’à 60&#160;Mo par seconde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrée max.<sup>2</sup> par compte de stockage (régions des États-Unis)</p></td>
   <td valign="middle"><p>10&#160;Gbit/s si GRS<sup>3</sup> activé, 20&#160;Gbit/s pour LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sortie max.<sup>2</sup> par compte de stockage (régions des États-Unis)</p></td>
   <td valign="middle"><p>20&#160;Gbit/s si GRS<sup>3</sup> activé, 30&#160;Gbit/s pour LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrée max.<sup>2</sup> par compte de stockage (régions d’Europe et d’Asie)</p></td>
   <td valign="middle"><p>5&#160;Gbit/s si GRS<sup>3</sup> activé, 10&#160;Gbit/s pour LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sortie max.<sup>2</sup> par compte de stockage (régions d’Europe et d’Asie)</p></td>
   <td valign="middle"><p>10&#160;Gbit/s si GRS<sup>3</sup> activé, 15&#160;Gbit/s pour LRS</p></td>
</tr>
</table>

<sup>1</sup>La limite de débit d’appels pour un compte de stockage est de 20 000 opérations E/S. Pour éviter toute limitation lorsqu’une machine virtuelle utilise le nombre maximal d’E/S par disque, vérifiez que le nombre total d’E/S sur l’ensemble des disques durs virtuels des machines virtuelles ne dépasse pas la limite du compte de stockage (20 000 E/S).

Vous pouvez calculer approximativement le nombre de disques fortement sollicités pris en charge par un seul compte de stockage en vous basant sur la limite de transaction. Par exemple, pour une machine virtuelle de niveau de base, le nombre maximal de disques fortement sollicités est d’environ 66 (20 000/300 E/S par disque) et pour une machine virtuelle de niveau Standard, il est d’environ 40 (20 000/500 E/S par disque). Toutefois, le compte de stockage peut prendre en charge un plus grand nombre de disques s’ils ne sont pas tous fortement sollicités en même temps.

<sup>2</sup>*Entrantes* désigne toutes les données (demandes) envoyées à un compte de stockage. *Sortantes* désigne toutes les données (réponses) reçues d’un compte de stockage.

<sup>3</sup>GRS désigne un stockage géo-redondant, tandis que LRS désigne un stockage localement redondant.

<!---HONumber=July15_HO3-->