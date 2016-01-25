<properties
   pageTitle="Importer et exporter un fichier de zone de domaine avec Azure DNS | Microsoft Azure"
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


Ce guide décrit l’importation et l’exportation de fichiers de zone DNS vers et depuis Azure DNS.

## Introduction à la migration de zone DNS

Un fichier de zone DNS est un fichier texte qui contient les détails de tous les enregistrements DNS contenus dans la zone. Il suit un format standard, ce qui permet le transfert d’enregistrements DNS entre différents systèmes DNS. L’utilisation d’un fichier de zone offre un moyen rapide, fiable et pratique de transférer une zone DNS vers ou depuis Azure DNS.

Azure DNS prend en charge l’importation et l’exportation de fichiers de zone via l’interface de ligne de commande Azure. Cet article explique comment utiliser cette fonctionnalité.

L’interface de ligne de commande Azure est un outil de ligne de commande multiplateforme permettant de gérer des services Azure. Compatible avec les plateformes Windows, Mac et Linux, elle est disponible à partir de la [page Téléchargements d’Azure](https://azure.microsoft.com/downloads/). Cette prise en charge multiplateforme est particulièrement importante pour les opérations d’exportation et d’importation de fichiers de zone depuis le logiciel de serveur de noms le plus courant, [BIND](https://www.isc.org/downloads/bind/), généralement exécuté sur Linux.

## Comment obtenir votre fichier de zone DNS existant

Avant d’importer votre fichier de zone DNS dans Azure DNS, vous devez obtenir une copie du fichier de zone. La source de ce fichier dépend de l’emplacement où la zone DNS est actuellement hébergée. Par exemple :

- Si votre zone DNS est hébergée par un service tiers (comme un bureau d’enregistrement, un hébergeur DNS dédié ou un autre fournisseur de cloud), ce service doit fournir la possibilité de télécharger le fichier de zone DNS.
-	Si votre zone DNS est hébergée à l’aide du serveur DNS Windows, les fichiers de zone figurent par défaut dans le dossier **% systemroot%\\system32\\dns**. Le chemin d’accès complet vers chaque fichier de zone s’affiche également sous l’onglet « Général » de la console de gestion du service DNS.
-	Si votre zone DNS est hébergée à l’aide de BIND, l’emplacement du fichier de zone pour chaque zone est spécifié dans le fichier de configuration BIND, **named.conf**. 

>[AZURE.NOTE]Les fichiers de zone téléchargés à partir de « GoDaddy » ont un format légèrement non standard, qui doit être corrigé avant leur importation dans Azure DNS. Plus précisément, les noms DNS dans la section RDATA de chaque enregistrement DNS sont spécifiés en tant que noms qualifiés complets, mais sans point (.) final, ce qui signifie qu’ils sont interprétés par d’autres systèmes DNS en tant que noms relatifs. Vous devez modifier le fichier de zone pour ajouter le point final à ces noms avant de les importer dans Azure DNS.

## Importation d’un fichier de zone DNS dans Azure DNS

La commande CLI Azure permettant d’importer une zone DNS utilise le format suivant : `azure network dns zone import [options] <resource group> <zone name> <zone file name>` où :

- **resource group** est le nom du groupe de ressources correspondant à la zone d’Azure DNS.
- **zone name** est le nom de la zone.
- **zone file name** est le chemin d’accès/nom du fichier de zone à importer.

S’il n’existe aucune zone de ce nom dans le groupe de ressources, cette zone sera automatiquement créée. Si la zone existe déjà, les jeux d’enregistrements importés seront fusionnés avec les jeux d’enregistrements existants. Si vous préférez remplacer les jeux d’enregistrements existants, utilisez l’option « --force ». Vous trouverez ci-dessous des informations supplémentaires sur [la manière de fusionner les jeux d’enregistrements](#merging-with-existing-data).

Pour vérifier le format d’un fichier de zone sans réellement l’importer, utilisez l’option « --parse-only ».

## Procédure détaillée d’importation d’un fichier de zone dans Azure DNS

Prenons un exemple. Supposons que vous souhaitez importer un fichier de zone pour la zone « contoso.com ».

### Étape 1 :
Connectez-vous à votre abonnement Azure à l’aide de l’interface de ligne de commande Azure.

	azure login

### Étape 2 :
Sélectionnez l’abonnement dans lequel vous souhaitez créer votre zone DNS.

	azure account set <subscription name>

### Étape 3
Azure DNS est un service Azure Resource Manager (ARM) uniquement ; vous devez donc basculer l’interface de ligne de commande Azure en mode ARM.

	azure config mode arm

### Étape 4
Avant d’utiliser le service Azure DNS, vous devez inscrire votre abonnement pour utiliser le fournisseur de ressources Microsoft.Network (il s’agit d’une opération ponctuelle effectuée pour chaque abonnement).

	azure provider register Microsoft.Network

### Étape 5 
Si ce n’est déjà fait, vous devez également créer un groupe de ressources ARM.
	
	azure group create myresourcegroup westeurope

### Étape 6	
Pour importer la zone « contoso.com » à partir du fichier « contoso.com.txt » dans une nouvelle zone DNS du groupe de ressources « myresourcegroup », exécutez la commande `azure network dns zone import`.

	azure network dns zone import myresourcegroup contoso.com contoso.com.txt

Cette commande charge le fichier de zone, l’analyser et exécute une série de commandes sur le service Azure DNS afin de créer la zone et tous les jeux d’enregistrements associés.

Elle rend compte de la progression dans la fenêtre de console et signale les éventuels avertissements ou erreurs. Étant donné que les jeux d’enregistrements sont créés en série, l’importation d’un fichier de zone volumineux peut prendre quelques minutes.

## Vérification de la zone DNS après l’importation

Vous pouvez répertorier les enregistrements à l’aide de la commande CLI Azure suivante (vous pouvez également effectuer cette opération via PowerShell, à l’aide de `Get-AzureRmDnsRecordSet`).

	azure network dns record-set list myresourcegroup contoso.com

Vous pouvez également utiliser « nlookup » pour vérifier la résolution de noms pour les enregistrements. Puisque la zone n’est pas encore déléguée, vous devez spécifier explicitement les serveurs de noms DNS Azure appropriés. L’exemple ci-dessous montre comment récupérer les noms du serveur de noms assignés à la zone et interroger l’enregistrement « www » à l’aide de « nslookup » :

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

Après avoir vérifié que la zone a bien été importée, vous devez [mettre à jour la délégation DNS](dns-domain-delegation.md) pour pointer vers les serveurs de noms Azure DNS.

## Fusion avec des données existantes

L’importation d’un fichier de zone créera une nouvelle zone dans Azure DNS si celle-ci n’existe pas. Si la zone existe déjà, les jeux d’enregistrements du fichier de zone doivent être fusionnés avec les jeux d’enregistrements existants. Le comportement de fusion est le suivant :

1.	Par défaut, les nouveaux jeux d’enregistrements sont fusionnés avec les jeux d’enregistrements existants. Les enregistrements identiques dans un jeu d’enregistrements fusionné sont dédupliqués.
2.	Si vous spécifiez l’option « --force », le processus d’importation remplace les jeux d’enregistrements existants par les nouveaux jeux d’enregistrements. Les jeux d’enregistrements existants qui ne correspondent pas à des jeux d’enregistrements du fichier de zone importé ne seront pas supprimés.
3.	Lorsque les jeux d’enregistrements sont fusionnés, la durée de vie des jeux d’enregistrements existants est utilisée. Lorsque l’option « --force » est utilisée, la durée de vie du nouveau jeu d’enregistrements est utilisée.
4.	Les paramètres SOA (à l’exception de « host ») sont toujours dérivés du fichier de zone importé, que l’option « --force » soit ou non utilisée. De même, pour le jeu d’enregistrements NS à l’extrémité de la zone, la durée de vie est toujours dérivée du fichier de zone importé.
5.	Un enregistrement CNAME importé ne remplace pas un enregistrement CNAME existant portant le même nom, sauf si l’option « --force » est spécifiée.
6.	En cas de conflit entre un enregistrement CNAME et un autre enregistrement du même nom ayant un type différent (qu’il soit nouveau ou existant), l’enregistrement existant est conservé. Ce principe s’applique également lorsque l’option « --force » est utilisée.

## Autres détails techniques
Les remarques suivantes fournissent des informations techniques supplémentaires concernant le processus d’importation de zones :

1.	La directive $TTL est facultative et est prise en charge. Lorsqu’aucune directive $TTL n’est spécifiée, les enregistrements sans durée de vie explicite seront importés avec une durée de vie de 3 600 secondes par défaut. Lorsque deux enregistrements du même jeu d’enregistrements spécifient des durées de vie différentes, la moindre valeur est utilisée.
2.	La directive $ORIGIN est facultative et est prise en charge. Lorsqu’aucune directive $ORIGIN n’est définie, la valeur par défaut utilisée est le nom de zone tel qu’il est spécifié sur la ligne de commande (avec le point final).
3.	Les directives $INCLUDE et $GENERATE ne sont pas prises en charge.
4.	Les types d’enregistrements A, AAAA, CNAME, MX, NS, SOA, SRV et TXT sont pris en charge.  
5.	L’enregistrement SOA est créé automatiquement par Azure DNS lors de la création d’une zone. Lorsque vous importez un fichier de zone, tous les paramètres SOA sont dérivés du fichier de zone, À L’EXCEPTION du paramètre « host » qui utilise la valeur fournie par Azure DNS. Ce paramètre doit en effet faire référence au serveur de noms principal fourni par Azure DNS.
6.	Le jeu d’enregistrements NS à l’extrémité de la zone est également créé automatiquement par Azure DNS en même temps que la création de la zone. Seule la durée de vie de ce jeu d’enregistrements est importée. Ces enregistrements contiennent les noms du serveur de noms fournis par Azure DNS, et par conséquent, les données d’enregistrement ne sont pas remplacées par les valeurs contenues dans le fichier de zone importé.
7.	Pour la version préliminaire publique, Azure DNS prend en charge uniquement les enregistrements TXT à chaîne unique, ce qui signifie que les enregistrements TXT à chaînes multiples seront concaténés et tronqués à 255 caractères.

## Exportation d’un fichier de zone DNS à partir d’Azure DNS

La commande CLI Azure permettant d’importer une zone DNS utilise le format suivant : `azure network dns zone export [options] <resource group> <zone name> <zone file name>` où :

- **resource group** est le nom du groupe de ressources correspondant à la zone d’Azure DNS.
- **zone name** est le nom de la zone.
- **zone file name** est le chemin d’accès/nom du fichier de zone à exporter.

## Procédure détaillée d’exportation d’un fichier Azure DNS
 
Comme pour l’importation de zones, vous devez d’abord vous connecter, choisir votre abonnement et configurer l’interface de ligne de commande Azure pour qu’elle utilise le mode « ARM » :

### Étape 1 :
Connectez-vous à votre abonnement Azure à l’aide de l’interface de ligne de commande Azure.

	azure login

### Étape 2 :
Sélectionnez l’abonnement dans lequel vous souhaitez créer votre zone DNS.

	azure account set <subscription name>

### Étape 3
Azure DNS est un service Azure Resource Manager (ARM) uniquement ; vous devez donc basculer l’interface de ligne de commande Azure en mode ARM.

	azure config mode arm
### Étape 4
Pour exporter la zone Azure DNS existante « contoso.com » du groupe de ressources « myresourcegroup » vers le fichier « contoso.com.txt » (dans le dossier actif), exécutez la commande `azure network dns zone export`.

	azure network dns zone export myresourcegroup contoso.com contoso.com.txt

Cette commande appelle le service Azure DNS pour énumérer les jeux d’enregistrements dans la zone et exporter les résultats dans un fichier de zone compatible BIND.

<!---HONumber=AcomDC_0114_2016-->