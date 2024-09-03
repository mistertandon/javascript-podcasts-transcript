**Wes Bos:**  
Welcome to Syntax. Today, we’re joined once again by Ryan Dahl to discuss Deno 2.0, its future trajectory, some of the exciting new features, and, of course, a bit about JSR and related topics that might arise. Most listeners are likely familiar with Ryan as the creator of Node.js—a prolific developer. Ryan, how long has Deno been around now? Four or five years?

**Ryan Dahl:**  
I believe it’s been around seven years since its inception, though it’s only been a company for about four years. 

**Wes Bos:**  
That's impressive. Could you provide a brief overview of Deno for those who may not be familiar with it?

**Ryan Dahl:**  
Certainly. Deno is a server-side JavaScript runtime. In many ways, it can be seen as a continuation of the Node.js project, but with a fresh perspective.

**Topic 1: Deno simplifies server-side JavaScript, making it elegant**

**Ryan Dahl:**  
Node.js operates with various modules, folders, and the package.json file, none of which are governed by any formal standards. Deno seeks to streamline this, making server-side JavaScript as elegant as possible. JavaScript is the de facto programming language, and it deserves a future that reflects its significance.

**Scott Tolinski:**  
Absolutely. One aspect of Deno that resonates with many is its adherence to web standards, coupled with its ease of use. It feels more intuitive and less cumbersome to set up. Was this emphasis on simplicity always a core objective with Deno?

**Ryan Dahl:**  
Yes, even with Node.js, the goal was to create something that wasn’t just aesthetically pleasing but familiar to developers. JavaScript, being a scripting language, allows you to program a web server with just a few lines of code, which was revolutionary back in 2009 when Node.js was launched. However, as JavaScript has evolved, the ecosystem has become increasingly complex, with a myriad of tools that need to be integrated into your projects. You almost need to be an expert in the Node.js ecosystem to navigate it efficiently. Deno addresses this by offering built-in tools like Prettier for code formatting and ESLint, reducing the friction for developers, especially those new to the language. This out-of-the-box readiness reignites the excitement in coding because it lets you focus on your actual work rather than the setup.

**Wes Bos:**  
Exactly. It eliminates the frustration of getting everything to work seamlessly. I often compare this to my experience with Python, where, as an outsider, I frequently struggle to get the correct version installed or to use the recommended packages. I hope the Node.js community doesn’t fall into the same trap of requiring extensive insider knowledge just to get started. Deno, in contrast, makes everything straightforward. You don’t even need a tsconfig to run your code. It’s that simple.

**Topic 2: Deno provides out-of-the-box tools, reducing Node ecosystem complexity**

**Ryan Dahl:**  
Indeed, with Deno, a single executable provides your entire development environment. It integrates with Visual Studio Code, offers a linter, a formatter, Deno compile, and type-checking—all bundled together. While it is opinionated, which might not sit well with some advanced users, it offers a polished and delightful experience for newcomers. This is what I believe JavaScript should be—simple and intuitive, like building with Duplo blocks, rather than bogged down by decisions about which tools to use.

**Wes Bos:**  
That’s fantastic. Alright, let’s delve into Deno 2.0. You mentioned something that piqued my interest—you’ll be able to use Next.js with it. Could you elaborate on what Deno 2.0 brings to the table?

**Topic 3: Deno 2.0 Adds NPM Package Support While Retaining Deno’s Simplicity**

**Ryan Dahl:**  
When Deno was initially conceived, the idea was to completely reset expectations within the JavaScript ecosystem.

**Ryan Dahl:**  
We believed it wasn’t necessary to support NPM. Instead, we could utilize HTTP imports and create an entirely new registry.

**Ryan Dahl:**  
We were committed to simplification and eager to experiment in various ways. However, it has become increasingly apparent over the last few years that many NPM packages are indispensable for developers. For instance, if you need to integrate the AWS SDK or a gRPC library, Deno’s inability to support these would be a dealbreaker. The allure of Deno quickly fades if it can't accommodate essential packages. Therefore, it became clear that we needed to support NPM packages, most of which are built on top of Node’s built-in APIs. This has been a monumental task, one we've been working on for nearly two years.

**Ryan Dahl:**  
Deno is now approaching a point where most NPM packages work out of the box, though it remains challenging to support everything in all its intricacies.

