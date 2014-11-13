<properties title="virtual-machines-how-to-inject-custom-data" pageTitle="Injection de donn&eacute;es personnalis&eacute;es dans des machines virtuelles&nbsp;Azure" description="Cette rubrique explique comment injecter des donn&eacute;es personnalis&eacute;es dans une machine virtuelle&nbsp;Azure lorsque l'instance est cr&eacute;&eacute;e et comment localiser les donn&eacute;es personnalis&eacute;es dans&nbsp;Windows ou&nbsp;Linux." metaKeywords="Azure linux vm, linux vm, userdata vm, user data vm, custom data vm, windows custom data" services="virtual-machines" solutions="" documentationCenter="" authors="rasquill" manager="timlt" editor="tysonn" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="rasquill" />

# Injection de données personnalisées dans une machine virtuelle Azure

L'injection d'un script ou d'autres données dans une machine virtuelle Azure lors de son approvisionnement est un scénario très commun, que le système d'exploitation soit Microsoft Windows ou une distribution Linux. Cette rubrique montre comment :

-   injecter des données dans une machine virtuelle Azure lors de son approvisionnement ;

-   les récupérer avec Windows et Linux ;

-   utiliser les outils spéciaux disponibles sur certains systèmes pour détecter et gérer les données personnalisées automatiquement.

> [WACOM.NOTE] Cette rubrique développe ce [billet du blog Azure][billet du blog Azure] relatif à cette fonctionnalité. Elle sera mise à jour au fur et à mesure des nouvelles fonctions.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

Dans cette rubrique :

-   [Injection des données personnalisées dans votre machine virtuelle Azure][Injection des données personnalisées dans votre machine virtuelle Azure]

-   [Utilisation de données personnalisées dans la machine virtuelle][Utilisation de données personnalisées dans la machine virtuelle]

-   [Étapes suivantes][Étapes suivantes]

## <span id="injectingCustomData"></span></a>Injection des données personnalisées dans votre machine virtuelle Azure

À ce jour, cette fonctionnalité n'est prise en charge que dans l'[interface de ligne de commande interplateforme de Microsoft Azure][interface de ligne de commande interplateforme de Microsoft Azure]. Bien qu'il soit possible d'utiliser n'importe laquelle des options de la commande `azure vm create`, la méthode ci-après est un exemple d'approche très basique.

        PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
        VMNAME=mycustomdataubuntu
        USERNAME=username
        VMIMAGE= An image chosen from among those listed by azure vm image list
        azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22

## <span id="usingCustomData"></span></a>Utilisation de données personnalisées dans la machine virtuelle

-   Si votre machine virtuelle Azure est une machine virtuelle Windows, le fichier de données personnalisées est enregistré dans `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Bien qu'il ait été encodé en Base64 pour le transfert entre l'ordinateur local et la nouvelle machine virtuelle, il est automatiquement décodé afin de pouvoir être ouvert ou utilisé immédiatement.

> [WACOM.NOTE] Si le fichier existe, il est remplacé. La sécurité du répertoire est définie sur **Système : Contrôle total** et **Administrateurs : Contrôle total**.

-   Si votre machine virtuelle Azure est une machine virtuelle Linux, le fichier de données personnalisées se trouve à l'un des deux emplacements ci-après, mais est encodé en Base64. Il est donc nécessaire de d'abord décoder les données.

    -   Dans `/var/lib/waagent/ovf-env.xml`
    -   Dans `/var/lib/waagent/CustomData`

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <span id="nextsteps"></span></a>Étapes suivantes : utilisation de cloud-init

Si votre machine virtuelle Azure est une image Ubuntu, vous pouvez utiliser cloud-init pour exécuter un script afin d'utiliser automatiquement vos données personnalisées (ou, si votre fichier de données est un script, l'exécuter). Pour plus d'informations, consultez la [documentation cloud-init pour Ubuntu][documentation cloud-init pour Ubuntu].

<!--Link references-->

[Référence sur l'opération Ajouter un rôle de l'API REST de gestion des services][Référence sur l'opération Ajouter un rôle de l'API REST de gestion des services]

[Interface de ligne de commande interplateforme Microsoft Azure][interface de ligne de commande interplateforme de Microsoft Azure]

  [billet du blog Azure]: http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/
  [Injection des données personnalisées dans votre machine virtuelle Azure]: #injectingCustomData
  [Utilisation de données personnalisées dans la machine virtuelle]: #usingCustomData
  [Étapes suivantes]: #nextsteps
  [interface de ligne de commande interplateforme de Microsoft Azure]: https://github.com/Azure/azure-sdk-tools-xplat
  [documentation cloud-init pour Ubuntu]: https://help.ubuntu.com/community/CloudInit
  [Référence sur l'opération Ajouter un rôle de l'API REST de gestion des services]: http://msdn.microsoft.com/library/azure/jj157186.aspx
