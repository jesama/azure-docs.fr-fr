---
title: Fonction définie par l’utilisateur (UDF) de Java avec Apache Hive dans HDInsight - Azure
description: Découvrez comment créer une fonction définie par l’utilisateur basée sur Java qui fonctionne avec Apache Hive. Cet exemple UDF convertit un tableau de chaînes de texte en minuscules.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 4a3da9e2ad4d5ab83c1e906b3ab43540e819b48c
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341998"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Utiliser une fonction UDF Java avec Apache Hive dans HDInsight

Découvrez comment créer une fonction définie par l’utilisateur basée sur Java qui fonctionne avec Apache Hive. L’UDF Java dans cet exemple convertit un tableau de chaînes de texte en caractères tous minuscules.

## <a name="requirements"></a>Configuration requise

* Un cluster HDInsight 

    > [!IMPORTANT]
    > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

    La plupart des étapes de ce document fonctionnent sur les deux clusters basés sur Windows et Linux. Toutefois, les étapes utilisées pour télécharger la fonction définie par l’utilisateur compilée dans le cluster et l’exécuter sont propres aux clusters basés sur Linux. Vous trouverez des liens vers des informations qui peuvent être utilisées avec les clusters Windows.

* [Java JDK](https://www.oracle.com/technetwork/java/javase/downloads/) 8 ou ultérieur (ou un équivalent, par exemple, OpenJDK)

* [Apache Maven](https://maven.apache.org/)

* Un éditeur de texte ou un IDE Java

    > [!IMPORTANT]
    > Si vous créez les fichiers Python sur un client Windows, vous avez besoin d’un éditeur qui utilise LF comme fin de ligne. Si vous ne savez pas si votre éditeur utilise LF ou CRLF, consultez la section Résolution des problèmes pour savoir comment supprimer le caractère CR.

## <a name="create-an-example-java-udf"></a>Créer un exemple Java UDF 

1. À partir d’une ligne de commande, utilisez ce qui suit pour créer un nouveau projet Maven :

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

   > [!NOTE]
   > Si vous utilisez PowerShell, vous devez placer les paramètres entre guillemets. Par exemple : `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Cette commande crée un répertoire nommé **exampleudf**, qui contient le projet Maven.

2. Une fois le projet créé, supprimez le répertoire **exampleudf/src/test** qui a été créé dans le cadre du projet.

3. Ouvrez le fichier **exampleudf/pom.xml** et remplacez l’entrée `<dependencies>` existante par le fichier XML suivant :

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Ces entrées spécifient la version de Hadoop et de Hive fournie avec HDInsight 3.6. Vous trouverez des informations sur les versions de Hadoop et Hive incluses dans HDInsight dans le document [Contrôle des versions du composant HDInsight](../hdinsight-component-versioning.md) .

    Ajoutez une section `<build>` avant la ligne `</project>` à la fin du fichier. Cette section doit comporter le fichier XML suivant :

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    Ces entrées définissent la génération du projet. Plus précisément, la version de java utilisée par le projet et la méthode de génération d’un uberjar pour le déploiement sur le cluster.

    Enregistrez le fichier une fois les modifications apportées.

4. Renommez **exampleudf/src/main/java/com/microsoft/examples/App.java** en **ExampleUDF.java**, puis ouvrez le fichier dans votre éditeur.

5. Remplacez le contenu du fichier **ExampleUDF.java** par le code suivant, puis enregistrez-le.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Ce code permet d’implémenter une fonction définie par l’utilisateur qui accepte une valeur de chaîne et renvoie une version de la chaîne en minuscules.

## <a name="build-and-install-the-udf"></a>Générer et installer la fonction UDF

1. Utilisez la commande suivante pour compiler et empaqueter la fonction UDF :

    ```bash
    mvn compile package
    ```

    Cette commande génère et conditionne l’UDF dans le fichier `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar`.

2. Utilisez la commande `scp` pour copier le fichier sur le cluster HDInsight.

    ```bash
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
    ```

    Remplacez `myuser` par le compte utilisateur SSH de votre cluster. Remplacez `mycluster` par le nom du cluster. Si vous utilisez un mot de passe pour sécuriser votre compte SSH, vous êtes invité à le saisir. Si vous avez utilisé un certificat, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le fichier de clé privée.

3. Connectez-vous au cluster à l'aide de SSH.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. À partir de la session SSH, copiez le fichier jar sur le stockage HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Utiliser la fonction UDF à partir de Hive

1. Utilisez la commande suivante pour démarrer le client Beeline depuis la session SSH.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Cette commande suppose que vous avez utilisé la valeur par défaut d’ **admin** pour le compte de connexion de votre cluster.

2. Une fois sur l’invite `jdbc:hive2://localhost:10001/>` , entrez la commande suivante pour ajouter la fonction UDF dans Hive et l’exposer en tant que fonction.

    ```hiveql
    ADD JAR wasb:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

    > [!NOTE]
    > Cet exemple suppose que le stockage Azure est le stockage par défaut pour le cluster. Si votre cluster utilise Data Lake Storage Gen2, remplacez la valeur `wasb:///` par `abfs:///`. Si votre cluster utilise Data Lake Storage Gen1, remplacez la valeur `wasb:///` par `adl:///`.

3. Utilisez la fonction UDF pour convertir les valeurs extraites d’une table en chaînes de caractères minuscules.

    ```hiveql
    SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
    ```

    Cette requête sélectionne la plateforme de l’appareil (Android, Windows, iOS, etc.) dans la table, convertit la chaîne en caractères minuscules, puis les affiche. Le résultat ressemble au texte suivant :

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir d’autres utilisations de Hive, consultez [Utiliser Apache Hive avec HDInsight](hdinsight-use-hive.md).

Pour plus d’informations sur les fonctions définies par l’utilisateur Hive, consultez [Apache Hive Operators and User-Defined Functions ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (Opérateurs et fonctions définies par l’utilisateur Apache Hive) du wiki Hive sur le site apache.org.
