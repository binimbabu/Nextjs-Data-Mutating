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

export default function Posts({ posts }) {
  const [optimisticPosts, updateOptimisticPosts] = useOptimistic(
    posts,
    (prevPosts, updatePostId) => {}
  );
  if (!optimisticPosts || optimisticPosts.length === 0) {
    return <p>There are no posts yet. Maybe start sharing some?</p>;
  }

  return (
    <ul className="posts">
      {optimisticPosts.map((post) => (
        <li key={post.id}>
          <Post post={post} />
        </li>
      ))}
    </ul>
  );
}














In 'app' folder we set different pages that we want on overall website. 'page.js' tells Nextjs that it should render a page ('page.js' a react function and 'page.js' is a server component, Nextjs ensures that 'page.js' is rendered on the server and executed on server, terminal in vscode is running on server) and 'layout.js'. Next.js has server components that are rendered and converted to HTML which is sent to browser. For example need to add a page called 'http://localhost:3000/about' we can do it in 'app' folder by adding the routes in 'app' folder by adding folders in 'app' folder. If we want to add 'http://localhost:3000/about' we need to add 'about' folder inside 'app' folder.  Additionally we need to create a file called 'page.js' inside 'about' folder to render a page.
Next.js have reserved filenames. But filename only matter inside app folder.

page.js - define page content
layout.js - define wrapper around pages
not-found.js - define 'Not found' fallback page 
error.js - define error fallback page
loading.js => Fallback page which is shown whilst sibling or nested pages (or layouts) are fetching data

route.js => Allows you to create an API route (i.e., a page which does NOT return JSX code but instead data, e.g., in the JSON format)

There is a root page inside 'app' folder which defines the content of page inside 'http://localhost:3000' and the file that defines content inside 'http://localhost:3000' is 'page.js' (inside app folder) and this 'page.js' is the root file inside app folder.

The 'page.js' inside the 'about' folder will determine that defines page content inside 'http://localhost:3000/about'.



app/about/page.js


export default function About() {
  return (
    <main >
     <p>About us</p>    
</main>
  );
}


app/page.js

import Link from 'next/link';

export default function Home() {
  return (
    <main >
     <p>About us</p>  
<p><Link to="/about>About Us</Link></p>  
</main>
  );
}


'Link' is used instead of 'a' anchor tag because when the home page is loaded and using anchor tag instead of  'Link' tag will reload and the single page application is not manitained, hence we use 'Link' which will load the content of 'http://localhost:3000/about' from Javascript client code from home page ('http://localhost:3000') will be replacing the UI to ('http://localhost:3000/about') without reloading and maintaining the single page application concept by using 'Link' instead of 'a' anchor tag. If we want to change the UI from one page to another page inside the website we use 'Link' in Nextjs. 

In 'app' folder 'layout.js' defines the shell around one or more pages . Especially 'app/page.js' place as a shell inside the 'app/layout.js'. Every Next.js application require one root 'layout.js' file that's 'app/layout.js'. In 'app/about' we can add 'layout.js' i.e 'app/about/layout.js'. In root 'layout.js' file (i.e app/layout.js) we are also exporting a React component 



app/layout.js


import './global.css';

export const metadata = {
title: 'NextJS course app',
description: 'Your first Nextjs app!'
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}


