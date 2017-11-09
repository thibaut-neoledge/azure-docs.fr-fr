---
title: "Importer et exporter un fichier de zone de domaine vers Azure DNS avec Azure CLI 1.0 | Microsoft Docs"
description: "Apprenez à importer et exporter un fichier de zone DNS vers Azure DNS à l’aide d’Azure CLI 1.0"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: d6d3fa7aa0e8b2462b3a6b4b66d3d87ab5535314
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli-10"></a>Importer et exporter un fichier de zone DNS en utilisant Azure CLI 1.0 

Cet article vous guide dans l’importation et l’exportation de fichiers de zone DNS pour Azure DNS à l’aide d’Azure CLI 1.0.

## <a name="introduction-to-dns-zone-migration"></a>Introduction à la migration de zone DNS

Un fichier de zone DNS est un fichier texte qui contient les détails de tous les enregistrements DNS contenus dans la zone. Il suit un format standard, ce qui permet le transfert d’enregistrements DNS entre différents systèmes DNS. L’utilisation d’un fichier de zone offre un moyen rapide, fiable et pratique de transférer une zone DNS vers ou depuis Azure DNS.

Azure DNS prend en charge l’importation et l’exportation de fichiers de zone à l’aide de l’interface de ligne de commande (CLI). L’importation de fichier de zone n’est actuellement **pas** prise en charge via Azure PowerShell ou le portail Azure.

