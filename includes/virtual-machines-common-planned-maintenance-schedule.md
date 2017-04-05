

## <a name="multi-and-single-instance-vms"></a>Machines virtuelles à instance unique ou multi-instance
De nombreux clients utilisant Azure trouvent cela essentiel de pouvoir programmer le moment où leurs machines virtuelles font l’objet d’une maintenance planifiée en raison de l’interruption de service (environ 15 minutes) qui se produit lors de la maintenance. Vous pouvez utiliser les groupes à haute disponibilité pour faciliter la programmation de la maintenance planifiée des machines virtuelles configurées.

Il existe deux configurations possibles pour les machines virtuelles s’exécutant sur Azure : en tant qu’instance unique ou multi-instance. Si les machines virtuelles sont dans un groupe à haute disponibilité, elles sont configurées en tant que multi-instance. Notez que même si des machines virtuelles uniques sont déployées dans un groupe à haute disponibilité, elles sont traitées comme si elles étaient des machines virtuelles multi-instances. Si les machines virtuelles ne sont PAS dans un groupe à haute disponibilité, elles sont configurées en tant qu’instance unique.  Pour plus d’informations sur les groupes à haute disponibilité, consultez [Gestion de la disponibilité des machines virtuelles Windows dans Azure](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Gestion de la disponibilité des machines virtuelles Linux](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Les mises à jour de maintenance planifiée pour les machines virtuelles à instance unique et multi-instance s’effectuent séparément. En reconfigurant vos machines virtuelles comme instance unique (si elles sont multi-instance) ou multi-instance (si elles sont à instance unique), vous pouvez programmer la maintenance planifiée. Consultez [Maintenance planifiée des machines virtuelles Linux dans Azure](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [Maintenance planifiée des machines virtuelles dans Azure](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour plus d’informations sur la maintenance planifiée des machines virtuelles Azure.

## <a name="for-multi-instance-configuration"></a>Pour la configuration multi-instance
Vous pouvez choisir l’heure de la maintenance planifiée de vos machines virtuelles déployées dans une configuration de groupe à haute disponibilité en supprimant ces machines virtuelles du groupe.

1. Un e-mail vous est envoyé 7 jours avant la maintenance planifiée de vos machines virtuelles dans une configuration multi-instance. Les ID d’abonnement et le nom des machines virtuelles multi-instances concernées sont inclus dans le corps de l’e-mail.
2. Pendant ces 7 jours, vous pouvez choisir l’heure de mise à jour de vos instances en supprimant de leur groupe à haute disponibilité vos machines virtuelles multi-instances dans cette région. Cette modification de la configuration entraîne un redémarrage, car la machine virtuelle est déplacée d’un hôte physique (ciblé pour la maintenance) vers un autre hôte physique non ciblé pour la maintenance.
3. Vous pouvez supprimer la machine virtuelle de son groupe à haute disponibilité dans le portail Azure.

   1. Dans le portail, sélectionnez la machine virtuelle à supprimer du groupe à haute disponibilité.  

   2. Sous **Paramètres**, cliquez sur **Groupe à haute disponibilité**.

      ![Sélection du groupe à haute disponibilité](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. Dans la liste déroulante du groupe à haute disponibilité, sélectionnez « Not part of an availability set » (Ne faisant pas partie d’un groupe à haute disponibilité).

      ![Supprimer du groupe](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. Cliquez sur **Enregistrer** en haut. Cliquez sur **Oui** pour confirmer le redémarrage de la machine virtuelle.

   >[!TIP]
   >Vous pouvez reconfigurer la machine virtuelle en machine virtuelle multi-instance ultérieurement en sélectionnant l’un des groupes à haute disponibilité répertoriés.

4. Les machines virtuelles supprimées des groupes à haute disponibilité sont déplacées vers des hôtes à instance unique et ne sont pas mises à jour pendant la maintenance planifiée des configurations de groupe à haute disponibilité.
5. Une fois la mise à jour des machines virtuelles du groupe à haute disponibilité terminée (en fonction de la planification décrite dans l’e-mail d’origine), vous devez rajouter les machines virtuelles à leur groupe à haute disponibilité. Devenir membre d’un groupe à haute disponibilité reconfigure les machines virtuelles en tant que machines virtuelles multi-instances et entraîne un redémarrage. En règle générale, une fois que toutes les mises à jour des hôtes multi-instance sont terminées sur l’ensemble de l’environnement Azure, la maintenance des hôtes à instance unique a lieu.

Supprimer une machine virtuelle d’un groupe à haute disponibilité est également possible à l’aide d’Azure PowerShell :

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>Pour la configuration en instance unique
Vous pouvez choisir l’heure de maintenance planifiée de vos machines virtuelles dans une configuration en instance unique en ajoutant ces machines virtuelles dans des groupes à haute disponibilité.

Procédure pas à pas

1. Un e-mail vous est envoyé 7 jours avant la maintenance planifiée de vos machines virtuelles dans une configuration en instance unique. Les ID d’abonnement et le nom des machines virtuelles à instance unique concernées sont inclus dans le corps de l’e-mail.
2. Pendant ces 7 jours, vous pouvez choisir le moment du redémarrage de l’instance en ajoutant vos machines virtuelles à instance unique dans un groupe à haute disponibilité de la même région. Cette modification de la configuration entraîne un redémarrage, car la machine virtuelle est déplacée d’un hôte physique (ciblé pour la maintenance) vers un autre hôte physique non ciblé pour la maintenance.
3. Suivez les instructions présentées ici pour ajouter des machines virtuelles existantes dans des groupes à haute disponibilité à l’aide du portail Azure et d’Azure PowerShell. (Consultez l’exemple Azure PowerShell présenté après cette procédure).
4. Une fois que ces machines virtuelles sont reconfigurées en tant que machines virtuelles multi-instances, elles sont exclues de la maintenance planifiée des machines virtuelles à instance unique.
5. Une fois la mise à jour de la machine virtuelle à instance unique terminée (en fonction de la planification donnée dans l’e-mail d’origine), vous pouvez reconfigurer les machines virtuelles en tant que machines virtuelles à instance unique en les supprimant de leur groupe à haute disponibilité.

Ajouter une machine virtuelle à un groupe à haute disponibilité est également possible à l’aide d’Azure PowerShell :

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
