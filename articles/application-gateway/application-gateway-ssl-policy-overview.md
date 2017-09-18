---   
title: "Vue d’ensemble de la stratégie SSL pour Azure Application Gateway | Microsoft Docs"
description: "Découvrir comme Azure Application Gateway vous permet de configurer la stratégie SSL"
services: application gateway
documentationcenter: na
author: amsriva
manager: 
editor: 
tags: azure resource manager
ms.service: application gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: ec36af282bbfdc22ff88082412dd18cd2a85f245
ms.contentlocale: fr-fr
ms.lasthandoff: 09/06/2017

---
   

# <a name="application-gateway-ssl-policy-overview"></a>Vue d’ensemble de la stratégie SSL Application Gateway

Vous pouvez utiliser Azure Application Gateway pour centraliser la gestion des certificats SSL et réduire la surcharge de chiffrement et de déchiffrement à partir d’une batterie de serveurs principaux. Cette gestion SSL centralisée permet également de spécifier une stratégie SSL centrale adaptée aux besoins de sécurité de votre organisation. Celle-ci vous aide à respecter les exigences de conformité ainsi que les directives en matière de sécurité et pratiques recommandées.

La stratégie SSL inclut le contrôle de la version du protocole SSL ainsi que des suites de chiffrement et de l’ordre dans lequel les chiffrements sont utilisés lors d’une négociation SSL. Application Gateway offre deux mécanismes pour contrôler la stratégie SSL. Vous pouvez utiliser une stratégie prédéfinie ou une stratégie personnalisée.

## <a name="predefined-ssl-policy"></a>Stratégie SSL prédéfinie

Application Gateway comporte trois stratégies de sécurité prédéfinies. Vous pouvez configurer votre passerelle avec n’importe laquelle de ces stratégies pour obtenir le niveau de sécurité approprié. Les noms de stratégie sont annotés en fonction de leur année et leur mois de configuration. Chaque stratégie offre différentes versions de protocole SSL et de suites de chiffrement. Nous vous recommandons d’utiliser les dernières stratégies SSL pour garantir la meilleure sécurité SSL.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Propriété  |Valeur  |
|---|---|
|Nom     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Default| True (si aucune stratégie prédéfinie n’est spécifiée) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
  ### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Propriété  |Valeur  |
|   ---      |  ---       |
|Nom     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Default| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Propriété  |Valeur  |
|---|---|
|Nom     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Default| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Stratégie SSL personnalisée

Si une stratégie SSL prédéfinie doit être configurée pour vos besoins, vous devez définir votre propre stratégie SSL personnalisée. Avec une stratégie SSL personnalisée, vous contrôlez totalement la version minimale du protocole SSL à prendre en charge, ainsi que les suites de chiffrement prises en charge et leur ordre de priorité.
 
### <a name="ssl-protocol-versions"></a>Versions du protocole SSL

* Les protocoles SSL 2.0 et 3.0 sont désactivés par défaut pour toutes les passerelles d’application. Ces versions de protocole ne sont pas configurables.
* Une stratégie SSL personnalisée vous permet de sélectionner l’un des trois protocoles suivants comme version minimale du protocole SSL pour votre passerelle : TLSv1_0, TLSv1_1 et TLSv1_2.
* Si aucune stratégie SSL n’est définie, les trois protocoles (TLSv1_0, TLSv1_1 et TLSv1_2) sont activés.

### <a name="cipher-suites"></a>Suites de chiffrement

Application Gateway prend en charge les suites de chiffrement suivantes à partir desquelles vous pouvez choisir votre stratégie personnalisée. L’ordre des suites de chiffrement détermine l’ordre de priorité pendant la négociation SSL.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez apprendre à configurer une stratégie SSL, consultez [Configurer une stratégie SSL sur une passerelle d’application](application-gateway-configure-ssl-policy-powershell.md).

