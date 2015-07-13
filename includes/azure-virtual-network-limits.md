<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Limite par défaut</th>
   <th align="left" valign="middle">Limite maximale</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Réseaux virtuels</a><sup>1</sup> par abonnement</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Nombre total de machines<sup>2</sup> par réseau virtuel</p></td>
   <td valign="middle"><p>2&#160;048</p></td>
   <td valign="middle"><p>2&#160;048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Connexions TCP simultanées pour une machine virtuelle ou une instance de rôle</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>Listes de contrôle d'accès par point de terminaison<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sites de réseau local par réseau virtuel</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>Chaque réseau virtuel prend en charge une [passerelle de réseau virtuel](http://msdn.microsoft.com/library/azure/jj156210.aspx) unique.

<sup>2</sup>Le nombre total de machines comprend les machines virtuelles et les instances de rôle de travail/web.

<sup>3</sup>La liste de contrôle d'accès est prise en charge sur les points de terminaison d'entrée pour les machines virtuelles. Pour les rôles de travail/web, elle est prise en charge sur les points de terminaison d'entrée et d'entrée d'instance.

<!---HONumber=62-->