**Ryan Dahl:**  
However, Deno can now handle most frameworks, including those with complex dependencies, such as a `node_modules` folder and an existing `package.json`. Even intricate frameworks like Next.js, with their extensive build processes, are supported. Once Deno 2.0 is released, you should be able to run these frameworks with commands like `deno run Node` in your next project. Although there are a few minor breaking changes, most Deno users won't notice much difference. These changes mainly pertain to installation procedures, which enable support for `package.json` projects.

**Ryan Dahl:**  
And yes, you’ll be able to run Next.js with Deno.

**Wes Bos:**  
Wow, that’s really exciting. What was the process of supporting the entire NPM ecosystem like? Initially, a significant aspect of Deno’s development was replicating the Node APIs. If you examine Deno’s source code, you’ll see that many Node modules are reimplemented using Deno’s equivalents. For example, if someone uses Node’s filesystem APIs in a package, Deno substitutes them with its own filesystem APIs. How did you approach this challenge?


**Topic 4: Supporting NPM Packages in Deno Required Extensive API and Framework Implementation**

**Ryan Dahl:**  
I would estimate that about 90% of the built-in Node APIs are straightforward mappings. For instance, if you use `cwd` in Node, there’s an equivalent in Deno, such as `Deno.cwd`. It’s essentially just API mapping. However, some APIs are far more intricate. For example, the HTTP client in Node.js is complex, supporting features like trailing headers and various other configurations. We've had to delve into lower-level implementations, often in Rust, to accommodate these complexities because, in Deno, the HTTP client is simply `fetch`, which is relatively basic compared to Node’s HTTP client.

**Ryan Dahl:**  
And this kind of work is multiplied a hundredfold. There are numerous APIs, each with its own intricacies, and that’s just on the API side. Then, there’s the task of loading modules, handling `node_modules`, and managing `package.json` files—what you might consider the framework side of things. When Deno is integrated into a Node project, how it interacts with all these components is a significant challenge. I’d say the effort is split about 50/50 between the API implementation and framework integration. Apologies if this sounds vague, but it’s an immensely complex undertaking.

**Wes Bos:**  
Yeah.

**Wes Bos:**  
I'm curious about which aspects might be unfeasible. For instance, when we spoke with the Cloudflare team, they mentioned that while most of Node works on Cloudflare, there are always some obscure edge cases, especially when dealing with native components in Node. Are there parts of the ecosystem that might be problematic for certain projects?

**Ryan Dahl:**  
Yes, there are definitely some esoteric elements that might pose challenges. For example, Deno supports N-API, allowing you to load native Node extensions, which will work in Deno. However, if a package, like the SQLite3 package on NPM, uses a `libuv` API that isn’t part of N-API, it becomes an issue. We don't support that particular `libuv` API, so the question arises: do we implement it, or do we modify SQLite3 to use N-API equivalents? It’s likely the latter. So, sometimes it’s a matter of updating packages to be compatible. Deno supports all of CommonJS, but you can’t use CommonJS directly in Deno programs. However, if you import an NPM package that uses CommonJS, Deno can handle it. While we are firm in our commitment to promoting browser-compatible JavaScript, and therefore don’t allow the `require` function in Deno, there is an implementation of `require` deep within the system to support such packages. Ideally, the code you write won't interact with that at all.

**Ryan Dahl:**  
On the topic of `require`, do you think we need to make a decisive break from it? I know some prominent package authors have already moved away from it, but CommonJS seems to persist indefinitely.

**Wes Bos:**  
Do you think we should take a more aggressive approach, or just wait it out for another six years?

**Ryan Dahl:**  
It’s likely akin to the transition from Python 2 to Python 3—a necessary change, though disruptive. While a hard break isn’t required, Deno demonstrates that it’s possible to have a strict, ECMAScript module (ESM)-first runtime that still supports CommonJS. So, I don't believe a drastic shift is necessary for users, but it might be a good opportunity to discuss JSR. JSR is a package registry that supports NPM dependencies. You can publish packages that depend on CommonJS packages from NPM, but we enforce a strict rule: you cannot publish or require CommonJS in JSR; only ESM is allowed. There's no turning back; the world will inevitably transition to ESM. It’s just a matter of how long it takes. I believe that those of us working in this space should accelerate this transition, as the alternative—an extended period of transition—benefits no one.

