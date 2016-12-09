---
title: "Sécurité Azure Government | Microsoft Docs"
description: "Fournit une vue d’ensemble des services disponibles dans Azure Government"
services: Azure-Government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
editor: 
ms.assetid: c3645bda-bf35-4232-a54d-7a0bfab2d594
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/18/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a9cc2bc044caddec63645932067f7a346945db78
ms.openlocfilehash: 11d0a67a4c368b50e2f2e8648c4fb2db8e0421c2


---
# <a name="security"></a>Sécurité
## <a name="principles-for-securing-customer-data-in-azure-government"></a>Principes de sécurisation des données client dans Azure Government
Azure Government fournit une gamme de fonctionnalités et de services que vous pouvez utiliser pour créer des solutions cloud répondant à vos besoins en matière de données réglementées/contrôlées. Une solution client conforme est tout simplement une implémentation efficace des capacités Azure Government prêtes à l’emploi, associée à des fonctionnalités solides pour la sécurité des données.

Lorsque vous hébergez une solution dans Azure Government, Microsoft gère un grand nombre de ces exigences au niveau de l’infrastructure cloud.

Le diagramme suivant illustre le modèle de défense approfondie d’Azure. Par exemple, Microsoft fournit une protection de base contre les DDoS à l’encontre de l’infrastructure cloud, ainsi que des fonctionnalités client telles que des dispositifs de sécurité pour les besoins DDoS des applications client spécifiques.

![alt text](./media/azure-government-Defenseindepth.png)

Cette page décrit les principes fondamentaux de la sécurisation de vos services et applications, et fournit des conseils et meilleures pratiques sur leur application. En d’autres termes, elle détaille comment utiliser Azure Government de manière optimale pour se conformer aux obligations et responsabilités requises pour une solution gérant des informations ITAR.

 Les principes fondamentaux pour la sécurisation des données client sont :

* Protection des données à l’aide du chiffrement
* Gestion des secrets
* Isolation pour restreindre l’accès aux données

### <a name="protecting-customer-data-using-encryption"></a>Protection des données client à l’aide du chiffrement
L’atténuation des risques et le respect des obligations réglementaires donnent un poids et une importance croissants au chiffrement des données. Implémentez le chiffrement de manière efficace pour améliorer les mesures de sécurité actuelles pour le réseau et les applications, et réduire le risque global pour votre environnement cloud.

#### <a name="encryption-at-rest"></a>Chiffrement au repos
Le chiffrement des données au repos s’applique à la protection du contenu client stocké sur disque. Il existe plusieurs façons de procéder :

#### <a name="storage-service-encryption"></a>Storage Service Encryption
Azure Storage Service Encryption est appliqué au niveau du compte de stockage, ce qui a pour effet de chiffrer automatiquement les objets blob de bloc et les objets blog de pages quand ils sont écrits dans Azure Storage. Les données lues à partir d’Azure Storage sont déchiffrées par le service de stockage avant d’être renvoyées. Utilisez ce processus pour sécuriser vos données sans avoir à modifier ni ajouter de code dans les applications.

#### <a name="client-side-encryption"></a>Chiffrement côté client
Le chiffrement côté client est intégré aux bibliothèques clientes de stockage Java et .NET, qui peuvent utiliser les API Azure Key Vault. Cela simplifie l’implémentation. Utilisez Azure Key Vault pour accorder l’accès aux clés secrètes dans Azure Key Vault à des utilisateurs spécifiques à l’aide d’Azure Active Directory.

#### <a name="encryption-in-transit"></a>Chiffrement en transit
Le chiffrement de base disponible pour la connectivité à Azure Government prend en charge le protocole de sécurité TLS (Transport Level Security) 1.2 et les certificats X.509. Les algorithmes de chiffrement de la norme FIPS (Federal Information Processing Standard) 140-2, niveau 1 sont également utilisés pour les connexions réseau de l’infrastructure entre les centres de données Azure Government.  Windows Server 2012 R2, les machines virtuelles Windows 8 et les partages de fichiers Azure peuvent utiliser SMB 3.0 pour le chiffrement entre la machine virtuelle et le partage de fichiers. Utilisez le chiffrement côté client pour chiffrer les données avant leur transfert vers un espace de stockage dans une application client, et les déchiffrer après leur transfert à partir de l’espace de stockage.