This 'app/layout.js' has a standard children prop (i.e 'children' ) which in React can be used by every component to inject some content between the body tags. 'RootLayout' react component renders an HTML and body tag (this is to set up HTML skeleton of the website). the 'head' tag element is placed in the 'metadata' variable. 'metadata' variable is also reserved name. 'RootLayout' isn't a reserved name. Here 'metadata' variable can have title, description and other metadata fields which is applied to all pages that are covered by the layout here inside 'app/layout.js'. All the content given in 'head' tag is set to 'metadata' variable. 'children' ( mentioned in 'app/layout.js' as props to  'RootLayout' component) denotes to the content of page that's currently active (because 'app/layout.js' is a wrapper around app/page.js or one or more pages and depending on which path you are children will be content of the page.js that's currently active). layout.js is the wrapper and page.js is the actual content that will be injected as 'children' props in the 'layout.js'.

Other reserved filenames:-

globals.css - some CSS styles and this file is imported into 'app/layout.js' and this 'globals.css' is available on every page that's being loaded.
icon.png - if we give a file named 'icon.png' in app then this image given as 'icon.png' will be set as image icon in the tab in browser. Because 'icon.png' given as favicon which is the icon to tab.

In 'app' folder we add folders to provide the routes.
If we want to add a new component inside another component say 'header.js'. ' 'header.js' can be included in 'app/page.js' by adding the 'header.js' inside a folder named 'components' (and the 'components' folder placed in the root next to 'app' folder) .

components/header.js

export default function Header(){
return 
<>
<img src="/logo.png" alt = "A logo" />
</>
}



app/page.js

import Link from 'next/link';
import Header from '../components/header.js';

export default function Home() {
  return (
    <main>
      <Header />  
      <p><Link to="/about>About Us</Link></p>  
   </main>
  );
}






If we want to add 'blog' folder inside app as route and inside blog route there should be post-1, post-2 so on. If 'app/blog' should be a route it should have individual page.js file (i.e blog/page.js). But there is a problem here there can be multiple posts inside 'blog' route which we don't know (which depends on dynamic data from the database like post-1, post-2 and so on). To overcome this problem we use dynamic route (i.e we define route once but is capable of rendering different pages for different blog posts). To create dynamic route we place an identifier/placeholder (name of any choice) enclosed in square bracket (example:- [slug]  ) . Like 'app/blog/[slug]'. the identifier or placeholder (here 'slug' ) and inside 'app/blog/[slug]' we need a file 'page.js' (like:-  app/blog/[slug]/page.js  ). Where this 'page.js' file will provide content to the dynamic route pages (here slug). There also a page.js file inside 'app/blog' folder (i.e app/blog/page.js  ).



 app/blog/page.js


import Link from 'next/link';

export default function BlogPage() {
  return (
<main>
 <h1>Blog</h1>
 <p>
  <Link href="/blog/post-1">Post 1 </Link>
 </p>
<p>
  <Link href="/blog/post-2">Post 2 </Link>
 </p>
</main>
);
}



 app/blog/[slug]/page.js


export default function BlogPostPage({ params }) {
  return (
<main>
 <h1>Blog Post</h1>
{params.slug}
</main>
);
}


When we type this in  URL 'http://localhost:3000/blog' (the file component 'BlogPage' is loaded) where you will get to Links To Post 1 and Post 2. On clicking on any of these links 'Post 1' or 'Post 2' the content in the file ' app/blog/[slug]/page.js' comes that's with a header named 'Blog Post' (where 'BlogPostPage' component is loaded). But the URL changes to 'http://localhost:3000/blog/post-1' if 'Post-1' link is clicked and the URL changes to 'http://localhost:3000/blog/post-2' if 'Post-2' link is clicked. '[slug]' (i.e placeholder enclosed in square bracket) denotes that after 'http://localhost:3000/blog' we need a dynamic route say 'post-1' or 'post-2' or so on which we don't know since the route is dynamic. Here 'slug' which is the placeholder/identifier which give access to concrete value that we get when route is loaded.
Here in 'BlogPostPage' gets a props called 'params' (which is set by Nextjs) , 'params' are not set manually since we don't have to pass it manually the 'BlogPostPage', instead Nextjs are setting up the props in 'BlogPostPage' (i.e params). 'params' is an object which comprises of placeholder (here 'slug' ) had in dynamic route will be a key and value stored under key will be a concrete value encoded in the URL (here in 'http://localhost:3000/blog/post-1') the value will be 'post-1'). '{params.slug}' (where slug is the placeholder and params is the props we pass in dynamic route page.js file i.e in 'BlogPostPage' component) will give value 'post-1' if we are in the URL 'http://localhost:3000/blog/post-1'. This '{params.slug}' helps to extract value from database value for 'post-1' here for the link 'http://localhost:3000/blog/post-1' from  '{params.slug}' and place the content for post-1 in the page.








for 'http://localhost:3000/meals' we place 'app/meals' that has content in app/meals/page.js which is following

app/meals/page.js

export default function MealsPage() {
  return (
    <>
      <h1>Meals Page</h1>
    </>
)
}


for 'http://localhost:3000/meals/share' we place 'app/meals/share' that has content in app/meals/share/page.js which is following 


app/meals/share/page.js

export default function ShareMealsPage() {
  return (
    <>
      <h1>Meals Share Page</h1>
    </>
)
}



In 'app/community' for 'http://localhost:3000/community'


app/community/page.js


export default function CommunityPage() {
  return (
    <>
      <h1>Community Page</h1>
    </>
)
}





app/page.js

import Link from 'next/link';


export default function Home() {
  return (
    <main>
      <Header />  
      <p><Link href="/community>Join community</Link></p> 
      <p><Link href="/meals/share>Share meal</Link></p>  
      <p><Link href="/meals>Explore meals</Link></p> 
   </main>
  );
}



For dynamic route for 'http://localhost:3000/meals/<anything>  'app/meals/[mealsSlug]' 


app/meals/[mealsSlug]/page.js



export default function MealsDetailsPage() {
  return (
    <>
      <h1>Meals Details Page</h1>
    </>
)
}






To add a proper header, logo and navigation to this website we can provide in root layout.js (i.e app/layout.js ). Nested layouts can also be provided (for example if there is 'layout.js' inside 'app/meals/layout.js' this layout will be active for meals related pages, but this 'app/meals/layout.js' file be nested into the root layout.

app/meals/layout.js

export default function MealsLayout({children}) {
  return (
    <>
      <p>MealsLayout</p>
     {children}
    </>
)
}


Here when we give like below

<MealsLayout>This content</MealsLayout>

'This content' will be made available through the 'children' props in 'MealsLayout'. 'children' props in 'MealsLayout' will receive any nested layouts or pages.






In 'components' folder and in 'main-header' folder we place 'main-header.js' where components' folder is outside 'app' folder. Where 'main-header.js' is  the portion we write main header which is there on all projects. The logo when clicked will be redirected to home page by the following code in 'main-header.js'


<Link href="/" className={classes.logo}>
          <Image src={logo} alt="Logo" priority />
          Next Level Food
        </Link>


navigate to different route links using 'NavLink' in 'main-header.js'.


 <nav className={classes.nav}>
          <ul>
            <li>
              <NavLink href="/meals">Browse Meals</NavLink>
            </li>
            <li>
              <NavLink href="/community">Foodies Community</NavLink>
            </li>
          </ul>
        </nav>





MainHeader.js

import Link from "next/link";
import logo from "../../assets/logo.png";
import classes from "./main-header.module.css";
import Image from "next/image";
import MainHeaderBackground from "./main-header-background";
import NavLink from "./nav-link";
export default function MainHeader() {
  return (
    <>
      <header className={classes.header}>
        <Link href="/" className={classes.logo}>
          <Image src={logo} alt="Logo" priority />
          Next Level Food
        </Link>
        <nav className={classes.nav}>
          <ul>
            <li>
              <NavLink href="/meals">Browse Meals</NavLink>
            </li>
            <li>
              <NavLink href="/community">Foodies Community</NavLink>
            </li>
          </ul>
        </nav>
      </header>
    </>
  );
}



Put 'MainHeader' component in the root layout in 'layout.js', since 'MainHeader' should be there in every pages.



layout.js


import MainHeader from "../../components/main-header/main-header";
import "./globals.css";

export const metadata = {
  title: "NextLevel Food",
  description: "Delicious meals, shared by a food-loving community.",
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <MainHeader />
        {children}
      </body>
    </html>
  );
}





CSS Modules

Where CSS is provided to a particular component. With the extension 'module.css' .
We are creating file 'main-header.module.css' in 'main-header' folder next to main-header.js


main-header.module.css


.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 2rem 1rem;
}

