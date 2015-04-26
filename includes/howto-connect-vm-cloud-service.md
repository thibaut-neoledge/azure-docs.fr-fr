<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#Connexion de machines virtuelles à un réseau virtuel ou un service cloud

Les machines virtuelles doivent se trouver dans un service cloud, qui fait office de conteneur et fournit un nom DNS public unique, une adresse IP publique et un ensemble de points de terminaison pour accéder à la machine virtuelle via Internet. Le service cloud peut éventuellement se trouver dans un réseau virtuel. 

Si un service cloud ne se trouve pas dans un réseau virtuel, les machines virtuelles de ce service cloud peuvent seulement communiquer avec d'autres machines virtuelles à l'aide des noms DNS publics de ces dernières, et ce trafic circule sur Internet. Si un service cloud se trouve dans un réseau virtuel, les machines virtuelles de ce service cloud peuvent communiquer avec toutes les autres machines virtuelles du réseau virtuel sans envoyer aucun trafic sur Internet.

Si vous placez vos machines virtuelles dans le même service cloud autonome, vous pouvez tirer parti de l'équilibrage de charge et des groupes à haute disponibilité. Pour plus d'informations, consultez les pages [Équilibrage de charge des machines virtuelles](../../articles/load-balance-virtual-machines/) et [Gestion de la disponibilité des machines virtuelles](../../articles/manage-availability-virtual-machines/). Toutefois, vous ne pouvez pas organiser les machines virtuelles sur des sous-réseaux ou connecter un service cloud autonome à votre réseau local. Voici un exemple.

![Virtual machines in a standalone cloud service](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
Si vous placez vos machines virtuelles dans un réseau virtuel, vous pouvez décider combien de services cloud vous voulez utiliser pour tirer parti de l'équilibrage de charge et des groupes à haute disponibilité. En outre, vous pouvez organiser les machines virtuelles sur les mêmes sous-réseaux que votre réseau local et connecter le réseau virtuel à votre réseau local. Voici un exemple.

![Virtual machines in a virtual network](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

Les réseaux virtuels sont la méthode recommandée pour connecter des machines virtuelles dans Azure. La meilleure pratique consiste à configurer chaque couche de votre application dans un service cloud distinct. Cela permet une délégation des droits d'utilisateur avancés via le contrôle d'accès en fonction du rôle (RBAC). Pour plus d'informations, consultez [Contrôle d'accès basé en fonction du rôle dans le portail Azure en version préliminaire](../../articles//role-based-access-control-configure/). Toutefois, vous devrez peut-être combiner des machines virtuelles à partir de différentes couches d'application dans le même service cloud pour ne pas dépasser le nombre maximum de services cloud par abonnement (200).

Pour connecter des machines virtuelles dans un réseau virtuel :

1.	Créez le réseau virtuel dans le portail de gestion Azure. Pour plus d'informations, consultez la page [Tâches de configuration du réseau virtuel](https://msdn.microsoft.com/library/azure/jj156206.aspx).
2.	Créez l'ensemble des services cloud pour votre déploiement dans le portail de gestion Azure de manière à refléter votre conception pour les groupes à haute disponibilité et l'équilibrage de charge avec **Nouveau > Calculer > Service cloud > Création personnalisée**.
3.	Lorsque vous créez chaque nouvelle machine virtuelle, spécifiez le service cloud correct et votre réseau virtuel. Si le service cloud a été associé précédemment à votre réseau virtuel, son nom est déjà sélectionné pour vous.

Voici un exemple d'utilisation du portail de gestion Azure.

![Selecting a cloud service for a virtual machine](./media/howto-connect-vm-cloud-service/VMConfig1.png)
 
Pour connecter des machines virtuelles dans un service cloud autonome :

1.	Créez le service cloud pour votre déploiement dans le portail de gestion Azure avec **Nouveau > Calculer > Service cloud > Création personnalisée**.
2.	Lorsque vous créez la machine virtuelle, spécifiez le nom du service cloud créé à l'étape 1. 
Vous pouvez également créer le service cloud pour votre déploiement lorsque vous créez votre première machine virtuelle.

Voici un exemple d'utilisation du portail de gestion Azure pour le service cloud existant nommé EndpointTest.
 
![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Ressources
[Équilibrage de charge des machines virtuelles](../../articles/load-balance-virtual-machines/)

[Gestion de la disponibilité des machines virtuelles](../../articles/manage-availability-virtual-machines/)

[Tâches de Configuration de réseau virtuel](https://msdn.microsoft.com/library/azure/jj156206.aspx)

Une fois que vous avez créé une machine virtuelle, il convient d'ajouter un disque de données pour que vos services et charges de travail disposent d'un emplacement de stockage des données. Consultez l'une des rubriques suivantes :

[Comment attacher un disque de données à une machine virtuelle Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Comment attacher un disque de données à une machine virtuelle Windows](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)


<!--HONumber=45--> 