#### <a name="best-practices-for-encryption"></a>Meilleures pratiques de chiffrement
* Machines virtuelles IaaS : utilisez Azure Disk Encryption. Activez Storage Service Encryption pour chiffrer les fichiers VHD utilisés pour la sauvegarde des disques dans Azure Storage. Notez que cette fonctionnalité chiffre uniquement les nouvelles données écrites. Autrement dit, si vous créez une machine virtuelle et activez ensuite Storage Service Encryption sur le compte de stockage qui contient le fichier VHD, les modifications apportées sont chiffrées, mais pas le fichier VHD d’origine.
* Chiffrement côté client : il s’agit de la méthode la plus sûre pour chiffrer vos données, car il chiffre les données avant leur transit et il chiffre les données au repos. Toutefois, cette méthode vous oblige à ajouter du code dans vos applications qui utilisent le stockage, ce qui peut ne pas vous convenir. Dans ce cas, vous pouvez utiliser le protocole HTTPS pour vos données en transit, et Storage Service Encryption pour chiffrer les données au repos. Le chiffrement côté client entraîne également une plus grande charge pour le client. Vous devez donc tenir compte de ce paramètre dans vos plans d’extensibilité, en particulier si vous chiffrez et transférez une grande quantité de données.

### <a name="protecting-customer-data-by-managing-secrets"></a>Protection des données client grâce à la gestion des secrets
Une gestion sécurisée des clés est primordiale pour la protection des données du cloud. Les clients doivent s’efforcer de simplifier la gestion des clés et de garder le contrôle des clés utilisées par les services et applications cloud pour chiffrer les données.

#### <a name="best-practices-for-managing-secrets"></a>Meilleures pratiques pour la gestion des secrets
* Utilisez Azure Key Vault pour réduire les risques d’exposition des secrets dans les fichiers de configuration codés en dur, les scripts ou le code source. Azure Key Vault chiffre les clés (par exemple, les clés de chiffrement pour Azure Disk Encryption) et les secrets (tels que les mots de passe), en les stockant dans des modules de sécurité matériels (HSM) conformes à la norme FIPS 140-2 de niveau 2. Pour une meilleure garantie, vous pouvez importer ou générer des clés dans ces HSM.
* Le code et les modèles d’application ne doivent contenir que des références URI à des secrets, ce qui signifie que les secrets réels ne se trouvent pas dans le code, la configuration ou les référentiels de code source. Cela évite les attaques de type hameçonnage sur les clés dans les référentiels internes ou externes, par exemple, de programmes zombies dans Github.
* Utilisez des contrôles RBAC forts dans Azure Key Vault. Si un opérateur de confiance quitte l’entreprise ou est transféré dans un autre groupe de l’entreprise, son accès aux secrets doit lui être retiré.

Pour plus d’informations, voir la [documentation publique d’Azure Key Vault](../key-vault/index.md).

### <a name="isolation-to-restrict-data-access"></a>Isolation pour restreindre l’accès aux données
L’isolation correspond à l’utilisation de limites, de segmentation et de conteneurs pour que seuls les utilisateurs, services et applications autorisés aient accès aux données. Par exemple, la séparation entre les clients est un mécanisme de sécurité essentiel pour les plateformes de cloud mutualisées comme Microsoft Azure. L’isolation logique permet d’empêcher un client d’interférer avec les opérations d’un autre client.

#### <a name="environment-isolation"></a>Isolation de l’environnement
L’environnement Azure Government est une instance physique distincte du reste du réseau de Microsoft. L’isolation s’effectue à l’aide d’une série de contrôles physiques et logiques qui incluent les éléments suivants :

* Sécurisation de barrières physiques à l’aide d’appareils biométriques et de caméras.
* Utilisation d’informations d’identification et d’une authentification multifacteur par le personnel Microsoft nécessitant un accès logique à l’environnement de production.
* Toute l’infrastructure de service d’Azure Government se trouve aux États-Unis.

#### <a name="per-customer-isolation"></a>Isolation par client
Azure implémente le contrôle d’accès réseau et la répartition via l’isolation des réseaux locaux virtuels (VLAN), des listes de contrôle d’accès, des équilibreurs de charge et des filtres IP.

