Next.js is a React framework. It builds up on React.js NextJS is a fullstack React framework. NextJS simplifies the process of building fullstack applications with React. Next.js handles route setup and config. Handles requests and responses . handles data fetching and submission.
Routes are configured via the filesystem (folders + files)

2 approaches for router Pages Router and  App Router. Any approach for routes where the pages are rendered to the server and use the file system to set the routes

Pages Router
Has been used for many years, very stable, used in many existing NextJS projects, allows you to build feature-rich fullstack apps with React.

App Router
Introduced with NextJS 13. Marked as stable but still relatively new and partially buggy. Supports modern Next and React features (fullstack React Apps)



React Server components

Rendered only on the server never on the client.


Next.js relies on reserved, special filenames. But the filenames only matter inside the 'app' folder. Root 'page.js' in 'app' folder is responsible for the home page (starting page). If there is a folder called 'about' and 'about' has 'page.js' then 'page.js' responsible for rendering the content in 'about' route. When we enter 'http://localhost:3000/about' then the server will download the page in 'about' route and sent to the client. But if there is a ' <a href="/about">About</a>' in root 'page.js' then the about page is rendered from client side javascript code staying in single page application. To maintain in single page application we need to use instead on anchor tag with 'Link' ( <Link href="/about">About</Link> )

page.js => Create a new page (e.g., app/about/page.js creates a <your-domain>/about page)

layout.js => Create a new layout that wraps sibling and nested pages

not-found.js => Fallback page for "Not Found" errors (thrown by sibling or nested pages or layouts)

error.js => Fallback page for other errors (thrown by sibling pages or nested pages or layouts)

loading.js => Fallback page which is shown whilst sibling or nested pages (or layouts) are fetching data

route.js => Allows you to create an API route (i.e., a page which does NOT return JSX code but instead data, e.g., in the JSON format)


Server and Client side working together

The content in the Next.js will be pre-rendered in the server, but will be updated in the client using client side javascript code.

server-side the backend executes the server component functions and hence derives the to be rendered HTML code send to client where in the client side receives and renders the to be rendered HTML code.


'page.js' defines the content of the page while 'layout.js' provides a shell around one or more pages. Project needs atleast one root 'layout.js' file at the just beneath 'app' folder. We can have also nested 'layout.js' file (like in about folder it can have both 'page.js'& 'layout.js' (and this 'layout.js' is responsible for pages in about folder ) ) 'layout.js' is a wrapper around 'page.js'.

layout.js

import "./globals.css";

export const metadata = {
  title: "NextJS Course App",
  description: "Your first NextJS app!",
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}

Here 'children' will be the content of the page (here page.js) placed between the body tag. Here we can see html and body tag which we dont normally see in React. But in Next.js html and body tag is necessary  in the RootLayout to provide the HTML skeleton.
In the above example we have this piece of code in 'layout.js'

export const metadata = {
  title: "NextJS Course App",
  description: "Your first NextJS app!",
};

here 'metadata' is reserved keyword used instead of 'head' in HTML where the 'title' and 'description' can be set in this const 'metadata'. where 'metadata' will covered by all the pages for the layout provided (here RootLayout )

'global.css' need to be imported in 'layout.js' (here root 'layout.js')  then only 'global.css' will have all css in other pages

If there is a 'blog' folder and it has 2 folders 'post-1' and 'post-2' with each having 'page.js' and route for each page is not scalable, so a common route can be provided with the concept of 'dynamic route'. 
Dynamic route folder syntax is :- [identifier-name] 
eg: [slug]
inside [slug] folder we add a 'page.js'


React.js is a pure client-side library running on the browser on client.
But Next.js is a Fullstack application. It has a backend (server side) and frontend (client side). Therefore in Next.js code gets executed in server side.
By default all the react components, pages and layouts in Next.js projects are rendered only in the server hence called as React Server components. This is an advantage of Next.js it has less client-side JS that must be downloaded which indeed improves the performance of the application.

Client components - components that are pre-rendered on the server but then also potentially on the client. These are components that need to be rendered on the client because that have code or have some features that are only available on the client. example : useState variables and useEffect which are available only in client side as shown in the below example (because 'interval' need to be set in client not in server), even the event listener are client side because user on clicks an action takes place and those action is written in the client side code. But since all components are server side components we need to explicitly tell its a client component by using special directive like 'use client' (above the components). 

