# Maven

### How to skip tests

To skip unit tests, uses this argument `-Dmaven.test.skip=true`

Alternatively, use this `-DskipTests` in surefire plugin.

Alternatively, you may create a custom profile to skip the unit test.

pom.xml

```xml
<profiles>
  <profile>
		<id>xtest</id>
		<properties>
			<maven.test.skip>true</maven.test.skip>
		</properties>
	</profile>
 </profiles>
```



Active the profile with a `-P` option.

```bash
$ mvn package -Pxtest
```

### How to use any profile you have created

Say profile name is dev

```bash
$ mvn [command] -Pdev
```

### Maven - to download sources and docs

```bash
mvn dependency:sources
mvn dependency:resolve -Dclassifier=javadoc
```

### To fetch from mvn repository to a local directory

```bash
mvn dependency:copy-dependencies -DoutputDirectory=/Users/johne/repositorytemp
```



Maven

```xml
<plugin> 
  <artifactId>maven-assembly-plugin</artifactId> 
  <configuration> 
    <archive> 
      <manifest> <mainClass>com.qaprosoft.carina.demo.main.CarinaMain</mainClass> </manifest> 
    </archive> 
    <descriptorRefs> <descriptorRef>jar-with-dependencies</descriptorRef> </descriptorRefs> </configuration> 
</plugin>
```



```bash
mvn clean compile assembly:single

mvn -Dtest=MySuperClassTest -Dmaven.surefire.debug test

mvn -Dtest=BaseAmountAuditImplTest test

mvn clean package -Preset-db

mvn pmd:check checkstyle:check

mvn clean install -Dpmd:check -Dcheckstyle:check -DskipAllTests
```



Another example:

```bash
mvn verify -Dit.test=com.elasticpath.extensions.db.test.integration.pricing.BaseAmountAuditIntegrationTest -Dmaven.failsafe.debug="-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=y,address=8000 -Xnoagent -Djava.compiler=NONE" -Dpmd.skip=true -Dcheckstyle.skip=true
```

