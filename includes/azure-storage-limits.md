<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource<sup>1</sup></th>
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
   <td valign="middle"><p>Maximum 8&#160;Ko d'opérations E/S par disque persistant (niveau De base)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximum 8&#160;Ko d'opérations E/S par disque persistant (niveau Standard)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Taux de demandes total (objets de 1&#160;Ko) par compte de stockage</p></td>
   <td valign="middle"><p>Jusqu'à 20&#160;000&#160;entités ou messages par seconde</p></td>
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
   <td valign="middle"><p>Entrée max. par compte de stockage (régions des États-Unis)</p></td>
   <td valign="middle"><p>10&#160;Gbit/s si GRS<sup>3</sup> activé, 20&#160;Gbit/s pour LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sortie max. par compte de stockage (régions des États-Unis)</p></td>
   <td valign="middle"><p>20&#160;Gbit/s si GRS<sup>3</sup> activé, 30&#160;Gbit/s pour LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrée max. par compte de stockage (régions d'Europe et d'Asie)</p></td>
   <td valign="middle"><p>5&#160;Gbit/s si GRS<sup>3</sup> activé, 10&#160;Gbit/s pour LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sortie max. par compte de stockage (régions d'Europe et d'Asie)</p></td>
   <td valign="middle"><p>10&#160;Gbit/s si GRS<sup>3</sup> activé, 15&#160;Gbit/s pour LRS</p></td>
</tr>
</table>

<sup>1</sup>Pour plus de détails sur ces limites, consultez la rubrique [Objectifs de performances et d'extensibilité du stockage Azure](../articles/storage/storage-scalability-targets.md).

<sup>2</sup>Pour les machines virtuelles utilisées au niveau De base, ne placez pas plus de 66 disques durs virtuels à haute utilisation dans un compte de stockage, afin d'éviter la limite de débit d'appels fixée à 20 000 au total (20 000/300). Pour les machines virtuelles utilisées dans le niveau Standard, ne placez pas plus de 40 disques durs virtuels à haute utilisation dans un compte de stockage (20 000/500). Pour plus d'informations, consultez la page [Tailles de machine virtuelle et de service cloud pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

<sup>3</sup>GRS signifie [Geo Redundant Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx), soit stockage géo-redondant. LRS signifie [Locally Redundant Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx), soit stockage localement redondant. Notez que GRS est également redondant en local.

<!---HONumber=62-->