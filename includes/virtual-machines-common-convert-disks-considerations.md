
* La conversion nécessite un redémarrage de la machine virtuelle. Par conséquent, planifiez la migration de vos machines virtuelles au cours d’une fenêtre de maintenance préexistante. 

* Vous ne pouvez pas annuler la conversion. 

* Veillez à la tester. Migrez une machine virtuelle de test avant d’effectuer la migration en production.

* Lors de la conversion, vous libérez la machine virtuelle. Celle-ci reçoit une nouvelle adresse IP lorsqu’elle est démarrée après la conversion. Si nécessaire, vous pouvez [affecter une adresse IP statique](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) à la machine virtuelle.

* Les disques durs virtuels et le compte de stockage d’origine utilisé par la machine virtuelle avant la conversion ne sont pas supprimés. Ils continuent à être facturés. Pour éviter la facturation de ces artefacts, supprimez les objets BLOB du disque dur virtuel d’origine après avoir vérifié que la conversion est terminée.