const [currentImageIndex, setCurrentImageIndex] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setCurrentImageIndex((prevIndex) =>
        prevIndex < images.length - 1 ? prevIndex + 1 : 0
      );
    }, 5000);

    return () => clearInterval(interval);
  }, []);
 



Client side data fetching from api

 "use client";

import NewsList from "@/components/news-list";
 import { useEffect, useState } from "react";

export default function News() {
   const [error, setError] = useState();
   const [isLoading, setIsLoading] = useState(false);
   const [news, setNews] = useState();


  useEffect(function () {
     async function fetchNews() {
       setIsLoading(true);
       const response = await fetch("http://localhost:8080/news");
       if (!response.ok) {
         setError("Failed to fetch the news api");
         setIsLoading(false);
       }
       const news = await response.json();
       setIsLoading(false);
       setNews(news);
     }

     fetchNews();
   }, []);

   if (isLoading) {
     return <p>Is Loading...</p>;
   }
   if (error) {
     return <p>{error}</p>;
   }
 let newsContent;
  if (news) {
    newsContent = <NewsList news={news} />;
  }
  return (
    <>
      <h1>News Page</h1>
      {newsContent}
    </>
  );
}




Server side data fetching from api




import NewsList from "@/components/news-list";
export default async function News() {
  //server side fetching of data from api
  const response = await fetch("http://localhost:8080/news");
  const news = await response.json();
  let newsContent;

  return (
    <>
      <h1>News Page</h1>
      <NewsList news={news} />
    </>
  );
}




Without fetch we can get values from db

by placing db in the root
run command :  npm install better-sqlite3
(In this case)
then in the lib folder in news.js
Without fetch we can get values from db only in server side components

import sql from "better-sqlite3";

const db = sql("data.db");

export default function getAllNews() {
  const news = db.prepare("SELECT * FROM news").all();
  return news;
}


then in news/page.js

import NewsList from "@/components/news-list";
import getAllNews from "@/lib/news";
export default async function News() {
  const news = getAllNews();

  return (
    <>
      <h1>News Page</h1>
      <NewsList news={news} />
    </>
  );
}


Suspense component

Suspense component tells Next.js in detail for which kind of data we want to wait and under which circumstance we should show the loading page. Using <Suspense> tag

import NewsList from "@/components/news-list";
import Link from "next/link";
import {
  getAvailableNewsMonths,
  getAvailableNewsYears,
  getNewsForYear,
  getNewsForYearAndMonth,
} from "@/lib/news";
import { Suspense } from "react";

async function FilterHeader({ year, month }) {
  const avaialbleYears = await getAvailableNewsYears();
  let links = avaialbleYears;
  if (
    (year && !avaialbleYears.includes(year)) ||
    (month && !getAvailableNewsMonths(year).includes(month))
  ) {
    throw new Error("invalid filter");
  }
  if (year && !month) {
    links = getAvailableNewsMonths(year);
  }
  if (year && month) {
    links = [];
  }
  return (
    <header id="archive-header">
      <ul>
        {links.map((link) => {
          const href = year ? `/archive/${year}/${link}` : `/archive/${link}`;
          return (
            <li key={link}>
              <Link href={href}>{link}</Link>
            </li>
          );
        })}
      </ul>
    </header>
  );
}

async function FilteredNews({ year, month }) {
  let news;
  if (year && !month) {
    news = await getNewsForYear(year);
  } else if (year && month) {
    news = await getNewsForYearAndMonth(year, month);
  }
  let newsContent = <p>No news found for the selected period.</p>;
  if (news && news.length > 0) {
    newsContent = <NewsList news={news} />;
  }
  return newsContent;
}
export default async function FilteredNewsPage({ params }) {
  const filter = params.filter;
  const selectedYear = filter?.[0];
  const selectedMonth = filter?.[1];

  return (
    <>
      <Suspense fallback={<p>Loading news...</p>}>
        <FilterHeader year={selectedYear} month={selectedMonth} />

        <FilteredNews year={selectedYear} month={selectedMonth} />
      </Suspense>
    </>
  );
}




