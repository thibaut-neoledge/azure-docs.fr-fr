---
title: "Optimiser le débit du réseau des machines virtuelles | Microsoft Docs"
description: "Découvrez comment optimiser le débit du réseau des machines virtuelles Azure."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2017
ms.author: steveesp
translationtype: Human Translation
ms.sourcegitcommit: 50be31e179bf52e009596fbc68339dfb5a1aa1e4
ms.openlocfilehash: d53b1cae9845be32bd053ef196203ea83df06b10
ms.lasthandoff: 02/15/2017


---

# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimiser le débit du réseau des machines virtuelles Azure

Les machines virtuelles Azure disposent de paramètres réseau par défaut qui peuvent être davantage optimisés pour le débit du réseau. Cet article décrit comment optimiser le débit du réseau pour les machines virtuelles Microsoft Azure Windows et Linux, notamment les distributions majeures telles que Ubuntu, CentOS et Red Hat.

## <a name="windows-vm"></a>Machine virtuelle Windows

Une machine virtuelle qui bénéficie de la mise à l’échelle côté réception (RSS) peut atteindre un débit maximal supérieur à celui d’une machine virtuelle sans RSS. La mise à l’échelle côté réception (RSS) peut être désactivée par défaut sur une machine virtuelle Windows. Effectuez les étapes suivantes pour déterminer si la mise à l’échelle côté réception (RSS) est activée et, si nécessaire, pour l’activer.

1. Entrez la commande PowerShell `Get-NetAdapterRss` pour savoir si la mise à l’échelle côté réception (RSS) est activée sur une carte réseau. Dans l’exemple de sortie suivant retourné par `Get-NetAdapterRss`, la mise à l’échelle côté réception (RSS) n’est pas activée.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Entrez la commande suivante pour activer la mise à l’échelle côté réception (RSS) :

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    La commande précédente n’a pas de sortie. La commande a modifié les paramètres de la carte réseau, entraînant une perte temporaire de la connectivité pendant environ une minute. Une boîte de dialogue de reconnexion s’affiche lors de la perte de connectivité. En général, la connectivité est rétablie après la troisième tentative.
3. Vérifiez que la mise à l’échelle côté réception (RSS) est activée sur la machine virtuelle en entrant de nouveau la commande `Get-NetAdapterRss`. Si l’opération réussit, l’exemple de sortie suivant est retourné :

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : True
    ```

## <a name="linux-vm"></a>Machine virtuelle Linux

La mise à l’échelle côté réception (RSS) est toujours activée par défaut sur une machine virtuelle Azure Linux. Les noyaux Linux publiés depuis janvier 2017 incluent de nouvelles options d’optimisation du réseau qui permettent à une machine virtuelle Linux d’obtenir un débit réseau plus élevé.

### <a name="ubuntu"></a>Ubuntu

Pour bénéficier de l’optimisation, mettez tout d’abord à jour vers la version la plus récente, à compter de janvier 2017, qui est :
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
Une fois la mise à jour terminée, entrez les commandes suivantes pour obtenir le noyau le plus récent :

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

Commande facultative :

`apt-get -y dist-upgrade`

### <a name="centos"></a>CentOS

Pour bénéficier de l’optimisation, mettez tout d’abord à jour vers la version la plus récente, à compter de janvier 2017, qui est :
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
Une fois la mise à jour terminée, installez les Services d’intégration Linux (LIS) les plus récents.
L’optimisation du débit est incluse dans les LIS, à partir de la version 4.1.3. Entrez les commandes suivantes pour installer LIS :

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Pour bénéficier de l’optimisation, mettez tout d’abord à jour vers la version la plus récente, à compter de janvier 2017, qui est :

"Publisher": "RedHat" "Offer": "RHEL" "Sku": "7.3" "Version": "7.3.20161104"

Une fois la mise à jour terminée, installez les Services d’intégration Linux (LIS) les plus récents.
L’optimisation du débit est incluse dans les LIS, à partir de la version 4.1.3. Entrez les commandes suivantes pour télécharger et installer les LIS :

```bash
mkdir lis4.1.3
cd lis4.1.3
wget https://download.microsoft.com/download/7/6/B/76BE7A6E-E39F-436C-9353-F4B44EF966E9/lis-rpms-4.1.3.tar.gz
tar xvzf lis-rpms-4.1.3.tar.gz
cd LISISO
install.sh� #or upgrade.sh if previous LIS was previously installed
```
 
Apprenez-en plus sur les Services d’intégration Linux version 4.1 pour Hyper-V en consultant la [page de téléchargement](https://www.microsoft.com/download/details.aspx?id=51612).

