1. Connectez-vous à votre abonnement Azure en suivant les étapes indiquées dans [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)](../articles/xplat-cli-connect.md).

2. Assurez-vous que vous êtes en mode de gestion des services, en utilisant :

        azure config mode asm

3. Trouvez l'image Linux que vous souhaitez charger à partir des images disponibles :

        azure vm image list | grep "Linux"

4. Utilisez `azure vm create` pour créer une machine virtuelle avec l'image Linux de la liste ci-dessus. Cette étape crée un nouveau service cloud ainsi qu'un compte de stockage. Vous pouvez également connecter cette machine virtuelle à un service cloud existant avec une option `-c`. Cela crée également un point de terminaison SSH pour se connecter à la machine virtuelle Linux avec l’option `-e`.

        ~$ azure vm create "MyTestVM" b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64 "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE]Pour une machine virtuelle Linux, vous devez fournir l’option `-e` dans `vm create` ; il n'est pas possible d'activer SSH une fois la machine virtuelle créée. Pour plus d’informations sur SSH, consultez la rubrique [Utilisation de SSH avec Linux dans Azure](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md).

    Notez que l'image *b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-opensuse-13.1-20141216-x86-64* est celle que nous avons choisie dans la liste d'images lors de l'étape ci-dessus. *MyTestVM* est le nom de notre nouvelle machine virtuelle et *adminUser* est le nom d'utilisateur pour la connexion SSH à la machine virtuelle. Vous pouvez remplacer ces variables selon vos besoins. Pour plus d'informations sur cette commande, consultez [Utilisation de l’interface de ligne de commande Azure avec Microsoft Azure Service Management.](../articles/virtual-machines/virtual-machines-command-line-tools.md).

5. La machine virtuelle Linux nouvellement créée s'affiche dans la liste donnée par :

        azure vm list

6. Vous pouvez vérifier les attributs de la machine virtuelle à l'aide de la commande :

        azure vm show MyTestVM

7. La machine virtuelle nouvellement créée est prête à démarrer avec la commande `azure vm start`.

Pour plus d’informations sur toutes les commandes des machines virtuelles Azure CLI, consultez [Utilisation de l’interface de ligne de commande Azure avec l’API Service Management](../articles/virtual-machines/virtual-machines-command-line-tools.md).

<!---HONumber=August15_HO8-->
