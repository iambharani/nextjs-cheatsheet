# nextjs-cheatsheet
Here’s a **more detailed Next.js cheatsheet** that includes additional sections for advanced routing, static generation, server-side rendering, image optimization, and more configuration options.

---

## **1. Pages and Routing**

- **Basic Routing**:
  - `pages/index.js` → `/`
  - `pages/about.js` → `/about`
  - `pages/blog/[id].js` → `/blog/123` (Dynamic routing)
  
  ```js
  // Access dynamic route params
  import { useRouter } from 'next/router';
  
  const Blog = () => {
    const router = useRouter();
    const { id } = router.query;
    return <p>Blog ID: {id}</p>;
  };
  ```

- **Nested Routes**:
  - `pages/blog/index.js` → `/blog`
  - `pages/blog/[id].js` → `/blog/:id`
  
- **Custom 404 Page**:
  ```js
  // pages/404.js
  export default function Custom404() {
    return <h1>404 - Page Not Found</h1>;
  }
  ```

- **API Routes**:
  Create API routes under `pages/api/` directory.
  ```js
  // pages/api/hello.js
  export default function handler(req, res) {
    res.status(200).json({ message: 'Hello World' });
  }
  ```

---

## **2. Dynamic Routing with getStaticProps and getStaticPaths**

- **`getStaticProps`**: Fetch data at build time.
  ```js
  export async function getStaticProps() {
    const res = await fetch('https://api.example.com/posts');
    const posts = await res.json();

    return {
      props: {
        posts,
      },
    };
  }
  ```

- **`getStaticPaths`**: Generates paths dynamically for each post.
  ```js
  export async function getStaticPaths() {
    const res = await fetch('https://api.example.com/posts');
    const posts = await res.json();

    const paths = posts.map((post) => ({
      params: { id: post.id.toString() },
    }));

    return { paths, fallback: false };
  }
  ```

- **`getServerSideProps`**: Fetch data at request time for SSR.
  ```js
  export async function getServerSideProps(context) {
    const res = await fetch('https://api.example.com/posts');
    const posts = await res.json();
    
    return { props: { posts } };
  }
  ```

---

## **3. Data Fetching Methods**

### **a. Static Generation (SSG)**

- `getStaticProps`: Fetches data at build time.
  ```js
  export async function getStaticProps() {
    const data = await fetchData();
    return { props: { data } };
  }
  ```

- `getStaticPaths`: Used with dynamic routes to pre-render pages.
  ```js
  export async function getStaticPaths() {
    return {
      paths: [{ params: { id: '1' } }],
      fallback: false,
    };
  }
  ```

### **b. Server-Side Rendering (SSR)**

- `getServerSideProps`: Fetches data on every request.
  ```js
  export async function getServerSideProps(context) {
    const res = await fetch(`https://api.example.com/${context.params.id}`);
    const data = await res.json();
    return { props: { data } };
  }
  ```

### **c. Incremental Static Regeneration (ISR)**

- **`revalidate`**: Incrementally regenerates static pages.
  ```js
  export async function getStaticProps() {
    const data = await fetchData();
    return {
      props: { data },
      revalidate: 60, // Rebuild every 60 seconds
    };
  }
  ```

---

## **4. API Routes**

- **Basic Example**:
  ```js
  // pages/api/hello.js
  export default function handler(req, res) {
    res.status(200).json({ text: 'Hello' });
  }
  ```

- **Dynamic API Routes**:
  ```js
  // pages/api/post/[id].js
  export default function handler(req, res) {
    const { id } = req.query;
    res.status(200).json({ postId: id });
  }
  ```

- **Middleware Example**:
  ```js
  export default async function handler(req, res) {
    if (req.method === 'POST') {
      // Handle POST request
    } else {
      res.status(405).end(); // Method Not Allowed
    }
  }
  ```

---

## **5. Image Optimization**

- **Image Component**:
  ```js
  import Image from 'next/image';

  <Image
    src="/me.png"
    alt="Profile Picture"
    width={500}
    height={500}
  />
  ```

- **Remote Image Optimization**:
  Update `next.config.js` to allow remote image domains.
  ```js
  module.exports = {
    images: {
      domains: ['example.com'],
    },
  };
  ```

---

## **6. Custom `App` and `Document`**

- **Custom App (`_app.js`)**: Useful for layout, providers, etc.
  ```js
  // pages/_app.js
  import '../styles/globals.css';

  function MyApp({ Component, pageProps }) {
    return <Component {...pageProps} />;
  }

  export default MyApp;
  ```

- **Custom Document (`_document.js`)**: Modify `<html>` or `<body>`.
  ```js
  // pages/_document.js
  import { Html, Head, Main, NextScript } from 'next/document';

  export default function Document() {
    return (
      <Html>
        <Head />
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
  ```

---

## **7. Static Files**

- **Serving Static Files**: Use the `public` directory for static files.
  ```bash
  public/images/me.png
  ```

  Access in code:
  ```js
  <img src="/images/me.png" alt="My Image" />
  ```

---

## **8. Environment Variables**

- **Using `.env.local`** for local environment variables:
  ```
  NEXT_PUBLIC_API_URL=https://api.example.com
  ```

- Access environment variables in the app:
  ```js
  const apiUrl = process.env.NEXT_PUBLIC_API_URL;
  ```

---

## **9. Internationalization (i18n)**

- **Configuring i18n**:
  ```js
  module.exports = {
    i18n: {
      locales: ['en', 'fr', 'de'],
      defaultLocale: 'en',
    },
  };
  ```

- **Usage in Components**:
  ```js
  import { useRouter } from 'next/router';

  const Home = () => {
    const { locale, locales, defaultLocale } = useRouter();
    return <p>Current Locale: {locale}</p>;
  };
  ```

---

## **10. Custom Configuration**

- **Next.js Config (`next.config.js`)**:
  ```js
  module.exports = {
    reactStrictMode: true,
    env: {
      CUSTOM_VAR: 'my-custom-var',
    },
  };
  ```

- **Custom Webpack Config**:
  ```js
  module.exports = {
    webpack(config, { isServer }) {
      if (!isServer) {
        config.resolve.fallback.fs = false;
      }
      return config;
    },
  };
  ```

---

## **11. Redirects & Rewrites**

- **Redirects**:
  ```js
  module.exports = {
    async redirects() {
      return [
        {
          source: '/old-route',
          destination: '/new-route',
          permanent: true,
        },
      ];
    },
  };
  ```

- **Rewrites**:
  ```js
  module.exports = {
    async rewrites() {
      return [
        {
          source: '/api/:path*',
          destination: 'https://external-api.com/:path*',
        },
      ];
    },
  };
  ```

---

## **12. Middlewares**

- **Next.js Middleware**: Run logic before a request is completed.
  ```js
  // middleware.js
  import { NextResponse } from 'next/server';

  export function middleware(request) {
    return NextResponse.redirect(new URL('/about', request.url));
  }
  ```

---

## **13. Deployment**

- **Vercel**:
  - Next.js is optimized for Vercel. Push to GitHub and deploy it seamlessly.

- **Self-hosting**:
  - Run Next.js with custom servers (e.g., Express or Node.js).
  ```bash
  npm run build
  npm run start
  ```

---

## **14. Next.js with TypeScript**

- **Setting Up TypeScript**:
  ```bash
  touch tsconfig.json
  npm install --save-dev typescript @types/react @types/node
  ```

- **Using TypeScript in Pages**:
  ```tsx
  // pages/index.tsx
  import { NextPage } from 'next';

  const Home: NextPage = () => {
    return <h1>Hello TypeScript!</h1>;
  };

  export default Home;
  ```

---
