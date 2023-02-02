
# blog todo

- [x] 复写 markdown parse ，增加 latex 支持
    - [x] remark-math
    - [x] rehype-katex
    - [x] rehype-slug
    - [x] https://github.com/unifiedjs/unified
    - [x] recma -> 修改 MDX , remark -> 修改 markdown ， rehype -> 修改 html
    - [x] https://www.haxibami.net/blog/posts/blog-renewal
    - [x] about img to next/Image https://zenn.dev/elpnt/articles/c17727e9d254ef00ea60
    - [x] introduction of remark https://vivliostyle.github.io/vivliostyle_doc/ja/vivliostyle-user-group-vol2/spring-raining/index.html
- [x] 下标语法行为 -> 修改默认不用 a 标签
    使用 remark gfm 后自动解决
- [x] 处理 picture 路径的问题
- [x] 默认元素映射，处理代码高亮
    - [x] mermaid 渲染
    - [x] latex 渲染 弃用，思考 remark-rehype 优先
    - [x] latex 中文渲染
    - [x] vscode-memo 语法
        - [x] 站内多媒体相对链接
    - [x] latex 显示两次
        - [x] katex 需要特殊的 css ，加载后修复
    - [x] 没有闭合的左箭头自动转义
        做不到， next-mdx-remote 调用 mdx-js ， createProcessor 中会在执行所有 remark 插件之前，先执行 remark-mdx ，无法通过插件修改 
        - https://github.com/mdx-js/mdx/tree/main/packages/remark-mdx
        - https://github.com/mdx-js/mdx/blob/main/packages/mdx/lib/core.js
- [x] 简单的样式
    - [x] post 页面
    - [ ] 主页
    - [ ] about 页
- [ ] 处理旧链接跳转 https://www.viget.com/articles/host-build-and-deploy-next-js-projects-on-github-pages/
    - [x] path prefix
        - [x] 环境变量？配置文件？
    - [x] site map https://github.com/iamvishnusankar/next-sitemap
    - [ ] 去除 path 中的日期
    - [ ] link jump 301 -> 308
- [ ] git time versioning
    - [ ] created at
    - [ ] updated at
- [ ] 部署脚本
    - [x] ghpage
        - [x] gh page 环境 path 前缀设置，
        - [x] env file 使用
    - [ ] s3 test + prod
- [x] 修复 mermaid 渲染
    - [x] 方法1 remark 时处理，变静态 svg （简单）
    - [x] 方法2 highlight 推延到渲染时处理，用组件包裹 （希望优先）
        - [x] 组建渲染时快速刷新可能导致 syntax error
    - [ ] 可能的处理闪动的信息： 
        - https://github.com/ant-design/ant-design/issues/16037
        - https://stackoverflow.com/questions/72248724/components-flicker-during-server-side-render-for-nextjs-and-material-ui
        - https://www.stackfive.io/work/nextjs/how-to-fix-styled-components-page-flicker-in-next-js-12
        - https://stackoverflow.com/questions/65527040/using-prism-js-line-highlight-plugin-with-next-js-app-causes-text-content-did-n
        - https://css-tricks.com/syntax-highlighting-prism-on-a-next-js-site/#aa-highlighting-lines
        - https://www.felixmokross.dev/blog/code-samples-line-highlighting

- tips:
    - md syntax -> mdx(react component) : use mdx provider component rewriting (code block to mermaid component)
    - md syntax -> special html -> use rehype plugin rewriting (katex, highlight)
    - special md syntax -> use remark plugin rewriting (could not to mdx, should first to html and then mdx provider rewrite)

plus:
- [ ] https://conv.denshochan.com/markdown
    - [ ] 段落内の改行
    - [ ] 行頭の文字下げ
    - [ ] Twitter アカウント
    - [ ] ルビ
    - [ ] ものルビ風の熟語ルビ
- [ ] https://www.haxibami.net/blog/posts/blog-renewal
    - [ ] link card
    - [ ] image to next image https://zenn.dev/elpnt/articles/c17727e9d254ef00ea60
    - [ ] precompile mermaid
- [ ] svg 输入
    - [ ] 绘画工具： KRITA ， 免费，有 mac os 版 https://docs.krita.org/zh_CN/user_manual.html
    - [ ] KRITA 推荐使用 Inkscape 进行编辑
- [ ] vscode 语法站内相对链接
    - [ ] Rss etc https://github.com/jpmonette/feed
    - [ ] mui/base could be very good
    - [ ] search lunr？ -> mini search https://www.webpro.nl/articles/how-to-add-search-to-your-static-site
        - [ ] 可以简单参考 3b1b 直接全局 hold 住，然后全文搜索
    - [ ] blog list pagition (could be later, use infinite scroll)
- [ ] nextjs 13 app directory https://beta.nextjs.org/docs/getting-started
- [ ] ja 等多语言支持
- [ ] 基于 git 的差量 build
- [ ] 组件 visual testing https://glebbahmutov.com/blog/open-source-visual-testing-of-components/
- [ ] gh-pages gls
- [ ] gh-pages public paths
- [ ] search console?


