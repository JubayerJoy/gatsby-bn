---
title: ট্রান্সফর্মার প্লাগিনসমূহ
typora-copy-images-to: ./
disableTableOfContents: true
---

> এই টিউটোরিয়ালটি Gatsby এর ডাটা লেয়ার সম্পর্কিত সিরিজের অংশ। [পার্ট ৪](/tutorial/part-four/) এবং [পার্ট ৫](/tutorial/part-five/) সম্পন্ন করেছেন কিনা নিশ্চিত হয়ে এটি শুরু করুন।

## কি আছে এই টিউটোরিয়ালে?

এর আগের টিউটোরিয়ালে দেখানো হয়েছে কিভাবে সোর্স প্লাগিন সমূহ ডেটা Gatsby এর ডেটা সিস্টেমে _নিয়ে আসে_। এই টিউটোরিয়ালে আপনি শিখবেন, কিভাবে সোর্স প্লাগিন সমূহের নিয়ে আসা raw ডেটা ট্রান্সফর্মার প্লাগিন সমূহ _পরিবর্তন_ করে। Gatsby সাইট তৈরির সময় সোর্স প্লাগিন এবং ট্রান্সফর্মার প্লাগিনের সমন্বয় করে প্রয়োজনীয় সকল ডেটা সোর্সিং এবং ডেটা পরিবর্তন নিয়ন্ত্রণ করতে পারবেন।

## ট্রান্সফর্মার প্লাগিনসমূহ

প্রায়শই, আপনার সাইট তৈরির সময় সোর্স প্লাগিনের মাধ্যমে প্রাপ্ত ডেটার ফরমেট দরকার
মতো হয় না। ফাইলসিস্টেম সোর্স প্লাগিন আপনাকে ফাইলের _সম্পর্কে_ ডেটা query করতে
দেয় কিন্তু আপনি যদি ফাইলের _অভ্যন্তরীণ_ ডেটা query করতে চান, সেটা কিভাবে সম্ভব?

এটা সম্ভব করতে, Gatsby ট্রান্সফর্মার প্লাগিন সাপোর্ট করে, যা সোর্স প্লাগিনসমূহের raw কন্টেট
গ্রহণ করে সেটা _পরিবর্তন_ করে অধিক ব্যবহারযোগ্য করে তুলে।

উদাহরণস্বরূপ, মার্কডাউন ফাইলকেই ধরি। মার্কডাউন ফরমেটে লিখতে অনেক সুবিধা হয়,
কিন্ত সাইট তৈরির সময় মার্কডাউনটি এইচটিএমএল (HTML) ফরমেটে থাকতে হয়।

আপনার সাইটে `src/pages/sweet-pandas-eating-sweets.md` 
এরকম একটা মার্কডাউন যোগ করুন (এটা আপনার প্রথম মার্কডাউন ব্লগপোস্ট হবে) এবং 
কিভাবে এটাকে ট্রান্সফর্মার প্লাগিন আর GraphQL ব্যবহার করে HTML ফরমেটে _পরিবর্তন_ করবেন
তা শিখুন।

```markdown:title=src/pages/sweet-pandas-eating-sweets.md
---
title: "Sweet Pandas Eating Sweets"
date: "2017-08-10"
---

Pandas are really sweet.

Here's a video of a panda eating sweets.

<iframe width="560" height="315" src="https://www.youtube.com/embed/4n0xNbfJLR8" frameborder="0" allowfullscreen></iframe>
```

একবার ফাইলটি সেইভ করবার পরে,  `/my-files/` এ আবার দেখুন- নতুন মার্কডাউন ফাইল টেবিলে
যুক্ত হয়েছে। এটা  Gatsby এর খুবই শক্তিশালী একটি ফিচার। আগের উদাহরণের `siteMetadata`
মতো, সোর্স প্লাগিন ডেটা লাইভ-রিলোড করতে পারে।
`gatsby-source-filesystem` সর্বদা নতুন ফাইল যুক্ত করার জন্য খোজ করতে থাকে এবং
পেয়ে গেলে আপনার query গুলো পুনরায় চালায়।

মার্কডাউন ফাইল সমূহ পরিবর্তন করতে পারে এমন একটা ট্রান্সফর্মার প্লাগিন যুক্ত করুন:

```shell
npm install --save gatsby-transformer-remark
```

এরপর এটাকে নরমালের মতো `gatsby-config.js` এ যুক্ত করুন:

```javascript:title=gatsby-config.js
module.exports = {
  siteMetadata: {
    title: `Pandas Eating Lots`,
  },
  plugins: [
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `src`,
        path: `${__dirname}/src/`,
      },
    },
    `gatsby-transformer-remark`, // highlight-line
    `gatsby-plugin-emotion`,
    {
      resolve: `gatsby-plugin-typography`,
      options: {
        pathToConfigModule: `src/utils/typography`,
      },
    },
  ],
}
```

ডেভেলপমেন্ট সার্ভার রিস্টার্ট করে GraphiQL রিফ্রেস করুন (অথবা আবার খুলুন) এবং 
autocomplete টা দেখুন:

