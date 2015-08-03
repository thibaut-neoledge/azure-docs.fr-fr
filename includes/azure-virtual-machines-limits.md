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

<sup>1</sup>Les machines virtuelles créées dans la gestion des services (plutôt que dans Resource Manager) sont automatiquement stockées dans un service cloud. Vous pouvez ajouter plus de machines virtuelles à ce service cloud pour l’équilibrage de charge et la disponibilité. Voir [Connexion de machines virtuelles à un réseau virtuel ou un service cloud](../virtual-machines/cloud-services-connect-virtual-machine.md).

<sup>2</sup>Les points de terminaison d’entrée autorisent les communications avec une machine virtuelle depuis l’extérieur du service cloud de la machine virtuelle. Les machines virtuelles d’un même service cloud ou réseau virtuel peuvent automatiquement communiquer entre elles. Voir [Configuration des points de terminaison sur une machine virtuelle](../virtual-machines/virtual-machines-set-up-endpoints.md).

<!---HONumber=July15_HO4-->