**Topic 5: Deno Enables Developers to Write JavaScript Rather Than Deno-Specific Code**

**Scott Tolinski:**  
I think Deno’s approach is commendable. Your applications will function even if someone, somewhere, is using `require`.

**Scott Tolinski:**  
But the fact that you're discouraged from using it seems like the right decision, or at least a step in the right direction. You also introduced `package.json`. Wasn’t that in Deno 1.3? I think that update came out last year.

**Scott Tolinski:**  
What prompted the decision to add `package.json`? Was it a difficult choice? Previously, people would create something like a `deps.ts` file for their imports, which functioned almost like a pseudo `package.json`. What led to the eventual inclusion of `package.json`?

**Ryan Dahl:**  
First of all, a vast amount of software relies on `package.json`. If we’re supporting NPM dependencies, it’s logical to include a `package.json` with a dependencies field. Browsers don’t recognize `package.json`, and we’re exploring ways to build on the future of JavaScript by providing a browser-compatible method for sharing modules.

**Ryan Dahl:**  
We recommend using import maps for defining dependencies and mapping long URLs to shorter, more manageable specifiers.

**Ryan Dahl:**  
This approach is what I’d call the second iteration of the `deps.ts` convention we initially adopted in Deno.

**Ryan Dahl:**  
Import maps work much more effectively than that. However, given the ubiquity of `package.json` and how easy it is for us to support it, not doing so would be somewhat unreasonable.

**Ryan Dahl:**  
So, essentially, there are two modes in Deno: you can run with a `package.json` or with an import map. It’s seamless for users, and it still provides that intuitive, Duplo block-like experience where you can just sit down, start using Deno, and think, "Wow, it just works."

**Wes Bos:**  
I appreciate it because I strongly advocate for standards. Any new server-side code I write is based on `fetch`, `web`, and similar standards. However, I still have legacy code that I need to update, and I love that Deno accommodates this. It smooths out the challenging parts, ensuring that my new code will be based solely on web standards, like JavaScript or Hono. By the way, is it pronounced "Hono"? I've been calling it that for at least two years, but I could be mistaken.

**Ryan Dahl:**  
You're probably correct. I know a few things, but pronunciation might not be my strong suit.

**Scott Tolinski:**  
CJ also calls it Node. Every time I say "Hono," I feel self-conscious, but I trust you on these things.

**Wes Bos:**  
I'm curious—people aren't paying attention, right? For instance, I have a Node app, an Express application, and a Next.js application. They all work together and connect to my database. What would I gain by migrating these applications from Node to Deno?

**Ryan Dahl:**  
The secure-by-default aspect of Deno is quite compelling. For instance, if you have an API server that merely handles some authentication and forwards requests, it shouldn't have unrestricted access to the file system. Implementing security at multiple layers and allowing only specific domains to connect makes a lot of sense. While it requires more configuration, V8 provides a secure sandbox, and leveraging these features out of the box is excellent. Moreover, Deno's TypeScript support is exceptional.

For Next.js specifically, the benefits might be limited because it already has a smooth development process, and using Deno wouldn't differ much from using Node. However, an Express API server is a perfect example where Deno can simplify complexity. You could eliminate your `tsconfig.json` and transition entirely to TypeScript.

**Ryan Dahl:**  
You might even remove `package.json` altogether.

**Ryan Dahl:**
You can enhance security by restricting net connections and preventing file system access. The development experience with Deno should be straightforward. If you're using auto-formatting, simply run `deno fmt`. For linting, our linter is incredibly fast—about 100 times faster than ESLint—and is implemented in Rust. It comes pre-configured, so as you start coding in VS Code, you'll see helpful error indicators without needing to set anything up. With Deno 2, the onboarding process has improved significantly. Previously, adopting Deno might have felt like an all-or-nothing shift, requiring a massive overhaul. But now, with the new features we've added, you can migrate incrementally. You can start with Deno and gradually incorporate it into your workflow. You might encounter minor issues, such as the absence of the `process` variable, but ESLint should guide you through them. You can simply import `Node:process` and move forward. Overall, the migration should be smooth, resulting in a project that's opinionated, streamlined, and forward-looking in terms of web standards.

