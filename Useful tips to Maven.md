### How parent project rocks
It is recommended to always defined each version of the plugins explicitly used by the build to guarantee the build reproducibility. 
A good practice is to specify them in the `<build><pluginManagement/></build>` elements for each build plugins (generally, you will define a `<pluginManagement/>` element in parent POM).

### How to Configuring Plug-ins
Refer to [Guide to Configuring Plug-ins](http://maven.apache.org/guides/mini/guide-configuring-plugins.html)
E.g. 
```xml
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <artifactId>maven-myquery-plugin</artifactId>
        <version>1.0</version>
        <executions>
          <execution>
            <id>execution1</id>
            <phase>test</phase>
            <configuration>
              <url>http://www.foo.com/query</url>
              <timeout>10</timeout>
              <options>
                <option>one</option>
                <option>two</option>
                <option>three</option>
              </options>
            </configuration>
            <goals>
              <goal>query</goal>
            </goals>
          </execution>
          <execution>
            <id>execution2</id>
            <configuration>
              <url>http://www.bar.com/query</url>
              <timeout>15</timeout>
              <options>
                <option>four</option>
                <option>five</option>
                <option>six</option>
              </options>
            </configuration>
            <goals>
              <goal>query</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  ...
</project>
```
### How to use ant and define version
See below   
```xml
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-antrun-plugin</artifactId>
        <version>1.2</version>
        ...
        <dependencies>
          <dependency>
            <groupId>org.apache.ant</groupId>
            <artifactId>ant</artifactId>
            <version>1.7.1</version>
          </dependency>
          <dependency>
            <groupId>org.apache.ant</groupId>
            <artifactId>ant-launcher</artifactId>
            <version>1.7.1</version>
          </dependency>
          <dependency>
            <groupId>ant-contrib</groupId>
            <artifactId>ant-contrib</artifactId>
            <version>1.0b3</version>
            <exclusions>
               <exclusion>
                  <groupId>ant</groupId>
                  <artifactId>ant</artifactId>
               </exclusion>
            </exclusions>
          </dependency>
        </dependencies>
      </plugin>
    </plugins>
  </build>
  ...
</project>
```
