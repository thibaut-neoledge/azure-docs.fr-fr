---
title: Configuration de DHCPv6 pour les machines virtuelles Linux | Microsoft Docs
description: Configurer DHCPv6 pour la machines virtuelles Linux
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
keywords: "IPv6, équilibreur de charge azure, double pile, adresse ip publique, ipv6 natif, mobile, iot"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: cf1eafc7bca5bddeb32f1e1e05e660d6877ed805
ms.openlocfilehash: f14a0473ebcf21e04759cb8eb77eb684c4d9a9cb

---

# <a name="configuring-dhcpv6-for-linux-vms"></a>Configuration de DHCPv6 pour les machines virtuelles Linux

Certaines des images de machines virtuelles Linux de la Place de marché Microsoft Azure ne présentent pas de configuration DHCPv6 par défaut. Pour prendre en charge IPv6, DHCPv6 doit être configuré dans la distribution du système d’exploitation Linux que vous utilisez. Différentes distributions Linux présentent des méthodes de configuration de DHCPv6 distinctes, car elles utilisent des packages non identiques.

> [!NOTE]
> Les images SUSE Linux et CoreOS récentes de la Place de marché Azure ont été préconfigurées avec DHCPv6. Aucune modification supplémentaire n’est requise pour l’utilisation de ces images.

Ce document vous explique comment activer DHCPv6 pour que votre machine virtuelle Linux obtienne une adresse IPv6.

> [!WARNING]
> Une modification incorrecte des fichiers de configuration réseau peuvent vous faire perdre l’accès du réseau à votre machine virtuelle. Nous vous recommandons de tester vos modifications de configuration sur des systèmes hors production. Les instructions de cet article ont été testées sur les dernières versions des images Linux dans la Place de marché Azure. Consultez la documentation de votre version spécifique de Linux pour obtenir des instructions plus détaillées.

## <a name="ubuntu"></a>Ubuntu

1. Modifiez le fichier `/etc/dhcp/dhclient6.conf` , puis ajoutez la ligne suivante :

        timeout 10;

2. Modifiez la configuration du réseau pour l’interface eth0, selon la méthode suivante :

   * Sur **Ubuntu 12.04 et 14.04**, modifiez le fichier `/etc/network/interfaces.d/eth0.cfg`.
   * Sur **Ubuntu 16.04**, modifiez le fichier `/etc/network/interfaces.d/50-cloud-init.cfg`.

        iface eth0 inet6 auto        up sleep 5        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renouvelez l’adresse IPv6 :

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Modifiez le fichier `/etc/dhcp/dhclient6.conf` , puis ajoutez la ligne suivante :

        timeout 10;

2. Modifiez le fichier `/etc/network/interfaces` , puis ajoutez la configuration suivante :

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renouvelez l’adresse IPv6 :

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. Modifiez le fichier `/etc/sysconfig/network` , puis ajoutez le paramètre suivant :

        NETWORKING_IPV6=yes

2. Modifiez le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0` , puis ajoutez les deux paramètres suivants :

        IPV6INIT=yes
        DHCPV6C=yes

3. Renouvelez l’adresse IPv6 :

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 et openSUSE 13

Les images SLES et openSUSE récentes d’Azure ont été préconfigurées avec DHCPv6. Aucune modification supplémentaire n’est requise pour l’utilisation de ces images. Si vous possédez une machine virtuelle basée sur une image SUSE plus ancienne ou personnalisée, appliquez les étapes suivantes :

1. Installez le package `dhcp-client` , si nécessaire :

    ```bash
    sudo zypper install dhcp-client
    ```

2. Modifiez le fichier `/etc/sysconfig/network/ifcfg-eth0` , puis ajoutez le paramètre suivant :

        DHCLIENT6_MODE='managed'

3. Renouvelez l’adresse IPv6 :

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 et openSUSE Leap

Les images SLES et openSUSE récentes d’Azure ont été préconfigurées avec DHCPv6. Aucune modification supplémentaire n’est requise pour l’utilisation de ces images. Si vous possédez une machine virtuelle basée sur une image SUSE plus ancienne ou personnalisée, appliquez les étapes suivantes :

1. Modifiez le fichier `/etc/sysconfig/network/ifcfg-eth0` , puis remplacez ce paramètre

        #BOOTPROTO='dhcp4'

    avec la valeur suivante :

        BOOTPROTO='dhcp'

2. Ajoutez le paramètre suivant à `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Renouvelez l’adresse IPv6 :

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Les images CoreOS récentes d’Azure ont été préconfigurées avec DHCPv6. Aucune modification supplémentaire n’est requise pour l’utilisation de ces images. Si vous possédez une machine virtuelle basée sur une image CoreOS ancienne ou personnalisée, appliquez les étapes suivantes :

1. Modifiez le fichier `/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renouvelez l’adresse IPv6 :

    ```bash
    sudo systemctl restart systemd-networkd
    ```



<!--HONumber=Nov16_HO3-->


