<properties
   pageTitle="Importer et exporter un fichier de zone de domaine avec Azure DNS | Microsoft Azure"
   description="Apprenez à importer et exporter un fichier de zone DNS vers Azure DNS à l’aide de l’interface de ligne de commande Azure"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/11/2016"
   ms.author="jonatul"/>

# Importer et exporter un fichier de zone DNS


Ce guide décrit l’importation et l’exportation de fichiers de zone DNS vers et depuis Azure DNS.

## Introduction à la migration de zone DNS

Un fichier de zone DNS est un fichier texte qui contient les détails de tous les enregistrements DNS contenus dans la zone. Il suit un format standard, ce qui permet le transfert d’enregistrements DNS entre différents systèmes DNS. L’utilisation d’un fichier de zone offre un moyen rapide, fiable et pratique de transférer une zone DNS vers ou depuis Azure DNS.

Azure DNS prend en charge l’importation et l’exportation de fichiers de zone à l’aide de l’interface de ligne de commande (CLI). Cet article explique comment utiliser cette fonctionnalité.

La CLI Azure est un outil de ligne de commande multiplateforme permettant de gérer des services Azure. Compatible avec les plateformes Windows, Mac et Linux, elle est disponible à partir de la [page Téléchargements d’Azure](https://azure.microsoft.com/downloads/). La prise en charge multiplateforme est particulièrement importante pour les opérations d’exportation et d’importation de fichiers de zone, car le logiciel de serveur de noms le plus courant, [BIND](https://www.isc.org/downloads/bind/), est généralement exécuté sur Linux.

## Comment obtenir votre fichier de zone DNS existant

Avant d’importer un fichier de zone DNS dans Azure DNS, vous devez obtenir une copie du fichier de zone. La source de ce fichier dépend de l’emplacement où la zone DNS est actuellement hébergée. Par exemple :

- Si votre zone DNS est hébergée par un service tiers (comme un bureau d’enregistrement de domaines, un hébergeur DNS dédié ou un autre fournisseur de cloud), ce service doit fournir la possibilité de télécharger le fichier de zone DNS.
-	Si votre zone DNS est hébergée sur le DNS de Windows, le dossier par défaut pour les fichiers de zone est **%systemroot%\\system32\\dns**. Le chemin d’accès complet vers chaque fichier de zone s’affiche également sous l’onglet **General** de la console de gestion du service DNS.
-	Si votre zone DNS est hébergée à l’aide de BIND, l’emplacement du fichier de zone pour chaque zone est spécifié dans le fichier de configuration BIND, **named.conf**.

>[AZURE.NOTE] Le format des fichiers de zone téléchargés à partir de GoDaddy diffère légèrement du format standard. Vous devez corriger ce problème avant d’importer ces fichiers de zone dans Azure DNS. Les noms DNS dans les RData de chaque enregistrement DNS sont considérés comme étant des noms complets, mais ne se terminent pas par un « . » Cela signifie qu’ils sont interprétés comme des noms relatifs par d’autres systèmes DNS. Vous devez modifier le fichier de zone pour ajouter le point final à ces noms avant de les importer dans Azure DNS.

## Importation d’un fichier de zone DNS dans Azure DNS

La commande de CLI Azure permettant d’importer une zone DNS utilise le format suivant :

	azure network dns zone import [options] <resource group> <zone name> <zone file name>

Où :

- `<resource group>` est le nom du groupe de ressources correspondant à la zone dans Azure DNS.
- `<zone name>` est le nom de la zone.
- `<zone file name>` est le chemin d’accès/nom du fichier de zone à importer.

S’il n’existe aucune zone de ce nom dans le groupe de ressources, cette zone sera automatiquement créée. Si la zone existe déjà, les jeux d’enregistrements importés seront fusionnés avec les jeux d’enregistrements existants. Pour remplacer les jeux d’enregistrements existants, utilisez l’option `--force`. Vous trouverez ci-dessous des informations supplémentaires sur [la manière de fusionner les jeux d’enregistrements](#merge-the-file-with-existing-data).

Pour vérifier le format d’un fichier de zone sans réellement l’importer, utilisez l’option `--parse-only`.

Examinons un exemple pas à pas. Supposons que vous souhaitiez importer un fichier de zone pour la zone **contoso.com**.

1. Connectez-vous à votre abonnement Azure à l’aide de la CLI Azure.

	`azure login`

2. Sélectionnez l’abonnement dans lequel vous souhaitez créer votre zone DNS.

	`azure account set <subscription name>`

3. Azure DNS est un service Azure Resource Manager uniquement ; vous devez donc basculer la CLI Azure en mode Resource Manager.

	`azure config mode arm`

4. Avant d’utiliser le service Azure DNS, vous devez inscrire votre abonnement pour utiliser le fournisseur de ressources Microsoft.Network. (Cette opération n’est à effectuer qu’une fois pour chaque abonnement.)

	`azure provider register Microsoft.Network`

5. Si ce n’est déjà fait, vous devez également créer un groupe de ressources Azure Resource Manager.

	`azure group create myresourcegroup westeurope`

6. Pour importer la zone **contoso.com** à partir du fichier **contoso.com.txt** dans une nouvelle zone DNS du groupe de ressources **myresourcegroup**, exécutez la commande `azure network dns zone import`.

	`azure network dns zone import myresourcegroup contoso.com contoso.com.txt`

    Cette commande charge le fichier de zone et l’analyse. Cette commande exécute une série de commandes sur le service Azure DNS, afin de créer la zone et tous les jeux d’enregistrements associés.

    Elle rend également compte de la progression dans la fenêtre de console et signale les éventuels avertissements ou erreurs. Étant donné que les jeux d’enregistrements sont créés en série, l’importation d’un fichier de zone volumineux peut prendre quelques minutes.

## Vérification de la zone DNS après l’importation du fichier

Vous pouvez répertorier les enregistrements à l’aide de la commande suivante de la CLI Azure :

	azure network dns record-set list myresourcegroup contoso.com

Vous pouvez aussi le faire via PowerShell, en utilisant `Get-AzureRmDnsRecordSet`.

Vous pouvez également utiliser `nslookup` pour vérifier la résolution de noms pour les enregistrements. Puisque la zone n’est pas encore déléguée, vous devez spécifier explicitement les serveurs de noms DNS Azure appropriés. L’exemple ci-dessous montre comment récupérer les noms du serveur de noms assignés à la zone. Il indique également comment interroger l’enregistrement « www » à l’aide de `nslookup`.

	C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
	info:    Executing command network dns record-set show
	+ Looking up the DNS Record Set "@" of type "NS"
	data:    Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-01.azure-dns.com
	data:        Name server domain name     : ns2-01.azure-dns.net
	data:        Name server domain name     : ns3-01.azure-dns.org
	data:        Name server domain name     : ns4-01.azure-dns.info
	data:
	info:    network dns record-set show command OK

	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  40.90.4.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
            134.170.188.221

Après avoir vérifié que la zone a bien été importée, vous devez [mettre à jour la délégation DNS](dns-domain-delegation.md) afin qu’elle pointe vers les serveurs de noms Azure DNS.

## Fusionner le fichier avec les données existantes

L’importation d’un fichier de zone créera une nouvelle zone dans Azure DNS si celle-ci n’existe pas. Si la zone existe déjà, les jeux d’enregistrements du fichier de zone doivent être fusionnés avec les jeux d’enregistrements existants. Le comportement de fusion est le suivant :

- Par défaut, les nouveaux jeux d’enregistrements sont fusionnés avec les jeux d’enregistrements existants. Les enregistrements identiques dans un jeu d’enregistrements fusionné sont dédupliqués.
- Si vous spécifiez l’option `--force`, le processus d’importation remplace les jeux d’enregistrements existants par les nouveaux jeux d’enregistrements. Les jeux d’enregistrements existants qui ne correspondent pas à des jeux d’enregistrements du fichier de zone importé ne seront pas supprimés.
- Lorsque les jeux d’enregistrements sont fusionnés, la TTL des jeux d’enregistrements existants est utilisée. Lorsque l’option `--force` est utilisée, la TTL du nouveau jeu d’enregistrements est utilisée.
- À l’exception de `host`, les paramètres SOA (source de noms) sont toujours dérivés du fichier de zone importé, que l’option `--force` soit ou non utilisée. De même, pour le jeu d’enregistrements du serveur de noms à l’extrémité de la zone, la TTL est toujours dérivée du fichier de zone importé.
- Un enregistrement CNAME importé ne remplace pas un enregistrement CNAME existant portant le même nom, sauf si le paramètre `--force` est spécifié.
- En cas de conflit entre un enregistrement CNAME et un autre enregistrement du même nom ayant un type différent (qu’il soit nouveau ou existant), l’enregistrement existant est conservé. Ce principe s’applique également lorsque l’option `--force` est utilisée.

## Autres détails techniques
Les remarques suivantes fournissent des informations techniques supplémentaires concernant le processus d’importation de zones.

- La directive `$TTL`, facultative, est prise en charge. Lorsqu’aucune directive `$TTL` n’est spécifiée, les enregistrements sans TTL explicite seront importés avec une TTL de 3 600 secondes par défaut. Lorsque deux enregistrements du même jeu d’enregistrements spécifient des TTL différentes, la moindre valeur est utilisée.
- La directive `$ORIGIN`, facultative, est prise en charge. Lorsqu’aucune directive `$ORIGIN` n’est définie, la valeur utilisée par défaut est le nom de zone tel qu’il est spécifié sur la ligne de commande (avec le point final).
- Les directives `$INCLUDE` et `$GENERATE` ne sont pas prises en charge.
- Les types d’enregistrements A, AAAA, CNAME, MX, NS, SOA, SRV et TXT sont pris en charge.  
- L’enregistrement SOA est créé automatiquement par Azure DNS lors de la création d’une zone. Lors de l’importation d’un fichier de zone, tous les paramètres SOA sont pris dans le fichier de zone, *sauf* le paramètre `host`. Ce paramètre utilise la valeur fournie par Azure DNS. Ce paramètre doit en effet faire référence au serveur de noms principal fourni par Azure DNS.
- Le jeu d’enregistrements du serveur de noms, à l’extrémité de la zone, est créé automatiquement par Azure DNS au moment de la création de la zone. Seule la durée de vie de ce jeu d’enregistrements est importée. Ces enregistrements contiennent les noms de serveurs de nom fournis par Azure DNS. Les données d’enregistrement ne sont pas remplacées par les valeurs contenues dans le fichier de zone importé.
- Pour la version préliminaire publique, Azure DNS prend uniquement en charge les enregistrements à chaîne unique. Les enregistrements TXT MultiString seront concaténés et tronqués à 255 caractères.

## Exportation d’un fichier de zone DNS à partir d’Azure DNS

La commande de CLI Azure permettant d’importer une zone DNS utilise le format suivant :

	azure network dns zone export [options] <resource group> <zone name> <zone file name>

Où :

- `<resource group>` est le nom du groupe de ressources correspondant à la zone dans Azure DNS.
- `<zone name>` est le nom de la zone.
- `<zone file name>` est le chemin d’accès/nom du fichier de zone à exporter.

Pour importer une zone, vous devez d’abord vous connecter, sélectionner votre abonnement, puis configurer l’interface de ligne de commande Azure pour utiliser le mode Azure Resource Manager. Examinons un exemple pas à pas.

1. Connectez-vous à votre abonnement Azure à l’aide de la CLI Azure.

	`azure login`

2. Sélectionnez l’abonnement dans lequel vous souhaitez créer votre zone DNS.

	`azure account set <subscription name>`

3. Azure DNS est un service Azure Resource Manager uniquement. La CLI Azure doit être basculée en mode Azure Resource Manager.

	`azure config mode arm`

4. Pour exporter la zone Azure DNS existante **contoso.com** du groupe de ressources **myresourcegroup** vers le fichier **contoso.com.txt** (dans le dossier actif), exécutez la commande `azure network dns zone export`.

	`azure network dns zone export myresourcegroup contoso.com contoso.com.txt`

Cette commande appelle le service Azure DNS pour énumérer les jeux d’enregistrements dans la zone et exporter les résultats dans un fichier de zone compatible BIND.

<!---HONumber=AcomDC_0204_2016-->