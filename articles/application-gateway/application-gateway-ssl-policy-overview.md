---   
title: "Vue d’ensemble de la stratégie SSL pour Azure Application Gateway | Microsoft Docs"
description: "Les informations sur Azure Application Gateway vous permettent de configurer la stratégie SSL"
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
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: dae04a093af8512d27edbae8d41bd58ccc33df53
ms.contentlocale: fr-fr
ms.lasthandoff: 08/30/2017

---
   

# <a name="application-gateway-ssl-policy-overview"></a>Vue d’ensemble de la stratégie SSL Application Gateway

Application Gateway vous permet de centraliser la gestion des certificats SSL et de réduire la surcharge de chiffrement et de déchiffrement à partir d’une batterie de serveurs principaux. Cette gestion SSL centralisée permet également de spécifier une stratégie SSL centrale adaptée à vos besoins de sécurité de l’organisation. Cela vous aide à répondre aux exigences de conformité ainsi qu’aux instructions de sécurité et pratiques recommandées.

La stratégie SSL inclut le contrôle de la version du protocole SSL ainsi que des suites de chiffrement et de l’ordre dans lequel les chiffrements sont utilisés lors d’une négociation SSL. Ainsi, Application Gateway offre deux mécanismes pour permettre aux clients de contrôler la stratégie SSL : la stratégie prédéfinie et la stratégie personnalisée.

## <a name="predefined-ssl-policy"></a>Stratégie SSL prédéfinie

Application Gateway comporte trois stratégies de sécurité prédéfinies. Vous pouvez configurer votre passerelle avec n’importe laquelle de ces stratégies pour obtenir le niveau de sécurité approprié. Les noms de stratégie sont annotés en fonction de leur année et leur mois de configuration. Chaque stratégie offre différentes versions de protocole SSL et de suites de chiffrement. Il est recommandé d’utiliser les dernières stratégies SSL pour garantir la meilleure sécurité SSL. Aujourd'hui, Application Gateway permet aux utilisateurs de choisir l’une des stratégies prédéfinies suivantes.

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

Si une stratégie SSL prédéfinie doit être configurée pour vos besoins, vous devez définir votre propre stratégie SSL personnalisée. Dans ce cas, vous avez un contrôle complet sur la version minimale du protocole SSL pour la prise en charge, ainsi que sur les suites de chiffrement prises en charge et leur ordre de priorité.
 
Versions du protocole SSL :

* Les protocoles SSL 2.0 et 3.0 sont désactivés par défaut pour toutes les passerelles Application Gateway. Ces versions de protocole ne sont pas configurables.
* La définition de la stratégie SSL personnalisée vous permet de sélectionner l’un des trois protocoles suivants comme version minimale du protocole SSL pour votre passerelle : TLSv1_0, TLSv1_1, TLSv1_2.
* Si aucune stratégie SSL n’est définie, les trois protocoles (TLSv1_0, TLSv1_1, TLSv1_2) sont activés.

Suite de chiffrement :

Application Gateway prend en charge les suites de chiffrement suivantes à partir desquelles vous pouvez choisir votre stratégie personnalisée. L’ordre des suites de chiffrement détermine l’ordre de priorité pendant la négociation SSL.

Suites de chiffrement disponibles :

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

[Configure SSL policy on an application gateway](application-gateway-configure-ssl-policy-powershell.md) (Configurer la stratégie SSL sur une passerelle d’application)

