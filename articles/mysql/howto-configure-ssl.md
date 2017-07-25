---
title: "Configuration de la connectivité SSL pour se connecter en toute sécurité à la base de données Azure pour MySQL | Microsoft Docs"
description: "Instructions pour configurer correctement la base de données Azure pour MySQL et les applications associées afin d’utiliser correctement les connexions SSL"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 06/01/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 1303e6cf6f6fdd10a7310d770127828276d5056e
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017

---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL
La base de données Azure pour MySQL prend en charge la connexion de votre serveur Azure Database pour MySQL aux applications clientes à l’aide de Secure Sockets Layer (SSL). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les attaques de « l’homme du milieu » en chiffrant le flux de données entre le serveur et votre application.

Par défaut, le service de base de données doit être configuré pour exiger des connexions SSL lors de la connexion au serveur Azure Database pour MySQL.  Évitez de désactiver l’option SSL chaque fois que possible.

## <a name="enforcing-ssl-connections"></a>Appliquer les connexions SSL
Lorsque vous configurez un nouveau serveur Azure Database pour MySQL via le portail Azure ou l’interface CLI, l’application de connexions SSL est activée par défaut.

De même, les chaînes de connexion prédéfinies dans les paramètres « Chaînes de connexion » du serveur sur le portail Azure incluent les paramètres requis par les langages courants pour exiger les connexions SSL. Le paramètre SSL varie en fonction du connecteur, par exemple « ssl=true », « sslmode=require » ou « sslmode=require » et d’autres variations.

## <a name="configure-enforcement-of-ssl"></a>Configuration de l’application du protocole SSL
Vous pouvez activer ou désactiver l’application du protocole SSL. Microsoft Azure recommande de toujours activer le paramètre Appliquer une connexion SSL pour renforcer la sécurité.

### <a name="using-azure-portal"></a>En passant par le portail Azure
À l’aide du portail Azure, accédez à votre serveur Azure Database pour MySQL et cliquez sur **Sécurité de la connexion**. Utilisez le bouton bascule pour activer ou désactiver le paramètre **Appliquer une connexion SSL**. Cliquez ensuite sur **Enregistrer**. Microsoft Azure recommande de toujours activer le paramètre **Appliquer une connexion SSL** pour renforcer la sécurité.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

Vous pouvez vérifier le paramètre en consultant la page **Vue d’ensemble** pour voir si l’indicateur d**’état de d’application SSL** est affiché.

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Vous pouvez activer ou désactiver le paramètre **ssl-mise en œuvre** en utilisant respectivement les valeurs Activé ou Désactivé dans l’interface de ligne de commande Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```
## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Vérification que votre application ou votre infrastructure prend en charge les connexions SSL
De nombreuses applications courantes qui utilisent des services de base de données MySQL, notamment Wordpress, Drupal et Magento, n’activent pas le protocole SSL par défaut lors de l’installation. Activez la connectivité SSL dans ces applications après l’installation ou par le biais des commandes CLI propres à l’application. Si votre serveur MySQL applique des connexions SSL alors que l’application associée n’est pas correctement configurée, celle-ci risque de ne pas pouvoir se connecter à votre serveur de base de données. Consultez la documentation de votre application pour savoir comment activer les connexions SSL.

## <a name="applications-that-require-a-local-certificate-for-ssl-connectivity"></a>Applications nécessitant un certificat local pour la connectivité SSL
Dans certains cas, les applications nécessitent un fichier de certificat local (.pem) généré à partir d’un fichier de certificat (.cer) d’autorité de certification (CA) pour se connecter en toute sécurité.  Consultez les étapes suivantes pour obtenir le fichier .cer, puis générer le fichier .pem local et le lier à votre application.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Télécharger le fichier de certificat émis par l’autorité de certification (AC)
Le certificat nécessaire pour communiquer via le protocole SSL avec votre serveur Azure Database pour MySQL se trouve [ici](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).  Téléchargez le fichier de certificat sur votre disque local (pour ce didacticiel, nous utilisons **c:\ssl**).

### <a name="download-and-install-openssl-on-your-pc"></a>Téléchargement et installation d’OpenSSL sur votre PC
Afin de générer le fichier **.pem** local nécessaire pour que votre application se connecte en toute sécurité à votre serveur de base de données, vous devez installer OpenSSL sur votre ordinateur local.  

Les sections suivantes décrivent l’approche que vous pouvez utiliser. Vous pouvez utiliser un PC Windows ou Linux, selon ce que vous préférez. Il vous suffit de suivre une méthode.

### <a name="linux-users---download-and-install-openssl-using-a-linux-pc"></a>Utilisateurs Linux : téléchargement et installation d’OpenSSL à l’aide d’un PC Linux
Les bibliothèques OpenSSL sont fournies dans le code source directement à partir d[’OpenSSL Software Foundation](http://www.openssl.org).  Les instructions suivantes vous guident tout au long des étapes d’installation d’OpenSSL sur votre PC Linux.  Pour ce guide, nous affichons les commandes pour Ubuntu 12.04 et versions ultérieures.

Ouvrez une session de terminal et installez OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
```  
Extrayez les fichiers du package téléchargé.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Entrez le répertoire où les fichiers ont été extraits.  Par défaut, le résultat se présente ainsi.

```bash
cd openssl-1.1.0e
```
Configurez OpenSSL en exécutant la commande suivante : si vous souhaitez placer les fichiers dans un dossier autre que /usr/local/openssl, veillez à modifier la commande suivante comme il convient.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Maintenant qu’OpenSSL est correctement configuré, vous devez le compiler afin de convertir votre certificat.  Pour cette compilation, exécutez la commande suivante :