**Wes Bos:**  
Deploying and hosting should be significantly easier since there aren’t numerous steps involved in compiling and caching everything.

**Wes Bos:**  
What about the actual performance of the website? Is there a possibility it could use fewer resources?

**Ryan Dahl:**  
Absolutely. I haven't emphasized this much because Bun also claims speed, but Deno is incredibly fast—often outperforming Bun significantly. While I don't feel confident enough to showcase a graph on our website, such benchmarks can be misleading. Still, Deno is very, very fast. For instance, if you compare Express throughput on Node with Deno, you might see a 2x improvement.

**Ryan Dahl:**  
However, a more critical metric, depending on your deployment environment—such as AWS Lambda—is cold start latency rather than throughput. Deno excels here with features like code caching, which essentially compiles your source code into bytecode. As a result, Deno has best-in-class cold start times on Lambda. Overall, Deno's performance is outstanding and generally surpasses Node, even rivaling Bun, although their benchmarks might exaggerate their advantage. If you want to see all the errors in your application...

**Scott Tolinski:**  
...you’ll want to check out Sentry at sentry.io/syntax.

**Scott Tolinski:**  
You don’t want a production application running without visibility into potential issues. So head over to sentry.io/syntax. We’ve used this tool for a long time, and it’s fantastic. Alright.

**Wes Bos:**  
What do you think about Bun's approach of aggressively implementing non-standard features?

**Ryan Dahl:**  
To each their own. I believe JavaScript is here to stay for the long haul—not just for a couple of years, but for decades. Infrastructure relies heavily on the browser, so it’s crucial to think long-term about where we’re headed. We’re building Deno with the future in mind.

**Scott Tolinski:**  
Yeah, and in that context, Node seems to be rapidly introducing new features lately, possibly due to pressure from Deno and Bun. Whether it's ENV support, SQLite, or type stripping, what are your thoughts on these additions? Do you think Node is overloading itself, or is this positive?

**Ryan Dahl:**  
I initiated the small-core philosophy in Node development, which dominated for a long time—keeping the core minimal and pushing everything else to NPM. That approach had its time and place, but it’s clear now that runtimes can do much more to simplify development. I think Node is looking at what Deno is doing and thinking, "We can do that too." 

**Ryan Dahl:**  
My concern with Deno is that we might end up acting as an R&D shop, pioneering features that then get implemented in Node. But, honestly, I see value in critically re-evaluating server-side development and making it more elegant. It's challenging to deeply innovate within a 15-year-old codebase like Node’s, which is written in C++. While I’m pleased that Node continues to evolve, I also recognize its limitations due to its origins and its vast user base. I think I recently saw that some experimental privacy features were removed, which, initially, I suggested should be more like Deno’s. When you fire up a Deno script, it immediately informs you if the code is attempting to access...


**Wes Bos:**  
Are you sure you want your file system to allow pinging external URLs? I was disappointed to see that because the frequency of supply chain attacks is becoming overwhelming. I read somewhere that about 70% of Node modules are potentially malicious, and several projects are trying to mitigate this risk. It's only a matter of time before you `pnpm install` something buried 11 levels deep that ends up compromising your computer. The fact that post-install scripts run by default is just...

**Ryan Dahl:**  
...absolutely terrible. People should be very cautious about post-install scripts. It’s one thing if you’ve audited your dependencies and understand their operations, but the idea that you can just `npm add` something and execute random code from the internet—code that hasn’t even been vetted by the package you’re installing—is a disaster waiting to happen. Post-install scripts must be reformed, Wes. There’s no scenario in which this current approach will continue to work.

**Ryan Dahl:**  
It’s one of those issues where we need to act quickly. We can either suffer for years or collectively agree that post-install scripts are bad and move forward as rapidly as possible.

**Scott Tolinski:**  
Yeah, I think that’s where Deno 2 really shines because it enhances security without requiring significant changes on your end.

**Ryan Dahl:**  
Exactly. I was just searching for SvelteKit Deno, and all you need to do is set the `DENO2_ENV` variable to ensure you're using Deno 2. It's incredible that enabling Deno features is as simple as setting `DENO_FEATURE=1`. If Deno 2 isn’t released by the time people listen to this, just know that this environment variable will activate all the new features. These updates will be transparent to most users, but the small changes can have a substantial impact.

