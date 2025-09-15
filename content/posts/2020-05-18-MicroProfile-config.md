---
layout:     post
title:      "RESTEasy 3.12.0.Final: MicroProfile Config"
subtitle:   ""
date:       2020-05-18 18:17:00
aliases:    [2020/05/18/MicroProfile-config/]
---
RESTEasy 3.12.0.Final, just released, will ship with the upcoming WildFly 20. Besides a number of bug fixes, it has one
new feature, the incorporation of [MicroProfile Config](https://github.com/eclipse/microprofile-config).
Previously, RESTEasy configuration parameters were retrieved
from web.xml context-params and init-params. Now, as long as the necessary
MicroProfile API and implementation jars are present (see Section 3.4.1.2. "Configuring MicroProfile Config" of the [RESTEasy User Guide](https://docs.jboss.org/resteasy/docs/3.12.0.Final/userguide/html/index.html)) are available, all parameters are retrieved by way of
an `org.eclipse.microprofile.config.Config`. A `Config` represents any number of
`org.eclipse.microprofile.config.spi.ConfigSource`s, each of which is a map
of parameters to values. There are several `ConfigSource`s required by the
MicroProfile Config spec, including one for system properties and one for environment
variables. To those, RESTEasy adds `ConfigSource`s for web.xml context-params, servlet
init-params, and filter init-params. Any of these can be used to retrieve RESTEASY
parameters such as "resteasy.servlet.mapping.prefix" and "resteasy.document.secure.processing.feature".

Each `ConfigSource` has an associated priority. For example, the system property
`ConfigSource` has a higher priority than the environment variable `ConfigSource`
so, if a given parameter is defined as both a system property and an environment
variable, the value in the system property  will be used. Similarly, a servlet
init-param will take precedence over a context-param.

If the necessary jars are not in the environment, RESTEasy 3.12.0.Final falls back
to the previous method of examining web.xml parameters.

Of particular interest to the JAX-RS developer is the fact that, if the necessary
MicroProfile API and implementation jars are present,
the same `Config` mechanism can be used in application code. For example,

```
Config config = ConfigProvider.getConfig();
Optional<String> opt = config.getOptionalValue("param", String.class);
String param = opt.orElse("d'oh");
```

For release notes, see [RESTEasy 3.12.0.Final](https://issues.redhat.com/secure/ReleaseNote.jspa?projectId=12310560&version=12344859)
