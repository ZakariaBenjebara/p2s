Properties To Settings [![Build Status](https://travis-ci.org/backuity/p2s.png?branch=master)](https://travis-ci.org/backuity/p2s)
======================


Generate at compile time settings classes that can load properties.
 
Here is an example from the processor-test module:
  
```java     
 import org.p2s.Settings;
 import java.util.Optional;
 
 @Settings
 public interface SomeSettings {
 
     Optional<Integer> timeout();
     String name();
     String theSurname();
 }
 ```

You can then automatically load properties file :

     name = John Doe
     timeout = 1234
     the.surname = Toto
     
With the following code :
    
```java
SomeSettings settings = SettingsFactory.from("your-properties-from-classpath.properties")
                                       .load(SomeSettings.class);

assertEquals("Toto", settings.theSurname());
assertEquals(Optional.of(1234), settings.timeout());
```     

## Nested types

You can use nested settings interfaces :

```java
@Settings
public interface ParentSettings {
    PersonSettings mother();
    PersonSettings father();
}

public interface PersonSettings {
    String firstName();
    String lastName();
    Integer age();
    AddressSettings address();
}

public interface AddressSettings {
    String city();
    String street();
}
```

Note : Optional nested types are not yet supported.

## Fallback

From properties:

```java
SomeSettings settings = SettingsFactory.from("your-properties-from-classpath.properties")
    .withFallback("a-fallback-from-classpath.properties")
    .load(SomeSettings.class);
```

From code:

```java
SomeSettings settings = SettingsFactory.from("your-properties-from-classpath.properties")
    .withFallback("the.surname", "Fantastic")
    .withFallback("timeout", "123")
    .load(SomeSettings.class);
```

## Override settings

```java
SomeSettings settings = SettingsFactory.from("your-properties-from-classpath.properties")
    .override("the.surname", "Fantastic")
    .override("timeout", "789")
    .load(SomeSettings.class);
     
assertEquals("Fantastic", settings.theSurname());
assertEquals(Optional.of(789), settings.timeout());     
```
     
## Maven     
     
Add the following to your pom (you need to set java version to 1.8):
 
 ```xml
 <dependencies>
     <dependency>
         <groupId>org.backuity.p2s</groupId>
         <artifactId>p2s</artifactId>
         <version>1.4</version>
     </dependency>
 </dependencies>
```


## Release

See <http://central.sonatype.org/pages/apache-maven.html>.

    cd p2s

    # deploy a snapshot
    mvn clean deploy
    
    # perform a release - NOTE : you need to have a published gpg key installed on your system    
    # update the version manually - commit push and tag
    mvn clean deploy -P release
    
    # promote to central (through the web interface)

## Licence

The project is licenced under the Apache 2 licence.
