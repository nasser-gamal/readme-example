# Api Features

This package, @wexcute/catalyst-api-features, provides a convenient way to interact with Linode Object Storage using AWS SDK through a set of utility methods tailored for database management and query handling in NestJS applications. It includes powerful tools for filtering, sorting, paginating, and searching through database records.

## Building

Run `nx build @wexcute/catalyst-api-features` to build the library.

## Installation

To install the NX NestJS api-features Module, run the following command in your project root:

```bash
npm  install @wexcute/catalyst-api-features
```


## Usage

Follow these steps to integrate and use the package in your application:

1. Define the Product entity

```typescript
// product.entity.ts

import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';

@Schema({ timestamps: true })
export class Product {
  @Prop({ required: true })
  title: string;

  @Prop()
  description: string;

  @Prop({ required: true })
  price: number;

  @Prop({ default: 0 })
  stock: number;

  @Prop({ default: 0 })
  sold: number;
}

export const ProductSchema = SchemaFactory.createForClass(Product);
```

2- Create Product Module

```typescript
// product.module.ts

import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { ProductService } from './product.service';
import { ProductController } from './product.controller';
import { Product, ProductSchema } from './schemas/product.schema';

@Module({
  imports: [
    MongooseModule.forFeature([{ name: Product.name, schema: ProductSchema }]),
  ],
  controllers: [ProductController],
  providers: [ProductService],
})
export class ProductModule {}

```

3- Create Dto for findManyProduct

```typescript
import { IsEnum } from 'class-validator';
import { FindManyOptionsDto, AllowedSortingFields } from '@wexcute/catalyst-api-features';

enum SearchBy {
  name = 'name',
 // Add any additional searchable fields here
}

export class FindManyProductsDto extends FindManyOptionsDto {
  // Specifies the fields that can be used for sorting results in the query. 
  @AllowedSortingFields(['createdAt', 'updatedAt', 'name', '_id', 'price', 'stock']) 
  sort?: string;

  @IsEnum(SearchBy)
  searchBy?: string;
}

```

4- Implement Product Service with ApiFeatures

```typescript
// product.service.ts

import { ApiFeatures, DatabaseProvider } from '@wexcute/catalyst-api-features';
import {
  Inject,
  Injectable,
} from '@nestjs/common';
import { InjectModel } from '@nestjs/mongoose';
import { Model, Types } from 'mongoose';
import { FindManyProductsDto } from './find-products.dto'

@Injectable()
export class ProductService {
  constructor(
    @InjectModel(Product.name) private productModel: Model<Product>,
  ) {}

  async find(query: FindManyProductsDto) {
    const products = this.productModel.find();
    const totalCount = await this.productModel.countDocuments();

    const { records } = new ApiFeatures({
      entitySource: products,
      query,
      type: DatabaseProvider.MONGOOSE,
    })
      .filter()
      .limitFields()
      .search()
      .sort()
      .paginate();

    // IMPORTANT: Return the keys 'records' and 'totalCount' to ensure consistency in the response structure.
    // 'records' contains the actual product data, while 'totalCount' provides the total number of products for pagination.
    return { records, totalCount };
  }

  // ... other methods
}

```
5- Create Product Controller
```typescript
// product.controller.ts

import { Controller, Get, Query, UseInterceptors } from '@nestjs/common';
import { ProductService } from './product.service';

@Controller('products')
export class ProductController {
  constructor(private readonly productService: ProductService) {}

  @Get()
  // The PaginateResponseInterceptor is used to transform the response of API calls,
  // adding pagination metadata such as the current page, number of pages, total records
  @UseInterceptors(PaginateResponseInterceptor)
  async getProducts(@Query() query: FindManyProductsDto) {
    return this.productService.find(query);
  }
}

```


## Features
<details>
<summary>
  Filtering
</summary>

  Allows you to filter database records based on specific fields.

**Example:**  
```plaintext
?name=electronics&price=100 
```

**Example:**  
 Retrieves records where name is "electronics" and price is 100

You can filter with operators such as `gt, gte, lt, lte, eq` to retrieve records based on conditions:

- gt (greater than)
- gte (greater than or equal to)
- lt (less than)
- lte (less than or equal to)
- eq (equal to)
**Examples with Operators:**  
```plaintext
?sold[gte]=10  // Retrieves records where sold is greater than or equal 10

?price[lt]=1000  // Retrieves records where the price is less than 1000
```
</details>

<details>
<summary>
  Sorting
</summary>

  Enables sorting of records in ascending or descending order by one or more fields.

**Example:**  
  
```plaintext
?sort=price,-stock  // Retrieves records sorted by price (ascending) and stock (descending)
```
  - `price`: Sorted in ascending order (ASC).

    - Ascending order (ASC): Records are sorted from the lowest to the highest value (e.g., from 1 to 10, or from A to Z).

  - `-stock`: Sorted in descending order (DESC).

    - Descending order (DESC): Records are sorted from the highest to the lowest value (e.g., from 10 to 1, or from Z to A).
</details>


<details>
<summary>
  Limit Fields
</summary>

  Allows you to specify which fields to include in the results, optimizing query performance.

**Example:**  

```plaintext
  ?fields=name,price  //  Retrieves only the `name` and `price` of each record, omitting all other fields.
```
  - name: Includes the name field in the response.
  - price: Includes the price field in the response.
</details>
  


<details>
<summary>
  Searching
</summary>

  Provides a simple way to perform text-based searches on specified fields.
  
**Example:**  
```plaintext
?searchBy=name&keyword=laptop   // Retrieves records where the name contains the keyword "laptop"

```

  - name: Specifies the field to search within (e.g., the product name).
  - keyword=`laptop`: The search term, which in this case is "laptop".
</details>


<details>
<summary>
  Pagination
</summary>

  Enables paginated responses for large datasets.

**Example:**  
```plaintext
?page=1&limit=10   // Retrieves the first 10 records on page 1

```
</details>


  
