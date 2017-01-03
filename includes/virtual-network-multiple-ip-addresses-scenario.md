## <a name="scenario"></a>Scénario
Une machine virtuelle avec une seule carte réseau est créée et connectée à un réseau virtuel. La machine virtuelle nécessite trois différentes adresses IP *privées* et deux adresses IP *publiques*. Les adresses IP sont affectées aux configurations IP suivantes :

* **IPConfig-1 :** affecte une adresse IP privée *dynamique* (par défaut) et une adresse IP publique *statique*.
* **IPConfig-2 :** affecte une adresse IP privée *statique* et une adresse IP publique *statique*.
* **IPConfig-3 :** affecte une adresse IP privée *dynamique* et aucune adresse IP publique.
  
    ![Plusieurs adresses IP](./media/virtual-network-multiple-ip-addresses-scenario/OneNIC-3IP.png)

Les configurations IP sont associées à la carte réseau lors de sa création, et la carte réseau est attachée à la machine virtuelle lorsque cette machine est créée. Les types des adresses IP utilisées pour le scénario sont fournis à titre indicatif. Vous pouvez affecter les types d’adresses IP et d’affectations dont vous avez besoin.

> [!NOTE]
> Bien que les étapes décrites dans cet article affectent toutes les configurations IP à une seule carte réseau, vous pouvez également affecter plusieurs configurations IP à une carte réseau quelconque dans une machine virtuelle équipée de plusieurs cartes réseau. Pour apprendre à créer une machine virtuelle avec plusieurs cartes réseau, consultez l’article [Créer une machine virtuelle avec plusieurs cartes d’interface réseau](../articles/virtual-network/virtual-network-deploy-multinic-arm-ps.md).

<!--HONumber=Dec16_HO2-->


