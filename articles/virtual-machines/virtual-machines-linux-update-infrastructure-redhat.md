---
title: Infrastructure RHUI (Red Hat Update Infrastructure) | Microsoft Docs
description: "Découvrez l’infrastructure RHUI (Red Hat Update Infrastructure) pour les instances Red Hat Enterprise Linux à la demande dans Microsoft Azure"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: borisb
translationtype: Human Translation
ms.sourcegitcommit: 6b54633b6beed738a93070aa4235ee4e24333b5e
ms.openlocfilehash: e1867aa3c5339373b494744ba26c750bcc11b5b5
ms.lasthandoff: 02/16/2017


---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infrastructure RHUI (Red Hat Update Infrastructure) pour machines virtuelles Red Hat Enterprise Linux à la demande dans Azure
Les machines virtuelles créées à partir des images Red Hat Enterprise Linux (RHEL) à la demande disponibles dans le service Place de marché Azure sont inscrites pour accéder à l’infrastructure RHUI (Red Hat Update Infrastructure) déployée dans Azure.  Les instances RHEL à la demande ont accès à un référentiel yum régional et peuvent recevoir des mises à jour incrémentielles.

La liste de référentiels yum, gérée par RHUI, est configurée dans votre instance RHEL lors de l’approvisionnement. Vous n’avez pas besoin d’effectuer de configuration supplémentaire : exécutez `yum update` une fois que votre instance RHEL est prête à recevoir les dernières mises à jour.

> [!NOTE]
> En septembre 2016, nous avons déployé une mise à jour de RHUI Azure, et en janvier 2017, nous avons progressivement désactivé l’ancienne version. Si vous avez utilisé les images RHEL (ou leurs instantanés) à partir de septembre 2016 ou après, il est probable qu’aucune action ne soit requise. Si toutefois vous avez des machines virtuelles/instantanés plus anciens, leur configuration doit être mise à jour pour un accès ininterrompu à RHUI Azure.
> 

## <a name="rhui-azure-infrastructure-update"></a>Mise à jour de l’infrastructure RHUI Azure
Depuis septembre 2016, Azure propose un nouvel ensemble de serveurs d’infrastructure de mise à jour de Red Hat (RHUI). Ces serveurs sont déployés avec [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) de sorte qu’un simple point de terminaison (rhui-1.micrsoft.com) peut être utilisé par n’importe quelle machine virtuelle, quelle que soit la région. Les nouvelles images RHEL avec paiement à l’utilisation (PAYG) dans la Place de marché Azure (versions à partir de septembre 2016) pointent vers les nouveaux serveurs RHUI Azure et ne nécessitent pas d’action supplémentaire.

### <a name="determine-if-action-is-required"></a>Déterminer si une action est requise
Si vous rencontrez des problèmes de connexion à RHUI Azure à partir de votre machine virtuelle Azure RHEL PAYG, procédez comme suit
1. Examen de la configuration de machine virtuelle pour le point de terminaison RHUI Azure

    Vérifiez si le fichier `/etc/yum.repos.d/rh-cloud.repo` contient la référence à `rhui-[1-3].microsoft.com` dans la section baseurl `[rhui-microsoft-azure-rhel*]` du fichier. Si c’est le cas, vous utilisez la nouvelle version de RHUI Azure.

    Si elle pointe vers un emplacement avec le modèle suivant `mirrorlist.*cds[1-4].cloudapp.net` - la mise à jour de la configuration est requise.

    Si vous utilisez la nouvelle configuration et ne parvenez toujours pas à vous connecter à RHUI Azure, ouvrez une demande de support avec Microsoft ou Red Hat.

    > [!NOTE]
    > L’accès à l’infrastructure RHUI hébergée sur Azure est limité aux machines virtuelles figurant dans les [plages IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
    > 

2. Si l’ancienne version de RHUI Azure est toujours disponible lorsque vous effectuez cette vérification et que vous souhaitez mettre à jour automatiquement la configuration, exécutez la commande suivante :

    `sudo yum update RHEL6` ou `sudo yum update RHEL7` selon la version de famille RHEL que vous utilisez.

3. Si vous ne vous connectez plus à l’ancien RHUI Azure, suivez les étapes manuelles décrites dans la section suivante.

4. Veillez à mettre à jour la configuration de l’image source/l’instantané depuis lequel la machine virtuelle affectée a été configurée.

### <a name="phased-shutdown-of-the-old-azure-rhui"></a>Arrêt progressif de l’ancienne version de RHUI Azure
Pendant l’arrêt de l’ancienne version de RHUI Azure, nous restreignons l’accès comme suit :

1. Restreignez davantage l’accès (ACL) aux adresses IP qui s’y connectent déjà. Effets indésirables possibles : si vous continuez à utiliser l’ancienne version de RHUI Azure, vos nouvelles machines virtuelles pourraient ne pas parvenir à s’y connecter. Les machines virtuelles RHEL avec des adresses IP dynamiques qui passent par la séquence arrêt/désallocation/démarrage peuvent recevoir une nouvelle IP et par conséquent ne pas pouvoir se connecter à l’ancienne version de RHUI Azure

2. Arrêtez les serveurs de distribution du contenu de mise en miroir. Effets indésirables possibles : comme nous arrêtons plus de serveurs, vous pourriez noter un temps de service `yum update` plus long, plus d’expirations de délai jusqu’à ne plus pouvoir vous connecter à l’ancienne version de RHUI Azure.

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>Les adresses IP pour les nouveaux serveurs de distribution de contenu RHUI sont
Si vous utilisez la configuration du réseau pour restreindre davantage l’accès à partir de machines virtuelles RHEL PAYG, assurez-vous que les adresses IP suivantes sont autorisées pour que `yum update` fonctionne en fonction de votre environnement. 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Procédure de mise à jour manuelle pour utiliser les nouveaux serveurs RHUI Azure
Télécharger (via curl) la signature de clé publique

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Vérifier la clé téléchargée

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Vérifier la sortie, vérifier `keyid` et `user ID packet` :

```bash
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Installer la clé publique

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Télécharger, vérifier et installer le package RPM Client

Télécharger : pour RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

pour RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Vérifier :

```bash
rpm -Kv azureclient.rpm
```

Vérifier dans la sortie que la signature du package est correcte

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Installer le package RPM

```bash
sudo rpm -U azureclient.rpm
```

Une fois l’installation terminée, vérifier que vous pouvez accéder à l’écran RHUI Azure de la machine virtuelle

### <a name="all-in-one-script-for-automating-the-preceding-task"></a>Script tout-en-un pour l’automatisation de la tâche précédente
Si nécessaire, utilisez le script suivant pour automatiser la tâche de mise à jour des machines virtuelles affectées vers les nouveaux serveurs RHUI Azure.

```sh
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Vue d’ensemble de RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) constitue une solution hautement extensible pour gérer le contenu du référentiel yum pour les instances cloud de Red Hat Enterprise Linux hébergées par des fournisseurs cloud certifiés Red Hat. À partir du projet Pulp en amont, RHUI permet aux fournisseurs de cloud de mettre en miroir localement le contenu du référentiel hébergé par Red Hat, de créer des référentiels personnalisés avec leur propre contenu et de rendre ces référentiels accessibles à un grand groupe d'utilisateurs finaux au moyen d’un système à équilibrage de charge de distribution de contenu.

