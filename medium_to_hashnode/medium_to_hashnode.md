---
title: Medium to Hashnode
subtitle: Pros, cons and workflows
slug: medium-to-hasgnode
tags: learning-journey, programming-blogs, best-of-hashnode
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1659175564353/vBBsXyzWA.png?auto=compress
domain: geods.hashnode.dev
publishAs: SebastianoF 
ignorePost: false
---


This post documents the transition of my newly born blog dedicated to geospatal data science from [Medium](https://medium.com/@sebastianof/) to [Hashnode](https://geods.hashnode.dev/). Here you will find the pros and cons of both platforms I have found so far and how my workflow have changed after this choice. In between there is the description of two issues with hashnode when articles are sources from github, and at the end you will find a list of possible alternatives for creating a blog.

## Content
1. [Pros and Cons](#pros-and-cons)
    - [Medium](#medium)
    - [Hashnode](#hashnode)
2. [Current issues with Hashnode](#hashnode-issues)
3. [Workflow](#workflows)
    - [Medium](#workflows-medium)
    - [Hashnode](#workflows-hashnode)
4. [Other alternatives](#alternatives) 
5. [Conclusions](#conclusions) 
6. [Links](#links) 
7. [Credits](#credits) 

## <a id="pros-and-cons"></a> 1. Pros and cons

The two reasons why hashnode is so attractive for tech writing are the possibility to source the articles directly from github with the github integration (like the article that you are reading right now!), and the possibility of collaborating with other users. But these are only the main reasons. Let's see a list of 4 pros and cons for each platform, starting with Medium.

### <a id="medium"></a> Medium ###

- **Pros:**

    - **Popularity:** Medium had been around since 2012, it has [200000 writers adn 54 million users](https://bloggingguide.com/medium-platform-statistics/#:~:text=Writers%20can%20write%20and%20publish,it%20was%20launched%20in%202017.) and thanks to the quality of its content, whatever you are looking for on google, a medium article is likely to appear in the top 20 results.

    - **Simplicity:** it is very simple to use, and does not require coding experience. For plain text Medium has the shortest possible pathway to connect your content with potential readers.

    - **Minimalism:** the UI for the Medium's readers is easily recognisable, iconic, clutter free, and simple to interact with. 

    - **Wide community:** Being widely used and popular, you are very likely to find an expert to ask questions to about something you want to know more about on Medium than anywhere else. As a writer, you can be sure your articles will receive immediate attention from other users with similar interests.

- **Cons:**
    
    I already wrote about the limitations of Medium on a post with the emphatic title [The 7 reasons why I am not writing on Medium](https://medium.com/@sebastianof/reading-on-medium-writing-on-hashnode-1ee7d6d4f2e4). Here you can find a condensed version, in only 4 points.

    - **WYSIWYG:** the intention of being usable by the widest possible audience has some limitations. The text editor follows the WYSIWYG (what you see is what you get) paradigm: what you write is exactly what you will see in the page, as it happens in other tools, such as in Microsoft Word, and FrontPage. This feature reduces the flexibility in formatting and customisation, and a technical writer typically used to compiled markup languages and text editors with shortcuts, multiple cursors, custom colorschemes, integrated terminals, and other goodies, will have the sensation of writing with handcuffs.

    - **Not flexible or programmable:** The interface of Medium has minimal editability. You can import third party contents, such as images, import code from github gists, and [cusomise the graphic](https://help.medium.com/hc/en-us/articles/360053582013-Customizing-your-publication-homepage-layout-with-beta-publication-tools). Here as well, all is done via UI and not via programming language, which reduces the flexibility developers are used to have.  

    - **No version control:** also providing the shortest path between writers and readers has a costs. As it would probably complicate the interface beyond the scopes of Medium and it would require some knowledge with version control systems, Medium lacks the possibility of having multiple branches for the same draft and the idea of checkpoint the work to be able to go back in the past at a tagged point.

    - **No collaboration:** probably for a similar reason why there is no version control, it is also not possible to collaborate with another author while writing an article. Git and its hosts such as github and gitlab have solved the problem of writing collaboration (as well as version control), though the learning curve may discourage the writer who is only looking for a simple platform.

### <a id="hashnode"></a> Hashnode Pros ###

- **Pros:**

    - **Programmable:** articles on hashnode are written in markdown. It is not a WYSIWYG language, but it is simple enough to allow the author to focus on the content, without loosing the benefits of a markup language. Moreover hashnode can be [customised directly in CSS](https://support.hashnode.com/docs/custom-css#:~:text=The%20Custom%20CSS%20feature%20on,maintain%20your%20personal%20branding%20effortlessly.), can have integration with github.
    
    - **Collaborative:** collaboration on hashnode can happen either on github integration or adding directly multiple team members to a single blog. The paradigm of the solitary developer in a dark room it's a thing of the past, and more and more devs are investing a percentage of their time in [looking at other people work and sharing theirs](https://www.threado.com/resources/writing-their-legacy-how-did-hashnode-give-a-voice-to-the-developer-community#:~:text=This%20flair%20for%20writing%20is,(now%20Hashnode)%20in%202015.), for learning, contaminations, and for finding direct collaborators. Imagine a novel writer reads only his own work, and does not even publish it!

    - **Tech community and support:** probably hashnode is not explicitly targeting only tech experts, though these are the writers and readers who will find themselves more at home here than in many other blog platforms I know of. Not only tech articles are encouraged and you will easily find [personal blogs](https://hashnode.com/@Manish-B) of developer talking about various topics such as Kotlin, XML and how to talk confidently in public.

    - **Github integration:** that's the main reason why hashnode is so attractive to me (and possibly to many others who have joined). The idea of writing a post, version control and collaborate on it on github, and having it automatically up to date on the community facing blog. There are currently a couple of issues that I have delineated [below](#hashnode-issues), though they will hopefully be soon solved to make the integration seamless. 

- **Cons:**

    - **Not as popular:** being more user friendly (for the writer and reader not in for technical content) medium certainly wins in popularity, and therefore in visibility. I expect hashnode will never be as popular, or will have as many users as medium, though it is certainly destined to improve, and to become the go-to choice for developer looking for a quick way of posting content and for a community to interact with.

    - **Not as minimalistic:** compared to the now iconic Medium UI, the dashboard of hashnode looks a crammed and still has to find its personality. Zooming out and going dark mode helps, though there is sometimes too much information in one go for my taste.

    - **Too many emoji:** emoji are like swearing 🤬, they are cool 🆒  and funny 🤣  only if not abused 🔫  or misplaced 🚽. An excessive 📈  amount would infantilise 👶  the reader 👓, visually saturate the page 📃, and divert the attention ㊟ from the content 🔃 (yes, you got me 🥇, I am showing you an example here 🤣🤣🤣🤣🤣, how funny 🤪). Also there is something odd in having 10 different ways of showing appreciation to an article (unlike the single black-and-white claps of Medium). A single option encourages the reader to give kudos, 10 options to chose from so far steered me away from upvoting.

## <a id="hashnode-issues"></a> 2. Current issues with Hashnode

This post is also the opportunity to document a couple of issues found (29 July 2022) sourcing articles from github.

1. All the underscores appearing in the markdown in the source code are escaped with a backslash. For example the following url that contains underscores appears on github as:
    ```txt
    https://github.com/SebastianoF/GeoBlog/blob/master/office_positioning/office_positioning.md
    ```
    though when it is formatted as a link, the underscores are escaped, and the link results to be broken.
    - [https://github.com/SebastianoF/GeoBlog/blob/master/office_positioning/office_positioning.md](https://github.com/SebastianoF/GeoBlog/blob/master/office_positioning/office_positioning.md)

    This happens also when linking images. I raised this issue on [stackoverflow](https://stackoverflow.com/questions/73099314/how-to-prevent-hashnode-dev-github-integration-from-automatically-escaping-under) as well and communicated it to the hashnode development team. Hopefully it will be only a matter of time before the bug will be fixed.

2. LaTeX code is also not formatted, remaining raw across the article. 
    For example, I'm adding below the formula for the bearing:

    $$
    \mathcal{B} = \arctan\left( 
        \frac{
            \sin(\Delta \text{lon}) \cos(\text{lat2}) 
        }{ 
            \cos(\text{lat1}) \sin(\text{lat2}) - \sin(\text{lat1}) \cos(\text{lat2}) \cos\left( \Delta \text{lon} \right)
        } 
    \right)
    $$
    
    And here the integral of the Gaussian:
    
    $$
    \frac{1}{\sqrt{2\pi}} \int_{-\infty}^{\infty} e^{-\frac{1}{2}\xi^2} \, d\xi = 1
    $$


To see how the correct formatting should look like (admitting that the bug had not been fixed in the meantime), the source of this article can be found [here](https://github.com/SebastianoF/GeoBlog/blob/master/office_positioning/office_positioning.md) (NOTE: you will have to remove the slash before each underscores manually from the url after clicking on the link).


## <a id="workflows"></a> 3. Workflows ##

The platform you are using changes your workflow, and so has an influence on the content, exactly as the [mean used to write influences the writing quality](https://www.researchgate.net/publication/276307449_A_Comparative_Study_of_Paper-and-Pen_Versus_Computer-Delivered_Assessment_Modes_on_Students'_Writing_Quality_A_Singapore_Study). If the workflow is too convoluted there will be detrimental effects on the results, and it would diminish the enthusiasm in starting a new article.

### <a id="workflows-medium"></a> 3. Medium ###

Since my technical writing involves producing content with a text editor (or a jupyter notebook) versioned controlled and stored on github, to publish on Medium this is what I would be doing:

1. Create, revise and improve content on a jupyter notebook in a versioned controlled repository on github, with images stored in a folder in the same repo, and latex formulae saved in markdown in the same place.
2. Manually copy paste each cell over to Medium, and re-format in place, transforming link, bold, italic, itemisation etc...
3. Manually copy each code snippet on github (with some naming that would allow to insert snippets in between snippets already created, and to find the snippets after a few months of not having worked on a post).
4. Manually create the images from latex formulae, and save them in an external folder somewhere (as it makes no sense to save them on the repository).
5. Manually upload the images, via drag and drop on the article, then copy paste the caption.
6. Re-read, find issues and correct in both places.

### <a id="workflows-hashnode"></a> 3. Hashnode ###

With hashnode I found two different workflows, depending on if I want to source the code from github or not.


1. Create, revise and improve content on a jupyter notebook in a versioned controlled repository on github, with images stored in a folder in the same repo, and latex formulae saved in markdown in the same place.
2. Automatically convert the Jupyter notebook to markdown with [nbconvert](https://nbconvert.readthedocs.io/en/latest/).

**With github integration:**

3. Add the [hashnode header](https://github.com/Hashnode/Hashnode-source-from-github-template) to the markdown file, then commit, and merge to master, as you would do anyway with github.
5. Re-read, find issues on hashnode and correct directly on github (look at the log if something went wrong in the uploading).

**Without github integration:**

3. Create a new article on hashnode and copy paste the created markdown.
4. Re-read, find issues on hashnode, correct directly on github (look at the log if something went wrong in the uploading), and re-do copy paste of the full file from the repository to hashnode.

## <a id="alternatives"></a> 4. Alternatives

Here we are in the *not tried yet* zone, so the following list of possible alternatives is based on google searches only (please leave a comment if you have hints and recommendations):

- **Create it from scratch with HTML, CSS and JavaScript.** if not already knowledgeable about it, it may be worth digging into HTML, CSS and JavaScript at least once in life. A blog can be the right opportunity for it. If you want to pursue this path, the blog by [Alex Nim](https://alexnim.com/coding-projects-building-my-website.html) telling you about his journey and secrets may be a good starting point.

- **[Wordpress](https://wordpress.com/)** is certainly a valid option. It has a very simple interface, has a wide range of customisation, and most importantly, it supports LaTeX. There are several technical blogs out there worth mentioning written in wordpress, one for all the blog by [Terence Tao](https://terrytao.wordpress.com/)

- **[Ghost](https://ghost.org/)** is another valid alternative, allowing to inject content directly in HTML or [render LaTeX equations](https://www.naut.ca/blog/2019/04/01/quickly-add-latex-math-rendering-to-a-ghost-blog/)

- **[Fastpages](https://github.com/fastai/fastpages)** a "wrapper" of [Jekyll](https://jekyllrb.com/) hosted on github pages and developed by volunteers, it allows the developers to create a blog directly from the jupyter notebooks. A cool example is the ML blog by [Maxime Labonne](https://mlabonne.github.io/blog/).

## <a id="conclusions"></a> 5. Conclusions

Despite the markdown and LaTeX formatting issues documented above for when sourcing an article from github, hashnode has plenty to offer for the technical writer. Using markdown instead of a WYSIWYG interface greatly reduces the editing time, it gives the possiblity of having collaborators, and set your blog into an ecosystem of bloggers producing content for developers.


## <a id="links"></a> 6. Links

(All the links below are underscore-free, so they should work despite the documented issue, even if this article is sourced from github)


- [Connect a repository to hashnode](https://townhall.hashnode.com/connect-a-github-repo-as-a-source-for-your-articles)
- [How to publish articles on hashnode](https://townhall.hashnode.com/start-using-github-to-publish-articles-on-hashnode)
- [List of tags](https://github.com/Hashnode/support/blob/main/misc/tags.json)
- [Files uploader](https://hashnode.com/uploader)
- [Template repository](https://github.com/Hashnode/Hashnode-source-from-github-template)
- [Give feedback to hashnode](https://feedback.hashnode.com/)

## <a id="credits"></a> 7. Credits

- [Qiusheng Wu:](https://wetlands.io/) his blog also had moved from Medium to Hashnode, and he introduced me to this platform.
- [Alex Nim](https://alexnim.com/) for the tutorial about how to build your own blog from scratch.
- [Maxime Labonne](https://mlabonne.github.io/blog/) for his very inspiring blog, and the insights about how to transition from a blog in fastpages to Medium.
- [Hashnode support team](https://support.hashnode.com/), in particular to uncle-big-bay, of the hashnode support team, for promptly answering my messages and looking into the issue.