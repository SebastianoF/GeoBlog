---
title: Medium to Hashnode
subtitle: An (almost) painless transition
slug: medium-to-hasgnode
tags: journey, 
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1658607085481/7KbyixY8x.png?auto=compress
domain: geods.hashnode.dev
publishAs: SebastianoF 
ignorePost: false
---
Gave up on this due to issues mentioned in the article medium to hashnode transition,
as well as documented on 
https://stackoverflow.com/questions/73099314/how-to-prevent-hashnode-dev-github-integration-from-automatically-escaping-under





A few days ago I moved from 

- [connect a repository to hashnode](https://townhall.hashnode.com/connect-a-github-repo-as-a-source-for-your-articles)
- [list of tags](https://github.com/Hashnode/support/blob/main/misc/tags.json)
- [how to upload a file](https://hashnode.com/uploader)
- [template repository](https://github.com/Hashnode/Hashnode-source-from-github-template)

This article is written on github and directly imported on [hashnode](https://geods.hashnode.dev/office-positioning) via its very handy github plugin. It seems that there are some issue in the formatting, as in the links, all the underscore are prepended with a backslash. I'm currently trying to understand how to solve the issue, and I already contacted the support from the hashnode team for help.

For example the link:

```bash
[https://github.com/SebastianoF/GeoBlog/blob/master/office_positioning/office_positioning.md](https://github.com/SebastianoF/GeoBlog/blob/master/office_positioning/office_positioning.md)
```

is formatted as:

[https://github.com/SebastianoF/GeoBlog/blob/master/office_positioning/office_positioning.md](https://github.com/SebastianoF/GeoBlog/blob/master/office_positioning/office_positioning.md)

Which is broken due to the backslash prepended to the underscores.

Also the LaTeX formaulae are not correctly formatted on this page, but they are well formatted on the github page of the link above.

**In the meantime** these problems are addressed, to see the article with images and with latex formatting, please use the link above.

-------------

References:

https://townhall.hashnode.com/start-using-github-to-publish-articles-on-hashnode

https://townhall.hashnode.com/connect-a-github-repo-as-a-source-for-your-articles