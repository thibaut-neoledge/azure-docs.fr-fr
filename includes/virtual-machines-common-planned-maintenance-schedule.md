

## Machines virtuelles à instance unique ou multi-instance
Pour de nombreux clients s’exécutant sur Azure, il est essentiel de pouvoir programmer la maintenance planifiée de leurs machines virtuelles, car elle entraîne une interruption de service d’environ 15 minutes. Vous pouvez utiliser les groupes à haute disponibilité pour faciliter la programmation de la maintenance planifiée des machines virtuelles configurées.

Il existe deux configurations possibles pour les machines virtuelles s’exécutant sur Azure : en tant qu’instance unique ou multi-instance. Si les machines virtuelles sont dans un groupe à haute disponibilité, elles sont configurées en tant que multi-instance. Notez que même si des machines virtuelles uniques sont déployées dans un groupe à haute disponibilité, elles sont traitées comme si elles étaient multi-instance. Si les machines virtuelles ne sont PAS dans un groupe à haute disponibilité, elles sont configurées en tant qu’instance unique. Pour plus d’informations sur les groupes à haute disponibilité, consultez [Gestion de la disponibilité de vos machines virtuelles Windows](../articles/virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gestion de la disponibilité de vos machines virtuelles Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

Les mises à jour de maintenance planifiée pour les machines virtuelles à instance unique et multi-instance s’effectuent séparément. En reconfigurant vos machines virtuelles comme instance unique (si elles sont multi-instance) ou multi-instance (si elles sont à instance unique), vous pouvez programmer la maintenance planifiée. Consultez [Maintenance planifiée pour les machines virtuelles Azure Linux](../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md) ou [Maintenance planifiée pour les machines virtuelles Azure Windows](../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md) pour plus d’informations sur la maintenance planifiée des machines virtuelles Azure.

## Pour la configuration multi-instance
Vous pouvez choisir l’heure de la maintenance planifiée de vos machines virtuelles déployées dans une configuration de groupe à haute disponibilité en supprimant ces machines virtuelles du groupe.

1. Un e-mail vous est envoyé 7 jours avant la maintenance planifiée de vos machines virtuelles dans une configuration multi-instance. L’ID d’abonnement et le nom des machines virtuelles multi-instance concernées sont inclus dans le corps de l’e-mail.
2. Pendant ces 7 jours, vous pouvez choisir l’heure de mise à jour de vos instances en supprimant de leur groupe à haute disponibilité les machines virtuelles multi-instance de cette région. Cette modification de la configuration entraîne un redémarrage, car la machine virtuelle est déplacée d’un hôte physique (ciblé pour la maintenance) vers un autre hôte physique non ciblé pour la maintenance.
3. Vous pouvez supprimer la machine virtuelle de son groupe à haute disponibilité dans le portail Classic.
   
   1. Dans le portail Classic, cliquez sur la machine virtuelle, puis sélectionnez Configurer.
   2. Sous Paramètres s’affiche le groupe à haute disponibilité contenant la machine virtuelle.
      
      ![Sélection du groupe à haute disponibilité](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)
   3. Dans la liste déroulante du groupe à haute disponibilité, sélectionnez Supprimer du groupe à haute disponibilité.
      
      ![Supprimer du groupe](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselectionconfiguration.png)
   4. En bas, sélectionnez Enregistrer. Sélectionnez Oui pour confirmer le redémarrage de la machine virtuelle.
4. Ces machines virtuelles sont déplacées vers des hôtes à instance unique et ne sont pas mises à jour pendant la maintenance planifiée des configurations de groupe à haute disponibilité.
5. Une fois terminée la mise à jour des machines virtuelles du groupe à haute disponibilité (en fonction de la planification décrite dans l’e-mail d’origine), vous devez rajouter les machines virtuelles à leur groupe à haute disponibilité pour qu’elles soient reconfigurées en tant que machines virtuelles multi-instance. Le déplacement des machines virtuelles d’un hôte à instance unique vers un hôte multi-instance entraîne un redémarrage. En règle générale, une fois que toutes les mises à jour des hôtes multi-instance sont terminées sur l’ensemble de l’environnement Azure, la maintenance des hôtes à instance unique a lieu.

Notez que vous pouvez également obtenir ce résultat à l’aide d’Azure PowerShell :

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## Pour la configuration en instance unique
Vous pouvez choisir l’heure de maintenance planifiée de vos machines virtuelles dans une configuration en instance unique en ajoutant ces machines virtuelles dans des groupes à haute disponibilité.

Procédure pas à pas

1. Un e-mail vous est envoyé 7 jours avant la maintenance planifiée de vos machines virtuelles dans une configuration en instance unique. L’ID d’abonnement et le nom des machines virtuelles à instance unique concernées sont inclus dans le corps de l’e-mail.
2. Pendant ces 7 jours, vous pouvez choisir le moment du redémarrage de l’instance en déplaçant vos machines virtuelles à instance unique dans un groupe à haute disponibilité de la même région. Cette modification de la configuration entraîne un redémarrage, car la machine virtuelle est déplacée d’un hôte physique (ciblé pour la maintenance) vers un autre hôte physique non ciblé pour la maintenance.
3. Suivez les instructions présentées ici pour ajouter des machines virtuelles existantes dans des groupes à haute disponibilité à l’aide du portail Azure Classic et d’Azure PowerShell (voir l’exemple Azure PowerShell dans la remarque ci-dessous).
4. Une fois que ces machines virtuelles sont reconfigurées sur des hôtes multi-instance, elles sont exclues de la maintenance planifiée des machines virtuelles à instance unique.
5. Une fois terminée la mise à jour des machines virtuelles du groupe à haute disponibilité (en fonction de la planification décrite dans l’e-mail d’origine), vous pouvez supprimer les machines virtuelles de leur groupe à haute disponibilité pour qu’elles soient reconfigurées en tant que machines virtuelles à instance unique.

Notez que vous pouvez également obtenir ce résultat à l’aide d’Azure PowerShell :

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=AcomDC_0803_2016-->