Les clients peuvent isoler davantage leurs ressources au niveau des abonnements, des groupes de ressources, des réseaux virtuels et des sous-réseaux.

## <a name="screening"></a>Filtrage
Microsoft a récemment annoncé la nouvelle accréditation « FedRAMP High and Department of Defense (DoD) Impact Level 4 ». Celle-ci a eu pour effet d’élever la barre de la sécurité et de la conformité au sein de l’environnement Azure Government.

Nous filtrons actuellement tous nos opérateurs au niveau du National Agency Check with Law and Credit (NACLC), comme défini dans la section 5.6.2.2 du Guide du Ministère de la défense relatif aux exigences de sécurité en matière de cloud computing (SRG) :

> [!NOTE]
> L’enquête sur les antécédents minimale requise pour le personnel de fournisseur de services de chiffrement (CSP) ayant accès à des informations de niveaux 4 et 5 basées sur des données « non critiques sensibles » (par exemple, classées ADP-2 par le Ministère de la défense) est une vérification auprès de la National Agency Check with Law and Credit (NACLC) (pour les fournisseurs « non critiques sensibles ») ou une Moderate Risk Background Investigation (MBI) pour une désignation de position « risque modéré ».
> 
> 

Le tableau suivant récapitule notre filtrage actuel pour les opérateurs Azure Government :

| Filtrages et contrôles d’antécédents pour Azure Government | Description |
| --- | --- |
| Nationalité américaine |Vérification de la nationalité américaine. |
| Contrôle d’antécédents Microsoft Cloud (tous les deux ans) |Recherche de numéro de sécurité sociale, vérification des antécédents judiciaires, liste du Bureau du contrôle des avoirs étrangers du Trésor américain (OFAC, Office of Foreign Assets Control), liste du Bureau de l’Industrie et de la sécurité (BIS, Bureau of Industry and Security), liste du Ministère de la défense des personnes radiées des contrôles commerciaux. |
| National Agency Check with Law and Credit (NACLC) (tous les cinq ans) |Ajoute un contrôle d’antécédents basé sur les empreintes digitales s’appuyant sur les bases de données du FBI. Pour plus d’informations, voir <a href="https://www.opm.gov/investigations/background-investigations/federal-investigations-notices/1997/fin97-02/"> Site d’administration du personnel de bureau</a>. |
| <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/CJIS"> CJIS (Criminal Justice Information Services) </a> |Le CJIS est un système de filtrage officiel au niveau des États, locaux et du FBI, qui traite les dossiers d’empreintes digitales et vérifie les antécédents judiciaires du personnel opérationnel qui pourrait avoir accès à des informations critiques de justice pénale.  Chaque État est son propre système de vérification des antécédents et d’approbation des employés susceptibles d’avoir accès à des informations de justice pénale. |

Pour le personnel des opérations d’Azure, les principes d’accès suivants s’appliquent :

* Les tâches sont clairement définies, avec des responsabilités distinctes pour la demande, l’approbation et le déploiement de modifications.
* L’accès s’effectue par le biais d’interfaces définies qui ont des fonctionnalités spécifiques.
* L’accès s’effectue juste-à-temps (JIT) et est autorisé uniquement sur la base d’incidents ou d’événements de maintenance spécifiques, et toujours pendant une durée limitée.
* L’accès est basé sur des règles, avec des rôles définis qui disposent uniquement des autorisations requises pour le dépannage.

Les normes de filtrage incluent la validation de la citoyenneté américaine de tout le personnel d’exploitation et de support technique de Microsoft avant tout octroi d’accès à des systèmes hébergés dans Azure Government. Le personnel de support technique qui doit transférer des données utilise les fonctionnalités sécurisées disponibles dans Azure Government. Le transfert sécurisé de données nécessite un ensemble distinct d’informations d’identification pour l’accès. Par exemple, pour accéder aux métadonnées système, le personnel d’exploitation utilise des outils de gestion internes spécifiques basés sur le web, des API en lecture seule, et une élévation juste-à-temps.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, veuillez vous inscrire au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.




<!--HONumber=Nov16_HO3-->