Data Mutation

How you can change data, store new data. Manipulate and update data using Next.js

Server Actions 

example of server actions

 async function createPost(formData) {
    "use server";
    const title = formData.get("title");
    const image = formData.get("image");
    const content = formData.get("content");
  }


Full example

export default function NewPostPage() {
  async function createPost(formData) {
    "use server";
    const title = formData.get("title");
    const image = formData.get("image");
    const content = formData.get("content");
  }
  return (
    <>
      <h1>Create a new post</h1>
      <form action={createPost}>
        <p className="form-control">
          <label htmlFor="title">Title</label>
          <input type="text" id="title" name="title" />
        </p>
        <p className="form-control">
          <label htmlFor="image">Image URL</label>
          <input
            type="file"
            accept="image/png, image/jpeg"
            id="image"
            name="image"
          />
        </p>
        <p className="form-control">
          <label htmlFor="content">Content</label>
          <textarea id="content" name="content" rows="5" />
        </p>
        <p className="form-actions">
          <button type="reset">Reset</button>
          <button>Create Post</button>
        </p>
      </form>
    </>
  );
}




useFormState

import { useFormState } from "react-dom";
  const [state, formAction] = useFormState(action, {});

state is the initial state or form state or 'error' state (used in new-post -> page.js).
formAction is the updated form's action, so for form's action we give formAction as the value

<form action={formAction}>
</form>

Full code where useFormState is used

"use client";
import FormSubmit from "./form-submit";
import { useFormState } from "react-dom";
export default function PostForm({ action }) {
  const [state, formAction] = useFormState(action, {});
  return (
    <>
      <h1>Create a new post</h1>
      <form action={formAction}>
        <p className="form-control">
          <label htmlFor="title">Title</label>
          <input type="text" id="title" name="title" required />
        </p>
        <p className="form-control">
          <label htmlFor="image">Image URL</label>
          <input
            type="file"
            accept="image/png, image/jpeg"
            id="image"
            name="image"
            required
          />
        </p>
        <p className="form-control">
          <label htmlFor="content">Content</label>
          <textarea id="content" name="content" rows="5" required />
        </p>
        <p className="form-actions">
          <FormSubmit />
        </p>
      </form>
    </>
  );
}



page.js in new-post where we are using 'PostForm'


import PostForm from "@/components/post-form";
import { storePost } from "@/lib/posts";
import { redirect } from "next/navigation";

export default function NewPostPage() {
  async function createPost(prevState, formData) {
    "use server";
    const title = formData.get("title");
    const image = formData.get("image");
    const content = formData.get("content");

    let errors = [];

    if (!title || title.trim().length === 0) {
      errors.push("Title is required");
    }
    if (!content || content.trim().length === 0) {
      errors.push("Content is required");
    }
    if (!image) {
      errors.push("Image is required");
    }
    if (errors.length > 0) {
      return { errors };
    }
    await storePost({
      imageUrl: "",
      title: title,
      content: content,
      userId: 1,
    });
    redirect("/feed");
  }
  return <PostForm action={createPost} />;
}



Next.js caches data pretty aggressively

revalidatePath() used to update the change in data in screen.
revalidatePath() will tell to update the screen by telling Next.js not to show the cache data in the screen. The argument passed into revalidatePath() is the path where the change has to be reflected.

Example

export async function togglePostLikeStatus(postId) {
  await updatePostLikeStatus(postId, 2);
  revalidatePath('/feed');
}


Here revalidatePath('/feed') will update the data change in the screen where path is 'feed'.


In the below example

export async function togglePostLikeStatus(postId) {
  await updatePostLikeStatus(postId, 2);
  revalidatePath("/", "layout");
}

Here revalidatePath("/", "layout") will update all root path where changes has been reflected.





useOptimistic

The useOptimistic hook in React, compatible with Next.js, enables the implementation of "optimistic UI updates." This means the user interface can be updated immediately with the expected result of an asynchronous action, such as a form submission or a network request, without waiting for the server's response. This improves the perceived responsiveness and user experience of web applications.

