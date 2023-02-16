# Next.js tutorial handson

# インストール ＆ 起動

何はともあれインストール ＆ 起動です。  
Node.js バージョン10.13以降が必要ですので、ご注意ください。

```console
$ npx create-next-app@latest nextjs-blog --use-npm --example "https://github.com/vercel/next-learn/tree/master/basics/learn-starter"

$ cd nextjs-blog

$ npm run dev
```

上記を実行したら、下記 URL にアクセスしてみましょう。

[http://localhost:3000](http://localhost:3000)

# Fast Refresh を感じる

ファイルに変更を加えると、Next.js は自動的に変更をほぼ瞬時に適用します。体感してみましょう。

```javascript
// pages/index.js

<h1 className={styles.title}>
  Welcome to <a href="https://nextjs.org">Next.js!</a>
</h1>

// ↓

<h1 className={styles.title}>
  Learn <a href="https://nextjs.org">Next.js!</a>
</h1>Learn
```

# ファイルシステムに沿ったルーター を感じる

Next.js にはファイルシステムベースのルーターがあります。  
ファイルがpagesディレクトリに追加されると、ルートとして自動的に利用可能になります。  
新しいページを追加してみましょう。

```javascript
// pages/posts/first-post.js

export default function FirstPost() {
  return <h1>First Post</h1>
}
```

上記を実行したら、下記 URL にアクセスしてみましょう。

[http://localhost:3000/posts/first-post](http://localhost:3000/posts/first-post)

# Link によるページ遷移を感じる

next/Link は、同じ Next.js アプリ内の 2 つのページ間のクライアント側のナビゲーションを有効にします。実装してみましょう。

```javascript
// pages/index.js

import Link from 'next/link'
```
```javascript
// pages/index.js

<h1 className="title">
  Welcome to <a href="https://nextjs.org">Next.js!</a>
</h1>

// ↓

<h1 className="title">
  Read <Link href="/posts/first-post">this page!</Link>
</h1>
<p><a href="/posts/first-post">a tag</a></p>
```
```javascript
// pages/posts/first-post.js

import Link from 'next/link';

export default function FirstPost() {
  return (
    <>
      <h1>First Post</h1>
      <h2>
        <Link href="/">Back to home</Link>
      </h2>
    </>
  );
}
```

# Head を感じる

メタデータの変更には next/head を使用します。実装してみましょう。

```javascript
// pages/posts/first-post.js

import Head from 'next/head';
import Link from 'next/link';

export default function FirstPost() {
  return (
    <>
      <Head>
        <title>First Post</title>
      </Head>
      <h1>First Post</h1>
      <h2>
        <Link href="/">← Back to home</Link>
      </h2>
    </>
  );
}
```

# Document を感じる

Document を変更する場合は `pages/_document.js` を作成する必要があります。実装してみましょう。

```javascript
// pages/_document.js

import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html lang="en">
      <Head />
      <body className="bg-white">
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

# Script を感じる

サードパーティの JavaScript を追加する場合は next/script を使用する方がベターです。

```javascript
// pages/posts/first-post.js

import Script from 'next/script';
```
```javascript
// pages/posts/first-post.js

<Script
  src="https://connect.facebook.net/en_US/sdk.js"
  strategy="lazyOnload"
  onLoad={() =>
    console.log(`script loaded correctly, window.FB has been populated`)
  }
/>
```

# Style を感じる

せっかくなので使用率世界 1 位の styled-components で Style をつけていきましょう。

```console
$ npm install --save styled-components
$ npm install --save babel-plugin-styled-components
```

```javascript
// /.babelrc

{
  "presets": ["next/babel"],
  "plugins": [["styled-components", { "ssr": true }]]
}
```

```javascript
// pages/posts/first-post.js

import Head from 'next/head';
import Link from 'next/link';
import styled from 'styled-components';

const PageTitle = styled.h1`
  color: blue;
  font-size: 50px;
`

const RedLink = styled.p`
  color: red;
`;

export default function FirstPost() {
  return (
    <>
      <Head>
        <title>First Post</title>
      </Head>
      <PageTitle>First Post</PageTitle>
      <h2>
        <Link href="/">
          <RedLink>投稿リストページへ遷移します</RedLink>
        </Link>
      </h2>
    </>
  );
}
```

# プリレンダリングを知る

```
By default, Next.js pre-renders every page. This means that Next.js generates HTML for each page in advance, instead of having it all done by client-side JavaScript. Pre-rendering can result in better performance and SEO.

デフォルトでは、Next.jsはすべてのページをプリレンダリングします。これは、Next.jsが各ページのHTMLを、クライアントサイドのJavaScriptですべて行うのではなく、あらかじめ生成しておくことを意味します。プリレンダリングは、パフォーマンスとSEOの向上につながります。
```
```
Each generated HTML is associated with minimal JavaScript code necessary for that page. When a page is loaded by the browser, its JavaScript code runs and makes the page fully interactive. (This process is called hydration.)

生成された各HTMLは、そのページに必要な最小限のJavaScriptコードと関連付けられています。ブラウザがページを読み込むと、そのJavaScriptコードが実行され、ページが完全にインタラクティブになります。(このプロセスをハイドレーションと呼びます）。
```

基本的に Next.js は静的生成（SG）ですが、ページ単位でサーバー側レンダリング（SSR）を選択する事も可能です。

- 静的生成は、ビルド時に HTML を生成する事前レンダリング方法です。その後、事前にレンダリングされた HTML が各リクエストで再利用されます。
- サーバーサイド レンダリングは、リクエストごとに HTML を生成する事前レンダリング方法です。

`ユーザーのリクエストに先立って、このページを事前にレンダリングできますか?` と自問する必要があります。答えが「はい」の場合は SG を選択する必要があります。

# SG を感じる

実際に SG を実装してみましょう。例としてブログを作成してみましょう。

```console
$ npm install --save gray-matter
```

```text
// posts/pre-rendering.md

---
title: 'Two Forms of Pre-rendering'
date: '2020-01-01'
---

Next.js has two forms of pre-rendering: **Static Generation** and **Server-side Rendering**. The difference is in **when** it generates the HTML for a page.

- **Static Generation** is the pre-rendering method that generates the HTML at **build time**. The pre-rendered HTML is then _reused_ on each request.
- **Server-side Rendering** is the pre-rendering method that generates the HTML on **each request**.

Importantly, Next.js lets you **choose** which pre-rendering form to use for each page. You can create a "hybrid" Next.js app by using Static Generation for most pages and using Server-side Rendering for others.
```

```text
// posts/ssg-ssr.md

---
title: 'When to Use Static Generation v.s. Server-side Rendering'
date: '2020-01-02'
---

We recommend using **Static Generation** (with and without data) whenever possible because your page can be built once and served by CDN, which makes it much faster than having a server render the page on every request.

You can use Static Generation for many types of pages, including:

- Marketing pages
- Blog posts
- E-commerce product listings
- Help and documentation

You should ask yourself: "Can I pre-render this page **ahead** of a user's request?" If the answer is yes, then you should choose Static Generation.

On the other hand, Static Generation is **not** a good idea if you cannot pre-render a page ahead of a user's request. Maybe your page shows frequently updated data, and the page content changes on every request.

In that case, you can use **Server-Side Rendering**. It will be slower, but the pre-rendered page will always be up-to-date. Or you can skip pre-rendering and use client-side JavaScript to populate data.
```

```javascript
// lib/posts.js

import fs from 'fs';
import path from 'path';
import matter from 'gray-matter';

const postsDirectory = path.join(process.cwd(), 'posts');

export function getSortedPostsData() {
  // Get file names under /posts
  const fileNames = fs.readdirSync(postsDirectory);
  const allPostsData = fileNames.map((fileName) => {
    // Remove ".md" from file name to get id
    const id = fileName.replace(/\.md$/, '');

    // Read markdown file as string
    const fullPath = path.join(postsDirectory, fileName);
    const fileContents = fs.readFileSync(fullPath, 'utf8');

    // Use gray-matter to parse the post metadata section
    const matterResult = matter(fileContents);

    // Combine the data with the id
    return {
      id,
      ...matterResult.data,
    };
  });
  // Sort posts by date
  return allPostsData.sort((a, b) => {
    if (a.date < b.date) {
      return 1;
    } else {
      return -1;
    }
  });
}
```

```javascript
// pages/index.js

import { getSortedPostsData } from '../lib/posts';

export async function getStaticProps() {
  const allPostsData = getSortedPostsData();
  return {
    props: {
      allPostsData,
    },
  };
}
```

```javascript
// pages/index.js

export default function Home ({ allPostsData }) { ... }
```

```javascript
// pages/index.js

<ul>
  {allPostsData.map(({ id, date, title }) => (
    <li key={id}>
      {title}
      <br />
      {id}
      <br />
      {date}
    </li>
  ))}
</ul>
```

# SSR を感じる

```text
getStaticProps() → getServerSideProps()
```

# Dynamic Routes を感じる

ファイルシステムベースのルーティングで動的な URL を構築する場合 Dynamic Routes を使用します。上記のブログの記事ページを実装してみましょう。

```console
$ npm install --save remark remark-html
```

```javascript
// lib/posts.js

import { remark } from 'remark';
import html from 'remark-html';
import fs from 'fs'
import path from 'path'
import matter from 'gray-matter'

const postsDirectory = path.join(process.cwd(), 'posts')

export function getSortedPostsData() {
  // Get file names under /posts
  const fileNames = fs.readdirSync(postsDirectory)
  const allPostsData = fileNames.map(fileName => {
    // Remove ".md" from file name to get id
    const id = fileName.replace(/\.md$/, '')

    // Read markdown file as string
    const fullPath = path.join(postsDirectory, fileName)
    const fileContents = fs.readFileSync(fullPath, 'utf8')

    // Use gray-matter to parse the post metadata section
    const matterResult = matter(fileContents)

    // Combine the data with the id
    return {
      id,
      ...matterResult.data
    }
  })
  // Sort posts by date
  return allPostsData.sort((a, b) => {
    if (a.date < b.date) {
      return 1
    } else {
      return -1
    }
  })
}

export function getAllPostIds() {
  const fileNames = fs.readdirSync(postsDirectory)
  return fileNames.map(fileName => {
    return {
      params: {
        id: fileName.replace(/\.md$/, '')
      }
    }
  })
}

export async function getPostData(id) {
  const fullPath = path.join(postsDirectory, `${id}.md`);
  const fileContents = fs.readFileSync(fullPath, 'utf8');

  // Use gray-matter to parse the post metadata section
  const matterResult = matter(fileContents);

  // Use remark to convert markdown into HTML string
  const processedContent = await remark()
    .use(html)
    .process(matterResult.content);
  const contentHtml = processedContent.toString();

  // Combine the data with the id and contentHtml
  return {
    id,
    contentHtml,
    ...matterResult.data,
  };
}
```

``` javascript
// pages/posts/[id].js

import { getAllPostIds, getPostData } from '../../lib/posts'

export async function getStaticPaths() {
  const paths = getAllPostIds()
  return {
    paths,
    fallback: false
  }
}

export async function getStaticProps({ params }) {
  const postData = await getPostData(params.id)
  return {
    props: {
      postData
    }
  }
}

export default function Post({ postData }) {
  return (
    <>
      {postData.title}
      <br />
      {postData.id}
      <br />
      {postData.date}
      <br />
      <div dangerouslySetInnerHTML={{ __html: postData.contentHtml }} />
    </>
  )
}
```

# Dynamic Routes を感じる Step 2

```javascript
// pages/index.js

<ul>
  {allPostsData.map(({ id, date, title }) => (
    <li key={id}>
      <Link href={`/posts/${id}`}>{title}</Link>
      <br />
      {date}
    </li>
  ))}
</ul>
```

```javascript
// pages/posts/[id].js

import Head from 'next/head';
```

```javascript
// pages/posts/[id].js

<Head>
  <title>{postData.title}</title>
</Head>
```