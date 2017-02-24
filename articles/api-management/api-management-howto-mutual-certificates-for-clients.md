---
title: "Sécuriser les API avec l’authentification de certificat client dans la gestion des API - Gestion des API Azure | Microsoft Docs"
description: "Apprenez à sécuriser l’accès aux API à l’aide des certificats clients"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: b04ce0fe0db7649cebc7a1eeb2a35f1d53bf9636
ms.openlocfilehash: 1ce9f07c3e91eacdc74ccab3fbb7dc433a25c197

---

# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Comment sécuriser les API à l'aide d'une authentification par certificat client dans la Gestion des API

La Gestion des API permet de sécuriser l'accès aux API (par ex. client à gestion des API) en utilisant des certificats client. Actuellement, vous pouvez vérifier l’empreinte numérique d’un certificat client par rapport à une valeur souhaitée. Vous pouvez également vérifier l’empreinte numérique par rapport aux certificats existants téléchargés dans la gestion des API.  

Pour plus d’informations sur la sécurisation de l’accès au service principal d’une API à l’aide de certificats clients (par exemple, gestion des API vers service principal), consultez [Comment sécuriser les services principaux à l'aide d'une authentification par certificat client](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-a-thumbprint-against-a-desired-value"></a>Vérification d’une empreinte par rapport à une valeur souhaitée

Les stratégies suivantes peuvent être configurées pour vérifier l’empreinte d’un certificat client :

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint-to-validate")" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Vérification d’une empreinte par rapport aux certificats téléchargés dans la gestion des API

L’exemple suivant montre comment vérifier l’empreinte d’un certificat client par rapport aux certificats téléchargés dans la gestion des API : 

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Étape suivante

*  [Comment sécuriser les services principaux à l'aide d'une authentification par certificat client](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)
*  [Comment télécharger des certificats](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)




<!--HONumber=Feb17_HO1-->


