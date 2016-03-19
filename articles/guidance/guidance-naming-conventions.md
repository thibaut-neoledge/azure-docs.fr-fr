<properties
   pageTitle="Conventions d’affectation de noms recommandées pour les ressources Azure | Recommandations | Microsoft Azure"
   description="Conventions d’affectation de noms recommandées pour les ressources Azure. Comment nommer les machines virtuelles, comptes de stockage, réseaux, réseaux virtuels, sous-réseaux et autres entités Azure"
   services=""
   documentationCenter="na"
   authors="masimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="masimms"/>
   
# Conventions d’affectation de noms recommandées pour les ressources Azure

Le choix du nom de chaque ressource dans Microsoft Azure est essentiel à plusieurs titres :

- Il est difficile de modifier un nom ultérieurement.
- Les noms doivent répondre aux exigences du type de ressource correspondant.

De même, des conventions d’affectation de noms cohérentes simplifient la localisation des ressources. Elles aident également à comprendre le rôle d’une ressource dans une solution.

Cet article résume les règles et restrictions d’affectation de noms pour les ressources Azure. Il offre également un ensemble de recommandations de base en matière de conventions d’affectation de noms. Vous pouvez utiliser ces recommandations comme point de départ pour vos propres conventions.

La clé du succès consiste à mettre en place les conventions d’affectation de noms, à les suivre à l’échelle de vos applications et des organisations, puis à les adapter au fil du déploiement de nouvelles applications et de nouveaux services sur la plateforme Azure.

## Affectation de nom aux abonnements

L’affectation de noms détaillés aux abonnements Azure permet de mieux comprendre leur contexte et leur objet. Lorsque vous travaillez dans un environnement qui peut contenir de nombreux abonnements, l’application d’une convention d’affectation de noms partagée permet de gagner considérablement en clarté.

À titre d’exemple, nous recommandons le modèle suivant pour nommer des abonnements :

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- Dans la plupart des cas, l’entreprise est la même pour chaque abonnement. Cependant, certaines peuvent comporter des sociétés enfant au sein de la structure organisationnelle. Elles peuvent être gérées par un service informatique central, auquel cas, elles pourraient être différenciées par la présence des noms de la société mère (*Contoso*) et de la société enfant (*North Wind*).

- Le service est le nom d’une entité, au sein de l’organisation, où travaille un groupe de personnes. Cet élément dans l’espace de noms est facultatif. En effet, la taille de certaines entreprises ne justifie pas un tel niveau de détail.

- La gamme de produits est le nom spécifique d’un produit ou d’une fonction réalisé au sein du service. Si cet élément est généralement facultatif pour les services et applications internes, il est fortement recommandé pour les services accessibles au public, qui nécessitent une séparation et une identification aisées (par exemple, pour une séparation claire des enregistrements de facturation).

- L’environnement est le nom qui décrit le cycle de vie de déploiement des applications ou services, par exemple, Dev, AQ ou Prod.

| Entreprise | Department | Gamme de produits ou service | Environnement | Nom complet |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Production | Contoso SocialGaming AwesomeService Production |
| Contoso | SocialGaming | AwesomeService | Dev | Contoso SocialGaming AwesomeService Dev |
| Contoso | IT | InternalApps | Production | Contoso IT InternalApps Production |
| Contoso | IT | InternalApps | Dev | Contoso IT InternalApps Dev |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc -->

## Utilisation des affixes pour éviter les ambiguïtés

Lorsque vous nommez des ressources dans Azure, il est recommandé d’utiliser des préfixes ou des suffixes courants pour identifier le type et le contexte de la ressource. Alors que toutes les informations sur le type, les métadonnées et le contexte sont disponibles par programme, l’utilisation d’affixes courants simplifie l’identification visuelle. Lorsque vous incorporez des affixes dans votre convention d’affectation de noms, il est important d’indiquer clairement s’il sera placé au début du nom (préfixe) ou à la fin (suffixe).

Voici deux exemples de noms possibles pour un service hébergeant un moteur de calcul :

- SvcCalculationEngine (préfixe)
- CalculationEngineSvc (suffixe)

Les affixes peuvent faire référence à différents aspects des ressources spécifiques. Le tableau suivant présente des exemples généralement utilisés.