## <a name="regions-where-rhui-is-available"></a>Régions où RHUI est disponible
L’infrastructure RHUI est disponible dans toutes les régions où les images RHEL à la demande sont disponibles. Cela inclut actuellement toutes les régions publiques répertoriées sur la page [Tableau de bord Statut Azure](https://azure.microsoft.com/status/) et les régions Azure du gouvernement des États-Unis et d’Allemagne. L’accès à l’infrastructure RHUI pour les machines virtuelles mises en service à partir d’images RHEL à la demande est inclus dans leur prix. Les disponibilités cloud régionales/nationales complémentaires seront mises à jour à mesure que nous développerons la disponibilité à la demande RHEL.

> [!NOTE]
> L’accès à l’infrastructure RHUI hébergée sur Azure est limité aux machines virtuelles figurant dans les [plages IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

## <a name="get-updates-from-another-update-repository"></a>Obtenir des mises à jour à partir d’un autre référentiel de mise à jour
Si vous avez besoin d’obtenir des mises à jour à partir d’un référentiel de mise à jour différent (à la place de l’infrastructure RHUI hébergée sur Azure), vous devez annuler l’inscription de vos instances à l’infrastructure RHUI. Ensuite vous devez les réinscrire auprès de l’infrastructure de mise à jour de votre choix, telle que le Portail client Red Hat ou Red Hat Satellite. Vous aurez besoin d’abonnements Red Hat appropriés pour ces services, et d’une inscription pour l’ [accès cloud Red Hat dans Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Pour annuler l’inscription à l’infrastructure RHUI et recréer une inscription à votre infrastructure de mise à jour, suivez les étapes ci-dessous :

1. Modifiez /etc/yum.repos.d/rh-cloud.repo et remplacez tous les `enabled=1` par `enabled=0`. Par exemple :
   
   ```bash
   sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo
   ```
   
2. Modifiez /etc/yum/pluginconf.d/rhnplugin.conf et remplacez `enabled=0` par `enabled=1`.
3. Ensuite inscrivez-vous auprès de l’infrastructure de votre choix, telle que le Portail client Red Hat. Suivez le guide des solutions Red Hat pour savoir [comment inscrire et abonner un système au Portail client Red Hat](https://access.redhat.com/solutions/253273).

> [!NOTE]
> L’accès à l’infrastructure RHUI hébergée sur Azure est inclus dans le prix de l’image RHEL avec paiement à l’utilisation. Annuler l’inscription à l’infrastructure RHUI hébergée sur Azure d’une machine virtuelle RHEL avec paiement à l’utilisation ne convertit pas la machine virtuelle en une machine virtuelle de type BYOL (apportez votre propre licence). Si vous enregistrez la même machine virtuelle avec une autre source de mises à jour, vous pouvez encourir des frais doubles : la première fois pour les frais de logiciel Azure RHEL, et la seconde fois pour les abonnements Red Hat. 
> 
> Si vous devez systématiquement utiliser une infrastructure de mise à jour autre que l’infrastructure RHUI hébergée sur Azure, envisagez de créer et déployer vos propres images (de type BYOL), comme décrit dans l’article [Créer et télécharger une machine virtuelle basée sur Red Hat pour Azure](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
> 

## <a name="next-steps"></a>Étapes suivantes
Pour créer une machine virtuelle Red Hat Enterprise Linux à partir d’une image avec paiement à l’utilisation dans le service Place de marché Azure et tirer parti de l’infrastructure RHUI hébergée sur Azure, voir [Place de marché Azure](https://azure.microsoft.com/marketplace/partners/redhat/). Vous serez en mesure d’utiliser `yum update` dans votre instance RHEL sans aucun paramétrage supplémentaire.


