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

3. Implement Product Service with ApiFeatures

```typescript
// product.service.ts

import { ApiFeatures, DatabaseProvider } from '@wexcute/catalyst-api-features';
import {
  Inject,
  Injectable,
} from '@nestjs/common';
import { InjectModel } from '@nestjs/mongoose';
import { Model, Types } from 'mongoose';

@Injectable()
export class ProductService {
  constructor(
    @InjectModel(Product.name) private productModel: Model<Product>,
  ) {}

  async findWithPagination(query) {
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
4. Create Product Controller
```typescript
// product.controller.ts

import { Controller, Get, Post, Body, Query, Param, Put, Delete } from '@nestjs/common';
import { ProductService } from './product.service';

@Controller('products')
export class ProductController {
  constructor(private readonly productService: ProductService) {}

  @Get()
  async getProducts(@Query() query: any) {
    return this.productService.findProducts(query);
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



