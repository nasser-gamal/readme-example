# Next Auth Config Package

This package provides a reusable configuration for NextAuth.js, allowing you to share authentication settings across multiple projects.

## Features

- Centralized NextAuth.js configuration
- Optional override for the `NEXTAUTH_SECRET`
- Custom JWT and session callbacks
- Custom providers  (e.g. BasicAuthProvider)

## Installation

To install the package, use npm or yarn:

```bash
npm install @wexcute/catalyst-next-auth-config
# or
yarn add @wexcute/catalyst-next-auth-config
```

## Usage

To use the `authOptions` in your Next.js project, import the `authOptions` function from the package and pass it to NextAuth.

### Example

1. **Install the necessary dependencies**:

   ```bash
   npm install next-auth jsonwebtoken
   ```

2. **Create your NextAuth route handler**:

   ```typescript
   // src/pages/api/auth/[...nextauth].ts
   import NextAuth from 'next-auth';
   import { NextRequest } from 'next/server';
   import { Provider } from 'next-auth/providers';
   import { authOptions } from '@wexcute/catalyst-next-auth-config';

   interface RouteHandlerContext {
     params: { nextauth: string[] };
   }

   const handler = async function auth(
     req: NextRequest,
     context: RouteHandlerContext
   ) {
     const nextAuthSecret = process.env.NEXTAUTH_SECRET;
     return await NextAuth(req, context, authOptions(providers, nextAuthSecret));
   };

   export { handler as GET, handler as POST };
   ```

## Configuration

The `authOptions` function accepts an optional `nextAuthSecret` parameter. If provided, this secret will be used; otherwise, the default secret defined in the package will be used.

### Example Configuration

```typescript
import { AuthOptions } from 'next-auth';
import jwt from 'jsonwebtoken';

const DEFAULT_NEXTAUTH_SECRET = 'your-static-secret';
const MAX_AGE = 60 * 60 * 24 * 7; // Example max age of 1 week

export const authOptions =(providers: Provider[],
  nextAuthSecret?: string)
  : AuthOptions => {
  const secret = nextAuthSecret || DEFAULT_NEXTAUTH_SECRET;

  return {
    secret,
    providers: [
      // Add your providers here
    ],
    session: {
      strategy: 'jwt',
      maxAge: MAX_AGE,
    },
    callbacks: {
      async redirect({ url, baseUrl }) {
        if (url.startsWith('/')) return `${baseUrl}${url}`;
        else if (new URL(url).origin === baseUrl) return `${url}`;
        return `${baseUrl}`;
      },
      async jwt({ token, account, user }) {
        if (account) {
          token.accessToken = account.access_token;
        }
        if (user) {
          token.accessToken = (user as any)?.token;
        }
        const jwtClaims = {
          user: {
            name: token.name,
            email: token.email,
            providerAccountId: token.sub,
            id: token?.authUser?._id,
          },
        };

        const jwtPayload = jwt.sign(jwtClaims, secret, {
          expiresIn: MAX_AGE,
          algorithm: 'HS512',
        });
        return { ...token, jwtPayload };
      },
      async session({ session, token }) {
        const customSession = {
          ...session,
          type: token.type,
          accessToken: token.accessToken,
          jwtPayload: token.jwtPayload,
          user: {
            id: token.id,
            email: token.email,
            name: token.name,
            image: token.picture,
            providerAccountId: token.sub,
          },
          createdAt: new Date(),
        };

        if (token.authUser) {
          customSession.user = {
            ...customSession.user,
            ...token.authUser,
          };
        }
        return customSession;
      },
      async signIn({ user, account, profile }) {
        const userProfile = {
          name: user.name,
          email: user.email,
          image: user.image,
          providerType: account?.provider,
          providerAccountId: account?.providerAccountId ?? profile?.sub,
        };

        return true;
      },
    },
    pages: {
      signIn: '/login',
      error: '/error',
    },
  };
};
```
## Providers

### - BasicAuthProvider
   The `BasicAuthProvider` is a custom provider for NextAuth.js that allows you to authenticate users with a backend endpoint.

### Example


1 - Import the `BasicAuthProvider` and add it to your providers:

``` typescript
// src/pages/api/auth/[...nextauth].ts
import NextAuth from 'next-auth';
import { NextRequest } from 'next/server';
import { authOptions, BasicAuthProvider } from '@wexcute/catalyst-next-auth-config';

interface RouteHandlerContext {
  params: { nextauth: string[] };
}

const handler = async function auth(
  req: NextRequest,
  context: RouteHandlerContext
) {
  return await NextAuth(req, context, authOptions(providers));
};

export { handler as GET, handler as POST };

const providers = [
  BasicAuthProvider({
    endpoint: 'YOUR_BACKEND_ENDPOINT' // This is the endpoint where the login request will be sent.
  })
];

```


## Building

Run `nx build @wexcute/catalyst-next-auth-config` to build the library.