![markdown-autocomplete](markdown-autocomplete.png)

আবার `allMarkdownRemark` সিলেক্ট করুন এবং এটাকে `allFile` যেভাবে চালিয়েছিলেন
সেভাবে চালান। আপনি সেখানে নতুন যুক্ত করা মার্কডাউন ফাইল দেখতে পাবেন।
`MarkdownRemark` নোড এর ফিল্ড সমূহ ঘুরে দেখুন।

![markdown-query](markdown-query.png)

আচ্ছা! আশা করি, কিছু বেসিক ধারণা পাচ্ছেন। Gatsby এর ডাটা সিস্টেমে
সোর্স প্লাগিনসমূহ ডাটা _নিয়ে আসে_ এবং _ট্রান্সফর্মার_ প্লাগিনসমূহ সোর্স প্লাগিনের
আনা raw কন্টেন্ট  পরিবর্তন করে। এইভাবে আপনার সাইটের প্রয়োজনীয় সকল
সোর্সিং এবং ট্রান্সফর্মেশন পরিচালনা করতে পারবেন।

## Create a list of your site's markdown files in `src/pages/index.js`

Now you'll have to create a list of your markdown files on the front page. Like many
blogs, you want to end up with a list of links on the front page pointing to each
blog post. With GraphQL you can _query_ for the current list of markdown blog
posts so you won't need to maintain the list manually.

Like with the `src/pages/my-files.js` page, replace `src/pages/index.js` with
the following to add a GraphQL query with some initial HTML and styling.

```jsx:title=src/pages/index.js
import React from "react"
import { graphql } from "gatsby"
import { css } from "@emotion/core"
import { rhythm } from "../utils/typography"
import Layout from "../components/layout"

export default ({ data }) => {
  console.log(data)
  return (
    <Layout>
      <div>
        <h1
          css={css`
            display: inline-block;
            border-bottom: 1px solid;
          `}
        >
          Amazing Pandas Eating Things
        </h1>
        <h4>{data.allMarkdownRemark.totalCount} Posts</h4>
        {data.allMarkdownRemark.edges.map(({ node }) => (
          <div key={node.id}>
            <h3
              css={css`
                margin-bottom: ${rhythm(1 / 4)};
              `}
            >
              {node.frontmatter.title}{" "}
              <span
                css={css`
                  color: #bbb;
                `}
              >
                — {node.frontmatter.date}
              </span>
            </h3>
            <p>{node.excerpt}</p>
          </div>
        ))}
      </div>
    </Layout>
  )
}

export const query = graphql`
  query {
    allMarkdownRemark {
      totalCount
      edges {
        node {
          id
          frontmatter {
            title
            date(formatString: "DD MMMM, YYYY")
          }
          excerpt
        }
      }
    }
  }
`
```

Now the frontpage should look like:

![frontpage](frontpage.png)

But your one blog post looks a bit lonely. So let's add another one at
`src/pages/pandas-and-bananas.md`

```markdown:title=src/pages/pandas-and-bananas.md
---
title: "Pandas and Bananas"
date: "2017-08-21"
---

Do Pandas eat bananas? Check out this short video that shows that yes! pandas do
seem to really enjoy bananas!

<iframe width="560" height="315" src="https://www.youtube.com/embed/4SZl1r2O_bY" frameborder="0" allowfullscreen></iframe>
```

![two-posts](two-posts.png)

Which looks great! Except… the order of the posts is wrong.

But this is easy to fix. When querying a connection of some type, you can pass a
variety of arguments to the GraphQL query. You can `sort` and `filter` nodes, set how
many nodes to `skip`, and choose the `limit` of how many nodes to retrieve. With
this powerful set of operators, you can select any data you want—in the format you
need.

In your index page's GraphQL query, change `allMarkdownRemark` to
`allMarkdownRemark(sort: { fields: [frontmatter___date], order: DESC })`. _Note: There are 3 underscores between `frontmatter` and `date`._ Save
this and the sort order should be fixed.

Try opening GraphiQL and playing with different sort options. You can sort the
`allFile` connection along with other connections.

For more documentation on our query operators, explore our [GraphQL reference guide.](/docs/graphql-reference/)

## Challenge

Try creating a new page containing a blog post and see what happens to the list of blog posts on the homepage!

## What's coming next?

This is great! You've just created a nice index page where you're querying your markdown
files and producing a list of blog post titles and excerpts. But you don't want to just see excerpts, you want actual pages for your markdown files.

You could continue to create pages by placing React components in `src/pages`. However, you'll
next learn how to _programmatically_ create pages from _data_. Gatsby is _not_
limited to making pages from files like many static site generators. Gatsby lets
you use GraphQL to query your _data_ and _map_ the query results to _pages_—all at build
time. This is a really powerful idea. You'll be exploring its implications and
ways to use it in the next tutorial, where you'll learn how to [programmatically create pages from data](/tutorial/part-seven/).