| Aspect | Exemple | Remarques |
| ------ | ------- | ----- |
| Environnement | dev, prod, aq | Identifie l’environnement de la ressource |
| Emplacement | uw (ouest des États-Unis), ue (est des États-Unis) | Identifie la région dans laquelle la ressource est déployée |
| Instance | 01, 02 | Pour les ressources possédant plusieurs instances nommées (serveurs web, etc.). |
| Produit ou service | service | Identifie le produit, l’application ou le service pris en charge par la ressource |
| Rôle | sql, web, messagerie | Identifie le rôle de la ressource associée |

Lors du développement d’une convention d’affectation de noms spécifique pour votre entreprise ou vos projets, il est important de choisir un ensemble d’affixes courants et de déterminer leur position (suffixe ou préfixe).

## Règles et restrictions d’affectation de noms

Chaque type de ressource ou de service dans Azure implique une portée et un ensemble de restrictions en matière d’affectation de noms. Chaque modèle ou convention d’affectation de noms doit respecter les règles et la portée imposées. Par exemple, si le nom d’une machine virtuelle est mappé à un nom DNS (et, par conséquent, doit être unique dans Azure), le nom d’un réseau virtuel est limité au groupe de ressources dans lequel il a été créé.

D’une manière générale, évitez d’utiliser des caractères spéciaux (`-` ou `_`) comme premier ou dernier caractère, et ce, pour tous les noms. En effet, le nom serait rejeté par la plupart des règles de validation.

| Catégorie | Service ou entité | Étendue | Longueur | Casse | Caractères valides | Modèle suggéré | Exemple |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Groupe de ressources | Groupe de ressources | Globale | 1-64 | Non-respect de la casse | Alphanumériques, trait de soulignement et trait d’union | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Groupe de ressources | Groupe à haute disponibilité | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumériques, trait de soulignement et trait d’union | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Généralités | Tag | Entité associée | 512 (nom), 256 (valeur) | Non-respect de la casse | Alphanumérique | `"key" : "value"` | `"department" : "Central IT"` |
| Calcul | Machine virtuelle | Groupe de ressources | 1-15 | Non-respect de la casse | Alphanumériques, trait de soulignement et trait d’union | `<name>-<role>-<instance>` | `profx-sql-001` |
| Storage | Nom du compte de stockage (données) | Globale | 3-24 | Minuscules | Alphanumérique | `<service short name><type><number>` | `profxdata001` |
| Storage | Nom du compte de stockage (disques) | Globale | 3-24 | Minuscules | Alphanumérique | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Storage | Nom du conteneur | Compte de stockage | 3-63 |	Minuscules | Alphanumériques et tiret | `<context>` | `logs` |
| Storage | Nom de l’objet blob | Conteneur | 1-1024 | Respect de la casse | Tout caractère d’URL | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Storage | Nom de la file d'attente | Compte de stockage | 3-63 | Minuscules | Alphanumériques et tiret | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Storage | Nom de la table | Compte de stockage | 3-63 |Non-respect de la casse | Alphanumérique | `<service short name>-<context>` | `awesomeservice-logs` |
| Storage | Nom de fichier | Compte de stockage | 3-63 | Minuscules | Alphanumérique | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Mise en réseau | Réseau virtuel (VNet) | Groupe de ressources | 2-80 | Non-respect de la casse | Alphanumériques, tiret, trait de soulignement et point | `<service short name>-[section]-vnet` | `profx-vnet` |
| Mise en réseau | Sous-réseau | Réseau virtuel parent | 2-80 | Non-respect de la casse | Alphanumériques, trait de soulignement, tiret et point | `<role>-subnet` | `gateway-subnet` |
| Mise en réseau | Interface réseau | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumériques, tiret, trait de soulignement et point | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Mise en réseau | Groupe de sécurité réseau | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumériques, tiret, trait de soulignement et point | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Mise en réseau | Règle de groupe de sécurité réseau | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumériques, tiret, trait de soulignement et point | `<descriptive context>` | `sql-allow` |
| Mise en réseau | Adresse IP publique | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumériques, tiret, trait de soulignement et point | `<vm or service name>-pip` | `profx-sql1-pip` |
| Mise en réseau | Load Balancer | Groupe de ressources | 1-80 | Non-respect de la casse | Alphanumériques, tiret, trait de soulignement et point | `<service or role>-lb` | `profx-lb` |
| Mise en réseau | Configuration des règles d’équilibrage de charge | Load Balancer | 1-80 | Non-respect de la casse | Alphanumériques, tiret, trait de soulignement et point | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | TODO | TODO
-->

