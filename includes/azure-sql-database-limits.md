<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Limite par défaut</th>
</tr>
<tr>
   <td valign="middle"><p>Taille de base de données</p></td>
   <td valign="middle"><p>Varie selon le niveau de performance <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Connexions</p></td>
   <td valign="middle"><p>Varie selon le niveau de performance <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Utilisation de la mémoire</p></td>
   <td valign="middle"><p>Allocation de mémoire de 16&#160;Mo pendant plus de 20&#160;secondes</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sessions</p></td>
   <td valign="middle"><p>Varie selon le niveau de performance <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Taille de tempdb</p></td>
   <td valign="middle"><p>5&#160;GO</p></td>
</tr>
<tr>
   <td valign="middle"><p>Durée de la transaction</p></td>
   <td valign="middle"><p>24&#160;heures<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Verrous par transaction</p></td>
   <td valign="middle"><p>1&#160;million</p></td>
</tr>
<tr>
   <td valign="middle"><p>Taille par transaction</p></td>
   <td valign="middle"><p>2&#160;Go</p></td>
</tr>
<tr>
   <td valign="middle"><p>Pourcentage d’espace total de journalisation utilisé par transaction</p></td>
   <td valign="middle"><p>20&#160;%</p></td>
</tr>
<tr>
   <td valign="middle"><p>Nombre maximal de demandes simultanées (threads de travail)</p></td>
   <td valign="middle"><p>Varie selon le niveau de performance <sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup>La base de données SQL a différents niveaux de performances, tels que de base, Standard et Premium. Standard et Premium sont également divisés en niveaux de performance. Pour les limites détaillées par niveau et de niveau de service, voir [Niveaux du service Azure SQL Database et niveaux de performances](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Si la transaction verrouille une ressource requise par une tâche système sous-jacente, la durée maximale est de 20 secondes.

<!---HONumber=July15_HO3-->