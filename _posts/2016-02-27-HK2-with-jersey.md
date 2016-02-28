---
layout: default 
title: HK2 with Jersey
---

### Using HK2 with Jersey

Add the HK2 inhabitant generator plugin to the `pom.xml` of your project

<!--more-->

{% highlight xml %}
<plugin>
  <groupId>org.glassfish.hk2</groupId>
    <artifactId>hk2-inhabitant-generator</artifactId>
    <version>2.4.0</version>
    <executions>
      <execution>
        <goals>
          <goal>generate-inhabitants</goal>
        </goals>
      <execution>
    </execution>
  </executions>
</plugin>
{% endhighlight %}

Create an component provider which will populate the `ServiceLocator` created by `Jersey`

{% highlight java %}
public class ApplicationComponentProvider implements ComponentProvider {

  @Override
  public void initialize(ServiceLocator locator) {
    DynamicConfigurationService dcs = locator.getService(DynamicConfigurationService.class);
    try {
      dcs.getPopulator().populate();
    } catch (IOException e) {
      throw new RuntimeException("Unable to populate HK2", e);
    }
  }

  @Override
  public boolean bind(Class<?> component, Set<Class<?>> providerContracts) {
    return false;
  }

  @Override
  public void done() {
  }
}
{% endhighlight %}

Identify the component provider by creating a file 

    org.glassfish.jersey.server.spi.ComponentProvider

in `META-INF/services` with the fully qualified name of the `ComponentProvider`

    uk.co.gcwilliams.jersey.ApplicationComponentProvider

You should now be able to use `@Contract` and `@Service` from the HK2 API to populate the Jersey `ServiceLocator` with your own compoments

{% highlight java %}
@Contract
public interface UserService {
  ...
}

@Service
public class UserServiceImpl implements UserService {

  private final UserDAO userDAO;

  @Inject
  public UserServiceImpl(UserDAO userDAO) {
    this.userDAO = userDAO;
  }

  ...
}
{% endhighlight %}

or if the components are part of another project and you don't want to add a reference to the HK2 API package you can use a factory to populate the `ServiceLocator`

{% highlight java %}
@Service
public class UserServiceFactory implements Factory<UserService> {

  @Override
  @Singleton
  public UserService provide() {
    ...
  }

  @Override
  public void dispose(UserService userService) {
    ...
  }
}
{% endhighlight %}

HK2 has builtin support for lots of features, AOP, Qualifiers, Scopes etc... checkout the HK2 [website](https://hk2.java.net)