---
title: "Vue d’ensemble du service de métadonnées Instance Azure | Microsoft Docs"
description: "Interface RESTful permettant d’obtenir des informations sur le calcul, le réseau et les événements de maintenance à venir de la machine virtuelle."
services: virtual-machines-windows, virtual-machines-linux,virtual-machines-scale-sets, cloud-services
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: 
ms.service: azure-instancemetadataservice
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: harijay
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: b5c0268e1a0ebfcb33781678a367090cad865907
ms.lasthandoff: 04/11/2017

---

# <a name="azures-instance-metadata-service---preview"></a>Service de métadonnées Instance Azure - Vue d’ensemble

> [!NOTE] 
> Les préversions sont à votre disposition, à condition que vous acceptiez les conditions d’utilisation. Pour plus d’informations, consultez la page [Conditions d’utilisation supplémentaires Microsoft Azure pour les préversions Microsoft Azure] (https://azure.microsoft.com/fr-fr/support/legal/preview-supplemental-terms/)
>

Le service de métadonnées Instance fournit des informations concernant vos instances de machine virtuelle en cours d’exécution. Ces informations peuvent être utilisées pour gérer et configurer vos instances sur Azure. Le service de métadonnées Instance Azure est un point de terminaison RESTful disponible pour toutes les machines virtuelles IaaS créées via le nouveau gestionnaire [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN). Le point de terminaison est disponible sur une adresse IP non routable bien connue (*169.254.169.254*) accessible uniquement à partir de la machine virtuelle. Ce service fournit des données importantes concernant l’instance de machine virtuelle, sa configuration réseau et les événements de maintenance à venir. Pour plus d’informations, voir [catégories de métadonnées](#instance-metadata-data-categories).

### <a name="important-information"></a>Informations importantes
Ce service est actuellement en **version préliminaire** et héberge des informations concernant l’instance de machine virtuelle et les événements de maintenance à venir. Le service continue à recevoir des mises à jour et cette page reflète les [catégories de données](#instance-metadata-data-categories) spécifiques disponibles.


## <a name="service-availability"></a>Disponibilité des services
La version préliminaire actuelle est disponible dans la région **Ouest du centre des États-Unis** d’Azure. Le tableau suivant est mis à jour en fonction des régions dans lesquelles le service devient disponible.
Pour tester le service de métadonnées Instance, créez une machine virtuelle à partir [d’Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) ou du [portail Azure](http://portal.azure.com) et suivez les exemples de la section Exemples pour accéder au service de métadonnées. 

Régions dans lesquelles la version préliminaire du service de métadonnées Instance est disponible|
------------------------------------------------------------|
Centre-Ouest des États-Unis |



## <a name="retrieving-instance-metadata"></a>Récupération des métadonnées Instance 

Les métadonnées Instance sont disponibles pour l’exécution de machines virtuelles créées/gérées à l’aide [d’Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN). Accéder à toutes les catégories de données pour une instance de machine virtuelle à l’aide de l’URI suivant

```
 curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

La sortie par défaut pour toutes les métadonnées Instance est au format json (type de contenu Application/json)

## <a name="usage-examples"></a>Exemples d’utilisation
Voici un ensemble d’exemples et de sémantiques d’utilisation pour le service de métadonnées Instance

### <a name="versioning"></a>Contrôle de version 
Le service de métadonnées Instance est géré. Les versions sont obligatoires et la version préliminaire actuelle est 2017-03-01.


```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

Au fur et à mesure que nous ajoutons des versions plus récentes, les versions antérieures sont toujours accessibles pour des questions de compatibilité si vos scripts ont des dépendances sur des formats de données spécifiques. La version préliminaire actuelle ne sera peut-être plus disponible lors de la mise à la disposition générale du service.


### <a name="data-output"></a>Sortie des données
Par défaut les métadonnées Instance retournent des données au format json (type de contenu = application/json). Des éléments de nœud différents peuvent retourner des données dans un autre format par défaut, selon le cas. Le tableau suivant fournit une référence rapide pour les formats de données 

Élément | Format de données par défaut | Autres formats
--------|---------------------|--------------
/instance | json | texte
/scheduledevents | json | Aucun

Pour le format texte, utilisez format=text dans l’URL de la demande, par exemple 

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01&format=text"
```
### <a name="security"></a>Sécurité
Le point de terminaison de métadonnées Instance est accessible uniquement à partir de l’instance de machine virtuelle en cours d’exécution sur une adresse IP non routable 169.254.169.254. En outre, toute demande avec un **en-tête X-Forwarded-For** est rejeté par le service de métadonnées. Les demandes doivent également envoyer l’en-tête**Metadata:true** pour garantir que la demande réelle est directement prévue et qu’elle ne fait pas partie d’une redirection involontaire. 
### <a name="error"></a>Error
S’il existe un élément de données introuvable ou des demandes mal formées, le service de métadonnées Instance retourne l’erreur HTTP standard. Voici quelques exemples de codes de retour 

Code de retour HTTP | Motif
----------------|-------
200 | OK
400 | Demande incorrecte, en-tête manquant, pass -H Metadata:true
404 | Introuvable, l’élément demandé n’existe pas 
405 | Méthode non prise en charge 
429 | Trop de demandes, nous prenons actuellement en charge un maximum de 5 requêtes par seconde

### <a name="examples"></a>Exemples
#### <a name="retrieving-the-network-information"></a>Récupération des informations réseau 

**Requête**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-03-01"

```
**Réponse**
> [!NOTE] 
> La réponse est une chaîne json. La sortie suivante est au format json avec un utilitaire comme [jq](https://stedolan.github.io/jq/).
>

```
{
  "interface": [
    {
      "ipv4": {
        "ipaddress": [
          {
            "ipaddress": "10.0.0.4",
            "publicip": "<>.<>.<>.<>"
          }
        ],
        "subnet": [
          {
            "address": "10.0.0.0",
            "dnsservers": [
              {
                "ipaddress": "10.0.0.2"
              },
              {
                "ipaddress": "10.0.0.3"
              }
            ],
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipaddress": []
      },
      "mac": "000D3A00FA89"
    }
  ]
}
```

#### <a name="retrieving-public-ip-address"></a>Récupération de l’adresse IP publique

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipaddress/0/publicip?api-version=2017-03-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Récupération de toutes les métadonnées d’une instance

**Requête**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```
**Réponse**
> [!NOTE]
> La réponse est une chaîne json. La sortie suivante est au format json avec un utilitaire comme [jq](https://stedolan.github.io/jq/).
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipaddress": [
            {
              "ipaddress": "10.0.0.4",
              "publicip": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.0.0",
              "dnsservers": [
                {
                  "ipaddress": "10.0.0.2"
                },
                {
                  "ipaddress": "10.0.0.3"
                }
              ],
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipaddress": []
        },
        "mac": "000D3A00FA89"
      }
    ]
  }
}

```
#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Récupération des métadonnées dans une machine virtuelle Windows

Les métadonnées Instance peuvent être récupérées dans Windows via Powershell utility curl. À partir d’une invite de commande PowerShell, exécutez les commandes suivantes : 

**Requête**
```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-03-01 | select -ExpandProperty Content
```
**Réponse**
>[!NOTE]
> La réponse est une chaîne json. La sortie suivante est au format json avec un utilitaire ConvertTo-Json Powershell.
> 

```
{
    "compute":  {
                    "location":  "CentralUSEUAP",
                    "name":  "SQLTest",
                    "offer":  "SQL2016SP1-WS2016",
                    "osType":  "Windows",
                    "platformFaultDomain":  "0",
                    "platformUpdateDomain":  "0",
                    "publisher":  "MicrosoftSQLServer",
                    "sku":  "Enterprise",
                    "version":  "13.0.400110",
                    "vmId":  "453945c8-3923-4366-b2d3-ea4c80e9b70e",
                    "vmSize":  "Standard_DS2"
                },
    "network":  {
                    "interface":  [
                                      "@{ipv4=; ipv6=; mac=002248020E1E}"
                                  ]
                }
}
```


## <a name="instance-metadata-data-categories"></a>Catégories de données de métadonnées Instance
Le tableau suivant comporte la liste de toutes les catégories de données disponibles via les métadonnées Instance

Données | Description
-----|------------
location | Région Azure dans laquelle la machine virtuelle est en cours d’exécution 
name | Nom de la machine virtuelle 
offer | Fournit des informations pour l’image de machine virtuelle, ces valeurs sont présentes uniquement pour les images déployées à partir de la galerie d’images Azure 
publisher | Éditeur de l’image de machine virtuelle
sku | Référence (SKU) spécifique pour l’image de machine virtuelle  
version | Version de l’image de machine virtuelle 
osType | Linux ou Windows 
platformUpdateDomain |  [Domaine de mise à jour](virtual-machines-windows-manage-availability.md) dans lequel la machine virtuelle est en cours d’exécution. 
platformFaultDomain | [Domaine par défaut](virtual-machines-windows-manage-availability.md) dans lequel la machine virtuelle est en cours d’exécution.
vmId | Identificateur unique de la machine virtuelle, plus d’informations [ici](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
vmSize | [Taille de la machine virtuelle](virtual-machines-windows-sizes.md)
ipv4/ipaddress | Adresse IP locale de la machine virtuelle 
ipv4/publicip | Adresse IP publique de l’instance 
subnet/address | Adresse du sous-réseau 
subnet/dnsservers/ipaddress1 | Serveur DNS principal
subnet/dnsservers/ipaddress2 | Serveur DNS secondaire
subnet/prefix | Préfixe de sous-réseau, par exemple 24
ipv6/ipaddress | Adresse IPv6 de la machine virtuelle
mac | Adresse MAC de la machine virtuelle 
scheduledevents | voir [scheduledevents](virtual-machines-scheduled-events.md)



## <a name="example-scenarios-for-usage"></a>Exemples de scénarios d’utilisation  

### <a name="tracking-vm-running-on-azure"></a>Suivi de la machine virtuelle s’exécutant sur Azure 

En tant que fournisseur de service, vous aurez peut-être besoin de suivre le nombre de machines virtuelles s’exécutant sur votre logiciel ou de disposer d’agents effectuant le suivi de l’unicité de la machine virtuelle. Pour pouvoir obtenir un ID unique pour une machine virtuelle, utilisez le champ vmId à partir du service de métadonnées Instance 

**Requête**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-03-01&format=text"
```
**Réponse**
```
5c08b38e-4d57-4c23-ac45-aca61037f084

```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Positionnement des conteneurs, partitions de données en fonction du domaine par défaut/de mise à jour 

Pour les scénarios dans lesquels le placement des réplicas différents est de prime importance. Par exemple, pour le [positionnement du réplica HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps), ou pour le positionnement des conteneurs via un [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/), vous devez connaître les domaines platformFaultDomain et platformUpdateDomain sur lesquels la machine virtuelle est en cours d’exécution. Vous pouvez interroger ce point de données directement via les métadonnées Instance

**Requête**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-03-01&format=text" 
```
**Réponse**
```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obtenir plus d’informations sur la machine virtuelle pour le dossier de support 

En tant que fournisseur de services, vous recevrez peut-être un appel du support pour lequel vous devrez connaître les informations de la machine virtuelle. Demandez au client de partager les métadonnées de calcul afin que l’équipe du support dispose des informations de base concernant le type de machine virtuelle sur Azure. 

**Requête**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-03-01"
```
**Réponse**
> [!NOTE] 
> La réponse est une chaîne json. La sortie suivante est au format json avec un utilitaire comme [jq](https://stedolan.github.io/jq/).
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }

}
```

## <a name="faq"></a>Forum Aux Questions
1. J’obtiens une erreur que 400 Demande incorrecte, en-tête de métadonnées requis non spécifié. Qu’est-ce que cela signifie ?
   * Le service de métadonnées Instance requiert Metadata:true pour être transmis dans cette demande. Transmettre l’en-tête dans l’appel REST permet d’accéder au service de métadonnées Instance. 
2. Pourquoi je n’obtiens pas les informations de calcul pour ma machine virtuelle ?
   * Pour l’instant, le service de métadonnées Instance prend uniquement en charge les instances créées par Azure Resource Manager. À l’avenir il se peut que nous ajoutions une prise en charge pour les machines virtuelles de services cloud. 
3. J’ai créé une machine virtuelle via Azure Resource Manager il y quelque temps déjà. Pourquoi ne puis-je pas voir les informations de métadonnées de calcul ?
   * Pour les machines virtuelles créées après septembre 2016, ajoutez une [balise](../azure-resource-manager/resource-group-using-tags.md) pour commencer à voir les métadonnées de calcul. Pour les anciennes machines virtuelles (créées avant septembre 2016), ajoutez/supprimez des extensions ou des disques de données à la machine virtuelle pour actualiser les métadonnées.
4. Pourquoi ai-je reçu une erreur HTTP 500 - Erreur interne du serveur ?
   * La version préliminaire des métadonnées Instance est pour le moment uniquement disponible dans la région Ouest du centre des États-Unis. Déployez vos machines virtuelles dans les régions prises en charge.  
4. Où puis-je partager des commentaires/des questions supplémentaires ?
   * Envoyez vos commentaires sur feedback.azure.com.
    
## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [scheduledevents] (virtual-machines-scheduled-events.md)

