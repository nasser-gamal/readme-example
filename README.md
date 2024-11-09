# Api Features

This package, @wexcute/catalyst-api-features, provides a convenient way to interact with Linode Object Storage using AWS SDK through a set of utility methods tailored for database management and query handling in NestJS applications. It includes powerful tools for filtering, sorting, paginating, and searching through database records.

## Building

Run `nx build @wexcute/catalyst-api-features` to build the library.

## Installation

To install the NX NestJS api-features Module, run the following command in your project root:

```bash
npm  install @wexcute/catalyst-api-features;
```


## Usage
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
import { ApiProperty } from '@nestjs/swagger';
import { IsOptional, IsString, IsEnum, IsNumber } from 'class-validator';
import { FindManyOptionsDto, AllowedSortingFields } from '@wexcute/catalyst-api-features';

enum SearchBy {
  name = 'name',
 // Add any additional searchable fields here
}

export class FindManyProductsDto extends FindManyOptionsDto {
  @ApiProperty({ example: 'updatedAt', required: false, description: 'Sort by field' })
  @IsOptional()
  @IsString()
  // Specifies the fields that can be used for sorting results in the query. 
  @AllowedSortingFields(['createdAt', 'updatedAt', 'name', '_id', 'price', 'stock']) 
  sort?: string;

  @ApiProperty({ type: 'enum', enum: SearchBy, required: false, description: 'Search by field' })
  @IsOptional()
  @IsString()
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

    return { records, totalCount };
  }

  // ... other methods
}

```
5- Create Product Controller
```typescript
// product.controller.ts

import { Controller, Get, Query } from '@nestjs/common';
import { ProductService } from './product.service';

@Controller('products')
export class ProductController {
  constructor(private readonly productService: ProductService) {}

  @Get()
  async getProducts(@Query() query: FindManyProductsDto) {
    return this.productService.find(query);
  }
}

```


## Features
### Filtering
    Allows you to filter database records based on specific fields.
    Example: ?category=electronics&brand=Apple
### Sorting
    Enables sorting of records in ascending or descending order by one or more fields.
    Example: ?sort=price,-rating (sorts by price ascending and rating descending)
### Filtering
    Allows you to specify which fields to include in the results, optimizing query performance.
    Example: ?fields=name,price (returns only the name and price of each record)
### Filtering
    Provides a simple way to perform text-based searches on specified fields.
    Example: ?search=Apple (finds all records containing "Apple")
### Pagination
    Enables paginated responses for large datasets.
    Example: ?page=2&limit=10 (returns 10 records starting from the 11th record)




| Name             | Description                              | Example             |
| -------------------- | ---------------------------------------- | ------------------- |
| `Filtering` | Allows you to filter database records based on specific fields. | `?title=Laptop&price=100|
|
| `Sorting` | Enables sorting of records in ascending or descending order by one or more fields. | `?sort=price,-rating |