## Organisation des ressources à l’aide de balises

Azure Resource Manager prend en charge le balisage d’entités avec des chaînes de texte arbitraires pour identifier le contexte et simplifier l’automatisation. Par exemple, une balise telle que `"sqlVersion: "sql2014ee"` peut identifier toute machine virtuelle d’un déploiement exécutant SQL Server 2014 Enterprise Edition, permettant d’exécuter un script automatisé sur ces derniers. Les balises doivent être intégrées aux conventions d’affectation de noms choisies pour préciser ou améliorer le contexte.

> [AZURE.TIP] Elles offrent également l’avantage de s’étendre sur les groupes de ressources, ce qui vous permet de lier et mettre en corrélation les entités dans des environnements disparates.

Chaque ressource ou groupe de ressources peut inclure un maximum de **15** balises. Le nom de balise est limité à 512 caractères, et la valeur de balise à 256 caractères.

Pour plus d’informations sur le balisage des ressources, consultez [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md).

Les balises sont notamment utilisées dans les cas suivants :

- **Facturation** ; regroupement et association des ressources à des codes de facturation interne ou externe
- **Identification du contexte de service** ; identification de groupes parmi les groupes de ressources pour les opérations courantes et à des fins de regroupement
- **Contrôle d’accès et contexte de sécurité** ; identification du rôle d’administrateur en fonction du portefeuille, du système, du service, de l’application, de l’instance, etc. 

> [AZURE.TIP] Effectuez un balisage précoce et fréquent. Il est préférable de mettre en place un système de balisage de base et de l’affiner progressivement, plutôt que d’avoir à effectuer des adaptations a posteriori.

Voici un exemple d’approche de balisage courante :

| Nom de la balise | Clé | Exemple | Commentaire |
| -------- | --- | ------- | ------- |
| Facturer à / ID de facturation interne | billTo | `IT-Chargeback-1234` | Un code d’E/S ou de facturation interne |
| Opérateur ou personne directement responsable | managedBy | `joe@contoso.com` | Alias ou adresse de messagerie |
| Nom du projet | nom-projet | `myproject` | Nom du projet ou de la gamme de produits |
| Version du projet | version-projet | `3.4` | Version du projet ou de la gamme de produits |
| Environnement | environnement | `<Production, Staging, QA >` | Identificateur de l’environnement | 
| Niveau | niveau | `Front End, Back End, Data` | Identification du niveau ou du rôle/contexte |
| Profil de données | dataProfile | `Public, Confidential, Restricted, Internal` | Niveau des données stockées dans la ressource |
 
## Astuces et conseils

Selon le type d’application, certains types de ressources peuvent nécessiter une attention particulière en termes d’affectation de noms et de conventions. Vous trouverez ci-dessous des détails et des informations de contexte supplémentaires.

### Machines virtuelles

Pour les topologies de grande envergure notamment, il convient de nommer les machines virtuelles avec une attention particulière. Cela simplifiera considérablement l’identification du rôle et de l’objet de chaque machine et permettra de mieux prévoir les scripts.

> [AZURE.WARNING] Notez que chaque machine virtuelle dans Azure possède un nom de ressource Azure et un nom d’hôte de système d’exploitation. Si le nom de la ressource et le nom d’hôte sont différents, la gestion de ces machines virtuelles peut être complexe (par exemple, si la machine virtuelle est créée à partir d’un fichier .vhd qui contient déjà un système d’exploitation configuré avec un nom d’hôte) et doit être évitée.

