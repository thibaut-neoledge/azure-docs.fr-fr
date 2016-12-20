---
title: "Gestion des jeux d’enregistrements DNS et des enregistrements dans Azure DNS à l’aide de l’interface de ligne de commande Azure | Microsoft Docs"
description: "Gestion des jeux d&quot;enregistrements DNS et des enregistrements dans Azure DNS lorsque votre domaine est hébergé dans Azure DNS. Toutes les commandes d’interface de ligne de commande pour les opérations sur les jeux d&quot;enregistrements et les enregistrements."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 2bc18d618cf8838209bea9f8a2d323e3b1042709

---

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Création d’enregistrements et de jeux d’enregistrements DNS à l’aide de l’interface de ligne de commande

> [!div class="op_single_selector"]
> * [Portail Azure](dns-operations-recordsets-portal.md)
> * [Interface de ligne de commande Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Cet article explique comment gérer des jeux d’enregistrements et des enregistrements pour votre zone DNS à l’aide de l’interface de ligne de commande (CLI) Azure multiplateforme.

Il est important de comprendre la différence entre les jeux d’enregistrements DNS et les enregistrements DNS individuels. Un jeu d’enregistrements est une collection d’enregistrements dans une zone ayant le même nom et le même type. Pour plus d’informations, consultez [Présentation des jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-cli.md).

## <a name="configure-the-cross-platform-azure-cli"></a>Configuration de l’interface de ligne de commande Azure multiplateforme

Azure DNS est un service Azure Resource Manager uniquement. Il ne dispose pas d’une API de gestion de Service Azure. Assurez-vous à l’aide de la commande `azure config mode arm` que l’interface de ligne de commande Azure est configurée pour utiliser le mode Resource Manager.

Si le message « **Erreur : « dns » n’est pas une commande azure**» s’affiche, cela est probablement dû au fait que vous utilisez l’interface de ligne de commande Azure en mode Azure Service Management et non pas en mode Resource Manager.

## <a name="create-a-new-record-set-and-record"></a>Création d’un jeu d’enregistrements et d’un enregistrement

Pour créer un jeu d’enregistrements dans le portail Azure, consultez [Création d’un jeu d’enregistrements et d’un enregistrement](dns-getstarted-create-recordset-cli.md).

## <a name="retrieve-a-record-set"></a>Récupérer un jeu d’enregistrements

Pour récupérer un jeu d’enregistrements existant, utilisez `azure network dns record-set show`. Spécifiez le groupe de ressources, le nom de la zone, le nom relatif du jeu d’enregistrements et le type d’enregistrement. Utilisez l’exemple ci-dessous en remplaçant les valeurs par les vôtres.

```azurecli
azure network dns record-set show myresourcegroup contoso.com www A
```

## <a name="list-record-sets"></a>Liste des jeux d'enregistrements

Vous pouvez répertorier tous les enregistrements d’une zone DNS à l’aide de la commande `azure network dns record-set list` . Vous devez spécifier le nom du groupe de ressources et le nom de la zone.

### <a name="to-list-all-record-sets"></a>Pour répertorier tous les jeux d’enregistrements

Cet exemple retourne tous les jeux d’enregistrements, quel que soit le nom ou le type d’enregistrement :

```azurecli
azure network dns record-set list myresourcegroup contoso.com
```

### <a name="to-list-record-sets-of-a-given-type"></a>Pour répertorier les jeux d’enregistrements d’un type donné

Cet exemple retourne tous les jeux d’enregistrements correspondant au type d’enregistrement donné (dans ce cas, les enregistrements « A »).

```azurecli
azure network dns record-set list myresourcegroup contoso.com A
```

## <a name="add-a-record-to-a-record-set"></a>Ajout d’un enregistrement à un jeu d'enregistrements

Vous pouvez utiliser la commande `azure network dns record-set add-record`pour ajouter des enregistrements aux jeux d’enregistrements. Les paramètres pour ajouter des enregistrements à un jeu d'enregistrements varient selon le type du jeu d'enregistrements créé. Par exemple, lors de l’utilisation d’un jeu d’enregistrements de type « A », vous pouvez spécifier uniquement des enregistrements avec le paramètre `-a <IPv4 address>`.

Pour créer un jeu d’enregistrements, utilisez la commande `azure network dns record-set create`. Spécifiez le groupe de la ressource, le nom de la zone, le nom relatif du jeu d’enregistrements, le type d’enregistrement et la durée de vie (TTL). Si le paramètre `--ttl` n’est pas défini, la valeur par défaut (en secondes) est quatre.

```azurecli
azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300
```

Après avoir créé le jeu d’enregistrements « A », ajoutez l’adresse IPv4 à l’aide de la commande `azure network dns record-set add-record`.

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1
```

Les exemples suivants montrent comment créer un jeu d’enregistrements de chaque type d’enregistrement. Chaque jeu d’enregistrements contient un seul enregistrement.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="update-a-record-in-a-record-set"></a>Mettre à jour un enregistrement dans un jeu d’enregistrements

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Pour ajouter une autre adresse IP (1.2.3.4) à un jeu d’enregistrements « A » (www) existant :

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Pour supprimer une valeur existante d’un jeu d’enregistrements

, utilisez `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK

## <a name="remove-a-record-from-a-record-set"></a>Suppression d’un enregistrement d’un jeu d’enregistrements

Des enregistrements peuvent être supprimés d’un jeu d’enregistrements à l’aide de la commande `azure network dns record-set delete-record`. L’enregistrement à supprimer doit correspondre exactement à un enregistrement existant relativement à tous les paramètres.

La suppression du dernier enregistrement d'un jeu d'enregistrements ne supprime pas le jeu d'enregistrements. Pour plus d’informations, consultez la section [Suppression d’un jeu d’enregistrements](#delete)de cet article.

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

azure network dns record-set delete myresourcegroup contoso.com www A
```

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Supprimer un enregistrement AAAA d’un jeu d’enregistrements

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"
```

### <a name="remove-a-cname-record-from-a-record-set"></a>Supprimer un enregistrement CNAME d’un jeu d’enregistrements

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
```

### <a name="remove-an-mx-record-from-a-record-set"></a>Supprimer un enregistrement MX d’un jeu d’enregistrements

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5
```

### <a name="remove-an-ns-record-from-record-set"></a>Supprimer un enregistrement NS d’un jeu d’enregistrements

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"
```

### <a name="remove-a-ptr-record-from-a-record-set"></a>Suppression d’un enregistrement PTR d’un jeu d’enregistrements

Dans ce cas « my-arpa-zone.com » représente la zone ARPA représentant votre plage d’adresses IP.  Chaque enregistrement PTR dans cette zone correspond à une adresse IP au sein de cette plage d’adresses IP.

```azurecli
azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"
```

### <a name="remove-an-srv-record-from-a-record-set"></a>Supprimer un enregistrement SRV d’un jeu d’enregistrements

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"
```

### <a name="remove-a-txt-record-from-a-record-set"></a>Supprimer un enregistrement TXT d’un jeu d’enregistrements

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"
```

## <a name="a-namedeleteadelete-a-record-set"></a><a name="delete"></a>Suppression d’un jeu d'enregistrements

Les jeux d’enregistrements peuvent être supprimés à l’aide de l’applet de commande `Remove-AzureRmDnsRecordSet` . Vous ne pouvez pas supprimer à l’extrémité de la zone les jeux d’enregistrements SOA et NS (nom = "@") qui ont été créés automatiquement durant la création de la zone. Ils sont supprimés automatiquement lors de la suppression de la zone.

Dans l’exemple suivant, le jeu d’enregistrements « A » « test-a » est supprimé de la zone DNS « contoso.com » :

```azurecli
azure network dns record-set delete myresourcegroup contoso.com  "test-a" A
```

Le commutateur facultatif `-q` peut être utilisé pour supprimer l’invite de confirmation.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure DNS, consultez la [Vue d’ensemble d’Azure DNS](dns-overview.md). Pour plus d’informations sur l’automatisation de DNS, consultez [Création des zones DNS et de jeux d’enregistrements à l’aide du Kit de développement logiciel (SDK) .NET](dns-sdk.md).

Si vous voulez utiliser des enregistrements DNS inversés, consultez [Gestion des enregistrements DNS inversés pour vos services à l’aide de l’interface de ligne de commande Azure](dns-reverse-dns-record-operations-cli.md).



<!--HONumber=Nov16_HO3-->


