<properties
    pageTitle="Réinitialiser l'accès sur des machines virtuelles Azure Linux à l'aide de l’extension VMAccess | Microsoft Azure"
    description="Réinitialisez l'accès sur des machines virtuelles Azure Linux à l'aide de l’extension VMAccess."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="v-livech"
/>

# Gérer les utilisateurs, SSH et vérifier ou réparer les disques de machines virtuelles Azure Linux à l'aide de l’extension VMAccess

Cet article vous explique comment utiliser l’extension Azure VMAccess pour vérifier ou réparer un disque, réinitialiser l’accès des utilisateurs, gérer les comptes d’utilisateur ou réinitialiser la configuration SSHD sous Linux.

Les conditions préalables sont : [un compte Azure](https://azure.microsoft.com/pricing/free-trial/), [des clés publiques et privées SSH](virtual-machines-linux-mac-create-ssh-keys.md) et l’interface de ligne de commande Azure installée en mode Resource Manager avec `azure config mode arm`.

## Commandes rapides

Il existe deux façons d’utiliser VMAccess sur vos machines virtuelles Linux :

- À l’aide de l’interface de ligne de commande Azure et des paramètres requis.
- À l’aide de fichiers JSON bruts que VMAccess traite et exploite.

Pour la section sur la commande rapide, nous utiliserons la méthode Interface de ligne de commande Azure `azure vm reset-access`. Dans les exemples de commandes suivants, remplacez les valeurs contenant « example » par les valeurs de votre propre environnement.

## Création d'un groupe de ressources et d’une machine virtuelle Linux

```bash
azure group create resourcegroupexample westus
```

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u userexample \
-g resourcegroupexample \
-l westus \
-y Linux \
-n debianexamplevm \
-Q Debian
```

## Réinitialisation du mot de passe racine

Pour réinitialiser le mot de passe racine :

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u root -p examplenewPassword
```

## Réinitialisation de la clé SSH

Pour réinitialiser la clé SSH d'un utilisateur non racine :

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u userexample -M ~/.ssh/id_rsa.pub
```

## Créer un utilisateur

Pour créer un utilisateur :

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -u userexample -p examplePassword
```

## Supprimer un utilisateur

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -R userexample
```

## Réinitialiser SSHD

Pour réinitialiser la configuration SSHD :

```bash
azure vm reset-access -g exampleResourceGroup -n exampleVMName -r
```


## Procédure pas à pas

### VMAccess défini :

Le disque de votre machine virtuelle Linux affiche des erreurs. Vous avez d'une certaine manière réinitialisé le mot de passe racine de votre machine virtuelle Linux ou supprimé accidentellement votre clé privée SSH. Dans les anciens centres de données, vous deviez aller sur place et ouvrir le KVM pour accéder à la console du serveur. Considérez l’extension Azure VMAccess comme ce commutateur KVM qui vous permet d’accéder à la console pour réinitialiser l’accès à Linux ou effectuer la maintenance au niveau du disque.

Pour la procédure pas à pas, nous allons utiliser la forme longue de VMAccess qui utilise des fichiers JSON bruts. Ces fichiers JSON VMAccess peuvent également être appelés à partir de modèles Azure.

### Utilisation de VMAccess pour vérifier ou réparer le disque d'une machine virtuelle Linux

À l'aide de VMAccess, vous pouvez lancer une opération fsck sur le disque de votre machine virtuelle Linux. Vous pouvez également effectuer une vérification de disque et une réparation de disque à l’aide d’un VMAccess.

Pour vérifier puis réparer le disque, utilisez ce script VMAccess :

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### Utilisation de VMAccess pour réinitialiser l'accès utilisateur à Linux

Si vous ne pouvez plus accéder à la racine de votre machine virtuelle Linux, vous pouvez lancer un script VMAccess pour réinitialiser le mot de passe racine.

Pour réinitialiser le mot de passe racine, utilisez ce script VMAccess :

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"exampleNewPassword",   
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Pour réinitialiser la clé SSH d'un utilisateur non racine, utilisez ce script VMAccess :

`reset_ssh_key.json`

```json
{
  "username":"exampleUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",   
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### Utilisation de VMAccess pour gérer des comptes d'utilisateur sous Linux

VMAccess est un script Python qui peut servir à gérer les utilisateurs de votre machine virtuelle Linux sans connexion ni utilisation du compte sudo ou racine.

Pour créer un utilisateur, utilisez ce script VMAccess :

`create_new_user.json`

```json
{
"username":"exampleNewUserName",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",
"password":"examplePassword",
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Pour créer un utilisateur :

`remove_user.json`

```json
{
"remove_user":"exampleUser",
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### Utilisation de VMAccess pour réinitialiser la configuration SSHD

Si vous apportez des modifications à la configuration SSHD des machines virtuelles Linux et fermez la connexion SSH avant de vérifier vos changements, vous risquez de ne plus pouvoir retourner à votre configuration SSH. VMAccess peut servir à rétablir la configuration SSHD avec des paramètres reconnus comme adéquats sans connexion par le biais de SSH..

Pour réinitialiser la configuration SSHD, utilisez ce script VMAccess :

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Exécutez le script VMAccess avec :

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## Étapes suivantes

La mise à jour de Linux à l’aide d’extensions Azure VMAccess est une méthode permettant d’apporter des modifications à une machine virtuelle Linux en cours d’exécution. Vous pouvez également utiliser des outils tels que cloud-init et des modèles Azure pour modifier votre machine virtuelle Linux au démarrage.

[À propos des extensions et des fonctionnalités des machines virtuelles](virtual-machines-linux-extensions-features.md)

[Création de modèles Azure Resource Manager avec des extensions de machine virtuelle Linux](virtual-machines-linux-extensions-authoring-templates.md)

[Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création](virtual-machines-linux-using-cloud-init.md)

<!---HONumber=AcomDC_0831_2016-->