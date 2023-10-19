# SwaggerHub API for Spring pagination

This is a repository to store the OpenAPI specification of the SwaggerHub API for Spring pagination, which is hosted at https://app.swaggerhub.com/apis/spring/pagination.

While a [SwaggerHub Domain](https://support.smartbear.com/swaggerhub/docs/domains/index.html) would be more appropriate for this use case, it is unfortunately a paid feature.

## Usage

1. Add `x-spring-paginated: true`.
2. Add `pageParam`, `sizeParam` and `sortParam` parameters.
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