```bash
make
```
Une fois la compilation terminée, vous êtes prêt à installer OpenSSL comme un fichier exécutable en exécutant la commande suivante :
```bash
make install
```
Pour confirmer que vous avez correctement installé OpenSSL sur votre système, exécutez la commande suivante. Vérifiez que vous obtenez le même résultat.

```bash
/usr/local/openssl/bin/openssl version
```
Si l’opération réussit, vous devriez voir le message suivant :
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

### <a name="windows-pc-users---download-and-install-openssl-using-a-windows-pc"></a>Utilisateurs Windows : téléchargement et installation d’OpenSSL à l’aide d’un PC Windows
Afin de générer le fichier **.pem** local nécessaire pour que votre application se connecte en toute sécurité à votre serveur de base de données, vous devez installer OpenSSL sur votre ordinateur local.

Les bibliothèques OpenSSL sont fournies dans le code source directement à partir d[’OpenSSL Software Foundation](http://www.openssl.org). Les instructions suivantes vous guident tout au long des étapes d’installation d’OpenSSL sur votre PC Linux.

L’installation d’OpenSSL sur un PC Windows peut être effectuée comme suit :

1. **(Recommandé)** : en utilisant la fonctionnalité Bash pour Windows intégrée à Windows 10 et versions ultérieures ; OpenSSL est installé par défaut.  Vous trouverez [ici](https://msdn.microsoft.com/commandline/wsl/install_guide) des instructions sur l’activation de la fonctionnalité Bash pour Windows dans Windows 10.

2. En téléchargeant une application Win32/64 fournie par la communauté. Même si OpenSSL Software Foundation ne fournit ni n’approuve aucun programme d’installation Windows spécifique, cet organisme propose une liste des programmes d’installation disponibles [ici](https://wiki.openssl.org/index.php/Binaries)

### <a name="convert-your-cer-certificate-to-a-local-pem"></a>Conversion de votre certificat .cer en un fichier .pem local
Le fichier CA racine est téléchargé au format **.crt**. Utilisez OpenSSL pour convertir le fichier de certificat en un fichier **.pem**.  Pour ce faire, exécutez l’outil de ligne de commande openssl.exe puis la commande suivante :

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.crt -out MyServerCACert.pem
```
Maintenant que vous avez correctement créé votre fichier de certificat (MyServerCACert.pem), vous pouvez vous connecter à votre serveur de base de données en toute sécurité via le protocole SSL.

Les exemples suivants montrent comment se connecter à votre serveur MySQL via l’interface de ligne de commande MySQL et via MySQL Workbench, en utilisant le fichier **MyServerCACert.pem**.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Connexion au serveur à l’aide de l’interface de ligne de commande MySQL via le protocole SSL
À l’aide de l’interface de ligne de commande MySQL, exécutez la commande suivante :

```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -uUsername@mysqlserver4demo -pYourPassword --ssl-ca=c:\ssl\MyServerCACert.pem
```
Exécuter la commande mysql **status** pour vérifier que vous êtes connecté à votre serveur MySQL à l’aide de SSL :

```dos
mysql> status
--------------
mysql.exe  Ver 14.14 Distrib 5.7.18, for Win64 (x86_64)

Connection id:          65535
Current database:
Current user:           jason@66.235.55.141
SSL:                    Cipher in use is AES256-SHA
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             janderserver.mysql.sqltest-eg1.mscds.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    cp850
Conn.  characterset:    cp850
TCP port:               3306
Uptime:                 1 day 19 hours 9 min 43 sec

Threads: 4  Questions: 26082  Slow queries: 0  Opens: 112  Flush tables: 1  Open tables: 105  Queries per second avg: 0.167
--------------
```

> [!NOTE]
> Il existe actuellement un problème connu lié à l’utilisation de l’option « --ssl-mode=VERIFY_IDENTITY » dans votre connexion mysql.exe au service. La connexion échoue avec l’erreur suivante : _ERREUR 2026 (HY000) : erreur de connexion SSL : échec de validation du certificat SSL_. Revenez aux [modes SSL](https://dev.mysql.com/doc/refman/5.7/en/secure-connection-options.html#option_general_ssl-mode) « --ssl-mode=VERIFY_CA » ou antérieurs. Si vous avez besoin d’utiliser « --ssl-mode=VERIFY_IDENTITY », utilisez le nom du serveur régional à la place, en attendant que ce problème soit résolu. Exécutez une commande ping sur le nom de votre serveur pour identifier le serveur régional, tel que `westeurope1-a.control.database.windows.net`, et utilisez le nom de ce serveur régional dans la connexion. Nous prévoyons de supprimer cette limitation dans le futur.

### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Connexion au serveur à l’aide de MySQL Workbench via le protocole SSL
Configurez MySQL Workbench pour vous connecter en toute sécurité via le protocole SSL. Accédez à l’onglet **SSL** dans MySQL Workbench, dans la boîte de dialogue Configurer une nouvelle connexion. Renseignez l’emplacement du fichier **MyServerCACert.pem** dans le champ **SSL CA File:** (Fichier CA SSL :).
![Enregistrement d’une mosaïque personnalisée](./media/howto-configure-ssl/mysql-workbench-ssl.png)

## <a name="next-steps"></a>Étapes suivantes
Passez en revue les différentes options de connectivité d’application de la rubrique [Bibliothèques de connexions pour Azure Database pour MySQL](concepts-connection-libraries.md)

