---
title: Que faire si une interruption de service Azure affecte Azure Key Vault | Microsoft Docs
description: Découvrez que faire si une interruption de service Azure affecte Azure Key Vault.
services: key-vault
documentationcenter: ''
author: adamglick
manager: mbaldwin
editor: ''

ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: sumedhb;aglick

---
# Disponibilité et redondance d’Azure Key Vault
Azure Key Vault dispose de plusieurs couches de redondance pour garantir que vos clés et secrets restent disponibles pour votre application même en cas d’échec de composants individuels du service.

Le contenu de votre coffre de clés est répliqué dans la région, ainsi que dans une région secondaire éloignée d’au moins 241 kilomètres mais située au sein de la même zone géographique. Cela maintient une durabilité élevée de vos clés et secrets.

Si les composants individuels du service Key Vault échouent, d’autres composants de la région interviennent pour traiter votre demande afin de garantir l’intégrité des fonctionnalités. Il est inutile d’intervenir pour déclencher cette action. Elle se produit automatiquement, de façon transparente pour vous.

Dans les rares cas d’indisponibilité d’une région Azure entière, les demandes d’Azure Key Vault effectuées dans cette région sont automatiquement acheminées (« basculées ») vers une région secondaire. Lorsque la région principale est de nouveau disponible, les demandes sont réacheminées (« basculées ») vers la région principale. Vous n’avez pas besoin d’agir dans la mesure où tout est automatique.

Il existe quelques inconvénients, que vous devez connaître :

* En cas de basculement d’une région, quelques minutes peuvent être nécessaires au basculement du service. Les demandes effectuées pendant cette période peuvent échouer jusqu’à ce que le basculement soit terminé.
* Après un basculement, votre coffre de clés est en mode ***lecture seule***. Les demandes prises en charge dans ce mode sont les suivantes :
  * list key vaults
  * get properties of key vaults
  * list secrets
  * get secrets
  * list keys
  * get (properties of) keys
  * encrypt
  * decrypt
  * wrap
  * unwrap
  * verify
  * sign
  * backup
* Une fois le basculement restauré, tous les types de demandes (par exemple, demandes de lecture ***et*** d’écriture) sont disponibles.

<!---HONumber=AcomDC_0831_2016-->