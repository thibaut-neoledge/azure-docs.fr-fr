<properties
	pageTitle="Injection de données personnalisées dans des machines virtuelles Azure"
	description="Cette rubrique explique comment injecter des données personnalisées dans une machine virtuelle Azure lorsque l’instance est créée et comment localiser les données personnalisées dans Windows ou Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2015"
	ms.author="rasquill"/>


#Injection de données personnalisées dans une machine virtuelle Azure

L'injection d'un script ou d'autres données dans une machine virtuelle Azure lors de son approvisionnement est un scénario très commun, que le système d'exploitation soit Microsoft Windows ou une distribution Linux. Cette rubrique montre comment :

- injecter des données dans une machine virtuelle Azure lors de son approvisionnement ;

- les récupérer avec Windows et Linux ;

- utiliser les outils spéciaux disponibles sur certains systèmes pour détecter et gérer les données personnalisées automatiquement.

> [AZURE.NOTE]Cette rubrique développe ce [billet du blog Azure](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/) relatif à cette fonctionnalité. Elle sera mise à jour au fur et à mesure des nouvelles fonctions.

## Injection de données personnalisées dans votre machine virtuelle Microsoft Azure

À ce jour, cette fonction est prise en charge uniquement dans l’[interface de ligne de commande Microsoft Azure](https://github.com/Azure/azure-sdk-tools-xplat). Bien qu’il soit possible d’utiliser n’importe laquelle des options de la commande `azure vm create`, la méthode ci-après est un exemple d’approche très basique.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## Utilisation de données personnalisées dans la machine virtuelle

+ Si votre machine virtuelle Microsoft Azure est une machine virtuelle Windows, le fichier de données personnalisées est enregistré dans `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Bien qu’il ait été encodé en Base64 pour le transfert entre l’ordinateur local et la nouvelle machine virtuelle, il est automatiquement décodé afin de pouvoir être ouvert ou utilisé immédiatement.

   >[AZURE.NOTE]Si le fichier existe, il est remplacé. La sécurité du répertoire est définie sur **Système : Contrôle total** et **Administrateurs : Contrôle total**.

+ Si votre machine virtuelle Azure est une machine virtuelle Linux, le fichier de données personnalisées se trouve à l’un des deux emplacements ci-après, mais est encodé en Base64. Il est donc nécessaire de d’abord décoder les données.

    + Dans `/var/lib/waagent/ovf-env.xml`
    + Dans `/var/lib/waagent/CustomData`



## Cloud-init sur Microsoft Azure

Si votre machine virtuelle Microsoft Azure est issue d’une image Ubuntu ou CoreOS, vous pouvez utiliser le fichier CustomData afin d’envoyer un fichier cloud-config à Cloud-init. Sinon, si votre fichier de données personnalisées est un script, Cloud-init peut l’exécuter.

### Images cloud Ubuntu

Dans la plupart des images Microsoft Azure Linux, il vous faut modifier « /etc/waagent.conf » pour configurer le disque de ressources temporaire et le fichier d’échange. Pour plus d’information, consultez le [Guide d’utilisation de l’agent Linux Azure](virtual-machines-linux-agent-user-guide.md).

Néanmoins, nous devons utiliser Cloud-init sur les images cloud Ubuntu afin de configurer le disque de ressources (le disque « éphémère ») et la partition d’échange. Consultez la page suivante du wiki Ubuntu pour en savoir plus :

 - [Wiki Ubuntu : Configurer les partitions d’échange](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes : Utilisation de Cloud-init

Pour plus d'informations, consultez la [documentation cloud-init pour Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Référence sur l’opération Ajouter un rôle de l’API REST de gestion des services](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interface de ligne de commande Azure](https://github.com/Azure/azure-sdk-tools-xplat)
 

<!---HONumber=58_postMigration-->