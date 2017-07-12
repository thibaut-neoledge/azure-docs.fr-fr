---
title: "Déplacement de fichiers vers et à partir d’une machine virtuelle Linux à l’aide de SCP | Microsoft Docs"
description: "Déplacement sécurisé de fichiers vers et à partir d’une machine virtuelle Linux à l’aide de SCP et d’une paire de clés SSH."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1935bde486d8f6f8e80a73447c4b0ea5d4671b45
ms.contentlocale: fr-fr
ms.lasthandoff: 04/03/2017

---

<a id="moving-files-to-and-from-a-linux-vm-using-scp" class="xliff"></a>

# Déplacement de fichiers vers et à partir d’une machine virtuelle Linux à l’aide de SCP

Cet article explique comment déplacer des fichiers à partir de votre poste de travail vers une machine virtuelle Linux Azure ou à partir d’une machine virtuelle Linux Azure vers votre poste de travail, à l’aide de la copie sécurisée (SCP).  Par exemple, nous déplaçons des fichiers de configuration Azure vers une machine virtuelle Linux et nous extrayons un répertoire de fichiers journaux, à l’aide de SCP et de clés SSH.   

Pour cet article, les conditions requises sont les suivantes :

- [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)

- [des fichiers de clés SSH publiques et privées](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

<a id="quick-commands" class="xliff"></a>

## Commandes rapides

Copier un fichier vers la machine virtuelle Linux

```bash
scp file user@host:directory/targetfile
```

Copier un fichier à partir de la machine virtuelle Linux

```bash
scp user@host:directory/file targetfile
```

<a id="detailed-walkthrough" class="xliff"></a>

## Procédure pas à pas

Le déplacement de fichiers entre votre poste de travail et une machine virtuelle Linux, de manière rapide et sécurisée, constitue une partie essentielle de la gestion de votre infrastructure Azure.  Pour cet article, nous utilisons SCP, un outil basé sur SSH et inclus dans le shell Bash par défaut de Linux, Mac et Windows.

<a id="ssh-key-pair-authentication" class="xliff"></a>

## Authentification avec une paire de clés SSH

SCP utilise SSH pour la couche de transport.  En utilisant SSH pour le transport, SSH gère l’authentification sur l’hôte de destination tout en déplaçant le fichier dans un tunnel chiffré fourni par défaut avec SSH.  Pour l’authentification SSH, il est possible d’utiliser des noms d’utilisateur et des mots de passe, mais l’authentification par clé SSH publique et privée est fortement recommandée en tant que pratique de sécurité optimale. Une fois que SSH a authentifié la connexion, SCP commence alors le processus de copie du fichier.  À l’aide d’une valeur `~/.ssh/config` correctement configurée et de clés publique et privée SSH, la connexion SCP peut être établie sans utiliser de nom d’utilisateur et simplement à l’aide d’un nom de serveur.  Si vous n’avez qu’une clé SSH, SCP la recherche dans le répertoire `~/.ssh/` et l’utilise par défaut pour se connecter à la machine virtuelle.

Pour plus d’informations sur la configuration de votre `~/.ssh/config` et les clés publique et privée SSH, consultez l’article [Créer des clés SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="scp-a-file-to-a-linux-vm" class="xliff"></a>

## Copie sécurisée (SCP) d’un fichier vers une machine virtuelle Linux

Pour le premier exemple, nous copions un fichier d’informations d’identification Azure vers une machine virtuelle Linux qui est utilisée pour déployer l’automatisation.  Étant donné que ce fichier contient des informations d’identification d’API Azure, notamment des secrets, la sécurité est importante et le tunnel chiffré fourni par SSH protège le contenu du fichier.

```bash
scp ~/.azure/credentials myserver:/home/ahmet/.azure/credentials
```

<a id="scp-a-directory-from-a-linux-vm" class="xliff"></a>

## Copie sécurisée (SCP) d’un répertoire à partir d’une machine virtuelle Linux

Pour cet exemple, nous copions un répertoire de fichiers journaux à partir de la machine virtuelle Linux sur votre poste de travail.  Un fichier journal peut contenir, ou pas, des données sensibles ou confidentielles et l’utilisation de SCP garantit que le contenu des fichiers journaux est chiffré.  L’utilisation de SCP pour transférer les fichiers de manière sécurisée est le moyen le plus simple pour copier le répertoire et les fichiers journaux sur votre poste de travail tout en respectant la sécurité.

```bash
scp -r myserver:/home/ahmet/logs/ /tmp/.
```

L’indicateur de l’interface de ligne de commande `-r` indique à SCP de copier de manière récursive les fichiers et les répertoires à partir du point du répertoire indiqué dans la commande.  Notez également que la syntaxe de ligne de commande est similaire à une commande de copie `cp`.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

* [Gérer les utilisateurs, SSH et vérifier ou réparer les disques de machines virtuelles Azure Linux à l'aide de l’extension VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Désactiver les mots de passe SSH sur votre machine virtuelle Linux en configurant SSHD](mac-disable-ssh-password-usage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