@media (min-width: 768px) {
  .header {
    padding: 2rem 10%;
  }
}

.nav ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  gap: 1.5rem;
  /* font-family: 'Montserrat', sans-serif; */
  font-size: 1.25rem;
}

.logo {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 2rem;
  text-decoration: none;
  color: #ddd6cb;
  font-weight: bold;
  font-family: "Montserrat", sans-serif;
  letter-spacing: 0.15rem;
  text-transform: uppercase;
  font-size: 1.5rem;
}

.logo img {
  width: 5rem;
  height: 5rem;
  object-fit: contain;
  filter: drop-shadow(0 0 0.75rem rgba(0, 0, 0, 0.5));
}



To import 'main-header.module.css' in 'main-header.js' file we do the following code in 'main-header.js'

import classes from "./main-header.module.css";

then use 'classes' as className like this in 'main-header.js'


<header className={classes.header}>


main-header.js

import Link from "next/link";
import logo from "../../assets/logo.png";
import classes from "./main-header.module.css";
import Image from "next/image";
import MainHeaderBackground from "./main-header-background";
import NavLink from "./nav-link";
export default function MainHeader() {
  return (
    <>
      <header className={classes.header}>
        <Link href="/" className={classes.logo}>
          <Image src={logo} alt="Logo" priority />
          Next Level Food
        </Link>
        <nav className={classes.nav}>
          <ul>
            <li>
              <NavLink href="/meals">Browse Meals</NavLink>
            </li>
            <li>
              <NavLink href="/community">Foodies Community</NavLink>
            </li>
          </ul>
        </nav>
      </header>
    </>
  );
}


