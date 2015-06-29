<properties
	pageTitle="Capturer l'image d'une machine virtuelle exécutant Linux à l'aide de l'interface de ligne de commande"
	description="Apprenez à capturer une image d'une machine virtuelle Azure exécutant Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="karthmut"
	manager="madhana"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="karthmut"/>




# Capture d’une machine virtuelle Linux à utiliser comme modèle à l’aide de l’interface de ligne de commande##



Cet article vous montre comment capturer une machine virtuelle Azure exécutant Linux pour l'utiliser comme modèle afin de créer d'autres machines virtuelles. Ce modèle comprend le disque du système d’exploitation ainsi que les disques de données attachés à la machine virtuelle. Il ne comprend pas la configuration réseau, vous devez donc la configurer lors de la création d’autres machines virtuelles utilisant le modèle.



Azure traite ce modèle comme une image et le stocke dans votre liste d'images. C’est aussi là que sont stockées les images que vous avez chargées. Pour plus d’informations sur les images, voir l’article [À propos des images de machine virtuelle dans Azure][].



##Avant de commencer##



Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d'exploitation et attaché les disques de données. Si vous ne l’avez pas encore fait, consultez ces instructions :



- [Création d’une machine virtuelle personnalisée][]

- [Association d’un disque de données à une machine virtuelle][]



##Capture de la machine virtuelle##



1. Connexion à la machine virtuelle. Pour plus d'informations, consultez [Connexion à une machine virtuelle exécutant Linux][].



2. La machine virtuelle ne peut être capturée que si elle présente l’état **StoppedDeallocated**. Dans la fenêtre SSH, tapez la commande suivante pour arrêter votre machine virtuelle :



        vm shutdown [options] <vm-name>



    Notez que l’une des options de `vm shutdown` est `-p`, qui permet de conserver la ressource de calcul à l’arrêt. N’activez **pas** cette option, car la machine virtuelle doit faire l’objet d’une annulation de déploiement pour être capturée.



3. Exécutez la commande suivante pour capturer votre image de machine virtuelle :



        vm capture <vm-name> <target-image-name> --deleted



    La machine virtuelle doit être supprimée pour que l'image soit capturée. Pour effectuer cette opération, vous pouvez utiliser `--deleted` ou `-t`.



4. Vous pouvez vérifier que votre image a été capturée en consultant votre liste d'images de machine virtuelle :



        vm image list | grep <target-image-name>



    La partie `| grep <target-image-name>` est facultative, mais elle vous permet de trouver plus facilement votre image dans la liste.



Voici un exemple de procédure de capture d'une machine virtuelle incluant la sortie de terminal :


    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status            Location  DNS Name                  IP Address

    data:    -----------  ----------------  --------  ------------------------  ------------

    data:    kmorig       RoleStateUnknown  West US   kmorig.cloudapp.net       100.92.56.16

    info:    vm list command OK

    ~$ azure vm shutdown kmorig

    info:    Executing command vm shutdown

    + Getting virtual machines

    + Shutting down VM

    info:    vm shutdown command OK

    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status              Location  DNS Name                  IP Address

    data:    -----------  ------------------  --------  ------------------------  ----------

    data:    kmorig       StoppedDeallocated  West US   kmorig.cloudapp.net

    info:    vm list command OK

    ~$ azure vm capture kmorig kmcaptured --deleted

    info:    Executing command vm capture

    + Getting virtual machines

    + Checking image with name kmcaptured exists

    + Capturing VM

    info:    vm capture command OK

    ~$ azure vm image list | grep kmcaptured

    data:    kmcaptured



Pour obtenir plus d’informations et découvrir des commandes supplémentaires, consultez la [page de documentation sur l’interface de ligne de commande Azure][].


[page de documentation sur l’interface de ligne de commande Azure]: ../virtual-machines-command-line-tools.md

[Connexion à une machine virtuelle exécutant Linux]: virtual-machines-linux-how-to-log-on.md

[À propos des images de machine virtuelle dans Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx

[Création d’une machine virtuelle personnalisée]: virtual-machines-create-custom.md

[Association d’un disque de données à une machine virtuelle]: storage-windows-attach-disk.md
 

<!---HONumber=58_postMigration-->