- [Conventions d’affectation de noms pour les machines virtuelles Windows Server](https://support.microsoft.com/fr-FR/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

###	Comptes de stockage et entités de stockage

Il existe deux principaux cas d’utilisation de comptes de stockage : sauvegarde des disques pour les machines virtuelles et stockage de données dans les objets blob, les files d’attente et les tables. Pour les comptes de stockage utilisés pour les disques de machine virtuelle, la convention d’affectation de noms doit impliquer leur association au nom de la machine virtuelle parent (de par la nécessité potentielle de disposer de plusieurs comptes de stockage pour les références (SKU) de machines virtuelles haut de gamme, il convient également d’utiliser un suffixe numérique).

> [AZURE.TIP] Les comptes de stockage (pour les données ou les disques) doivent suivre une convention d’affectation de noms permettant d’exploiter plusieurs comptes de stockage (par exemple, en utilisant systématiquement un suffixe numérique).

Vous pouvez configurer un nom de domaine personnalisé pour accéder à des données d’objets blob dans votre compte de stockage Azure. Le point de terminaison par défaut pour le service Blob est `https://mystorage.blob.core.windows.net`.

Cependant, si vous mappez un domaine personnalisé (tel que www.contoso.com) au point de terminaison des objets blob de votre compte de stockage, vous pouvez également accéder aux données d’objets blob de votre compte de stockage à l’aide de ce domaine. Par exemple, avec un nom de domaine personnalisé, `http://mystorage.blob.core.windows.net/mycontainer/myblob` est accessible sous la forme `http://www.contoso.com/mycontainer/myblob`.

Pour plus d’informations sur la configuration de cette fonctionnalité, consultez [Configurer un nom de domaine personnalisé pour un point de terminaison Blob Storage](../storage/storage-custom-domain-name.md).

Pour plus d’informations sur l’affectation de noms aux objets blob, conteneurs et tables :

- [Désignation et référencement des conteneurs, des objets BLOB et des métadonnées](https://msdn.microsoft.com/library/dd135715.aspx)
- [Affectation de noms pour les files d’attente et les métadonnées](https://msdn.microsoft.com/library/dd179349.aspx).
- [Noms de table](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Un nom d’objet blob peut contenir n’importe quelle combinaison de caractères, mais les caractères réservés d’URL doivent être placés dans des séquences d’échappement appropriées. Évitez les noms d’objets blob se terminant par un point (.), une barre oblique (/) ou une séquence ou combinaison des deux. Par convention, la barre oblique est le séparateur de répertoire **virtuel**. N’utilisez pas de barre oblique inverse dans un nom d’objet blob. Les API clientes peuvent autoriser ces caractères, mais ne peuvent effectuer un hachage approprié. De plus, les signatures ne correspondront pas.

Vous ne pouvez pas modifier le nom d’un compte de stockage ou d’un conteneur après sa création. Si vous souhaitez utiliser un nouveau nom, vous devez le supprimer et le recréer.

> [AZURE.TIP] Nous vous recommandons d’établir une convention d’affectation de noms pour tous les types et comptes de stockage avant de commencer le développement d’un nouveau service ou d’une nouvelle application.

## Exemple : déploiement d’un service multiniveau

Dans cet exemple, nous allons définir une configuration de service multiniveau intégrant des serveurs frontaux IIS (hébergés dans des machines virtuelles Windows Server), avec SQL Server (hébergé dans deux machines virtuelles Windows Server), un cluster ElasticSearch (hébergé dans 6 machines virtuelles Linux) et les comptes de stockage, les réseaux virtuels, le groupe de ressources et l’équilibreur de charge associés.

Nous commencerons par définir les conventions contextuelles de cette application :

| Entité | Convention | Description |
| ------ | ---------- | ------------ |  
| Nom du service | `profx` | Le nom court de l’application ou du service en cours de déploiement |
| Environnement | `prod` | Il s’agit du déploiement de production (par opposition à l’aq, au test, etc.). |

À partir de cette ligne de base, nous pouvons définir les conventions pour chacun des types de ressource :

| Type de ressource | Base de la convention | Exemple |
| ------------- | --------------- | ------- |
| Abonnement | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Groupe de ressources | `servicename-rg` | `profx-rg` |
| Réseau virtuel | `servicename-vnet` | `profx-vnet` |
| Sous-réseau | `role-subnet` | `sql-vnet` |
| Load Balancer | `servicename-lb` | `profx-lb` |
| Machine virtuelle | `servicename-role[number]` | `profx-sql0` |
| Compte de stockage | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Le diagramme ci-dessous en offre une illustration :

![diagramme de topologie d’application](media/guidance-naming-conventions/guidance-naming-convention-example.png "Exemple de topologie d’application")

## Exemple - script Azure CLI pour le déploiement de l’exemple ci-dessus

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

	# Create the network interface card for this VM
	azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

	# Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
	azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
		--boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
	create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```

<!---HONumber=AcomDC_0302_2016-->