# Configuration
Simple implementation that reads configuration properties from files available on the classpath and on the filesystem. The file on the classpath (distributed inside the module jar file) should be used to have the properties default values. The file on the filesystem (not included inside the module jar file) override the properties default values if required.

## Content

#### ConfigurationManagerException
Exception class to handle exceptions related with the ConfigurationManager.
#### ConfigurationManagerExceptionType
Enumeration that contains all the exceptions we are handling on ConfigurationManagerException.
#### ConfigurationManagerManager
Abstract utility class that reads the configuration properties from a file on filesystem and classpath

## Prerequisites
- Java 8
- Maven

## Installation
To make use of this library insert the following dependency into the pom.xml file.

    <dependency>
        <groupId>com.criticalsoftware.sts.commons</groupId>
        <artifactId>configuration</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </dependency>

## Usage Examples
### ConfigurationManager
Implement a final class with specific getters for all of your properties. Consider using a specific package e.g. com.criticalsoftware.sts.commons.configuration.


```java
import com.criticalsoftware.sts.commons.configuration.ConfigurationManager;
import com.criticalsoftware.sts.commons.configuration.ex.ConfigurationManagerException;

/**
 * Utility class that reads the configuration properties related with the Request Manager.
 *
 * @author bamateus
 */
public final class RequestManagerConfigurationManager extends ConfigurationManager {

  static final String PROPERTIES_FILE_NAME =
      RequestManagerConfigurationManager.class.getSimpleName() + ".properties";

  // For each property create a specific constant with the property key.

  private static final String CONF_PROP_KEY_DUMMY1 = "smets1.configuration.requestmanager.dummy1";


  /**
   * Loads the configuration properties from filesystem and classpath.
   *
   * @throws ConfigurationManagerException if an exception occurs reading the properties
   */
  public static void loadConfiguration() throws ConfigurationManagerException {
    loadConfiguration(PROPERTIES_FILE_NAME);
  }


  // For each property create a specific getter. Implement additional logic you may need,
  // for example, validate the property value against a specific format or interval.

  /**
   * Get dummy1 configuration property.
   *
   * @return the configuration property value
   */
  public static String getDummy1() {
    return getConfigurationProperty(String.class, CONF_PROP_KEY_DUMMY1);
  }
}
```

Add a configuration file to your module resources (e.g. src/main/resources or src/test/resources, the last one for unit and integration testing).

**NOTE:**
> The file name can be any but it's recommended to use a unique name, eventually the class name, as presented on the code above. Using unique names allows:
> * To have multiple configuration managers on the same module. For example, if you want to separate properties loggicaly.
> * Avoid conflicts with configuration files, when reusing modules.

##### conf.properties
```java
# This is a dummy comment.
smets1.configuration.requestmanager.database.dummy1 = dummy string for request manager
```
**NOTE:**
>Consider using the following format for the configuration properties: sts.commons.configuration.<module_name>.<property_group>.<property_name>.


This file should contain the properties default values. These properties can then be overriden by the properties inside the file on the file system. To use an external file you need to set a system property with the file path when starting your application. If using multiple configuration managers, all the properties files must be located on the same directory.
<br />
 `-Dconfiguration.file.path=/path/to/properties/file`



During your application startup load the configurations.
<br />
 `ConfigurationManager.loadConfiguration();`
<br />
It's safe to execute this method multiple times but should be avoided. Each time the method is executed, the configuration files are read. Consider loading the configuration once during the application startup.



Then use specific configurations when required.
<br />
`final String dummy1 = RequestManagerConfigurationManager.getDummy1();`

If the property is not available on any file (classpath and filesystem) then a null value is returned. Null checks should be avoided, if the property is required make sure it's available on the files. If not, then the application should crash during startup in order to fix this as soon as possible.

**WARNING:**
> If a problem occurs reading the configuration (e.g. the path for the file on the filesystem is not correct), then a ConfigurationManagerException is thrown, with the original cause.


During the utility class lifecycle the following logs are generated.
```java
15:49:39.488 [main] INFO c.c.s.c.c.ConfigurationManager - Reading configuration from filesystem - /home/smets1/projectrepos/smets1/implementation/smets1/smets1-core/src/test/resources/conf-filesystem.properties
15:49:39.497 [main] INFO c.c.s.c.c.ConfigurationManager - Reading configuration from classpath - conf.properties
15:49:39.499 [main] INFO c.c.s.c.c.ConfigurationManager - Configuration loaded successfully:
15:49:39.501 [main] INFO c.c.s.c.c.ConfigurationManager - smets1.configuration.filesystem.dummy1 - dummy string for request manager
```





## Test Execution
    mvn test

## Local Deployment
    mvn clean install

## Version
    1.0.0-SNAPSHOT


