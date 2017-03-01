---
title: "Création d’un jeu d’enregistrements et d’enregistrements pour une zone DNS à l’aide de PowerShell | Microsoft Docs"
description: "Comment créer des enregistrements d’hôtes pour Azure DNS. Définition de jeux d’enregistrements et d’enregistrements à l’aide de PowerShell"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a8068c5a-f248-4e97-be97-8bd0d79eeffd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 67b780d66eac4199b0a2367f575477191542cfa7
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Création de jeux d’enregistrements et d’enregistrements DNS à l’aide de PowerShell

> [!div class="op_single_selector"]
> * [Portail Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

Cet article vous guide dans le processus de création d’enregistrements et de jeux d’enregistrements à l’aide de Azure PowerShell.

## <a name="introduction"></a>Introduction

Avant de créer des enregistrements DNS dans Azure DNS, vous devez comprendre comment Azure DNS organise les enregistrements DNS en jeux d’enregistrements DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Pour plus d’informations sur les enregistrements DNS dans Azure DNS, voir [Enregistrements et zones DNS](dns-zones-records.md).

## <a name="create-a-record-set-and-record"></a>Création d’un jeu d’enregistrements et d’un enregistrement

Cette section décrit la procédure de création d’enregistrements DNS dans Azure DNS. Les exemples reposent sur l’hypothèse [que vous avez déjà installé Azure PowerShell que vous êtes connecté et que vous avez créé une zone DNS](dns-getstarted-create-dnszone.md).

Les exemples fournis sur cette page utilisent tous le type d’enregistrement DNS « A ». Pour plus d’informations sur les autres types d’enregistrements ainsi que sur la procédure de gestion des enregistrements et des jeux d’enregistrements DNS, voir l’article [Gérer les enregistrements et les jeux d’enregistrements DNS à l’aide de PowerShell](dns-operations-recordsets.md).

Si votre nouvel enregistrement porte le même nom et est du même type qu’un enregistrement existant, vous devez l’[ajouter au jeu d’enregistrements existant](#add-a-record-to-an-existing-record-set). En revanche, si le nom et le type de votre nouvel enregistrement diffèrent de ceux de tous les enregistrements existants, vous devez [créer un jeu d’enregistrements](#create-records-in-a-new-record-set). 

### <a name="create-records-in-a-new-record-set"></a>Créer des enregistrements dans un nouveau jeu d’enregistrements

Vous pouvez utiliser l’applet de commande `New-AzureRmDnsRecordSet` pour créer des jeux d’enregistrements. Lors de la création d’un jeu d’enregistrements, vous devez en spécifier le nom, la zone et la durée de vie (TTL), ainsi que les enregistrements à créer et leur type.

Pour créer un jeu d’enregistrements à l’apex de la zone (dans cet exemple, « contoso.com »), utilisez le nom d’enregistrement "@" (guillemets compris). Il s'agit d'une convention DNS courante.

L’exemple suivant crée un jeu d’enregistrements portant le nom relatif « www » dans la zone DNS « contoso.com ». Le nom complet du jeu d’enregistrements est « www.contoso.com ». Le type d’enregistrement est « A » et la durée de vie 3 600 secondes. Le jeu d’enregistrements ne contient qu’un seul enregistrement, dont l’adresse IP est « 1.2.3.4 »

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Si vous devez créer un jeu d’enregistrements contenant plusieurs enregistrements, vous devez commencer par créer un groupe local contenant les enregistrements à ajouter.  Ce groupe est transmis à `New-AzureRmDnsRecordSet` comme suit :

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

### <a name="add-a-record-to-an-existing-record-set"></a>Ajouter un enregistrement à un jeu d’enregistrements existant

Pour ajouter un enregistrement à un jeu d’enregistrements existant, suivez les trois étapes suivantes :

1. Obtenez le jeu d’enregistrements existant

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Ajoutez le nouvel enregistrement au jeu d’enregistrements local. Cette opération se fait hors connexion.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Validez la modification sur le service Azure DNS. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

### <a name="verify-name-resolution"></a>Vérifier la résolution de noms

Vous pouvez vérifier la présence de vos enregistrements DNS sur les serveurs de noms Azure DNS à l’aide d’outils DNS comme nslookup, dig ou [l’applet de commande PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone dans Azure DNS, vous devez [diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone](dns-getstarted-create-dnszone.md#test-name-servers). Veillez à spécifier les valeurs correctes pour votre zone d’enregistrements dans l’exemple suivant :

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déléguer votre nom de domaine aux serveurs de noms Azure DNS](dns-domain-delegation.md).

Découvrez comment [gérer les zones DNS à l’aide de PowerShell](dns-operations-dnszones.md).

Découvrez comment [gérer des enregistrements et des jeux d’enregistrements DNS à l’aide de PowerShell](dns-operations-recordsets.md).