**Scott Tolinski:**  
One of my favorite recent Deno projects is Loom (or Loomay—I'm not sure how people pronounce it), an incredible static site generator and CMS.

**Ryan Dahl:**  
When we shared that on our daily dev, it garnered a massive response. People really connected with it. Beyond Loom, there are other impressive projects in the Deno ecosystem. I’d be remiss if I didn’t mention Fresh, a web framework we’re developing, with a version 2 release on the horizon. There’s also a subcommunity within Deno focused on desktop development. For example, Deno has a built-in WebGPU API that allows you to render GPU-accelerated windows without a browser, unlike Node, which requires Chrome or Electron. Developers are creating small games with this, and combined with Deno Compile, they can ship a 60MB GPU-accelerated game written in JavaScript. It’s a fascinating aspect of the Deno community. I should provide you with some links for this...

**Ryan Dahl:**  
...especially for the Tolinski section of your website. We do a lot of sharing there. Ultimately, our goal is not just to encourage Deno-specific development but to promote modern, TypeScript-first ESM software. Deno can consume JavaScript as long as it’s modern. We encourage developers to publish to JSR, which has a built-in linting system and encourages best practices. One of the ideas we hope to solidify with Deno 2 is that you don’t need to write Deno-specific software. Just write JavaScript, and it will work in Deno. Avoid using `require`.

**Ryan Dahl:**  
Use import statements for Node APIs.

**Ryan Dahl:**  
If you need a Node API, import it directly. This approach should work seamlessly without requiring a complete rewrite.

**Wes Bos:**  
Yeah, and you don’t need to learn Deno APIs for the most part. I’ve been advocating for a unified file system API for a long time through WinterCG. Do you think we’ll ever see that?

**Ryan Dahl:**  
Not all systems have file systems. For a long time, Deno Deploy didn’t have one, and Cloudflare Workers still don’t, as far as I know. So it’s not clear that a standardized file system API is necessary.

**Ryan Dahl:**  
For those situations, it’s probably better to rely on built-in Node or Deno APIs, which are similar but a bit more refined.

**Wes Bos:**  
I almost wish—and maybe this is possible—that the Deno FS APIs were published as a Node package. I write a lot of scripts on my computer, particularly build scripts and file management tasks for syntax websites, which are all in Markdown. Often, when it’s time to save a file, I wonder if it should run immediately. Perhaps there’s some sort of universal...

**Ryan Dahl:**  
...file system API out there. Yes, we have a standard library in Deno that’s on JSR, but it’s worth noting that JSR isn’t exclusive to Deno. Our goal is to acknowledge the multitude of JavaScript runtimes out there—browsers, Deno, Node, Bun, Cloudflare Workers—and cater to developers who want their JavaScript to work across these environments. We’ve published what was previously the Deno standard library, but we hope to evolve it into the standard library for JavaScript in general.

**Ryan Dahl:**  
There’s a file system library in that standard library, equipped with advanced utilities like `warp`. We’re still working on porting this to Node, but in the future, I hope you’ll be able to import the standard file system library from JSR. It will be fully TypeScript-complete based on promises and async iterators, with support for `for-await` loops. Ideally, it will work across most platforms, although maybe not in Cloudflare Workers or browsers since they don’t have file systems like Node does.

**Wes Bos**  
When it comes to TypeScript, we had Luca on the podcast to discuss JSR. One of the aspects I was particularly excited about is how you can now directly use TypeScript in Node.js without needing a build step. There’s no need for the cumbersome process of minifying or unpacking someone else’s work from NPM. With JSR, it compiles when necessary, but the idea of simply importing TypeScript as it was originally written is incredibly appealing to me.

**Ryan Dahl:**  
Yes, importing the TypeScript directly.

**Wes Bos**  
Exactly, importing the file right here. You can tell I’m still in my old JavaScript mindset when I say “requiring it.” But yes, importing JavaScript directly. I’m curious about your thoughts on the proposal to include types as comments—maybe TypeScript isn't the final destination for typed JavaScript.

**Ryan Dahl:**  
You know, I often discuss the future of JavaScript and how we aim to narrow the gap between server-side and browser JavaScript. A point of contention is that Deno, for instance, heavily relies on TypeScript, whereas web browsers don’t support it natively. This discrepancy highlights the need for standardization. My perspective is that TypeScript represents the future of JavaScript. We shouldn't delay; we need to expedite this transition. While the proposal to include types as comments is not fully developed, I believe it's the direction JavaScript will take. I can envision a scenario where, five years from now, Chrome could execute TypeScript natively by performing type stripping within V8 itself, which would be a remarkable advancement.

**Ryan Dahl:**  
Type stripping and type checking algorithms differ significantly in complexity. Type stripping is relatively straightforward—you can develop a simple tool to strip types quickly. However, type checking involves a much higher level of complexity, likely resulting in only one implementation of the TypeScript type checker, which is the TSC.

**Wes Bos**  
Exactly, the complexity involved is why many attempts at type checking never reach completion. I'm curious to see what happens once we integrate types as comments into the JavaScript syntax. The syntax itself will allow for additional type annotations but won't define the types—like string, number, etc.—for JavaScript. Do you think this will lead to a new iteration of TypeScript, perhaps one with a full type checker built in Rust, and without enums?

**Ryan Dahl:**  
Honestly, I’m not sure. I can certainly envision a future with iterations on JavaScript plus types, perhaps a simpler version that doesn’t encompass all of TypeScript’s intricacies. It's quite clear, though, that browsers themselves will likely never handle type checking. That’s not a realistic expectation.

**Ryan Dahl:**  
Ideally, the types-as-comments proposal will leave room for further iterations and won’t lock TypeScript into being the de facto standard. However, it's undeniable that adding types to JavaScript is essential for scaling the language further. Given JavaScript's enduring presence, I believe this evolution is inevitable.

**Scott Tolinski**  
It’s almost like a matter of coding style. What’s your take on nonstandard TypeScript features like enums or namespaces? Do you avoid them in your own TypeScript writing, or do you utilize all available features?

**Ryan Dahl:**  
Personally, I tend to avoid enums, but it’s not something I feel strongly about. As someone involved in runtime development, my priority is ensuring that whatever people write in TypeScript works seamlessly.

**Wes Bos**  
I’m very particular about how TypeScript itself is written. One interesting development I wanted to highlight is Jupyter Vercel for Deno. It’s gaining significant traction in the AI and academic communities, especially for those who use Jupyter Notebooks to write Python code and visualize data.

**Wes Bos**  
While Jupyter Notebooks are primarily used in data science, they can be applied to a wide range of tasks. As someone who frequently creates demos, I’ve long desired a Jupyter Notebook-like environment for JavaScript. People have suggested alternatives like Observable, but none have truly met my needs. However, Deno quietly introduced unstable support for Jupyter Notebooks about six months ago.

**Wes Bos**  
What was the motivation behind that?

**Ryan Dahl:**  
Deno excels as a single-file scripting environment. You don’t need a package.json file; you can simply include some imports and start coding in one file. This approach is ideal for notebook-style programming, which is prevalent in the Python ecosystem. With Deno, you can execute JavaScript directly within Jupyter, making it a great tool for this purpose.

**Ryan Dahl:**  
It works exceptionally well, especially when combined with Observable Plot, which is the successor to D3, developed by Mike Bostock. These days, I often integrate Polars, the new Rust-based data frame library that is akin to Pandas but for JavaScript. The experience is quite remarkable, and I’m optimistic that we could eventually transition many Python-based data scientists to JavaScript for data analysis.

**Wes Bos**  
That’s impressive because, honestly, one of the biggest barriers to entry in this field is that so much of it is written in Python. If you want to create a custom UI for your data, JavaScript, HTML, and CSS are far superior for that purpose. By writing your Deno code for data processing and using a bit of JavaScript for visualization with Observable Plot, you can achieve results far beyond what’s currently possible in Python. People still use Matplotlib in Python, which is vastly inferior to Observable Plot.

**Ryan Dahl:**  
Exactly, the visualization aspect in JavaScript is light years ahead when it comes to data analysis. The mathematics and data-loading components still need to mature, but with tools like TensorFlow.js and Polars, this is becoming increasingly feasible. We’ve been quietly working on NPM compatibility to ensure web developers can operate seamlessly within this ecosystem. Behind the scenes, there’s a significant evolution occurring in the data visualization capabilities of JavaScript. I look forward to the day when JavaScript supplants Python for these use cases because, frankly, it’s a superior language.

**Wes Bos**  
And what about AI? Do you see a lot of that moving towards JavaScript? We had a guest from Hugging Face who introduced us to his new Transformers.js, and we’re pretty excited about it. Do you think AI development will shift towards JavaScript?

**Ryan Dahl:**  
I hope so, possibly.

**Ryan Dahl:**  
However, the reality is that many machine learning professionals rely heavily on Python, especially with frameworks like PyTorch dominating the field. There’s substantial momentum behind Python, making it the primary language for AI. While JavaScript has seen improvements in its tooling, it still has a long way to go to match Python’s capabilities in this area. With the ongoing AI boom, it will be interesting to see how long it takes for JavaScript to reach parity. That said, TensorFlow.js is an excellent tool, and I think people would be surprised at how sophisticated machine learning models can be written in JavaScript. Since all the computation is offloaded to the GPU, runtime performance isn’t a limiting factor in building these models.

**Wes Bos**  
It's all offloaded. Speaking of performance, I'm curious about your thoughts on WebAssembly (WASM) and WASI. We ask every guest about it.

**Ryan Dahl:**  
Is WASM still relevant? I’m not sure.

**Wes Bos**  
Go on.

**Ryan Dahl:**  
WASM is a promising concept, but I think the industry prematurely overhyped it before it was mature enough to fulfill the ambitious visions people had. Many saw it as the next-generation container system, expecting everything to transition to WASM. Companies like Fastly even built WASM-based edge services.

**Ryan Dahl:**  
However, this vision clashed with reality. Most web developers prefer writing JavaScript; they don’t want to write in Rust or deal with additional build steps in their workflow. They don’t want to compile JavaScript with Quick.js into WASM, only to execute it at a fraction of the speed compared to running it directly in V8. In my view, WASM is a useful subset of JavaScript, primarily for tasks like compiling ImageMagick into a binary for execution within a JavaScript environment. WASM will continue to evolve, but those who attempted to build companies around WASM container hosting services have found limited success because it doesn’t align with how developers want to build applications.

**Wes Bos**  
That makes sense. Is there anything about Deno 2 that we haven't covered, which you think is important for people to know?

**Ryan Dahl:**  
Yes, there are a few things. First, we now support workspaces, including NPM workspaces and our own version within the Deno JSON file. This is particularly useful for setting up a monorepo with multiple packages, which integrates well with JSR if you're publishing multiple projects. Additionally, we're planning to release a long-term support (LTS) version of Deno. We've been working on Deno for a while, and it’s now a stable Node environment. With Deno 2, we want to explicitly communicate to users that Deno is production-ready, with long-term support guarantees. This version marks a significant milestone, signaling that Deno is ready for widespread adoption and production use.

**Ryan Dahl:**  
Post Deno 2, I don't foresee any major changes or a Deno 3 in the near future. We've put a lot of thought into aligning with practical needs, and we’re very satisfied with the current state of Deno. So, definitely keep an eye out for the Deno 2 release.

**Wes Bos**  
That’s great. Now, moving on to the next segment: Sick Picks and Shameless Plugs. What do you have for a Sick Pick today? Last time, you picked flying a kite in New York City, which was memorable. What’s your pick today?

**Ryan Dahl:**  
Something similar—spending time in McCarren Park in Brooklyn. I like to bring a blanket, take the kids out, maybe enjoy a beer, and just relax on a Saturday. That’s my Sick Pick.

**Wes Bos**  
That sounds awesome. I’m just looking at photos of it—New York is so cool. I really need to visit again.

**Wes Bos**  
Right on. And for the Shameless Plug, what would you like to promote?

**Ryan Dahl:**  
Deno for Enterprise. If your company is using Deno and you want support to ensure everything runs smoothly, with a direct line to our team, visit deno.com/enterprise.

**Scott Tolinski**  
Fig.

**Scott Tolinski**  
Cool. Wes, thank you so much, Ryan. This has been fantastic. It's always great having you on. Thank you!

**Wes Bos**  
Thanks, everyone.