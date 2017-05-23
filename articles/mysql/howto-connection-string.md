---
title: "Connexion d’applications à Azure Database pour MySQL | Microsoft Docs"
description: "Ce document répertorie toutes les chaînes de connexion actuellement prises en charge pour les applications qui se connectent à Azure Database pour MySQL"
services: mysql
author: wuta
ms.author: wuta
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b74f146cd0a75437e85dbb1c941b29534ffab2
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Connexion d’applications à Azure Database pour MySQL
Ce document répertorie tous les types de chaînes de connexion pris en charge par Azure Database pour MySQL, ainsi que des modèles et des exemples. Vous pouvez utiliser différents paramètres et réglages dans votre chaîne de connexion.

- Reportez-vous au document [Guide pratique pour configurer SSL](./howto-configure-ssl.md) afin d’obtenir le certificat
- {votre_hôte} = <servername>.mysql.database.azure.com

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password};[SslMode=Required;]
```

Par exemple, supposons que le nom de serveur est **wpdemo**, que le nom de la base de données est **wpdb**, que le nom d’utilisateur est **WPAdmin**, et que le mot de passe est **orcas!2**.

La chaîne de connexion devrait être :

```ado.net
Server= "wpdemo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@wpdemo"; Pwd="orcas!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {your_username}, {your_password}";
```

## <a name="nodejs"></a>Node.JS
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {your_username}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {your_username}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={your_username}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {your_username}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-connection-string-from-portal"></a>Obtention de la chaîne de connexion du portail
Dans le [portail Azure](https://portal.azure.com), accédez à Azure Database pour MySQL et cliquez sur **Chaînes de connexion** pour obtenir la liste des chaînes de votre instance : ![chaînes de connexion sur le portail](./media/howto-connection-strings/connection-strings-on-portal.png)

La chaîne fournit des détails tels que le pilote, le serveur et d’autres paramètres de connexion de base de données. Copiez la chaîne de connexion dans votre demande, puis modifiez-la avec vos propres paramètres comme le nom de base de données, le mot de passe, etc. Vous pouvez ensuite utiliser cette chaîne pour vous connecter au serveur.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la bibliothèque de connexions, reportez-vous à la rubrique [Concepts - Bibliothèques de connexions](./concepts-connection-libraries.md)

