# catalyst-ui-components

This package provides a collection of reusable UI components designed to streamline your development process.

## Table of Contents

- [Building](#Building)
- [Installation](#installation)
- [Features](#Features)

## Building

Run `nx build @wexcute/catalyst-ui-components` to build the library.

## Installation

To install the NX PageWrapper, run the following command in your project root:

```bash
npm install '@wexcute/catalyst-ui-components';

```
## Features


### PageWrapper Component

The `PageWrapper` component is a React component designed to wrap pages with a dynamic header and footer. It utilizes the current route to display the appropriate page title and content.


## Usage

To use the `PageWrapper` component, follow the example below:

### Example

```typescript
// your-page-name/page.tsx 
import React from 'react';
import { PageWrapper } from '@wexcute/catalyst-ui-components';

// Example for routes 
const routes = {
  home: { path: '/', label: 'Home' },
  about: { path: '/dashboard', label: 'Dashboard' },
};

const MyPage = () => {

  const body = () => <div>Page Body Content</div>;
  const footer = () => <footer>Footer Content</footer>; // Optional

  return (
     <PageWrapper body={body} routes={routes} footer={footer} />
  );
};

export default MyPage;
```

