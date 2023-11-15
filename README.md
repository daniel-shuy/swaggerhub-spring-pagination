# SwaggerHub API for Spring pagination

This is a repository to store the OpenAPI specification of the SwaggerHub API for Spring pagination, which is hosted at https://app.swaggerhub.com/apis/spring/pagination.

While a [SwaggerHub Domain](https://support.smartbear.com/swaggerhub/docs/domains/index.html) would be more appropriate for this use case, it is unfortunately a paid feature.

## Usage

1. Add `x-spring-paginated: true`.
2. Add `pageParam`, `sizeParam` and `sortParam` parameters (note that this will add `page`, `size` and `sort` parameters to the generated controller methods, until OpenAPITools/openapi-generator#8315 is implemented).
3. Create a schema that inherits from `PaginationResponse` and use it in the response content schema.

Example usage:
```yaml
# ...

paths:
  /pet:
    get:
      tags:
        - pet
      operationId: findPets
      x-spring-paginated: true
      parameters:
        - $ref: 'https://api.swaggerhub.com/apis/spring/pagination/1.0.0#/components/parameters/pageParam'
        - $ref: 'https://api.swaggerhub.com/apis/spring/pagination/1.0.0#/components/parameters/sizeParam'
        - $ref: 'https://api.swaggerhub.com/apis/spring/pagination/1.0.0#/components/parameters/sortParam'
      responses:
        '200':
          description: successful operation
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PetPage'

components:
  schemas:
    Pet:
      # ...
    PetPage:
      type: object
      allOf:
        - $ref: 'https://api.swaggerhub.com/apis/spring/pagination/1.0.0#/components/schemas/PaginationResponse'
        - type: object
          properties:
            content:
              type: array
              items:
                $ref: '#/components/schemas/Pet'
              default: []
```

### OpenAPI Generator

If using with OpenAPI Generator, configure the following mappings:

[**Type Mappings**](https://openapi-generator.tech/docs/usage/#type-mappings-and-import-mappings)
- object+pageable=Pageable
- object+sort=Sort

[**Schema Mappings**](https://openapi-generator.tech/docs/customization/#schema-mapping)
- Pageable=org.springframework.data.domain.Pageable
- Sort=org.springframework.data.domain.Sort

e.g.

With [openapi-generator-cli](https://github.com/OpenAPITools/openapi-generator-cli):

```shell
openapi-generator-cli generate \
    -i petstore.yaml \
    -g spring \
    --schema-mappings=Pageable=org.springframework.data.domain.Pageable,Sort=org.springframework.data.domain.Sort \
    --type-mappings=object+pageable=Pageable,object+sort=Sort
```

With [openapi-generator-maven-plugin](https://github.com/OpenAPITools/openapi-generator/tree/master/modules/openapi-generator-maven-plugin):

```yaml
<plugin>
    <groupId>org.openapitools</groupId>
    <artifactId>openapi-generator-maven-plugin</artifactId>
    <!-- ... -->
    <executions>
        <execution>
            <goals>
                <goal>generate</goal>
            </goals>
            <configuration>
                <inputSpec>${project.basedir}/src/main/resources/petstore.yaml</inputSpec>
                <generatorName>spring</generatorName>
                <typeMappings>
                    <typeMapping>object+pageable=Pageable</typeMapping>
                    <typeMapping>object+sort=Sort</typeMapping>
                </typeMappings>
                <schemaMappings>
                    <schemaMapping>Pageable=org.springframework.data.domain.Pageable</schemaMapping>
                    <schemaMapping>Sort=org.springframework.data.domain.Sort</schemaMapping>
                </schemaMappings>
            </configuration>
        </execution>
    </executions>
</plugin>
```
