# NX NestJS Linode Module

This package provides a service to interact with Linode Object Storage using the AWS SDK. It includes methods for creating, listing, and deleting buckets and objects, as well as other utility functions.

## Table of Contents

- [Generation](#Generation)
- [Installation](#installation)
- [Environment Variables](#environment-variables)
- [Usage](#usage)
- [Features](#Features)
- [License](#license)


## Generation

This library was generated with [Nx](https://nx.dev).
command used for generation :
`nx g @nx/nest:lib linode-sdk --buildable --publishable --importPath=@catalyst/linode-sdk --directory packages/linode-sdk`


## Building

Run `nx build @wexcute/catalyst-linode-sdk` to build the library.

## Installation

To install the NX NestJS Linode-sdk Module, run the following command in your project root:

```bash
npm  install '@wexcute/catalyst-linode-sdk';

```
## Environment Variables

To configure the logger, the following environment variables must be set in your application's environment:

| Variable             | Description                              | Example             |
| -------------------- | ---------------------------------------- | ------------------- |
| `LINODE_AUTH_TOKEN`       | Your linode auth-token               | `your-linode-auth-token` |
| `LINODE_REGION`   | Your Linode Region                    | `your-linode-region`    |
| `LINODE_BUCKET`     | Your Bucket in Linode | `your-bucket`        |
| `LINODE_ENDPOINT` | Your Linode endpoint              | `https://{region}.linodeobjects.com`   |
| `LINODE_ACCESS_KEY` | Your Linode AccessKeyID               | `your-linode-access-key`   |
| `LINODE_SECRET_KEY` | Your Linode SecretAccessKey               | `your-linode-secret-key`   |


## Configuration
Make sure to add these variables to your `.env` file or your environment configuration:

```env
LINODE_AUTH_TOKEN=your-linode-auth-token
LINODE_REGION=your-linode-region
LINODE_BUCKET=your-default-bucket
LINODE_ENDPOINT=https://{region}.linodeobjects.com
LINODE_ACCESS_KEY=your-linode-access-key
LINODE_SECRET_KEY=your-linode-secret-key
```

## Usage
### Importing the Module

To use the NX NestJS Linode-sdk Module in your NestJS application, import it in your `AppModule` or any other module:

```typescript
import { Module } from '@nestjs/common';
import { LinodeModule } from '@wexcute/catalyst-linode-sdk';

@Module({
  imports: [
    LinodeModule.forRoot(),
    // other modules...
  ],
})
export class AppModule {}
```

### Using the Linode-sdk service

Once the module is imported, it will read from the same @nestjs/config module the specified environment variables you can use the linode-sdk in your services or controllers by injecting it:


```typescript
import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk'

@Injectable()
export class ExampleService {
  constructor(
    private readonly linodeService: LinodeService,
  ) {}
```

## Features

<details>
<summary>
  CreateBucket
</summary>

```typescript
// The following example creates a bucket.

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

  async createBucket() {
    const input = {
      acl: 'private',         
      corsEnabled: false,      
      bucket: 'example-bucket', 
      region: 'ue-central-1',  
    };
    const response = await this.linodeService.createBucket(input);

    /* response example
    {
      "hostname": "example-bucket.eu-central-1.linodeobjects.com",
      "label": "example-bucket",
      "created": "2024-07-07T18:29:43",
      "region": "eu-central",
      "cluster": "eu-central-1",
    }
    */


  }
}
```
## CreateBucket Input

| Parameter             | Type                              | Description             |
| -------------------- | ---------------------------------------- | ------------------- |
| `acl`       |  string               | `(Optional)` Access Control List (ACL) settings for the bucket. Possible values are: private, public-read, public-read-write. private makes the bucket and its contents accessible only to the owner. public-read allows read access by anyone. public-read-write allows both read and write access by anyone. Defaults to private.|
| `corsEnabled`   |  boolean                    | `(Optional)` Whether Cross-Origin Resource Sharing (CORS) is enabled for the bucket. `Default is false.`    |
| `bucket`     |string |`(Required)` The name of the bucket to create.   |
| `region` | string             | `(Optional)` The region where the bucket will be created. Defaults to the region specified in environment variables.|
 


 # Note: 
  - The bucket must be unique and should not be used by any other bucket (from any customer) in the selected data center. Keep the following formatting requirements in mind:
   - Must be between 3 and 63 characters in length.
   - Can only contain lower-case characters, numbers, periods, and dashes.
   - Must start with a lowercase letter or number.
   - Cannot contain underscores (_), end with a dash (-) or period (.), have consecutive periods (.), or use dashes (-) adjacent to periods (.).
   - Cannot be formatted as IP addresses.



</details>


<details>
<summary>
  GetBuckets
</summary>

```typescript
// The following example retrieves a list of buckets.

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const response = await this.linodeService.getListBuckets();

    /* response example
    {
    "data": [
          {
              "hostname": "example-bucket.eu-central-1.linodeobjects.com",
              "label": "example-bucket",
              "created": "2024-07-04T22:29:48",
              "region": "eu-central",
              "cluster": "eu-central-1",
              "size": 0,
              "objects": 0
          },
         ],
          "page": 1,
          "pages": 1,
          "results": 3
      }
    */
    
  }
}
```

</details>

  <details>
  <summary>
     GetBucketsPerRegion
  </summary>

```typescript
// The following example retrieve all buckets per region

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}
 
      const response = await this.linodeService.getBucketsPerRegion(region);

    /* response example
   {
    "data": [
        {
            "hostname": "example-bucket.eu-central-1.linodeobjects.com",
            "label": "example-bucket",
            "created": "2024-07-04T22:29:48",
            "region": "eu-central",
            "cluster": "eu-central-1",
            "size": 0,
            "objects": 0
        },
    ],
    "page": 1,
    "pages": 1,
    "results": 2
  }
    */
  }
}
```


## GetBucketsPerRegion Input

| Parameter             | Type                              | Description             |
| -------------------- | ---------------------------------------- | ------------------- |
| `region` | string             | `(Optional)` Defaults to the region specified in environment variables.|


</details>

  <details>
  <summary>
     GetBucket
  </summary>

```typescript
// The following example retrieve bucket

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}
      const input = {
        bucket: 'eample-bucket;
        region: 'eu-central-1;
      }

      const response = await this.linodeService.getBucket(input);

    /* response example
    {
      "hostname": "example-bucket.eu-central-1.linodeobjects.com",
      "label": "example-bucket",
      "created": "2024-06-28T12:58:05",
      "region": "eu-central",
      "cluster": "eu-central-1",
      "size": 0,
      "objects": 0
    }
    */
    
  }
}
```



## GetBucket Input

| Parameter             | Type                              | Description             |
| -------------------- | ---------------------------------------- | ------------------- |
| `bucket` | string             | `(Optional)` Defaults to the bucket specified in environment variables.|
| `region` | string             | `(Optional)` Defaults to the region specified in environment variables.|



</details>



<details>
  <summary>
  putObjectInBucket
  </summary>


```typescript
// The following example put object in bucket

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const input = {
          bucket: "example-bucket",
          key: "files.txt",
          content: "Hello World!",
       }

      const response = await this.linodeService.putObjectInBucket(input);

    /* response example
     {
      "$metadata": {
            "httpStatusCode": 200,
            "requestId": "tx00000c889496f38466545-00232316b6-65e3314d-default",
            "attempts": 1,
            "totalRetryDelay": 0
          },
        "ETag": "\"2c935d77a374c30f029e8a131229087\""
      }
    */
  }
}
```
## PutObjectInBucket Input

| Parameter             | Type                              | Description             |
| -------------------- | ---------------------------------------- | ------------------- |
| `acl`       |  string               | `(Optional)` Access Control List (ACL) settings for the bucket. Possible values are: private, public-read, public-read-write. private makes the bucket and its contents accessible only to the owner. public-read allows read access by anyone. public-read-write allows both read and write access by anyone. Defaults to private.|
| `key`   |  string                    | `(Required)` filename   |
| `content`   | Buffer \| Uint8Array \| string | `(Required)` The file body, which can be provided as a Buffer, Uint8Array, or string.                  |
| `bucket`     |string |`(Optional)` Defaults to the bucket specified in environment variables.   |
| `region` | string             | `(Optional)` Defaults to the region specified in environment variables.|
| `options(path)`  | string | `(Optional)`  The folder path.     |
| `options(acl)`   | string | `(Optional)` Access Control List (ACL) settings for the folder. Possible values are: `private`, `public-read`, `public-read-write`. Defaults to `private`. |
 
</details>

<details>
  <summary>
  GetListObjectsInBucket
  </summary>


```typescript
// The following example retrieves a list of objects in bucket
import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const input = {
        bucket: 'example-bucket' 
      }

      const response = await this.linodeService.getListObjectsInBucket(input);

    /* response example
    {
      {
    "$metadata": {
        "httpStatusCode": 200,
        "requestId": "tx000000195b2520f23g4fa-01268c073f-65db69c4-default",
        "attempts": 1,
        "totalRetryDelay": 0
    },
    "Contents": [
        {
            "Key": "example.txt",
            "LastModified": "2024-07-08T08:48:24.119Z",
            "ETag": "\"2c935d77a378c30f029e868236949087\"",
            "Size": 628,
            "StorageClass": "STANDARD"
        }
    ],
    "IsTruncated": false,
    "KeyCount": 1,
    "MaxKeys": 1000,
    "Name": "example-bucket",
    "Prefix": ""
    } 
    }
    */
  }
}
```



## GetListObjectsInBucket Input

| Parameter             | Type                              | Description             |
| -------------------- | ---------------------------------------- | ------------------- |
| `bucket` | string             | `(Optional)` Defaults to the bucket specified in environment variables.|
 `prefix`  | string | `(Optional)` A prefix to filter the objects in the bucket. `(folderName)` |


</details>





<details>
  <summary>
  GetObjectInBucket
  </summary>


```typescript
// The following example retrieve an object in bucket
import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const input = {
        bucket: 'example-bucket',
        key: "file.txt"
      }

      const response = await this.linodeService. getObjectInBucket(input);

    /* response example
    {
      {
    "$metadata": {
        "httpStatusCode": 200,
        "requestId": "tx000000195b2520f23g4fa-01268c073f-65db69c4-default",
        "attempts": 1,
        "totalRetryDelay": 0
    },
    "Contents": [
        {
            "Key": "example.txt",
            "LastModified": "2024-07-08T08:48:24.119Z",
            "ETag": "\"2c935d77a378c30f029e868236949087\"",
            "Size": 628,
            "StorageClass": "STANDARD"
        }
    ],
    "IsTruncated": false,
    "KeyCount": 1,
    "MaxKeys": 1000,
    "Name": "example-bucket",
    "Prefix": ""
    } 
    }
    */
  }
}
```





## GetObjectInBucket Input

| Parameter             | Type                              | Description             |
| -------------------- | ---------------------------------------- | ------------------- |
| `bucket` | string             | `(Optional)` Defaults to the bucket specified in environment variables.|
 `key`  | string | `(Required)` The file path |


</details>





<details>
  <summary>
  GetFoldersInBucket
  </summary>


```typescript
// The following example retrieves list of folders in bucket

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const input = {
        bucket: 'example-bucket',
      }

      const response = await this.linodeService.getFoldersInBucket(input);

    /* response example
      [
        {
            "Prefix": "images/"
        },
        {
            "Prefix": "programming/"
        }
      ]
    */
  }
}
```



## GetFoldersInBucket Input

| Parameter             | Type                              | Description             |
| -------------------- | ---------------------------------------- | ------------------- |
| `bucket` | string             | `(Optional)` Defaults to the bucket specified in environment variables.|


</details>




<details>
  <summary>
  CreateObjectURL
  </summary>


```typescript
// The following example  for an create object url within a bucket.

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const input = {
        bucket: 'example-bucket',
        key: 'filePath';
        expires: 60;
      }

      const response = await this.linodeService.createObjectURL(input);

    /* response example
        return url link for example:
         https://example-bucket.eu-central-1.linodeobjects.com/files/file.txt
    */
  }
}
```



## CreateObjectURL Input

| Parameter             | Type                              | Description             |
| -------------------- | ---------------------------------------- | ------------------- |
| `bucket` | string             | `(Optional)` Defaults to the bucket specified in environment variables.|
 `key`  | string | `(Required)` The file path |
 `expires`  | number | `(Optional)`The time in seconds before the URL expires. . Defaults to `60 sec`. |


</details>





<details>
  <summary>
  CopyObjectInBucket
  </summary>


```typescript
// The following example copy an object within a bucket.

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const input = {
        sourceBucket: 'example-bucket';
        fileSource: 'files/file.txt';
        destinationBucket: 'new-bucket;
        key: 'newfiles/file.txt';
      }

      const response = await this.linodeService.copyObjectInBucket(input);

    /* response example
       {
        "$metadata": {
            "httpStatusCode": 200,
            "requestId": "tx00000d14dc914ce654640-00668c6626-65d412d8-default",
            "attempts": 1,
            "totalRetryDelay": 0
        },
        "CopyObjectResult": {
            "ETag": "2e4b6f1117e6d0f63c973c79ff88531f",
            "LastModified": "2024-07-08T22:20:22.501Z"
        }
      }
    */
  }
}
```


## CopyObjectInBucket Input

| Parameter             | Type   | Description                                                                                          |
| --------------------- | ------ | ---------------------------------------------------------------------------------------------------- |
| `sourceBucket`      | string | `(Optional)` The name of the bucket containing the source object to be copied. Defaults to the bucket specified in environment variables.                     |
| `fileSource`        | string | `(Required)` The file path of the source object.                           |
| `destinationBucket` | string | `(Optional)` The name of the bucket where the object will be copied to. Defaults to the bucket specified in environment variables.                              |
| `key`               | string | `(Required)` The new file path.        |                 


</details>




<details>
  <summary>
  MoveObjectInBucket
  </summary>


```typescript
// The following example moves an object within a bucket.

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const input = {
        sourceBucket: 'example-bucket';
        fileSource: 'files/file.txt';
        destinationBucket: 'new-bucket;
        key: 'newfiles/file.txt';
      }

      const response = await this.linodeService.moveObjectInBucket(input);

    /* response example
      {
      "$metadata": {
          "httpStatusCode": 200
        }
      }
    */
  }
}
```


## MoveObjectInBucket Input

| Parameter             | Type   | Description                                                                                          |
| --------------------- | ------ | ---------------------------------------------------------------------------------------------------- |
| `sourceBucket`      | string | `(Optional)` The name of the bucket containing the source object to be moved. Defaults to the bucket specified in environment variables.                     |
| `fileSource`        | string | `(Required)` The file path of the source object.                           |
| `destinationBucket` | string | `(Optional)` The name of the bucket where the object will be moved to. Defaults to the bucket specified in environment variables.                              |
| `key`               | string | `(Required)` The new file path.        |                 


</details>






<details>
  <summary>
  DeleteObjectInBucket
  </summary>


```typescript
// The following example deletes an object in a bucket.

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const input = {
        bucket: 'example-bucket';
        key: 'files/file.txt';
      }

      const response = await this.linodeService.deleteObjectInBucket(input);

    /* response example
      {
        "$metadata": {
            "httpStatusCode": 204,
            "requestId": "tx000003a4416952f3dc6c7-00668c682f-b85eae8f-default",
            "attempts": 1,
            "totalRetryDelay": 0
          }
       }
      */
  }
}
```


## DeleteObjectInBucket Input

| Parameter             | Type   | Description                                                                                          |
| --------------------- | ------ | ---------------------------------------------------------------------------------------------------- |
| `bucket`  | string | `(Optional)` The name of the bucket containing the object to be deleted. Defaults to the bucket specified in environment variables. |
| `key`               | string | `(Required)` The  file path.        |                 


</details>





<details>
  <summary>
  DeleteObjectsInBucket
  </summary>


```typescript
// The following example deletes multiple objects in a bucket.

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const input = {
        bucket: 'example-bucket';
        key: ['files/file.txt', 'programming.file.js'];
      }

      const response = await this.linodeService.deleteObjectsInBucket(input);

    /* response example
      {
        "$metadata": {
            "httpStatusCode": 204,
            "requestId": "tx000003a4416952f3dc6c7-00668c682f-b85eae8f-default",
            "attempts": 1,
            "totalRetryDelay": 0
          }
       }
      */
  }
}
```


## DeleteObjectsInBucket Input

| Parameter             | Type   | Description                                                                                          |
| --------------------- | ------ | ---------------------------------------------------------------------------------------------------- |
| `bucket`  | string | `(Optional)` The name of the bucket containing the object to be deleted. Defaults to the bucket specified in environment variables. |
| `keys`    | array  | `(Required)` An array of file paths of the objects to be deleted.                                       |
               


</details>



<details>
  <summary>
  DeleteBucket
  </summary>


```typescript
// The following example deletes a bucket from Linode Object Storage.

import { Injectable } from '@nestjs/common';
import { LinodeService } from '@wexcute/catalyst-linode-sdk';

@Injectable()
export class ExampleService {
  constructor(private readonly linodeService: LinodeService) {}

      const input = {
        bucket: 'example-bucket';
      }

      const response = await this.linodeService.deleteBucket(input);

    /* response example
      {
          $metadata: {
            httpStatusCode: 204,
          },
       }
    */
  }
}
```


## DeleteBucket Input

| Parameter             | Type   | Description                                                                                          |
| --------------------- | ------ | ---------------------------------------------------------------------------------------------------- |
| `bucket`  | string | `(Optional)` The name of the bucket to be deleted. Defaults to the bucket specified in environment variables. |
   

</details>

