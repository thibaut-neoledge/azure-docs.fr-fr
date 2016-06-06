<properties
	pageTitle="Que faire si une interruption de service Azure affecte Azure Key Vault | Microsoft Azure"
	description="Découvrez ce que vous devez faire si une interruption de service Azure affecte Azure Key Vault."
	services="key-vault"
	documentationCenter=""
	authors="adamglick"
	manager="danpl"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="key-vault"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="sumedhb;aglick"/>
	

# Disponibilité et redondance d’Azure Key Vault

Azure Key Vault intègre plusieurs couches de redondance pour garantir que vos clés et secrets restent disponibles pour votre application même lors de l’échec de composants individuels du service.

Le contenu de votre coffre de clés est répliqué dans la région, ainsi que dans une région secondaire éloignée d’au moins 241 kilomètres au sein de la même zone géographique. Cela garantit une très haute durabilité de vos clés et secrets

Si les composants individuels du service de coffre de clés échouent, d’autres composants de la région interviennent pour traiter votre demande. Aucune dégradation de fonctionnalité n’est observée dans ce cas. Vous n’avez pas besoin d’agir dans la mesure où tout est automatique et transparent pour vous.

Dans les rares cas d’indisponibilité d’une région Azure entière, les demandes de coffre de clés effectuées dans cette région sont automatiquement acheminées (« basculées ») vers une région secondaire. Lorsque la région principale est de nouveau disponible, les demandes sont réacheminées (« basculées ») vers la région principale. Vous n’avez pas besoin d’agir dans la mesure où tout est automatique. Il existe quelques inconvénients, que vous devez connaître :

  * en cas de basculement d’une région, quelques minutes peuvent être nécessaires au basculement du service. Les demandes effectuées pendant cette période peuvent échouer jusqu’à ce que le basculement soit terminé.

  * Après un basculement, votre coffre de clés est en mode ___lecture seule___. Les demandes prises en charge dans ce mode sont les suivantes :
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

  * Une fois le basculement restauré, tous les types de demandes (par exemple, demandes de lecture ___et___ d’écriture) sont disponibles.

<!---HONumber=AcomDC_0525_2016-->