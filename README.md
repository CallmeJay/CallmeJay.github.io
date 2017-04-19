# CallmeJay.github.io

##在新电脑初次使用时：
1. 安装node,git
2. 使用git clone https://github.com/CallmeJay/CallmeJay.github.io.git拷贝仓库；
3. 在本地CallmeJay.github.io文件夹下通过Git bash依次执行npm install hexo -g、npm install 和 npm install hexo-deployer-git --save（此时当前分支应显示为hexo）（记得不要使用hexo init命令，因为hexo init 会把 .git 干掉 当然，如果不小心删掉的话，暴力点删除重新开始。）;
4. 关键的一步： 在根目录下git clone https://github.com/iissnan/hexo-theme-next themes/next ，安装next主题（远端themes/next是空的，git无法上传，原因不详），然后找到在/source/next_theme_backup 里面的——config.yml配置文件，覆盖/themes/next中的对用文件。

##在日常的改动流程
1. 依次执行git add .、git commit -m "..."、git push origin hexo指令将改动推送到GitHub（此时当前分支应为hexo）；
2. 然后才执行hexo clean, hexo g(generate), hexo d(deploy)发布网站到master分支上。

(注):虽然两个过程顺序调转一般不会有问题，不过逻辑上这样的顺序是绝对没问题的（例如突然死机要重装了，悲催....的情况，调转顺序就有问题了）。
