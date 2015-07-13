<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Limite par défaut</th>
   <th align="left" valign="middle">Limite maximale</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">Machines virtuelles</a> par service cloud<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Points de terminaison internes par service cloud<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Lors de la création d'une machine virtuelle en dehors d'un groupe de ressources Azure, un service cloud est automatiquement créé pour la contenir. Vous pouvez ensuite ajouter plusieurs machines virtuelles au même service cloud.

<sup>2</sup>Les points de terminaison d'entrée permettent d'autoriser la communication vers des machines virtuelles externes au service cloud qui les contient. Les machines virtuelles dans le même service cloud autorisent automatiquement la communication entre tous les ports UDP et TCP pour la communication interne.

<!---HONumber=62-->