Azure CLI 1.0 est un outil de ligne de commande multiplateforme permettant de gérer des services Azure. Elles est disponible pour les plateformes Windows, Mac et Linux sur la [page des téléchargements d’Azure](https://azure.microsoft.com/downloads/). La prise en charge multiplateforme est importante pour l’importation et l’exportation de fichiers de zone car le logiciel serveur de noms le plus courant, [BIND](https://www.isc.org/downloads/bind/), s’exécute en général sur Linux.

> [!NOTE]
> Il existe actuellement deux versions d’Azure CLI. CLI1.0 est basé sur Node.js et offre des commandes commençant par « azure ».
> CLI2.0 est basé sur Python et offre des commandes commençant par « az ». Si l’importation de fichier de zone est prise en charge dans les deux versions, nous recommandons d’utiliser les commandes CLI1.0, comme décrit dans cette page.

## <a name="obtain-your-existing-dns-zone-file"></a>Comment obtenir votre fichier de zone DNS existant

Avant d’importer un fichier de zone DNS dans Azure DNS, vous devez obtenir une copie du fichier de zone. La source de ce fichier dépend de l’emplacement où la zone DNS est actuellement hébergée.

* Si votre zone DNS est hébergée par un service tiers (comme un bureau d’enregistrement de domaines, un hébergeur DNS dédié ou un autre fournisseur de cloud), ce service doit fournir la possibilité de télécharger le fichier de zone DNS.
* Si votre zone DNS est hébergée sur le DNS Windows, le dossier par défaut pour les fichiers de zone est **%systemroot%\system32\dns**. Le chemin complet vers chaque fichier de zone s’affiche également sous l’onglet **Général** de la console DNS.
* Si votre zone DNS est hébergée via BIND, l’emplacement du fichier de zone pour chaque zone est spécifié dans le fichier de configuration BIND, **named.conf**.

> [!NOTE]
> Le format des fichiers de zone téléchargés à partir de GoDaddy diffère légèrement du format standard. Vous devez corriger ce problème avant d’importer ces fichiers de zone dans Azure DNS.
>
> Les noms DNS dans le RDATA de chaque enregistrement DNS sont considérés comme étant des noms complets, mais ils ne se terminent pas par un « . » Cela signifie qu’ils sont interprétés comme des noms relatifs par d’autres systèmes DNS. Vous devez modifier le fichier de zone pour ajouter le point final à ces noms avant de les importer dans Azure DNS.
>
> Par exemple, l’enregistrement CNAME « www 3600 IN CNAME contoso.com » doit être remplacé par « www 3600 IN CNAME contoso.com. »
> (avec un «. » final).

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importation d’un fichier de zone DNS dans Azure DNS

L’importation d’un fichier de zone crée une nouvelle zone dans Azure DNS si celle-ci n’existe pas. Si la zone existe déjà, les jeux d’enregistrements du fichier de zone doivent être fusionnés avec les jeux d’enregistrements existants.

### <a name="merge-behavior"></a>Comportement de la fusion

* Par défaut, les nouveaux jeux d’enregistrements sont fusionnés avec les jeux d’enregistrements existants. Les enregistrements identiques dans un jeu d’enregistrements fusionné sont dédupliqués.
* Si vous spécifiez l’option `--force`, le processus d’importation remplace les jeux d’enregistrements existants par les nouveaux jeux d’enregistrements. Les jeux d’enregistrements existants qui ne correspondent pas à des jeux d’enregistrements du fichier de zone importé ne sont pas supprimés.
* Lorsque les jeux d’enregistrements sont fusionnés, la TTL des jeux d’enregistrements existants est utilisée. Quand l’option `--force` est utilisée, la durée de vie du nouveau jeu d’enregistrements est utilisée.
* Les paramètres SOA (Start of Authority) (excepté `host`) sont toujours pris dans le fichier de zone importé, que l’option `--force` soit ou non utilisée. De même, pour le jeu d’enregistrements du serveur de noms à l’extrémité de la zone, la TTL est toujours dérivée du fichier de zone importé.
* Un enregistrement CNAME importé ne remplace pas un enregistrement CNAME existant portant le même nom, sauf si le paramètre `--force` est spécifié.
* En cas de conflit entre un enregistrement CNAME et un autre enregistrement du même nom ayant un type différent (qu’il soit nouveau ou existant), l’enregistrement existant est conservé. Ceci est indépendant de l’utilisation de `--force`.

### <a name="additional-information-about-importing"></a>Informations supplémentaires sur l’importation

Les remarques suivantes fournissent des informations techniques supplémentaires concernant le processus d’importation de zones.

* La directive `$TTL` , facultative, est prise en charge. Quand aucune directive `$TTL` n’est spécifiée, les enregistrements sans durée de vie explicite sont importés avec une durée de vie de 3 600 secondes par défaut. Lorsque deux enregistrements du même jeu d’enregistrements spécifient des TTL différentes, la moindre valeur est utilisée.
* La directive `$ORIGIN` , facultative, est prise en charge. Quand aucune directive `$ORIGIN` n’est définie, la valeur utilisée par défaut est le nom de zone tel qu’il est spécifié sur la ligne de commande (avec le point final).
* Les directives `$INCLUDE` et `$GENERATE` ne sont pas prises en charge.
* Les types d’enregistrements A, AAAA, CNAME, MX, NS, SOA, SRV et TXT sont pris en charge.
* L’enregistrement SOA est créé automatiquement par Azure DNS lors de la création d’une zone. Quand vous importez un fichier de zone, tous les paramètres SOA sont pris dans le fichier de zone, *sauf* le paramètre `host`. Ce paramètre utilise la valeur fournie par Azure DNS. Ce paramètre doit en effet faire référence au serveur de noms principal fourni par Azure DNS.
* Le jeu d’enregistrements du serveur de noms, à l’extrémité de la zone, est créé automatiquement par Azure DNS au moment de la création de la zone. Seule la durée de vie de ce jeu d’enregistrements est importée. Ces enregistrements contiennent les noms de serveurs de nom fournis par Azure DNS. Les données d’enregistrement ne sont pas remplacées par les valeurs contenues dans le fichier de zone importé.
* Pour la version préliminaire publique, Azure DNS prend uniquement en charge les enregistrements à chaîne unique. Les enregistrements TXT MultiString sont concaténés et tronqués à 255 caractères.

### <a name="cli-format-and-values"></a>Format et valeurs de l’interface CLI

Le format de la commande de l’interface CLI Azure pour importer une zone DNS est : 

```azurecli
azure network dns zone import [options] <resource group> <zone name> <zone file name>
```

Valeurs :

* `<resource group>` est le nom du groupe de ressources correspondant à la zone dans Azure DNS.
* `<zone name>` est le nom de la zone.
* `<zone file name>` est le chemin/nom du fichier de zone à importer.

S’il n’existe aucune zone de ce nom dans le groupe de ressources, cette zone est automatiquement créée. Si la zone existe déjà, les jeux d’enregistrements importés sont fusionnés avec les jeux d’enregistrements existants. Pour remplacer les jeux d’enregistrements existants, utilisez l’option `--force` .

Pour vérifier le format d’un fichier de zone sans l’importer, utilisez l’option `--parse-only` .

### <a name="step-1-import-a-zone-file"></a>Étape 1. Importer un fichier de zone

Pour importer un fichier de zone pour la zone **contoso.com**.

1. Connectez-vous à votre abonnement Azure à l’aide d’Azure CLI 1.0.

    ```azurecli
    azure login
    ```

2. Sélectionnez l’abonnement dans lequel vous souhaitez créer votre zone DNS.

    ```azurecli
    azure account set <subscription name>
    ```

3. Azure DNS est un service Azure Resource Manager uniquement ; vous devez donc basculer la CLI Azure en mode Resource Manager.

    ```azurecli
    azure config mode arm
    ```

4. Avant d’utiliser le service Azure DNS, vous devez inscrire votre abonnement pour utiliser le fournisseur de ressources Microsoft.Network. (Cette opération n’est à effectuer qu’une fois pour chaque abonnement.)

    ```azurecli
    azure provider register Microsoft.Network
    ```

5. Si ce n’est déjà fait, vous devez également créer un groupe de ressources Azure Resource Manager.

    ```azurecli
    azure group create myresourcegroup westeurope
    ```

6. Pour importer la zone **contoso.com** à partir du fichier **contoso.com.txt** dans une nouvelle zone DNS du groupe de ressources **myresourcegroup**, exécutez la commande `azure network dns zone import`.<BR>Cette commande charge le fichier de zone et l’analyse. Cette commande exécute une série de commandes sur le service Azure DNS, afin de créer la zone et tous les jeux d’enregistrements associés. Elle rend compte de la progression dans la fenêtre de console et signale les éventuels avertissements ou erreurs. Étant donné que les jeux d’enregistrements sont créés en série, l’importation d’un fichier de zone volumineux peut prendre quelques minutes.

    ```azurecli
    azure network dns zone import myresourcegroup contoso.com contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Étape 2. Vérifier la zone

Pour vérifier la zone DNS après avoir importé le fichier, vous pouvez utiliser une des méthodes suivantes :

* Vous pouvez répertorier les enregistrements à l’aide de la commande Azure CLI suivante :

    ```azurecli
    azure network dns record-set list myresourcegroup contoso.com
    ```

* Vous pouvez répertorier les enregistrements à l’aide de l’applet de commande PowerShell `Get-AzureRmDnsRecordSet`.
* Vous pouvez également utiliser `nslookup` pour vérifier la résolution des noms pour les enregistrements. Puisque la zone n’est pas encore déléguée, vous devez spécifier explicitement les serveurs de noms DNS Azure appropriés. L’exemple suivant montre comment récupérer les noms du serveur de noms assignés à la zone. Il montre également comment interroger l’enregistrement « www » à l’aide de `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/.../resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Étape 3. Mettre à jour la délégation DNS

Après avoir vérifié que la zone a été importée correctement, vous devez mettre à jour la délégation DNS afin qu’elle pointe vers les serveurs de noms Azure DNS. Pour plus d’informations, consultez l’article [Mettre à jour la délégation DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportation d’un fichier de zone DNS à partir d’Azure DNS

Le format de la commande de l’interface CLI Azure pour importer une zone DNS est : 

```azurecli
azure network dns zone export [options] <resource group> <zone name> <zone file name>
```

Valeurs :

* `<resource group>` est le nom du groupe de ressources correspondant à la zone dans Azure DNS.
* `<zone name>` est le nom de la zone.
* `<zone file name>` est le chemin d’accès/nom du fichier de zone à exporter.

Pour importer une zone, vous devez d’abord vous connecter, sélectionner votre abonnement, puis configurer l’interface de ligne de commande Azure pour utiliser le mode Azure Resource Manager.

### <a name="to-export-a-zone-file"></a>Pour exporter un fichier de zone

1. Connectez-vous à votre abonnement Azure à l’aide de la CLI Azure.

    ```azurecli
    azure login
    ```

2. Sélectionnez l’abonnement dans lequel vous souhaitez créer votre zone DNS.

    ```azurecli
    azure account set <subscription name>
    ```

3. Azure DNS est un service Azure Resource Manager uniquement. La CLI Azure doit être basculée en mode Azure Resource Manager.

    ```azurecli
    azure config mode arm
    ```

4. Pour exporter la zone Azure DNS existante **contoso.com** du groupe de ressources **myresourcegroup** vers le fichier **contoso.com.txt** (dans le dossier actuel), exécutez `azure network dns zone export`. Cette commande appelle le service Azure DNS pour énumérer les jeux d’enregistrements dans la zone et exporter les résultats dans un fichier de zone compatible BIND.

    ```azurecli
    azure network dns zone export myresourcegroup contoso.com contoso.com.txt
    ```
