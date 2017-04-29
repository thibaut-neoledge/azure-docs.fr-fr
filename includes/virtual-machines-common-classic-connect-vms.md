

![Machines virtuelles dans un service cloud autonome](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Si vous placez vos machines virtuelles dans un réseau virtuel, vous pouvez décider combien de services cloud vous voulez utiliser pour utiliser l’équilibrage de charge et des groupes à haute disponibilité. En outre, vous pouvez organiser les machines virtuelles sur les mêmes sous-réseaux que votre réseau local et connecter le réseau virtuel à votre réseau local. Voici un exemple :

![Machines virtuelles dans un réseau virtuel](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Les réseaux virtuels sont la méthode recommandée pour connecter des machines virtuelles dans Azure. La meilleure pratique consiste à configurer chaque couche de votre application dans un service cloud distinct. Toutefois, vous devrez peut-être combiner des machines virtuelles à partir de différentes couches d'application dans le même service cloud pour ne pas dépasser le nombre maximum de services cloud par abonnement (200). Pour vérifier cela et les autres limites, consultez [Abonnement Azure et limites, quotas et contraintes du service](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Connexion de machines virtuelles dans un réseau virtuel
Pour connecter des machines virtuelles dans un réseau virtuel :

1. Créez le réseau virtuel dans le [portail Azure](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md).
2. Créez l’ensemble des services cloud pour votre déploiement de manière à refléter votre conception des groupes à haute disponibilité et l’équilibrage de la charge. Dans le Portail Azure Classic, cliquez sur **Nouveau > Compute > Service cloud > Création personnalisée** pour chaque service cloud.
3. Pour créer chaque machine virtuelle, cliquez sur **Nouveau > Compute > Machine virtuelle > À partir de la galerie**. Choisissez le service cloud et le réseau virtuel appropriés à la machine virtuelle. Si le service cloud est déjà joint à un réseau virtuel, son nom est sélectionné pour vous.

![Sélection d'un service cloud pour une machine virtuelle](./media/virtual-machines-common-classic-connect-vms/VMConfig1.png)

## <a name="connect-vms-in-a-standalone-cloud-service"></a>Connexion de machines virtuelles dans un service cloud autonome
Pour connecter des machines virtuelles dans un service cloud autonome :

1. Créez le service cloud dans le [portail Azure Classic](http://manage.windowsazure.com). Cliquez sur **Nouveau > Compute > Service cloud > Création personnalisée**. Vous pouvez également créer le service cloud pour votre déploiement lorsque vous créez votre première machine virtuelle.
2. Lorsque vous créez la machine virtuelle, sélectionnez le nom du service cloud créé à l'étape précédente.
   
   ![Ajouter un ordinateur virtuel à un service cloud existant](./media/virtual-machines-common-classic-connect-vms/Connect-VM-to-CS.png)