First argument for useOptimistic is the data where we want to start in the below example it is 'posts', second argument passed to useOptimistic is a call back function which will tell to update the 'posts' data optimisticly. the callback function is the second argument will update the data 'posts' in client side until the 'posts' data will be updated in the server side. Which means update the client data and then sync with the server data after server data is updated. The callback function which invokes has 2 arguments where the first argument is old 'posts' data and the second argument is the data that helps to perform the update here its the updated posts id, so 'updatePostId'

 const [optimisticPosts, updateOptimisticPosts] = useOptimistic(posts, (prevPosts, updatePostId) => {});

where 'optimisticPosts' is the updated posts array
where 'updateOptimisticPosts' is triggers the call back function of 'useOptimistic' (i.e  (prevPosts, updatePostId) => {} )


Example

components/post.js

export default function Post({ post, action }) {
  return (
    <article className="post">
      <div className="post-image">
        <img src={post.image} alt={post.title} />
      </div>
      <div className="post-content">
        <header>
          <div>
            <h2>{post.title}</h2>
            <p>
              Shared by {post.userFirstName} on{" "}
              <time dateTime={post.createdAt}>
                {formatDate(post.createdAt)}
              </time>
            </p>
          </div>
          <div>
            <form
              action={action.bind(null, post.id)}
              className={post.isLiked ? "liked" : ""}
            >
              <LikeButton />
            </form>
          </div>
        </header>
        <p>{post.content}</p>
      </div>
    </article>
  );
}


components/posts.js

export default function Posts({ posts }) {
 const [optimisticPosts, updateOptimisticPosts] = useOptimistic(
    posts,
    (prevPosts, updatePostId) => {
      const updatedPostIndex = prevPosts.findIndex(
        (p) => p.id === updatePostId
      );

 if (updatedPostIndex === -1) {
        return prevPosts;
      }

 const updatedPost = { ...prevPosts[updatedPostIndex]}

 updatedPost.likes = updatedPost.likes + (updatedPost.isLiked ? -1 : 1);

 updatedPost.isLiked = !updatedPost.isLiked;

    const newPosts = [...prevPosts];
      newPosts[updatedPostIndex] = updatedPost;

   return newPosts;
    }
    }
  );

  if (!optimisticPosts || optimisticPosts.length === 0) {
    return <p>There are no posts yet. Maybe start sharing some?</p>;
  }

 async function updatePost(postId) {
    updateOptimisticPosts(postId);
    await togglePostLikeStatus(postId);
  }

  return (
    <ul className="posts">
      {optimisticPosts.map((post) => (
        <li key={post.id}>
          <Post post={post} action ={updatePost} />
        </li>
      ))}
    </ul>
  );
}



'updatedPostIndex' the updated post index is available in this by the following code. 'prevPosts' is the previous post array. If the updated post index doesn't exist (i.e 'updatedPostIndex === -1') then return the 'prevPosts'. Get the 'updatedPost' by passing to the 'prevPosts' the 'updatedPostIndex' (i.e prevPosts[updatedPostIndex] )

const [optimisticPosts, updateOptimisticPosts] = useOptimistic(
    post,
    (prevPosts, updatePostId) => {
      const updatedPostIndex = prevPosts.findIndex(
        (p) => p.id === updatePostId
      );

 if (updatedPostIndex === -1) {
        return prevPosts;
      }

 const updatedPost = { ...prevPosts[updatedPostIndex]}

 updatedPost.likes = updatedPost.likes + (updatedPost.isLiked ? -1 : 1);

 updatedPost.isLiked = !updatedPost.isLiked;

    const newPosts = [...prevPosts];
      newPosts[updatedPostIndex] = updatedPost;

   return newPosts;
    }
    }
  );


If 'updatedPost.isLiked' was already selected then we need to subtract the likes otherwise add the likes by this code 'updatedPost.likes = updatedPost.likes + (updatedPost.isLiked ? -1 : 1)
'newPosts' is initially set to prevPosts array then for the 'updatedPostIndex' set to the 'updatedPost'. And return 'newPosts' array. React will make the 'newPosts' array available to 'optimisticPosts' in the 'useOptimistic'.


'updateOptimisticPosts' is called in the function 'updatePost' and pass 'action ={updatePost}' in Post.





