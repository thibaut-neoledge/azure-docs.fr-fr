


Cette rubrique montre comment :

- injecter des données dans une machine virtuelle Azure lors de son approvisionnement ;

- les récupérer avec Windows et Linux ;

- utiliser les outils spéciaux disponibles sur certains systèmes pour détecter et gérer les données personnalisées automatiquement.

> [AZURE.NOTE] Cet article explique comment des données personnalisées peuvent être injectées à l’aide d’une machine virtuelle créée avec l’API Azure Service Management. Pour savoir comment utiliser l’API Azure Service Management, consultez cet [exemple de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

## Injection de données personnalisées dans votre machine virtuelle Azure

À ce jour, cette fonction est prise en charge uniquement dans l’[interface de ligne de commande Microsoft Azure](https://github.com/Azure/azure-xplat-cli). Vous pouvez utiliser toutes les options pour la commande `azure vm create`. L’exemple ci-dessous montre une approche très basique.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## Utilisation de données personnalisées dans la machine virtuelle

+ Si votre machine virtuelle Azure est une machine virtuelle Windows, le fichier de données personnalisées est enregistré à l’emplacement `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Même s’il a été encodé en base 64 pour son transfert depuis l’ordinateur local vers la nouvelle machine virtuelle, il est décodé automatiquement et peut être ouvert et utilisé immédiatement.

   > [AZURE.NOTE] Si le fichier existe, il est remplacé. La sécurité du répertoire est définie sur **Système : Contrôle total** et **Administrateurs : Contrôle total**.

+ Si votre machine virtuelle Azure est une machine virtuelle Linux, le fichier de données personnalisées se trouvera aux deux emplacements suivants. Les données seront encodées en base 64. Vous devrez donc d’abord les décoder.

    + Dans `/var/lib/waagent/ovf-env.xml`
    + Dans `/var/lib/waagent/CustomData`



## Cloud-init sur Azure

Si votre machine virtuelle Azure est issue d’une image Ubuntu ou CoreOS, vous pouvez utiliser le fichier CustomData afin d’envoyer un fichier cloud-config à Cloud-init. Sinon, si votre fichier de données personnalisées est un script, Cloud-init peut l’exécuter.

### Images cloud Ubuntu

Dans la plupart des images Azure Linux, il vous faut modifier « /etc/waagent.conf » pour configurer le disque de ressources temporaire et le fichier d’échange. Pour plus d’information, consultez le [Guide d’utilisation de l’agent Linux Azure](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md).

Néanmoins, nous devons utiliser Cloud-init sur les images cloud Ubuntu pour configurer le disque de ressources (en d’autres termes, le disque « éphémère ») et la partition d’échange. Consultez la page suivante du wiki Ubuntu pour en savoir plus : [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes : Utilisation de Cloud-init

Pour plus d'informations, consultez la [documentation cloud-init pour Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Référence sur l’opération Ajouter un rôle de l’API REST de gestion des services](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interface de ligne de commande Azure](https://github.com/Azure/azure-xplat-cli)

<!---HONumber=AcomDC_0330_2016-->