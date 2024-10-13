[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=wuan_json-schema&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=wuan_json-schema)
[![Lines of Code](https://sonarcloud.io/api/project_badges/measure?project=wuan_json-schema&metric=ncloc)](https://sonarcloud.io/summary/new_code?id=wuan_json-schema)
[![Duplicated Lines (%)](https://sonarcloud.io/api/project_badges/measure?project=wuan_json-schema&metric=duplicated_lines_density)](https://sonarcloud.io/summary/new_code?id=wuan_json-schema)
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=wuan_json-schema&metric=coverage)](https://sonarcloud.io/summary/new_code?id=wuan_json-schema)
[![MavenCentral](https://img.shields.io/maven-central/v/com.mercateo/json-schema.svg)](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.mercateo%22%20AND%20a%3A%22json-schema%22)

# json-schema

Generic JSON-Schema generator.

## Features

 * Handles Generics
 * Configurable
 * Supports dynamic schema with allowed values and default values
 
## Example

```$java
    SchemaGenerator schemaGenerator = new SchemaGenerator(
            Collections.emptyList(),
            Collections.<RawPropertyCollector>singletonList(new FieldCollector()),
            new HashMap<>());
    
    ObjectNode schema = schemaGenerator.generateSchema(Foo.class);
    
    String schemaString = schema.toString();
```

Generates the following result:
```$javascript
{
  "type": "object", 
  "properties": {
    "baz": {
      "type": "object", 
      "properties": {
        "qux": {
          "type": "string"
        }
      }
    }, 
    "bar": {
      "type": "string"
    }
  }
}}
```
## Property structure

```
class Foo {
    String bar;
    Baz baz;
}

class Baz {
    String qux;
}
```

`bar`, `baz` and `qux` are properties.

### Property representation

Properties are represented as Property Objects specified by containing and contained type:

```
Property<Foo, String> bar;
Property<Foo, Baz> baz;
Property<Baz, String> qux;
```

The part of the property which is represented by its type is contained in a `PropertyDescriptor` which has a generic type representing its contained type.

### Property unwrapping


```
class Foo {
    String bar;
    
    @JsonUnwrapped
    Baz baz;
}

class Baz {
    String qux;
}
```

```
Property<Foo, String> bar;
Property<Foo, String> qux;
```

# Benchmarks

current results

```
Benchmark                         Mode  Cnt        Score       Error  Units
Benchmarks.createRepeatedSchema  thrpt   10  1818545.697 ± 38533.578  ops/s
Benchmarks.createSchema          thrpt   10   260946.251 ± 15201.070  ops/s
```

`createSchema` creates `SchemaGenerator` and schema for each iteration, `createdRepeatedSchema` uses caching in the `SchemaGenerator` for schema creation