'Image' tag called from 'import Image from "next/image";' and the 'priority' property is to ensure image is loaded with priority as shown below 

   <Image src={logo} alt="Logo" priority />


Place 'MainHeaderBackground' component in main-header folder. Where the background gradient css is included in this component  'MainHeaderBackground'


MainHeaderBackground.js

import classes from "./main-header.-background.module.css";
export default function MainHeaderBackground() {
  return (
    <div className={classes["header-background"]}>
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1440 320">
        <defs>
          <linearGradient id="gradient" x1="0%" y1="0%" x2="100%" y2="0%">
            <stop
              offset="0%"
              style={{ stopColor: "#59453c", stopOpacity: "1" }}
            />
            <stop
              offset="100%"
              style={{ stopColor: "#8f3a09", stopOpacity: "1" }}
            />
          </linearGradient>
        </defs>
        <path
          fill="url(#gradient)"
          d="M0,256L48,240C96,224,192,192,288,181.3C384,171,480,181,576,186.7C672,192,768,192,864,181.3C960,171,1056,149,1152,133.3C1248,117,1344,107,1392,101.3L1440,96L1440,0L1392,0C1344,0,1248,0,1152,0C1056,0,960,0,864,0C768,0,672,0,576,0C480,0,384,0,288,0C192,0,96,0,48,0L0,0Z"
        ></path>
      </svg>
    </div>
  );
}




And import 'MainHeaderBackground' component in 'MainHeader' component, 'MainHeaderBackground' is a custom component.



NavLink.js


"use client";

import Link from "next/link";
import { usePathname } from "next/navigation";
import classes from "./nav-link.module.css";

export default function NavLink({ href, children }) {
  const path = usePathname();
  return (
    <Link
      href={href}
      className={
        path.startsWith(href)
          ? `${classes.link} ${classes.active}`
          : classes.link
      }
    >
      {children}
    </Link>
  );
}



MainHeader.js


import Link from "next/link";
import logo from "../../assets/logo.png";
import classes from "./main-header.module.css";
import Image from "next/image";
import MainHeaderBackground from "./main-header-background";
import NavLink from "./nav-link";
export default function MainHeader() {
  return (
    <>
      <MainHeaderBackground />
      <header className={classes.header}>
        <Link href="/" className={classes.logo}>
          <Image src={logo} alt="Logo" priority />
          Next Level Food
        </Link>
        <nav className={classes.nav}>
          <ul>
            <li>
              <NavLink href="/meals">Browse Meals</NavLink>
            </li>
            <li>
              <NavLink href="/community">Foodies Community</NavLink>
            </li>
          </ul>
        </nav>
      </header>
    </>
  );
}


And main-header-background.css is shown below



main-header-background.css


.header-background {
  position: absolute;
  width: 100%;
  height: 320px;
  top: 0;
  left: 0;
  z-index: -1;
}
.header-background svg {
  display: block;
  width: 100%;
  height: auto;
}




root 'page.js' is added the following content where it gives the main content in the opening page




page.js



import Link from "next/link";
import classes from "./page.module.css";
import ImageSlideshow from "../../components/images/image-slideshow";
export default function Home() {
  return (
    <>
      <header className={classes.header}>
        <div className={classes.slideshow}>
          <ImageSlideshow />
        </div>
        <div>
          <div className={classes.hero}>
            <h1>NextLevel Food for NextLevel Foodies</h1>
            <p>Taste and share food from all over the world</p>
          </div>
          <div className={classes.cta}>
            <Link href="/community">Join the community</Link>
            <Link href="/meals">Explore Meals</Link>
          </div>
        </div>
      </header>
      <main>
        <section className={classes.section}>
          <h2>How it works</h2>
          <p>
            NextLevel Food is a platform for foodies to share their favorite
            recipes with the world. It&apos;s a place to discover new dishes,
            and to connect with other food lovers.
          </p>
          <p>
            NextLevel Food is a place to discover new dishes, and to connect
            with other food lovers.
          </p>
        </section>

        <section className={classes.section}>
          <h2>Why NextLevel Food?</h2>
          <p>
            NextLevel Food is a platform for foodies to share their favorite
            recipes with the world. It&apos;s a place to discover new dishes,
            and to connect with other food lovers.
          </p>
          <p>
            NextLevel Food is a place to discover new dishes, and to connect
            with other food lovers.
          </p>
        </section>
      </main>
    </>
  );
}








