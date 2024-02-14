# Backend

## Setup
Our backend is an extended version of the base [damap-backend](https://github.com/tuwien-csd/damap-backend). In order to include the base implementation, it will get imported as a library.
This is achieved by specifying it as a requirement in our `pom.xml` (package configuration file). Since the base backend is not published in a maven repository, we have to provide it as a library. This can be achieved in two ways:
- Build the base backend on the same machine running our adapted backend
  - This will cache the base backend and make it available for our adapted backend.
  - Include it as dependency without any further steps required.
- Include the `.jar` and `pom.xml` as library
  - Build the base backend version locally.
  - Copy files found in the build folder into correct library folder (e.g. `lib/at/ac/tuwien/damap-backend/2.5.0`)
  - Remove older version of base backend
  - Add it to version control
  - Specify a local repository for dependency lookup
  - Include it as dependency

As can be seen, building it on the same machine running our adapted backend would not require to locally build and manage versions. Also, it would not require to add files to version control.
Preferably, the base backend would be published on a public maven repository, so it can be included like any other library. This is currently being discussed.

## Migrate base backend
Our current workflow for migrating the base backend is to build it locally and include it in the version control. These are the steps to achieve this.

Assume we have the following folder structure:
- dmp
  - damap-backend
  - damap-backend-tugraz

### Build base backend
Change directory to the damap-backend folder.
Clone the base backend and check out the correct version from version control. These should be tagged accordingly. Then build or install the package.
```bash
cd dmp/damap-backend
git clone git@github.com:tuwien-csd/damap-backend.git .
git checkout 2.5.0
# choose one (usually install)
mvn clean package -Dquarkus-profile=dev # this will only build the package
mvn clean install -Dquarkus-profile=dev # this will also install it so it is available on the same machine
```
This will build the base backend and put the output .jar in the `./target` folder (e.g. `./target/damap-backend-2.5.0.jar`)

### Manage version control
Now that the package is build, it must be copied over to our adapted backend.

Change directory to the damap-backend-folder.
Create a folder in the adapted backend for the new version
```bash
cd ../damap-backend-tugraz
mkdir lib/at/ac/tuwien/damap-backend/2.5.0
```

Copy the output .jar from the previous step and the base backend `pom.xml` into the previously created library folder.
```
cp ../damap-backend/pom.xml ../damap-backend/target/damap-backend-2.5.0.jar lib/at/ac/tuwien/damap-backend/2.5.0
```

Add to version control
```bash
git add lib/at/ac/tuwien/damap-backend/2.5.0
```

Remove older version (e.g. 2.4.0)
```bash
# remove from version control
git rm lib/at/ac/tuwien/damap-backend/2.4.0 -r
# optional: also delete it locally
rm lib/at/ac/tuwien/damap-backend/2.4.0 -r
```

### Include as dependency
Since we include the base backend in version control and load it from our library folder, we also have to specify this library folder as a potential dependency repository. This allows mvn to lookup dependencies from `lib` folder.

Open the `pom.xml` of our adapted backend.

Below </properties> closing tag, add another tag for repositories. Here, we will specify our `lib` folder as repository. If it is already here, you do not have to add it again.
```xml
  <repositories>
    <repository>
      <id>local-repo</id>
      <name>Local Repository for Additional Libraries</name>
      <url>file://${project.basedir}/lib</url>
    </repository>
  </repositories>
```

Add/Update the dependency for the base backend. Under `<dependencies>`, add/update the base backend dependency to reflect the required version (e.g. 2.5.0)
Under `<version>`tag, update the version accordingly (e.g. 2.5.0)
```xml
    <!-- Base image -->
    <dependency>
      <groupId>at.ac.tuwien</groupId>
      <artifactId>damap-backend</artifactId>
      <type>jar</type>
      <version>2.5.0</version>
    </dependency>
```

Now when building our adapted backend, all dependencies can be resolved either via our local `lib` folder or via the maven repository (default).

Make sure to commit and push these changes, so it is available via version control.
