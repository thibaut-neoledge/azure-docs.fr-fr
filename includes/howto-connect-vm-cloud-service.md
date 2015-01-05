<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#Connexion des machines virtuelles dans un service cloud

Lors de la création d'une machine virtuelle, un service cloud est automatiquement créé pour la contenir. Vous pouvez créer plusieurs machines virtuelles dans le même service cloud pour qu'elles puissent communiquer entre elles. 

> [WACOM.NOTE] Quand les machines virtuelles sont dans le même service cloud, vous pouvez aussi en équilibrer la charge et gérer leur disponibilité, ce qui requiert des étapes supplémentaires. Pour plus d'informations, consultez [Équilibrage de charge des machines virtuelles](../../articles/load-balance-virtual-machines/) et [Gestion de la disponibilité des machines virtuelles](../../articles/manage-availability-virtual-machines/). 

Tout d'abord, vous devez créer une machine virtuelle avec un nouveau service cloud. Ensuite, vous allez créer des machines virtuelles supplémentaires dans le même service cloud. Elle sont ainsi " liées ". 

1. Créez une machine virtuelle en suivant la procédure indiquée à la page [Comment créer une machine virtuelle personnalisée](../../articles/virtual-machines-create-custom/).

2. Suivez la même procédure de base pour créer les autres machines virtuelles, sauf que vous allez les ajouter au service cloud au lieu de créer un service cloud. Par exemple, si vous avez créé un service cloud nommé *EndpointTest*, choisissez ce service. Le graphique suivant illustre cet exemple :

	![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

14. Complétez le reste des champs dans cette page et la suivante, puis cliquez sur la coche pour créer la machine virtuelle connectée.

#Ressources

Une fois que vous avez créé une machine virtuelle, il convient d'ajouter un disque de données pour que vos services et charges de travail disposent d'un emplacement de stockage des données. Consultez l'une des rubriques suivantes :

[Comment attacher un disque de données à une machine virtuelle Linux](http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Comment attacher un disque de données à une machine virtuelle Windows](http://azure.microsoft.com/fr-fr/documentation/articles/storage-windows-attach-disk/)



<!--HONumber=35.1-->