React Server and Client components

Nextjs is a fullsatck application framework where server-side (backend , the backend executes the server component functions and hence derives the to be rendered HTML code) and client-side (front end, the client-side receives and renders the to be rendered HTML code). All the components including the root page.js and layout.js are server side components.

React Server components are components only rendered on the server. By default all React components are React Server components. Advantage of React Server components are less client side javascript and greater for SEO (search engine optimization). 

React Client components are components are pre-rendered on server but then also potentially on the client.
React hooks are not available in React Server components but only available in React Client components and also 'onClick' are also need to be React Client components since interaction of the browser is required. To make React Client components we use 'use client' on the top of React components.


Here 'usePathname' is giving the current link that's active

import {usePathname} from "next/navigation";
import classes from "./main-header.module.css";

const path = usePathname();

We can provide 'className' like as below

className={path.startsWith('/meals) ? classes.active : undefined }






src/app/meals/page.js


import Link from "next/link";
import classes from "./page.module.css";
import MealsGrid from "../../../components/meals/meals-grid";
import { getMeals } from "../../../lib/meal";
import { Suspense } from "react";

export const metadata = {
  title: "All Meals",
  description: "Browse the delcious meal shared by our vibrant community",
};

async function Meals() {
  const meals = await getMeals();
  return <MealsGrid meals={meals} />;
}

export default function MealsPage() {
  return (
    <>
      <header className={classes.header}>
        <h1>
          Delicious meals created{" "}
          <span className={classes.highlight}> by you</span>
        </h1>
        <p>Choose your recipe and cook yourself. It is easy and fun!!</p>
        <p className={classes.cta}>
          <Link href="/meals/share">Share Your Favourite Recipe</Link>
        </p>
      </header>
      <main className={classes.main}>
        <Suspense
          fallback={<p className={classes.loading}>Fetching Meals....</p>}
        >
          <Meals />
        </Suspense>
      </main>
    </>
  );
}



components/meals/meals-grid.js

import classes from "../meals/meals-grid.module.css";
import MealItem from "./meal-item";

export default function MealsGrid({ meals }) {
  return (
    <ul className={classes.meals}>
      {meals.map((meal) => (
        <li key={meal.id}>
          <MealItem {...meal} />
        </li>
      ))}
    </ul>
  );
}




MealItem.js


import Link from "next/link";
import Image from "next/image";

import classes from "./meal-item.module.css";

export default function MealItem({ title, slug, image, summary, creator }) {
  return (
    <article className={classes.meal}>
      <header>
        <div className={classes.image}>
          <Image src={image} alt={title} fill />
        </div>
        <div className={classes.headerText}>
          <h2>{title}</h2>
          <p>by {creator}</p>
        </div>
      </header>
      <div className={classes.content}>
        <p className={classes.summary}>{summary}</p>
        <div className={classes.actions}>
          <Link href={`/meals/${slug}`}>View Details</Link>
        </div>
      </div>
    </article>
  );
}



Server components can be converted to async functions by using 'async' keyword.

Adding a 'loading.js' file next to the root 'page.js' for showing a loading page. So the page next to where we place 'loading.js' file will be active if the page next to 'loading.js' file is loading. Here we give 'loading.js' file next to root 'page.js' hence when 'page.js' loads the 'loading.js' file will be shown in browser.
To the nested pages we can add 'loading.js' file so that the nested page when loaded this 'loading.js' file will be shown.

  <Suspense
          fallback={<p className={classes.loading}>Fetching Meals....</p>}
        >
          <Meals />
        </Suspense>

Here 'Suspense' is shown the fallback until 'Meals' component is loaded. 'Meals' component is loading the vales from api until then Suspense will shown the fallback.



async function Meals() {
  const meals = await getMeals();
  return <MealsGrid meals={meals} />;
}



src/app/meals/page.js


import Link from "next/link";
import classes from "./page.module.css";
import MealsGrid from "../../../components/meals/meals-grid";
import { getMeals } from "../../../lib/meal";
import { Suspense } from "react";

export const metadata = {
  title: "All Meals",
  description: "Browse the delcious meal shared by our vibrant community",
};

async function Meals() {
  const meals = await getMeals();
  return <MealsGrid meals={meals} />;
}

export default function MealsPage() {
  return (
    <>
      <header className={classes.header}>
        <h1>
          Delicious meals created{" "}
          <span className={classes.highlight}> by you</span>
        </h1>
        <p>Choose your recipe and cook yourself. It is easy and fun!!</p>
        <p className={classes.cta}>
          <Link href="/meals/share">Share Your Favourite Recipe</Link>
        </p>
      </header>
      <main className={classes.main}>
        <Suspense
          fallback={<p className={classes.loading}>Fetching Meals....</p>}
        >
          <Meals />
        </Suspense>
      </main>
    </>
  );
}



For handling errors we can add 'error.js', here we are adding 'error.js' in the root next to 'page.js'. This 'error.js' will handle potential errors when errors occur this page content 'error.js' will be loaded. We can add 'error.js' in nested structures also. 'error.js' file when writing code we should to convert to client component using "use client" above the component since 'error.js' should be client component.



src/app/meals/error.js


"use client";
export default function Error() {
  return (
    <main className="error">
      <h1>Error occured</h1>
      <p>Failed to fetch meals data. Please try again later</p>
    </main>
  );
}


If page not found url is entered then we need to show a page that the page doesn't exists for that we need to add 'not-found.js' in the root file. So when the url matched doesn't enter then we will load the page we enter in the component 'not-found.js' in the root level. When placing 'not-found.js' in root level all the url that doesn't match will show this content shown in 'not-found.js'.


src/not-found.js

export default function NotFoundpage() {
  return (
    <main className="not-found">
      <h1>Meal not Found</h1>
      <p>Unfortunately we cannot found the requested page or meal data.</p>
    </main>
  );
}



To populate dynamic route we can add a 'page.js' file where the dynamic route when typed in the url will display the content in the browser shown in 'src/app/meals/[mealSlug]/page.js'
'params' we get as value to the component here 'MealDetailsPage' will get the route params in the entered url. The 'mealSlug' will be the key and the value will be the url entered 'http://localhost:3000/meals/juicy-burger'. Here 'juicy-burger' will be the value to 'mealSlug' key.
'params.mealSlug' will get the url params through this

  const meal = getMeal(params.mealSlug); will pass the url value to database and fetch the 'meal' from the api.



src/app/meals/[mealSlug]/page.js



import classes from "./page.module.css";
import Image from "next/image";
import { getMeal } from "../../../../lib/meal";
import { notFound } from "next/navigation";
export async function generateMetadata({ params }) {
  const meal = getMeal(params.mealSlug);
  return {
    title: meal.title,
    description: meal.summary,
  };
}
export default function MealDetailsPage({ params }) {
  const meal = getMeal(params.mealSlug);
  if (!meal) notFound();
  meal.instructions = meal.instructions.replace(/\n/g, "<br />");
  return (
    <>
      <header className={classes.header}>
        <div className={classes.image}>
          <Image src={meal.image} alt={meal.title} fill />
        </div>
        <div className={classes.headerText}>
          <h1>{meal.title}</h1>
          <p className={classes.creator}>
            by <a href={`mailto:${meal.creator_email}`}>{meal.creator}</a>
          </p>
          <p className={classes.summary}>{meal.summary}</p>
        </div>
      </header>
      <main>
        <p
          className={classes.instructions}
          dangerouslySetInnerHTML={{
            __html: meal.instructions,
          }}
        ></p>
      </main>
    </>
  );
}






In Next.js (and React in general), dangerouslySetInnerHTML is a special JSX prop that lets you insert raw HTML directly into the DOM instead of rendering it as plain text.

Basic meaning

Normally, React escapes HTML to protect you from XSS (Cross-Site Scripting) attacks:

const html = "<p>Hello <strong>world</strong></p>";

<div>{html}</div>


⬆️ This will render the HTML as text, not as markup.

What dangerouslySetInnerHTML does

It tells React:

“I know this HTML is safe—please insert it as real HTML.”

const html = "<p>Hello <strong>world</strong></p>";

<div dangerouslySetInnerHTML={{ __html: html }} />



dangerouslySetInnerHTML={{ __html: html }}
This is required because:

JSX attributes expect objects

__html is a deliberate, awkward key name to prevent accidental use






