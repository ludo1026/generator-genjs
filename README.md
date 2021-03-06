# generator-genjs

This is a Yeoman generator to create GenJS for code generation in javascript.

# Projects organization

The generated project will be in the root directory which contains the generated files.

This generation project will be in the subdirectory ```genjs``` which contains the templates and the model to generate the files.

```
[project]
\- ... files of the generated project
\- genjs
   \- ... files of the generation project
```

# Create a new project

Create and go to the directory with the name of your project :
```
mkdir [project]
cd [project]
```
, replace ```[project]``` by the name of the project

Create and go to the subdirectory which contains the GenJS generation project :
```
mkdir genjs
cd genjs
```

Create the GenJS project :
```
yo genjs
```

Answer the questions :
- name of the project
- version of the project
- base package

The GenJS project is created.

You should have these directories :
```
[project]
\- genjs
   \- config
   \- model
   \- templates
   \- node_modules
   \- gen.js
   \- main.js
```

Run the GenJS generator :
```
node main.js
```

# Add a bundle

* Launch the ```genjs:bundles``` subgenerator :
```
cd genjs
yo genjs:bundles
```
* Select the bundle of your choice : 
=> these bundles are on Github : https://github.com/gen-js-bundles

# Sample generation

We will see how to define our model and our templates to generate Java classes for MongoDB and Spring Data :

## Model

* Edit the file ```genjs/model/model.js``` with this content :
```
var entities = {
  "book": {
    "attributes":{
      "title": {"type":"String"}
    }
  },
  "author": {
    "attributes":{
      "firstname": {"type":"String"},
      "lastname": {"type":"String"}
    }    
  },
  "publisher": {
    "attributes":{
      "name": {"type":"String"}
    }    
  }
}

module.exports=entities;
```

## Templates

All the templates are in the directory ```genjs/templates```.

* In the directory ```genjs/templates```
* Create the directory ```src/main/java/PPP/domain```
* Add the file ```[name_A].java``` in this directory with this content :
```
package <%= gen.package %>;

public class <%= gen.name %> {


  <% each(entity.attributes, function(attr) { %>
    private <%= attr.type.A()%> <%= attr.name.a() %>;
  <% }) %>


  <% each(entity.attributes, function(attr) { %>
    public <%= gen.name %> set<%= attr.name.A() %>(<%= attr.type.A()%> <%= attr.name.a() %>) {
    	this.<%= attr.name.a() %> = <%= attr.name.a() %>;
    	return this;
    }
    public <%= attr.type.A() %> get<%= attr.name.A() %>() {
    	return this.<%= attr.name.a() %>;
    }
  <% }) %>
  
}
```

* Create the directory ```src/main/java/PPP/repository```
* Add the file ```[name_A]Repository.java```
```
package <%= gen.package %>;

import java.util.List;

import org.springframework.data.mongodb.repository.MongoRepository;
import <%= packageBase %>.domain.<%= entity.name.A() %>;

public interface <%= gen.name %> extends MongoRepository<<%= entity.name.A() %>, String> {


  <% each(entity.attributes, function(attr) { %>
    public List<<%= entity.name.A()%>> findBy<%= attr.name.A() %>(<%= attr.type.A() %> <%= attr.name.a() %>);
  <% }) %>

}
```

* Add the ```pom.xml``` in the folder ```genjs/templates```
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId><%= maven.groupId %></groupId>
  <artifactId><%= maven.artifactId %></artifactId>
  <version><%= maven.version %></version>
  <packaging>war</packaging>

  <name><%= projectName %></name>

  <prerequisites>
    <maven>3.0.0</maven>
  </prerequisites>

  <properties>
    <java.version>1.8</java.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <start-class><%= packageBase %>.Main</start-class>
  </properties>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.2.0.RELEASE</version>
  </parent>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>

</project>
```

* Add the file ```Main.java``` in the folder ```src/main/java/PPP``` :
```
package <%= gen.package %>;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.ComponentScan;
import <%= packageBase %>.domain.Author;
import <%= packageBase %>.repository.AuthorRepository;

@EnableAutoConfiguration
@ComponentScan
public class Main implements CommandLineRunner {

    @Autowired
    private AuthorRepository repository;

    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }

    @Override
    public void run(String... args) throws Exception {

        repository.deleteAll();

        // save a couple of authors
        repository.save(new Author().setFirstname("Robert").setLastname("Smith"));
        repository.save(new Author().setFirstname("Robert").setLastname("Steve"));

        // fetch all authors
        System.out.println("Authors found with findAll():");
        System.out.println("-------------------------------");
        for (Author author : repository.findAll()) {
            System.out.println(author);
        }
        System.out.println();

        // fetch an individual author
        System.out.println("Author found with findByFirstname('Robert'):");
        System.out.println("--------------------------------");
        System.out.println(repository.findByFirstname("Robert"));

        System.out.println("Authors found with findByLastname('Smith'):");
        System.out.println("--------------------------------");
        for (Author author : repository.findByLastname("Smith")) {
            System.out.println(author);
        }

    }

}
```

* Restart the GenJS generator if there are missing files :
```
node main.js
```

## Start the application

* install ```mongodb```
* run ```mongodb```

* import the project as a Maven Project in your IDE 
* launch the main class ```Main.java```

In the output, you can see the authors found by the Main class.

You have finished this tutorial.

You can now have some explainations about the generator in the following chapter :

# Explained sample generation

This chapter explained the role and syntax of each files of the GenJS project.

## Model

In the ```genjs/model/model.js``` the declaration of the entity ```Book``` :
```
var entities = {
  "book": {
    "attributes": {
      "title": {
        "type": "String"
      }
    } 
  }
}
```
The ```id``` and ```name``` properties are automatically added by GenJS for each entity and attribute. They are equals to the key name in the map.
```
var entities = {
  "book": {
    "id": "book",
    "name": "book",
    "attributes": {
      "title": {
        "id": "title",
        "name": title",
        "type": "[attribute type]"
      }
    } 
  }
}
```

## Template

Templates have the EJS syntax.

### Template generated for each entity

If the template is used for each entity, then the name of the template must contain one of these expressions :
- [name] : name of the entity (= entity.name)
- [name_a] : name of the entity with the first letter in lowercase
- [name_A] : name of the entity with the first letter in uppercase

### Template generated only once

If the template is only generated once time, you must not set expressions in the file name.

### Expressions in directories

```PPP``` folder name will be replaced by the base package name of the project.

### Expressions in templates of entities

#### ```entity```

The variable ```entity``` targets the data of one entity in the ```model.js``` file.

This ```entity``` variable is available only if the template name has the expression ```[name]```in its name.

#### ```entities```
For all templates, the variable ```entities``` targets the array which contains all entities of the ```model.js``` file.

### Lowercase and Uppercase

Strings value have these functions :
* str.a() : the first letter is in lowercase
* str.A() : the first letter is in uppercase
